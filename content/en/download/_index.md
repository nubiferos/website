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

## Latest Releases

{{< releases >}}

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

[Full Installation Guide →](/docs/getting-started/installation/)

---

## Previous Releases

| Version | Release Date | Status | Notes |
|---------|--------------|--------|-------|
| 1.0 "Nimbus" (Alpha) | Q1 2026 | Current | First public release |

---

## Coming Soon

| Version | Target | Highlights |
|---------|--------|------------|
| **1.5 "Stratus" (Beta)** | Q2 2026 | Single-password boot, YubiKey support |
| **2.0 "Cumulus"** | Q3-Q4 2026 | TPM support, team features |

[View Full Roadmap →](/about/#roadmap)

---

## Source Code

NubiferOS is open source under GPL-3.0.

| Resource | Link |
|----------|------|
| **GitHub Repository** | [github.com/nubiferos/nubiferos](https://github.com/nubiferos/nubiferos) |
| **Release Notes** | [GitHub Releases](https://github.com/nubiferos/nubiferos/releases) |
| **Report Issues** | [GitHub Issues](https://github.com/nubiferos/nubiferos/issues) |
| **Build Scripts** | [Build Documentation](https://github.com/nubiferos/nubiferos/tree/trunk/build) |
