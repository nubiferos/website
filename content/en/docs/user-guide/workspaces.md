---
title: "Workspaces"
linkTitle: "Workspaces"
weight: 10
description: "Isolated environments for managing multiple cloud accounts safely"
---

Workspaces are the core concept in NubiferOS. Each workspace is an isolated environment for a specific cloud account, preventing credential leakage and accidental cross-account operations.

## Why Workspaces?

Cloud engineers face a common problem: managing multiple cloud accounts without accidentally running commands in the wrong one.

**The risk is real:**
- Running `terraform destroy` in production instead of staging
- Deploying to the wrong AWS account
- Leaking credentials between accounts
- No visual indication of which account is active

**Workspaces solve this by:**
- Isolating credentials per account using Linux namespaces
- Providing visual context (colored prompts, icons)
- Blocking destructive commands with read-only mode
- Logging all workspace operations for audit

## How It Works

When you activate a workspace, NubiferOS:

1. **Creates isolated namespaces** - Your shell runs in a Firejail sandbox with its own mount, PID, and IPC namespaces
2. **Injects environment variables** - Cloud CLIs automatically use the correct region, account, and credentials
3. **Updates visual indicators** - Terminal prompt shows provider, account, and read-only status
4. **Enforces read-only mode** - If enabled, destructive commands are blocked

```
┌─────────────────────────────────────────────────────────┐
│  Your Terminal                                          │
│  [🔒 RO][☁️ prod-account | us-east-1] user@host:~$     │
├─────────────────────────────────────────────────────────┤
│  Firejail Sandbox (Mount + PID + IPC namespaces)       │
│  ├── AWS CLI → sees only prod-account credentials      │
│  ├── Terraform → uses prod-account state               │
│  └── kubectl → connects to prod cluster                │
└─────────────────────────────────────────────────────────┘
```

## Quick Start

### Create a Workspace

```bash
# AWS workspace
nubifer-workspace create \
  --name "AWS Production" \
  --provider aws \
  --account-id 123456789012 \
  --account-name "prod-account" \
  --region us-east-1

# Azure workspace
nubifer-workspace create \
  --name "Azure Dev" \
  --provider azure \
  --account-id "subscription-id" \
  --account-name "dev-subscription" \
  --region eastus

# GCP workspace
nubifer-workspace create \
  --name "GCP Staging" \
  --provider gcp \
  --account-id "project-id" \
  --account-name "staging-project" \
  --region us-central1
```

### Switch Workspaces

```bash
# List available workspaces
nubifer-workspace list

# Switch by name (case-insensitive)
nubifer-workspace switch "aws production"

# Switch by ID
nubifer-workspace switch abc123def456

# Activate in current shell
eval $(nubifer-workspace env abc123def456)
```

### View Current Workspace

```bash
nubifer-workspace current
```

Output:
```
============================================================
☁️ Workspace: AWS Production
============================================================
Provider:  AWS
Account:   prod-account
Region:    us-east-1
Mode:      🔒 Read-Only
============================================================
```

## Visual Context

Each cloud provider has a distinct color for instant recognition:

| Provider | Color | Icon | Example Prompt |
|----------|-------|------|----------------|
| AWS | 🟠 Orange | ☁️ | `[🔒 RO][☁️ prod-account]` |
| Azure | 🔵 Blue | ⛅ | `[🔒 RO][⛅ dev-subscription]` |
| GCP | 🔵🔴🟡🟢 Multi | 🔵🔴🟡🟢 | `[🔓 RW][🔵🔴🟡🟢 staging-project]` |
| Oracle | 🔴 Red | ☁️ | `[🔒 RO][☁️ oci-tenancy]` |

The mode indicator uses colored backgrounds:
- **Green background (🔒 RO)**: Safe mode, writes blocked
- **Red background (🔓 RW)**: Danger mode, writes allowed

## Read-Only Mode

Lock workspaces to prevent destructive operations.

### Enable/Disable Read-Only

```bash
# Lock workspace (enable read-only)
nubifer-workspace ro

# Unlock workspace (requires sudo for security)
sudo nubifer-workspace rw

# Timed unlock (auto-reverts after N minutes)
sudo nubifer-workspace rw -d 30   # 30 minutes
sudo nubifer-workspace rw -d 60   # 1 hour
```

### Why Sudo for Write Mode?

Enabling writes requires `sudo` as a security measure:
- **Prevents unauthorized writes** from compromised sessions
- **Creates audit trail** via sudo logs
- **Forces intentional action** - can't accidentally enable writes
- **Limits blast radius** if an attacker gains shell access

### When Writes Are Blocked

```
┌─────────────────────────────────────────────────────────┐
│ 🔒 WRITE BLOCKED  Workspace is in read-only mode        │
└─────────────────────────────────────────────────────────┘

  Command: aws ec2 terminate-instances --instance-ids i-123

  To enable writes (requires sudo):
    sudo nubifer-workspace rw

  For timed write access (auto-reverts):
    sudo nubifer-workspace rw -d 30  # 30 minutes
```

## Use Cases

### Use Case 1: Separate Production and Development

Create distinct workspaces for each environment:

```bash
# Production (enable read-only for safety)
nubifer-workspace create \
  --name "AWS Prod" \
  --provider aws \
  --account-id 111111111111 \
  --region us-east-1 \
  --read-only

# Development (read-write)
nubifer-workspace create \
  --name "AWS Dev" \
  --provider aws \
  --account-id 222222222222 \
  --region us-west-2
```

Now you can't accidentally run `terraform destroy` in production.

### Use Case 2: Multi-Cloud Management

Manage AWS, Azure, and GCP from the same workstation:

```bash
# Morning: Work on AWS infrastructure
nubifer-workspace switch "aws prod"
terraform plan

# Afternoon: Deploy to Azure
nubifer-workspace switch "azure staging"
az deployment group create ...

# Evening: Check GCP logs
nubifer-workspace switch "gcp logging"
gcloud logging read ...
```

Each switch updates your environment variables, credentials, and visual context.

### Use Case 3: Client Isolation (Consultants)

Keep client environments completely separate:

```bash
# Client A
nubifer-workspace create \
  --name "Client A - Prod" \
  --provider aws \
  --account-id 333333333333

# Client B
nubifer-workspace create \
  --name "Client B - Prod" \
  --provider aws \
  --account-id 444444444444
```

Credentials from Client A are invisible when working in Client B's workspace.

### Use Case 4: Safe Production Changes

Make changes to production with a safety net:

```bash
# 1. Switch to production workspace
nubifer-workspace switch "aws prod"

# 2. Verify you're in read-only mode
nubifer-workspace current
# Mode: 🔒 Read-Only

# 3. Enable writes for 15 minutes
sudo nubifer-workspace rw -d 15

# 4. Make your changes
terraform apply

# 5. Workspace auto-reverts to read-only after 15 minutes
# Or lock immediately:
nubifer-workspace ro
```

## Environment Variables

Each workspace sets provider-specific environment variables:

### AWS
```bash
AWS_DEFAULT_REGION=us-east-1
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012
NUBIFER_WORKSPACE_PROVIDER=aws
NUBIFER_WORKSPACE_ACCOUNT=prod-account
```

### Azure
```bash
AZURE_LOCATION=eastus
AZURE_SUBSCRIPTION_ID=subscription-id
NUBIFER_WORKSPACE_PROVIDER=azure
NUBIFER_WORKSPACE_ACCOUNT=dev-subscription
```

### GCP
```bash
GOOGLE_CLOUD_PROJECT=project-id
GOOGLE_CLOUD_REGION=us-central1
NUBIFER_WORKSPACE_PROVIDER=gcp
NUBIFER_WORKSPACE_ACCOUNT=staging-project
```

## Namespace Isolation

Workspaces use Linux namespaces via Firejail for strong isolation:

| Namespace | What It Isolates | Benefit |
|-----------|------------------|---------|
| Mount | Filesystem view | Each workspace sees only its credentials |
| PID | Process IDs | Can't see/signal other workspace processes |
| IPC | Inter-process communication | No shared memory attacks |
| UTS | Hostname | Can set per-workspace hostname |

**Example**: When you run `aws s3 ls` in Workspace A, it runs in a sandbox that can only see Workspace A's credentials. Even if malware compromises that process, it can't access Workspace B's credentials.

Learn more: [Workspace Isolation](/docs/security/workspace-isolation/)

## Best Practices

### 1. One Workspace Per Account
Don't mix multiple accounts in one workspace. Create separate workspaces for each AWS account, Azure subscription, or GCP project.

### 2. Enable Read-Only for Production
Always create production workspaces with `--read-only`:
```bash
nubifer-workspace create --name "Prod" --provider aws \
  --account-id 123456789012 --read-only
```

### 3. Use Timed Write Sessions
When making production changes, use timed unlocks:
```bash
sudo nubifer-workspace rw -d 15  # Auto-reverts after 15 minutes
```

### 4. Check Before You Run
Always verify your workspace before running commands:
```bash
nubifer-workspace current
```

### 5. Use Descriptive Names
Include environment and purpose:
- ✅ "AWS Production - Web Servers"
- ✅ "Azure Dev - Database"
- ❌ "workspace1"

## Command Reference

| Command | Shortcut | Description |
|---------|----------|-------------|
| `nubifer-workspace create` | `nw create` | Create a new workspace |
| `nubifer-workspace list` | `nw list` | List all workspaces |
| `nubifer-workspace switch <id>` | `nw-switch <id>` | Switch to a workspace |
| `nubifer-workspace current` | `nw-context` | Show current workspace |
| `nubifer-workspace ro` | `nw ro` | Enable read-only mode |
| `sudo nubifer-workspace rw` | `sudo nw rw` | Enable read-write mode |
| `sudo nubifer-workspace rw -d N` | `sudo nw rw -d N` | Timed read-write (N minutes) |
| `nubifer-workspace delete <id>` | `nw delete <id>` | Delete a workspace |
| `nubifer-workspace update <id>` | `nw update <id>` | Update workspace settings |

### Shell Aliases

NubiferOS provides shortcuts for common commands. These are loaded automatically via `/etc/nubifer/shell-integration.sh`:

```bash
alias nw='nubifer-workspace'        # Main workspace command
alias nw-switch='nubifer_switch'    # Quick switch with activation
alias nw-context='nubifer_context'  # Show current workspace
alias nw-activate='nubifer_activate' # Activate workspace in current shell
```

**Examples:**
```bash
# These are equivalent:
nubifer-workspace list
nw list

# Quick switch (switches and activates in one step)
nw-switch "aws prod"

# Check current context
nw-context
```

## Troubleshooting

### Prompt Not Updating

```bash
# Reload shell integration
source /etc/nubifer/shell-integration.sh

# Or restart your shell
exec bash
```

### Environment Variables Not Set

```bash
# Make sure to eval the output
eval $(nubifer-workspace env <workspace-id>)
```

### Can't Delete Workspace

You can't delete the currently active workspace. Switch to another first:
```bash
nubifer-workspace switch "other-workspace"
nubifer-workspace delete <workspace-id>
```

## Source Code

- [Workspace Manager Component](https://github.com/nubiferos/nubiferos/tree/trunk/components/workspace-manager)
- [Namespace Isolation Explained](https://github.com/nubiferos/nubiferos/blob/trunk/docs/NAMESPACE_ISOLATION_EXPLAINED.md)
- [Firejail Integration](https://github.com/nubiferos/nubiferos/blob/trunk/components/workspace-manager/FIREJAIL_INTEGRATION.md)
