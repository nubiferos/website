---
title: "Security Architecture"
linkTitle: "Security"
weight: 25
description: "How NubiferOS protects your cloud credentials with defense-in-depth"
---

# Security Architecture

NubiferOS is built with security as the foundation, not an afterthought. We publish our [threat model](/docs/security/threat-model/) so you can verify our claims and make informed decisions.

## Why Security Matters

Cloud credentials are the keys to your infrastructure. A leaked AWS access key can result in:
- Unauthorized resource creation (cryptomining)
- Data exfiltration
- Infrastructure destruction
- Massive cloud bills

**NubiferOS protects against these threats with multiple security layers.**

## Required Security Components

These are always installed and cannot be disabled:

| Component | Purpose | Why Required |
|-----------|---------|--------------|
| **LUKS** | Full disk encryption | Protects data if device is stolen |
| **Wayland** | Display server | Prevents keylogging between apps |
| **GPG + pass** | Credential encryption | Encrypts credentials at rest |
| **Firejail** | Workspace sandboxing | Isolates credentials per workspace |
| **AppArmor** | Access control | Limits application permissions |
| **UFW** | Firewall | Blocks unauthorized network access |
| **fail2ban** | Intrusion prevention | Blocks brute force attacks |
| **auditd** | Audit logging | Logs security events |

## Security Dashboard

Monitor your security posture in real-time:

```
┌─────────────────────────────────────────────────────┐
│              Security Score: 85                     │
│         ████████████████████░░░░░░░░░░░░░           │
└─────────────────────────────────────────────────────┘

┌──────────────────────┐  ┌──────────────────────┐
│  CPU Security        │  │  Network Exposure    │
│  ⚠️  Partial         │  │  ✅ Secure           │
│                      │  │                      │
│  • Spectre v2: ✅    │  │  Firewall: Active    │
│  • RETBleed: ⚠️      │  │  Open Ports: 0       │
│  • Meltdown: ✅      │  │  SSH: Disabled       │
└──────────────────────┘  └──────────────────────┘
```

### What the Dashboard Monitors

| Check | What It Verifies |
|-------|------------------|
| **CPU Mitigations** | Spectre, Meltdown, RETBleed protection status |
| **Disk Encryption** | LUKS enabled and properly configured |
| **Network Security** | Firewall active, no unexpected open ports |
| **System Hardening** | AppArmor profiles loaded, kernel hardened |
| **Credential Status** | Vault locked/unlocked, rotation reminders |
| **Workspace Mode** | Read-only vs read-write status |

## Security Layers

NubiferOS implements defense-in-depth with 8 security layers:

| Layer | Technology | What It Protects |
|-------|------------|------------------|
| **1. Disk Encryption** | LUKS (AES-256-XTS) | Data at rest, physical theft |
| **2. CPU Mitigations** | Kernel parameters | Spectre, Meltdown, MDS |
| **3. Kernel Hardening** | sysctl, UFW, fail2ban | Network attacks, brute force |
| **4. Access Control** | AppArmor | Privilege escalation |
| **5. Desktop Isolation** | Wayland | Keylogging, screen capture |
| **6. Credential Encryption** | GPG + pass | Credential theft |
| **7. App Sandboxing** | Firejail | Cross-workspace leakage |
| **8. User Education** | Documentation | Social engineering |

## Three-Layer Credential Encryption

Your credentials are protected by three encryption layers:

```
┌─────────────────────────────────────────────────────────┐
│  Layer 1: LUKS Disk Encryption (AES-256)               │
│  └── Layer 2: GPG Encryption (via pass)                │
│      └── Layer 3: GNOME Keyring (runtime)              │
│          └── Your cloud credentials                    │
└─────────────────────────────────────────────────────────┘
```

**What this means:** If someone steals your laptop, they'd need your disk passphrase, your GPG key, AND your keyring password to access your credentials.

### No Plaintext Credentials

Unlike traditional setups, NubiferOS never stores credentials in plaintext:

| Traditional Setup | NubiferOS |
|-------------------|-----------|
| `~/.aws/credentials` with plaintext keys | File doesn't exist |
| Any process can read credentials | Credentials in GPG-encrypted pass store |
| `cat ~/.aws/credentials` exposes all | `pass show` requires GPG unlock |

### CLI Wrappers

When you run `aws s3 ls`, the CLI wrapper:
1. Detects your active workspace
2. Fetches credentials from pass (GPG-encrypted)
3. Injects them directly into the CLI process
4. Logs the access for audit

Credentials are never exposed in environment variables or written to disk in plaintext.

## Wayland Display Security

| Feature | X11 (Old) | Wayland (NubiferOS) |
|---------|-----------|---------------------|
| Keylogging | Any app can read all keystrokes | Apps isolated from each other |
| Screen capture | Any app can capture any screen | Requires explicit permission |
| Window injection | Possible | Blocked |
| Credential theft | Easy via display server | Protected by isolation |

**What this means:** Malicious apps can't spy on your keyboard input or capture screenshots of your cloud console.

## Firejail Workspace Isolation

Each workspace runs in isolated Linux namespaces:

| Namespace | What It Isolates | Security Benefit |
|-----------|------------------|------------------|
| **Mount** | Filesystem view | Each workspace sees only its credentials |
| **PID** | Process IDs | Can't see/signal other workspace processes |
| **IPC** | Shared memory | No shared memory attacks |
| **UTS** | Hostname | Per-workspace hostname |

**What this means:** Credentials from Workspace A are invisible to Workspace B, even if both are running simultaneously.

## Read-Only Mode

Lock workspaces to prevent destructive operations:

| Mode | Indicator | Behavior |
|------|-----------|----------|
| **Read-Only** | 🔒 RO (green) | Destructive commands blocked |
| **Read-Write** | 🔓 RW (red) | All commands allowed |

```bash
# Lock workspace (no sudo needed)
nubifer-workspace ro

# Unlock workspace (requires sudo)
sudo nubifer-workspace rw

# Timed unlock (auto-reverts)
sudo nubifer-workspace rw -d 15  # 15 minutes
```

## Battle-Tested Tools

We use proven tools instead of custom implementations:

| Tool | Purpose | In Use Since |
|------|---------|--------------|
| **GPG** | Credential encryption | 1999 (25+ years) |
| **pass** | Password management | 2012 |
| **Firejail** | Application sandboxing | 2014 |
| **AppArmor** | Mandatory access control | 2007 |
| **Wayland** | Display isolation | 2012 |
| **LUKS** | Disk encryption | 2004 |

## What We Protect Against

| Threat | Protection | Status |
|--------|------------|--------|
| Credential theft from disk | LUKS + GPG encryption | ✅ Protected |
| Cross-account credential access | Firejail namespace isolation | ✅ Protected |
| Accidental destructive operations | Read-only mode | ✅ Protected |
| Keylogging between applications | Wayland isolation | ✅ Protected |
| Supply chain attacks | SBOM + GPG signing | ✅ Protected |
| Physical device theft | LUKS full disk encryption | ✅ Protected |

## What We Don't Protect Against

| Threat | Reason | Mitigation |
|--------|--------|------------|
| Hardware-level attacks | Software can't defend against hardware | Use trusted hardware |
| Nation-state actors | Beyond scope of workstation OS | Use specialized security |
| Social engineering | Human factors | User education |
| Compromised cloud providers | Provider's responsibility | Use MFA, rotate credentials |
| Physical access with passwords | By design (no backdoors) | Protect your passwords |

## Privacy First

| Aspect | NubiferOS Approach |
|--------|-------------------|
| **Telemetry** | None. Zero data collection. |
| **Network calls** | Credentials never leave your machine |
| **External services** | No cloud dependencies for security |
| **Audit** | All code is open source (GPL-3.0) |

## Learn More

| Topic | Link |
|-------|------|
| **Threat Model** | [What we protect against](/docs/security/threat-model/) |
| **Credential Security** | [How credentials are encrypted](/docs/security/credential-security/) |
| **Workspace Isolation** | [How namespaces work](/docs/security/workspace-isolation/) |
| **Source Code** | [Security documentation on GitHub](https://github.com/nubiferos/nubiferos/tree/main/docs) |
