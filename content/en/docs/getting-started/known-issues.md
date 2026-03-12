---
title: "Known Issues"
linkTitle: "Known Issues"
weight: 10
description: "Known issues and workarounds for NubiferOS Alpha"
---

{{% alert title="Alpha Software" color="warning" %}}
NubiferOS is in alpha. This page tracks known issues and workarounds. If you encounter something not listed here, please [report it on GitHub](https://github.com/nubiferos/nubiferos/issues).
{{% /alert %}}

## Installation

### Bootloader Installation Fails (Non-UEFI Boot)

**Error:** "The bootloader could not be installed"

This error appears on the final Calamares installation screen when the ISO is booted in legacy BIOS mode instead of UEFI mode.

**Fix:** Ensure your virtual machine or physical hardware is set to boot in UEFI mode:

- **VirtualBox:** Settings → System → Enable EFI (special OSes only)
- **VMware Workstation:** VM → Settings → Options → Advanced → Firmware type → UEFI
- **QEMU/KVM:** Add `-bios /usr/share/ovmf/OVMF.fd` or use UEFI firmware
- **Physical hardware:** Enter BIOS/firmware settings and switch from Legacy/CSM to UEFI boot

**Why:** NubiferOS uses LUKS full-disk encryption with GPT partitioning, which requires UEFI firmware for reliable GRUB installation. Legacy BIOS boot with GPT requires a special `bios_grub` partition that may not be created correctly by the installer.

## Boot & Login

### LUKS Passphrase Prompted Three Times on Boot

**Symptom:** During boot, you are asked to enter your LUKS passphrase three times before reaching the login screen.

This is expected behavior in the current release, not a bug. Each prompt is decrypting a different layer of the system.

**What's happening:**

1. **First prompt (GRUB):** GRUB decrypts the `/boot` partition to load the kernel and initramfs
2. **Second prompt (initramfs):** The kernel's initramfs decrypts `/boot` again during early system initialization — GRUB's decryption does not carry over to the kernel
3. **Third prompt (initramfs):** The initramfs decrypts the root (`/`) partition to mount the full filesystem

**Fix:** This will be resolved in a future release. The planned approach is to embed a keyfile in the initramfs so that once GRUB decrypts `/boot`, the remaining partitions unlock automatically. See our [Disk Encryption roadmap](/docs/security/disk-encryption/#planned-improvements) for details.

**Why:** NubiferOS encrypts both `/boot` and root with LUKS to provide full disk encryption with no unencrypted partitions. The trade-off in the current release is multiple passphrase prompts because GRUB and the kernel's initramfs operate independently and cannot share decryption state.

---

### Failed LUKS Password Falls Through to GRUB Rescue

**Error:** After entering an incorrect LUKS passphrase, the system drops to a `grub rescue>` prompt instead of re-prompting for the password.

This can appear as if the bootloader is broken, but the system is actually working correctly — GRUB just can't decrypt the disk and has nowhere to go.

**Fix:** Reboot the machine and enter the correct LUKS passphrase:

- Power off or press `Ctrl+Alt+Del` to restart
- When prompted for the LUKS passphrase, enter it carefully
- If you're unsure of your passphrase, check Caps Lock and keyboard layout

**Why:** NubiferOS encrypts the entire disk including `/boot` with LUKS. When GRUB fails to decrypt the disk with the provided passphrase, it cannot load the kernel or initramfs, so it falls back to its rescue shell. This is a GRUB limitation — it does not gracefully retry the passphrase prompt on failure.

## Workspaces

*No issues reported yet.*

## Cloud Tools

*No issues reported yet.*

## Other

*No issues reported yet.*
