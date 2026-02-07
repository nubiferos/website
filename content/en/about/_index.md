---
title: "About NubiferOS"
linkTitle: "About"
weight: 30
description: "A specialized Linux distribution for cloud engineers who manage multiple accounts"
type: page-toc
---

NubiferOS is a specialized Linux distribution designed for cloud engineers who manage multiple cloud accounts—whether across the same provider or different ones. Built on Debian 12, it provides workspace isolation, secure credential management, and visual context indicators that prevent costly mistakes.

## Why NubiferOS Exists

Cloud engineers face real risks every day:

| Problem | Risk | How NubiferOS Helps |
|---------|------|---------------------|
| **Wrong account** | Running `terraform destroy` in production | Workspace isolation prevents accidental cross-account operations |
| **Credential leakage** | Secrets exposed in environment variables | GPG encryption + on-demand injection |
| **Setup time** | Days configuring a new machine | 50+ tools pre-installed, ready in 30 minutes |
| **No context** | Which account am I in right now? | Visual indicators show active workspace |
| **Destructive commands** | Accidental `aws ec2 terminate-instances` | Read-only mode blocks writes until unlocked |

## The Name

**Nubifer** (NOO-beh-fehr /ˈnuː.bɛ.fɛr/) comes from Latin meaning "cloud-bearer"—fitting for a workstation designed to carry your cloud workloads safely.

## Design Philosophy

### 1. Security First

We use battle-tested tools like GPG and pass rather than inventing our own encryption. Your credentials are protected by proven technology that has been audited for decades.

### 2. Proven Over Novel

We integrate the best existing tools rather than building custom solutions:

| Need | Our Choice | Why |
|------|------------|-----|
| Credential encryption | GPG + pass | 25+ years of security audits |
| Workspace isolation | Firejail | Lightweight, proven namespaces |
| Display security | Wayland | Modern isolation, no keylogging |
| Disk encryption | LUKS | Industry standard, hardware-accelerated |

### 3. Transparency Over Obscurity

All code is open source under GPL-3.0. You can audit every line that touches your credentials. No hidden components, no telemetry, no data collection.

### 4. Prevent Disasters

Workspace isolation significantly reduces the risk of wrong-account operations. Read-only mode lets you lock workspaces to block destructive commands. Visual indicators ensure you always know which account is active.

## Technical Foundation

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Base System** | Debian 12 (Bookworm) | Stable, well-supported foundation |
| **Desktop** | GNOME with Wayland | Modern display isolation |
| **Disk Encryption** | LUKS2 (mandatory) | Protect data at rest |
| **Credentials** | pass + GPG + GNOME Keyring | Three-layer encryption |
| **Isolation** | Firejail namespaces | Workspace separation |
| **Access Control** | AppArmor profiles | Limit application permissions |
| **Firewall** | UFW + fail2ban | Network protection |

## Roadmap

### Version 1.0 "Nimbus" (Alpha) — Q1 2026

**Target**: Individual cloud engineer, local workstation

| Feature | Status |
|---------|--------|
| Core workspace isolation | ✅ Complete |
| Credential management (GPG + pass) | ✅ Complete |
| Pre-configured tools (50+) | ✅ Complete |
| LUKS1 full disk encryption (mandatory) | ✅ Complete |
| Basic documentation | ✅ Complete |
| Wayland display isolation | ✅ Complete |
| AppArmor security profiles | ✅ Complete |

### Version 1.5 "Stratus" (Beta) — Q2 2026

**Target**: Power users, multi-machine workflows

| Feature | Status |
|---------|--------|
| Single-password boot (keyfile in initramfs) | 🔄 Planned |
| Multi-machine credential sync via git | 🔄 Planned |
| Enhanced documentation | 🔄 Planned |
| Community feedback integration | 🔄 Planned |
| Hardware security key support (YubiKey) | 🔄 Planned |

### Version 2.0 "Cumulus" — Q3-Q4 2026

**Target**: Small teams (5-20 engineers)

| Feature | Status |
|---------|--------|
| TPM 2.0 + PIN boot option | 📋 Planned |
| LUKS2 with Argon2id (GPU-resistant) | 📋 Planned |
| Team configuration management | 📋 Planned |
| Optional audit logging | 📋 Planned |
| Golden image support | 📋 Planned |
| Container-based workspaces (Podman) | 📋 Planned |
| Encrypted backup system | 📋 Planned |

### Version 3.0 "Cirrus" — 2026+

**Target**: Enterprise deployments

| Feature | Status |
|---------|--------|
| Secure Boot enforcement | 📋 Future |
| MicroVM-based workspaces (Firecracker) | 📋 Future |
| Centralized management | 📋 Future |
| SSO/OIDC integration | 📋 Future |
| Compliance reporting | 📋 Future |
| Zero-trust networking | 📋 Future |
| Advanced threat detection | 📋 Future |
| Enterprise support options | 📋 Future |

### Boot Security Options (Planned)

Currently, NubiferOS requires a LUKS passphrase at every boot. Future versions will offer:

| Option | Security Level | UX | Best For |
|--------|---------------|-----|----------|
| **Current (v1.0)** | High | Password at boot | Security-focused users |
| **Keyfile (v1.5)** | High | Single password | General use, VMs |
| **TPM + PIN (v2.0)** | Very High | PIN at boot | Enterprise, high-security |

### Out of Scope

These features are intentionally not planned:

| Feature | Reason |
|---------|--------|
| Real-time monitoring | Use existing tools (Datadog, Grafana) |
| Cloud cost analysis | Use cloud provider tools |
| Custom cloud APIs | Use official SDKs |
| Gaming support | Not our target audience |
| Unencrypted installation | LUKS is currently required (may be optional for cloud/VM deployments in future) |

## Open Source

NubiferOS is fully open source under the GPL-3.0 license.

| Resource | Link |
|----------|------|
| **Download** | [Get NubiferOS](/download/) |
| **Features** | [What's included](/features/) |
| **Why NubiferOS?** | [How we compare](/why-nubiferos/) |
| **Security** | [Security architecture](/security/) |
| **NubiferAI** | [AI-native cloud operations](/nubiferai/) |
| **Built with AI** | [Our development story](/built-with-ai/) |
| **Community** | [Join Discord](https://discord.gg/nubiferos) |
| **Source Code** | [GitHub Repository](https://github.com/nubiferos/nubiferos) |
| **Issues** | [Report a Bug](https://github.com/nubiferos/nubiferos/issues) |
