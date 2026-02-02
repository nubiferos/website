---
title: "Download NubiferOS"
linkTitle: "Download"
weight: 10
description: "Get NubiferOS Alpha and start managing your cloud accounts securely"
---

# Download NubiferOS

The secure cloud engineer's workstation with workspace isolation, encrypted credentials, and 50+ cloud tools available.

{{% alert title="Alpha Software" color="warning" %}}
This is alpha software intended for testing and feedback. Expect bugs and rough edges. Please [report issues on GitHub](https://github.com/nubiferos/nubiferos/issues).
{{% /alert %}}

---

## NubiferOS 1.0 "Nimbus" (Alpha)

The first public release of NubiferOS. Built on Debian 12 with GNOME/Wayland.

<div style="background: linear-gradient(135deg, #1E3A8A 0%, #1e40af 100%); padding: 2rem; border-radius: 12px; margin: 1.5rem 0;">
<div style="display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 1rem;">
<div>
<h3 style="color: white; margin: 0 0 0.5rem 0;">Installer Image (Recommended)</h3>
<p style="color: rgba(255,255,255,0.8); margin: 0;">64-bit (AMD64) • ~3.0 GB • UEFI required</p>
</div>
<div>
<a href="/downloads/nubiferos-1.0-alpha-amd64.iso" class="btn btn-warning btn-lg" style="font-weight: 600;">
<i class="fas fa-download me-2"></i>Download ISO
</a>
</div>
</div>
</div>

| File | Size | Description |
|------|------|-------------|
| [nubiferos-1.0-alpha-amd64.iso](/downloads/nubiferos-1.0-alpha-amd64.iso) | ~3.0 GB | Bootable installer image |
| [SHA256SUMS](/downloads/SHA256SUMS) | 1 KB | Checksum file |
| [SHA256SUMS.gpg](/downloads/SHA256SUMS.gpg) | 1 KB | GPG signature |

### What's Included

| Component | Details |
|-----------|---------|
| **Base System** | Debian 12 (Bookworm) |
| **Desktop** | GNOME 43 with Wayland |
| **Encryption** | LUKS full disk encryption (mandatory) |
| **Credentials** | pass + GPG + GNOME Keyring |
| **Isolation** | Firejail workspace namespaces |
| **Cloud Tools** | 50+ tools available (AWS, Azure, GCP, Terraform, kubectl, etc.) |

### System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| **Architecture** | 64-bit (x86_64) | 64-bit (x86_64) |
| **RAM** | 4 GB | 8 GB+ |
| **Disk Space** | 25 GB | 50 GB+ |
| **Firmware** | UEFI | UEFI |

---

## Virtual Machine Images

NubiferOS works in VMware Workstation and VirtualBox. UEFI mode must be enabled.

| Platform | Instructions |
|----------|--------------|
| **VMware Workstation** | VM Settings → Options → Advanced → Firmware: UEFI |
| **VirtualBox** | Settings → System → Enable EFI (special OSes only) |

Use the same ISO above for VM installations.

---

## Verify Your Download

Always verify your download before installation.

### Verify Checksum

```bash
cd ~/Downloads
sha256sum -c SHA256SUMS
```

Expected output:
```
nubiferos-1.0-alpha-amd64.iso: OK
```

### Verify GPG Signature (Optional)

```bash
# Import the NubiferOS signing key
gpg --keyserver keyserver.ubuntu.com --recv-keys <KEY_ID>

# Verify signature
gpg --verify SHA256SUMS.gpg SHA256SUMS
```

---

## Installation

1. **Download** the ISO and verify the checksum
2. **Create bootable USB** using [Balena Etcher](https://etcher.balena.io/), [Rufus](https://rufus.ie/), or `dd`
3. **Boot from USB** (UEFI mode required)
4. **Follow the installer** — encryption is mandatory
5. **Reboot** and enter your LUKS passphrase

[Full Installation Guide →](/en/docs/getting-started/installation/)

---

## Previous Releases

| Version | Release Date | Status | Notes |
|---------|--------------|--------|-------|
| 1.0 "Nimbus" (Alpha) | Q1 2025 | Current | First public release |

---

## Coming Soon

| Version | Target | Highlights |
|---------|--------|------------|
| **1.5 "Stratus" (Beta)** | Q2 2025 | Single-password boot, YubiKey support |
| **2.0 "Cumulus"** | Q3-Q4 2025 | TPM support, team features |

[View Full Roadmap →](/en/about/#roadmap)

---

## Source Code

NubiferOS is open source under GPL-3.0.

| Resource | Link |
|----------|------|
| **GitHub Repository** | [github.com/nubiferos/nubiferos](https://github.com/nubiferos/nubiferos) |
| **Release Notes** | [GitHub Releases](https://github.com/nubiferos/nubiferos/releases) |
| **Report Issues** | [GitHub Issues](https://github.com/nubiferos/nubiferos/issues) |
| **Build Scripts** | [Build Documentation](https://github.com/nubiferos/nubiferos/tree/main/build) |
