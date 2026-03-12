---
title: "Download NubiferOS"
linkTitle: "Download"
weight: 10
description: "Get NubiferOS Alpha and start managing your cloud accounts securely"
type: page-toc
---

The secure cloud engineer's workstation with workspace isolation, encrypted credentials, and 50+ cloud tools available.

{{% alert title="Alpha Software" color="warning" %}}
This is alpha software intended for testing and feedback. Expect bugs and rough edges. Please [report issues on GitHub](https://github.com/nubiferos/nubiferos/issues).
{{% /alert %}}

---

## Choose Your Edition

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
| **NubiferAI** | Optional package — install via `sudo apt install nubifer-ai` |

### System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| **Architecture** | 64-bit (x86_64) | 64-bit (x86_64) |
| **RAM** | 4 GB | 8 GB+ |
| **Disk Space** | 25 GB | 50 GB+ |
| **Firmware** | UEFI | UEFI |

---

## Container

Run NubiferOS cloud tools without a full installation:

```bash
docker pull ghcr.io/nubiferos/nubiferos:latest
docker run -it ghcr.io/nubiferos/nubiferos
```

The container includes all NubiferOS cloud tools and works with Docker or Podman. Ideal for CI/CD pipelines or quick access to the toolset.

---

## VM Images

NubiferOS works in VMware Workstation and VirtualBox. UEFI mode must be enabled.

| Platform | Instructions |
|----------|--------------|
| **VMware Workstation** | VM Settings → Options → Advanced → Firmware: UEFI |
| **VirtualBox** | Settings → System → Enable EFI (special OSes only) |

Use the same ISO above for VM installations.

---

## Verification

Always verify your download before installation.

### Verify Checksum

```bash
cd ~/Downloads
sha256sum -c SHA256SUMS
```

Expected output:
```
NubiferOS-0.1.0-20260310-a65efef-amd64.iso: OK
```

### Verify GPG Signature (Optional)

```bash
# Import the NubiferOS signing key
gpg --import nubiferos-signing-key.pub

# Verify signature
gpg --verify *.iso.asc *.iso
```

---

## Installation

1. **Download** the ISO and verify the checksum
2. **Create bootable USB** using [Balena Etcher](https://etcher.balena.io/), [Rufus](https://rufus.ie/), or `dd`
3. **Boot from USB** (UEFI mode required)
4. **Follow the installer** — encryption is mandatory
5. **Reboot** and enter your LUKS passphrase

[Full Installation Guide →](/docs/getting-started/installation/)

{{% alert title="Running into issues?" color="info" %}}
Check the [Known Issues](/docs/getting-started/known-issues/) page and [Troubleshooting Guide](/docs/getting-started/installation/#troubleshooting) for common problems and solutions.
{{% /alert %}}

---

## Previous Releases

| Version | Release Date | Status | Notes |
|---------|--------------|--------|-------|
| 0.1.1 | March 2026 | Current | CI/CD pipeline, container image, OTA updates |

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

## Learn More

| Topic | Link |
|-------|------|
| **Features** | [What's included in NubiferOS](/features/) |
| **Why NubiferOS?** | [How we compare to alternatives](/why-nubiferos/) |
| **Security** | [Security architecture](/security/) |
| **NubiferAI** | [AI-native cloud operations](/nubiferai/) |
| **Built with AI** | [How we built NubiferOS](/built-with-ai/) |
