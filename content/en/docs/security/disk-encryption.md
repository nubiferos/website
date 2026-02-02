---
title: "Disk Encryption"
linkTitle: "Disk Encryption"
weight: 15
description: "Understanding LUKS encryption in NubiferOS and planned improvements"
---

NubiferOS uses LUKS (Linux Unified Key Setup) for full disk encryption. This page explains the current implementation, why encryption matters, and planned improvements.

## Current Implementation (v1.0)

### LUKS1 Everywhere

NubiferOS Alpha uses LUKS1 for simplicity:

- Single passphrase at boot
- AES-256-XTS encryption
- Mandatory for all installations
- Works on all hardware

**Boot process:**
1. Power on → GRUB loads
2. Enter LUKS passphrase
3. Disk decrypts → System boots
4. Login with user password

### Why Encryption is Mandatory

For a cloud engineer workstation, disk encryption isn't optional:

| Scenario | Without Encryption | With Encryption |
|----------|-------------------|-----------------|
| Laptop stolen | All credentials exposed | Data unreadable |
| Device lost | Cloud accounts compromised | Data protected |
| Decommissioned drive | Must physically destroy | Secure wipe possible |
| Forensic analysis | Full data recovery | Nothing recoverable |

Your cloud credentials (AWS keys, Azure service principals, GCP service accounts) are too valuable to leave unprotected.

## Planned Improvements

### Version 1.5: Single-Password Boot

**Problem:** Currently, encrypted `/boot` + encrypted root can require multiple password prompts.

**Solution:** Keyfile in initramfs

**How it works:**
1. Random keyfile generated during install
2. Keyfile added as second LUKS key slot for root partition
3. Keyfile embedded in initramfs (which lives on encrypted `/boot`)
4. GRUB decrypts `/boot` with your password → initramfs uses keyfile to decrypt root
5. You only enter one password

**Security:**
- Keyfile is protected by `/boot` encryption
- Attacker still needs your passphrase
- No hardware dependency
- Works in VMs

### Version 2.0: LUKS2 with Argon2id

**Problem:** LUKS1 uses PBKDF2, which is vulnerable to GPU-based brute force attacks.

**Solution:** LUKS2 with Argon2id key derivation

**Benefits:**
- Memory-hard function resists GPU attacks
- Significantly slower to brute force
- Modern cryptographic design

**Trade-off:** Slightly slower unlock (acceptable for boot-time operation)

### Version 2.0: TPM + PIN

**Problem:** Passphrase-only encryption doesn't detect boot chain tampering.

**Solution:** TPM 2.0 integration with PIN

**How it works:**
1. TPM chip stores encryption key sealed to system state
2. Key only released if: correct PIN + correct boot chain (PCR values)
3. Measures BIOS, bootloader, kernel to detect tampering

**Security benefits:**
- Hardware-bound: disk useless on different machine
- Tamper detection: modified bootloader = key not released
- Evil maid resistant: can't modify boot chain without detection
- PIN can be shorter (TPM has anti-hammering)
- Cold boot resistant: key in TPM, not RAM

**Trade-offs:**
- Hardware dependency: TPM failure = need recovery key
- Less portable: can't easily move disk to new machine
- Not all VMs have TPM support

## Comparison Matrix

| Option | Passwords | Hardware | Evil Maid | Portable | VM Compatible |
|--------|-----------|----------|-----------|----------|---------------|
| **LUKS1 (current)** | 1 | None | ❌ No | ✅ Yes | ✅ Yes |
| **Keyfile (v1.5)** | 1 | None | ❌ No | ✅ Yes | ✅ Yes |
| **TPM + PIN (v2.0)** | 1 (PIN) | TPM 2.0 | ✅ Yes | ❌ No | ⚠️ Depends |

## Future: Cloud/VM Deployments

For cloud-based NubiferOS deployments (AWS EC2, Azure VMs, etc.), LUKS may become optional:

**Why encryption might be unnecessary in cloud:**
- Cloud providers already encrypt EBS/managed disks at rest
- No physical theft risk
- TPM not available in all cloud VMs
- Boot passphrase impractical for auto-scaling

**What would still be protected:**
- Credentials still encrypted with GPG/pass
- Workspace isolation still enforced
- Wayland display isolation still active
- All other security layers remain

**Timeline:** Under consideration for v2.0+

## Recovery

### If You Forget Your LUKS Passphrase

**Bad news:** There is no recovery. This is by design.

LUKS encryption is designed so that without the passphrase, the data is mathematically unrecoverable. This is a feature, not a bug—it's what protects you if your device is stolen.

**Prevention:**
- Write down your passphrase and store it securely (safe, safety deposit box)
- Use a password manager to store the passphrase
- Create a LUKS recovery key during installation (stored on USB)
- Test your passphrase before completing installation

### LUKS Recovery Key

During installation, you can create a recovery key:

```bash
# Add a recovery key to LUKS
sudo cryptsetup luksAddKey /dev/sda2 --key-slot 1

# Or use a keyfile
sudo dd if=/dev/urandom of=/path/to/recovery.key bs=4096 count=1
sudo cryptsetup luksAddKey /dev/sda2 /path/to/recovery.key
```

Store the recovery key on a USB drive in a secure location (safe, not with your laptop).

## Technical Details

### Current LUKS1 Configuration

```bash
# View LUKS header
sudo cryptsetup luksDump /dev/sda2

# Typical output:
# Version:        1
# Cipher name:    aes
# Cipher mode:    xts-plain64
# Hash spec:      sha256
# Key Bits:       512
```

### Planned LUKS2 Configuration

```bash
# Future LUKS2 with Argon2id
# Version:        2
# Cipher name:    aes
# Cipher mode:    xts-plain64
# PBKDF:          argon2id
# Time cost:      4
# Memory:         1048576 (1GB)
# CPUs:           4
```

## Further Reading

- [LUKS Documentation](https://gitlab.com/cryptsetup/cryptsetup/-/wikis/home)
- [Arch Wiki - dm-crypt](https://wiki.archlinux.org/title/Dm-crypt)
- [Debian Encrypted Boot Guide](https://cryptsetup-team.pages.debian.net/cryptsetup/encrypted-boot.html)
- [TPM 2.0 and LUKS](https://wiki.archlinux.org/title/Trusted_Platform_Module#Securing_LUKS_volumes)
