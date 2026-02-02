---
title: "Workspace Isolation"
linkTitle: "Workspace Isolation"
weight: 30
description: "How Linux namespaces and Firejail prevent cross-account credential access"
---

Workspace isolation is NubiferOS's core defense against cross-account mistakes and credential leakage. Each workspace runs in an isolated sandbox using Linux namespaces, ensuring credentials from one cloud account are invisible to another.

## Why Workspace Isolation?

Cloud engineers face a critical security challenge: managing multiple cloud accounts without accidentally leaking credentials between them.

**The risks are real:**
- Malware in one workspace stealing credentials from another
- Browser extensions reading AWS credentials from disk
- Accidental credential exposure via environment variables
- Cross-account operations due to credential confusion

**Workspace isolation solves this by:**
- Running each workspace in separate Linux namespaces
- Making credentials from other workspaces invisible
- Preventing processes from seeing or signaling each other
- Blocking inter-process communication attacks

## How Credentials Are Protected

NubiferOS uses a fundamentally different approach than traditional setups:

### Traditional Setup (Insecure)
```bash
# ~/.aws/credentials - PLAINTEXT FILE
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

# Any process can read this file
# Any malware can steal these credentials
# cat ~/.aws/credentials exposes everything
```

### NubiferOS Approach (Secure)
```bash
# ~/.aws/credentials - DOESN'T EXIST
# Credentials stored in GPG-encrypted pass store

# ~/.aws/config - ONLY CONTAINS CONFIGURATION
[profile production]
region = us-east-1
credential_process = /usr/local/bin/nubifer-aws-credential-helper production

# Credentials fetched on-demand from encrypted store
# Never written to disk in plaintext
# Each workspace has its own encrypted credential path
```

### How CLI Wrappers Work

When you run `aws s3 ls`, here's what happens:

```
┌─────────────────────────────────────────────────────────┐
│  1. You type: aws s3 ls                                │
├─────────────────────────────────────────────────────────┤
│  2. CLI wrapper detects active workspace               │
├─────────────────────────────────────────────────────────┤
│  3. Wrapper calls credential_process helper            │
├─────────────────────────────────────────────────────────┤
│  4. Helper fetches from pass (GPG-encrypted)           │
│     pass show nubifer/workspace-abc/cloud/aws/prod     │
├─────────────────────────────────────────────────────────┤
│  5. Credentials injected directly into CLI process     │
│     (never exposed in environment variables)           │
├─────────────────────────────────────────────────────────┤
│  6. Firejail ensures only current workspace visible    │
├─────────────────────────────────────────────────────────┤
│  7. Access logged to audit log                         │
└─────────────────────────────────────────────────────────┘
```

**Key security benefits:**
- No plaintext credentials on disk
- Credentials fetched on-demand, not stored in memory
- Each workspace has isolated credential paths
- Firejail namespace prevents cross-workspace access
- All access is logged for audit

## How It Works

When you activate a workspace, NubiferOS creates an isolated sandbox using Firejail with multiple Linux namespaces:

```
┌─────────────────────────────────────────────────────────┐
│  Workspace A (AWS Production)                           │
│  ├── Mount Namespace: sees ~/.aws/workspace-abc123/    │
│  ├── PID Namespace: can only see its own processes     │
│  ├── IPC Namespace: isolated shared memory             │
│  └── UTS Namespace: can have its own hostname          │
├─────────────────────────────────────────────────────────┤
│  Workspace B (AWS Development)                          │
│  ├── Mount Namespace: sees ~/.aws/workspace-def456/    │
│  ├── PID Namespace: can only see its own processes     │
│  ├── IPC Namespace: isolated shared memory             │
│  └── UTS Namespace: can have its own hostname          │
└─────────────────────────────────────────────────────────┘
```

**Key principle**: Workspace A cannot see Workspace B's credentials, processes, or memory—even if both are running simultaneously.

## Linux Namespaces Explained

Linux namespaces are a kernel feature that partitions system resources so processes in different namespaces have isolated views of the system. Think of them as lightweight containers.

### The 7 Types of Linux Namespaces

| Namespace | What It Isolates | Used by NubiferOS? |
|-----------|------------------|-------------------|
| **Mount (mnt)** | Filesystem mount points | ✅ Yes (default) |
| **PID** | Process IDs | ✅ Yes (default) |
| **Network (net)** | Network interfaces, routing | ⚠️ Optional |
| **IPC** | Inter-process communication | ✅ Yes (default) |
| **UTS** | Hostname and domain name | ✅ Yes (default) |
| **User** | User and group IDs | ⚠️ Optional |
| **Cgroup** | Control group hierarchy | ❌ No |

### Mount Namespace (Filesystem Isolation)

Each workspace sees a different filesystem view:

```bash
# Workspace 1 (AWS Prod)
$ ls ~/.aws/
workspace-abc123/    # ✅ Can see this
                     # ❌ Cannot see workspace-def456/

# Workspace 2 (AWS Dev)  
$ ls ~/.aws/
workspace-def456/    # ✅ Can see this
                     # ❌ Cannot see workspace-abc123/
```

### PID Namespace (Process Isolation)

Processes in the sandbox can't see or signal processes outside:

```bash
# Outside sandbox
$ ps aux | wc -l
247 processes

# Inside Firejail sandbox
$ ps aux | wc -l
12 processes  # Can only see processes in this namespace
```

**Security benefit**: Malicious code can't kill other processes, inspect their memory, or attach debuggers.

### IPC Namespace (Communication Isolation)

Isolates System V IPC objects (message queues, semaphores, shared memory).

**Security benefit**: Prevents shared memory attacks between workspaces, message queue snooping, and semaphore manipulation.

## Quick Start

### Create Isolated Workspaces

```bash
# AWS Production workspace
nubifer-workspace create \
  --name "AWS Production" \
  --provider aws \
  --account-id 123456789012 \
  --region us-east-1

# AWS Development workspace
nubifer-workspace create \
  --name "AWS Development" \
  --provider aws \
  --account-id 987654321098 \
  --region us-west-2
```

### Switch Between Workspaces

```bash
# Switch to production
nubifer-workspace switch "aws production"

# Run AWS CLI - only sees production credentials
aws s3 ls

# Switch to development
nubifer-workspace switch "aws development"

# Run AWS CLI - only sees development credentials
aws s3 ls
```

### Verify Isolation

```bash
# Check namespace IDs
ls -la /proc/self/ns/mnt
# lrwxrwxrwx 1 user user 0 mnt -> 'mnt:[4026532539]'

# Different workspace = different namespace ID
```

## Firejail Profile Example

Each workspace generates a Firejail profile that enforces isolation:

```
# Workspace ABC123 profile
whitelist ~/.aws/workspace-abc123
blacklist ~/.aws/workspace-def456
blacklist ~/.aws/workspace-ghi789
read-only ~/.aws/workspace-abc123  # If read-only mode enabled
rlimit-as 2G  # Max 2GB memory
rlimit-cpu 3600  # Max 1 hour CPU
```

## Use Cases

### Use Case 1: Running Firefox in Two Workspaces

```bash
# Terminal 1: AWS Prod workspace
$ nubifer-workspace switch "aws-prod"
$ firefox &
# Firefox runs in namespace A
# Can access: ~/.aws/workspace-prod/
# Cannot access: ~/.aws/workspace-dev/

# Terminal 2: AWS Dev workspace  
$ nubifer-workspace switch "aws-dev"
$ firefox &
# Firefox runs in namespace B (different from A)
# Can access: ~/.aws/workspace-dev/
# Cannot access: ~/.aws/workspace-prod/
```

**Key point**: These are TWO DIFFERENT Firefox processes in TWO DIFFERENT namespaces. They cannot see each other's files, credentials, or communicate via shared memory.

### Use Case 2: Protecting Against Malicious Browser Extensions

```
❌ Without namespaces:
Firefox extension → reads ~/.aws/credentials → steals ALL credentials

✅ With namespaces:
Firefox extension → reads ~/.aws/credentials → only sees current workspace
```

### Use Case 3: Limiting Blast Radius of Compromised CLI

```
❌ Without namespaces:
Malicious aws-cli → scans ~/.aws/ → finds all workspaces → exfiltrates all

✅ With namespaces:
Malicious aws-cli → scans ~/.aws/ → only sees current workspace → limited damage
```

### Use Case 4: Preventing Accidental Credential Exposure

```
❌ Traditional setup (NOT NubiferOS):
User runs: cat ~/.aws/credentials
Output: Shows plaintext credentials visible to any process

✅ NubiferOS approach:
User runs: cat ~/.aws/credentials
Output: File doesn't exist - credentials stored in GPG-encrypted pass store
```

**How it works**: NubiferOS doesn't store credentials in plaintext files. Instead:

1. Credentials are stored in GPG-encrypted pass store, organized by workspace:
   ```bash
   pass show nubifer/<workspace-id>/cloud/aws/<profile>/access-key-id
   # Requires GPG unlock, shows only the specific credential requested
   ```

2. AWS CLI uses `credential_process` to fetch credentials on-demand:
   ```ini
   # ~/.aws/config
   [profile myprofile]
   credential_process = /usr/local/bin/nubifer-aws-credential-helper myprofile
   ```

3. Even if someone accesses the pass store, Firejail namespace limits visibility to the current workspace only

## Comparing Isolation Technologies

| Technology | Mount NS | PID NS | Net NS | IPC NS | Overhead |
|------------|----------|--------|--------|--------|----------|
| **Firejail (default)** | ✅ | ✅ | ❌ | ✅ | ~5-10ms |
| **Firejail (full)** | ✅ | ✅ | ✅ | ✅ | ~15-20ms |
| **Docker** | ✅ | ✅ | ✅ | ✅ | ~50-100ms |
| **VM** | ✅ | ✅ | ✅ | ✅ | ~1-2s |
| **No isolation** | ❌ | ❌ | ❌ | ❌ | 0ms |

**Why Firejail for NubiferOS**: Best balance of security and performance for a daily-driver workstation.

### Firejail vs Docker

| Feature | Firejail | Docker |
|---------|----------|--------|
| Namespaces | 5/7 (default) | 7/7 |
| Startup time | ~10ms | ~100ms |
| Memory overhead | ~1MB | ~10MB |
| Desktop apps | ✅ Excellent | ⚠️ Requires X11 forwarding |
| CLI tools | ✅ Excellent | ✅ Excellent |

### Firejail vs VMs

| Feature | Firejail | VM |
|---------|----------|-----|
| Isolation level | Process-level | Kernel-level |
| Startup time | ~10ms | ~2s |
| Memory overhead | ~1MB | ~512MB |
| Performance | Near-native | 5-10% slower |

## Performance Impact

### Namespace Creation Overhead

| Operation | Time |
|-----------|------|
| Create mount namespace | ~5ms |
| Create PID namespace | ~2ms |
| Create IPC namespace | ~1ms |
| Create UTS namespace | ~1ms |
| **Total (default)** | **~10ms** |

### Runtime Overhead

| Operation | Without Namespaces | With Namespaces | Overhead |
|-----------|-------------------|-----------------|----------|
| File access | 0.1ms | 0.11ms | +10% |
| Process creation | 1ms | 1.05ms | +5% |
| AWS API call | 200ms | 201ms | +0.5% |

**Conclusion**: Namespace overhead is negligible for typical cloud operations.

## What Namespaces DON'T Isolate

### Kernel Resources
- **CPU**: All processes share the same CPU (use cgroups for CPU isolation)
- **Memory**: All processes share physical memory (use cgroups for limits)
- **Kernel**: All processes run on the same kernel

### Hardware
- **GPU**: Shared across all namespaces
- **USB devices**: Shared (unless explicitly blacklisted)
- **Network hardware**: Shared (unless network namespace enabled)

## Advanced: Enabling Additional Namespaces

### Enable Network Namespace

For stronger isolation, enable network namespace in the Firejail profile:

```bash
# Enable network namespace
net eth0

# Restrict to specific IP ranges (e.g., AWS endpoints only)
netfilter /etc/firejail/nubifer/aws-endpoints.net
```

Example network filter:

```
*filter
:INPUT DROP [0:0]
:OUTPUT ACCEPT [0:0]

# Allow AWS IP ranges
-A OUTPUT -d 52.0.0.0/8 -j ACCEPT
-A OUTPUT -d 54.0.0.0/8 -j ACCEPT
-A OUTPUT -d 3.0.0.0/8 -j ACCEPT

# Allow DNS
-A OUTPUT -p udp --dport 53 -j ACCEPT

# Block everything else
-A OUTPUT -j DROP
COMMIT
```

## Best Practices

### 1. One Workspace Per Account
Don't mix multiple accounts in one workspace. Create separate workspaces for each AWS account, Azure subscription, or GCP project.

### 2. Enable Read-Only for Production
Always create production workspaces with read-only mode:
```bash
nubifer-workspace create --name "Prod" --provider aws \
  --account-id 123456789012 --read-only
```

### 3. Verify Isolation Regularly
Test that isolation is working:
```bash
# In workspace A
echo "secret-A" > ~/.aws/test.txt

# Switch to workspace B
nubifer-workspace switch "workspace-b"

# Try to read workspace A file (should fail)
cat ~/.aws/test.txt
# Permission denied or file not found
```

### 4. Use Timed Write Sessions
When making production changes:
```bash
sudo nubifer-workspace rw -d 15  # Auto-reverts after 15 minutes
```

## Troubleshooting

### Check Active Namespaces

```bash
# Find Firejail process
ps aux | grep firejail

# Check its namespaces
sudo ls -la /proc/<PID>/ns/

# Compare to host namespaces
ls -la /proc/self/ns/
```

### Verify Isolation

```bash
# Run in sandbox
firejail --profile=~/.config/firejail/nubifer-aws-*.profile bash

# Check what you can see
ls ~/.aws/          # Should only see current workspace
ps aux              # Should only see sandbox processes
```

### Debug Namespace Issues

```bash
# Run with debug output
firejail --debug --profile=<profile> <command>

# Check kernel support
ls /proc/self/ns/
# Should show: mnt, pid, net, ipc, uts, user, cgroup
```

## Security Benefits Summary

| Threat | Without Isolation | With Isolation |
|--------|-------------------|----------------|
| Credential leakage | All workspaces exposed | Only current workspace |
| Process snooping | Can inspect all processes | Only sandbox processes |
| Filesystem traversal | Full access | Restricted to whitelist |
| IPC attacks | Shared memory accessible | Isolated per workspace |
| Privilege escalation | Full system access | Limited by namespace |

## Source Code

- [Namespace Isolation Explained](https://github.com/nubiferos/nubiferos/blob/main/docs/NAMESPACE_ISOLATION_EXPLAINED.md)
- [Workspace Manager Component](https://github.com/nubiferos/nubiferos/tree/main/components/workspace-manager)
- [Firejail Integration](https://github.com/nubiferos/nubiferos/blob/main/components/workspace-manager/FIREJAIL_INTEGRATION.md)
- [Linux Namespaces Man Page](https://man7.org/linux/man-pages/man7/namespaces.7.html)
