---
title: "Features"
linkTitle: "Features"
weight: 15
description: "What makes NubiferOS the secure choice for cloud engineers"
---

# Features

NubiferOS is built for cloud engineers who manage multiple accounts and need security they can trust. Here's what sets it apart.

## Security by Default

These components are always installed and cannot be disabled. They form the security foundation.

| Component | What It Does | Why It Matters |
|-----------|--------------|----------------|
| **LUKS Disk Encryption** | Encrypts entire disk with AES-256 | Laptop stolen? Data unreadable. |
| **GPG + pass** | Encrypts credentials at rest | No plaintext `~/.aws/credentials` |
| **Firejail** | Isolates workspaces | Prod credentials invisible to dev workspace |
| **Wayland** | Modern display server | Apps can't keylog each other |
| **AppArmor** | Limits application access | Compromised app can't escape |

→ [Learn more about security architecture](/security/)

## No Plaintext Credentials

Traditional setups store credentials in plaintext files. NubiferOS doesn't.

```bash
# Traditional (INSECURE)
cat ~/.aws/credentials
# Shows plaintext keys to any process

# NubiferOS
cat ~/.aws/credentials
# File doesn't exist - credentials in GPG-encrypted pass store
```

When you run `aws s3 ls`, our CLI wrapper:
1. Fetches credentials from encrypted pass store
2. Injects them directly into the CLI process
3. Logs the access for audit

→ [How credential security works](/docs/security/credential-security/)

## Workspace Isolation

Each workspace runs in isolated Linux namespaces via Firejail:

| What's Isolated | Security Benefit |
|-----------------|------------------|
| **Filesystem** | Each workspace sees only its own credentials |
| **Processes** | Can't see or signal other workspace processes |
| **IPC** | No shared memory attacks between workspaces |

→ [How workspace isolation works](/docs/security/workspace-isolation/)

## Read-Only Mode

Lock workspaces to prevent destructive operations:

```bash
# Lock workspace (no sudo needed)
nubifer-workspace ro
# Or use the shortcut: nw ro

# Unlock for 15 minutes (requires sudo, auto-reverts)
sudo nubifer-workspace rw -d 15
# Or: sudo nw rw -d 15
```

| Mode | Indicator | Behavior |
|------|-----------|----------|
| **Read-Only** | 🔒 RO (green) | Destructive commands blocked |
| **Read-Write** | 🔓 RW (red) | All commands allowed |

→ [Workspace management guide](/docs/user-guide/workspaces/)

## Security Dashboard

Monitor your security posture in real-time:

| Check | What It Verifies |
|-------|------------------|
| **CPU Mitigations** | Spectre, Meltdown, RETBleed protection |
| **Disk Encryption** | LUKS enabled and configured |
| **Network Security** | Firewall active, no unexpected open ports |
| **Credential Status** | Vault status, rotation reminders |
| **Workspace Mode** | Read-only vs read-write |

```bash
# Launch dashboard
nubifer-dashboard

# Quick CLI check
sudo verify-security
```

## 50+ Cloud Tools

Choose what you need during installation. Don't need Azure tools? Don't install them.

| Category | Examples |
|----------|----------|
| **Cloud CLIs** | AWS CLI, Azure CLI, gcloud |
| **Infrastructure as Code** | Terraform, Pulumi, Ansible |
| **Containers & K8s** | Docker, kubectl, Helm, k9s |
| **CI/CD & GitOps** | GitHub CLI, ArgoCD, Flux |
| **IDEs** | VS Code, IntelliJ, PyCharm |

→ [Full tool list](/docs/reference/tools/)

## Battle-Tested Tools

We use proven, audited tools—not custom implementations:

| Tool | Purpose | In Use Since |
|------|---------|--------------|
| **GPG** | Credential encryption | 1999 (25+ years) |
| **pass** | Password management | 2012 |
| **Firejail** | Application sandboxing | 2014 |
| **AppArmor** | Mandatory access control | 2007 |
| **LUKS** | Disk encryption | 2004 |

## Privacy First

| Aspect | NubiferOS Approach |
|--------|-------------------|
| **Telemetry** | None. Zero data collection. |
| **Network calls** | Credentials never leave your machine |
| **External services** | No cloud dependencies for security |
| **Audit** | All code is open source (GPL-3.0) |

## Ready to Try It?

<a href="/download/" class="btn btn-warning btn-lg"><i class="fas fa-download me-2"></i>Download NubiferOS</a>
<a href="/docs/getting-started/quick-start/" class="btn btn-outline-primary btn-lg ms-2">Quick Start Guide</a>

## Learn More

| Topic | Link |
|-------|------|
| **Why NubiferOS?** | [How we compare to alternatives](/why-nubiferos/) |
| **Security Architecture** | [Defense-in-depth details](/security/) |
| **Built with AI** | [Our AI-assisted development story](/built-with-ai/) |
| **About** | [Project philosophy and roadmap](/about/) |
