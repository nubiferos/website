---
title: "Installation Guide"
linkTitle: "Installation"
weight: 1
description: "Complete guide to downloading and installing NubiferOS"
---

This guide walks you through installing NubiferOS, including important details about disk encryption, the installer, and known issues.

## Download

Download the latest NubiferOS ISO from our [download page](/download/).

After downloading, verify the checksum:

```bash
sha256sum nubiferos-alpha-1.0-amd64.iso
# Compare with SHA256SUMS file from download page
```

## Create Bootable USB

### Using dd (Linux/macOS)

```bash
# Find your USB device
lsblk

# Write the ISO (replace /dev/sdX with your USB device)
sudo dd if=nubiferos-alpha-1.0-amd64.iso of=/dev/sdX bs=4M status=progress conv=fsync
sync
```

**Warning**: Double-check the device name. `dd` will overwrite whatever you point it at.

### Using Balena Etcher (All Platforms)

1. Download [Balena Etcher](https://www.balena.io/etcher/)
2. Select the NubiferOS ISO
3. Select your USB drive
4. Click "Flash!"

### Using Rufus (Windows)

1. Download [Rufus](https://rufus.ie/)
2. Select the NubiferOS ISO
3. Select your USB drive
4. Use "DD Image" mode (not ISO mode)
5. Click "Start"

## Installing in a Virtual Machine

NubiferOS works well in VMware Workstation and VirtualBox for testing or development. Here's how to set it up:

### VMware Workstation

1. Create a new VM with these settings:
   - Guest OS: Debian 12.x 64-bit
   - Memory: 4GB minimum (8GB recommended)
   - Disk: 40GB minimum
   - **Firmware: UEFI** (required)

2. To enable UEFI:
   - Edit VM Settings → Options → Advanced
   - Set "Firmware type" to **UEFI**
   - Or edit the .vmx file and add: `firmware = "efi"`

3. Mount the ISO and boot

### VirtualBox

1. Create a new VM with these settings:
   - Type: Linux, Version: Debian (64-bit)
   - Memory: 4GB minimum (8GB recommended)
   - Disk: 40GB minimum (VDI, dynamically allocated)

2. Enable UEFI:
   - Settings → System → Motherboard
   - Check **"Enable EFI (special OSes only)"**

3. For better performance:
   - Settings → System → Processor: 2+ CPUs
   - Settings → Display → Video Memory: 128MB
   - Settings → Display → Enable 3D Acceleration (optional)

4. Mount the ISO and boot

### VM-Specific Notes

- **UEFI is required**: NubiferOS uses a UEFI bootloader. Legacy BIOS mode won't work.
- **LUKS encryption is mandatory**: Full disk encryption is required even in VMs. This is a core security feature of NubiferOS.
- **Nested virtualization**: If you need Docker/containers inside the VM, enable nested virtualization in your hypervisor.

## Boot and Install (Physical Hardware)

1. Insert the USB drive and boot from it (usually F12, F2, or Del to access boot menu)
2. Select "Install NubiferOS" from the boot menu
3. The Calamares installer will launch

## Understanding the Installer

NubiferOS uses the Calamares installer, which is the same installer used by many Linux distributions. Here's what to expect:

### Step 1: Welcome & Language

Select your language and click "Next".

### Step 2: Location & Timezone

Select your timezone. This affects system time and locale settings.

### Step 3: Keyboard Layout

Choose your keyboard layout. You can test it in the text box provided.

### Step 4: Partitioning

This is where you configure disk encryption.

**Recommended: Erase disk with encryption**

1. Select "Erase disk"
2. Check "Encrypt system" 
3. Enter a strong LUKS passphrase

**Important**: This passphrase is required every time you boot. Choose something memorable but secure.

### Step 5: Users

Create your user account. This screen has some important details:

#### The Three Passwords Explained

You'll notice the installer asks for passwords in multiple places. Here's what each one does:

| Password | Purpose | When You'll Use It |
|----------|---------|-------------------|
| **LUKS Passphrase** | Decrypts your disk at boot | Every boot, before login screen |
| **User Password** | Logs into your account | After boot, at login screen |
| **Root Password** | System administration | When using `sudo` (optional, can match user password) |

**Why three passwords?**

- **LUKS passphrase**: Protects your data if someone steals your laptop. Without it, the disk is unreadable.
- **User password**: Protects your session from other users on the same machine.
- **Root password**: Allows administrative tasks. In NubiferOS, you can use `sudo` with your user password instead.

**Tip**: You can use the same password for all three if you prefer simplicity, but using different passwords provides defense-in-depth.

#### Required Password Checkbox

You'll see a checkbox that says "Require a password to log in". **This checkbox cannot be unchecked** in NubiferOS.

**Why?** NubiferOS is designed for security-conscious cloud engineers. Auto-login would bypass the security model and leave your cloud credentials exposed if someone accesses your machine.

### Step 6: Summary

Review your choices. This is your last chance to go back and change anything.

### Step 7: Install

The installation takes 10-20 minutes depending on your hardware. Don't interrupt it.

## Known Issues & Workarounds

### Alpha Release Limitations

This is an alpha release. Some things may not work perfectly.

#### Issue: LUKS Password Checkbox Bug

**Symptom**: If you uncheck "Encrypt system" after initially checking it, the installer may still prompt for a LUKS passphrase, or behave unexpectedly.

**Workaround**: Don't uncheck it. LUKS encryption is mandatory in NubiferOS - it's a core security requirement, not optional. If you accidentally unchecked it:
1. Go back to the partitioning step
2. Select "Erase disk" and ensure "Encrypt system" is checked
3. Continue with installation

**Note**: LUKS encryption protects your cloud credentials at rest. Without it, anyone with physical access to your machine could read your credentials.

#### Issue: Installer Crashes on Some Hardware

**Symptom**: Calamares crashes during partitioning on certain NVME drives.

**Workaround**: 
1. Boot into the live environment
2. Open a terminal and manually partition:
   ```bash
   sudo fdisk /dev/nvme0n1
   # Create partitions manually
   ```
3. Restart the installer and select "Manual partitioning"

#### Issue: Slow Installation on USB 2.0

**Symptom**: Installation takes 45+ minutes.

**Workaround**: Use a USB 3.0 port if available. The ISO is large due to included tools.

## Post-Installation

### First Boot

1. You'll see the GRUB bootloader
2. Enter your LUKS passphrase when prompted
3. The GNOME login screen appears
4. Log in with your user credentials

### Verify Installation

Open a terminal and verify NubiferOS is working:

```bash
# Check version
cat /etc/nubifer-release

# Verify workspace tools
nubifer-workspace --version

# Check credential manager
nubifer-creds --version
```

### Initialize Your GPG Key

NubiferOS uses GPG for credential encryption. On first boot, you'll be prompted to create a GPG key:

```bash
# If not prompted automatically, run:
nubifer-setup gpg

# Follow the prompts to create your key
# This key encrypts your cloud credentials
```

**Important**: Remember your GPG passphrase. It's needed to access your encrypted credentials.

### Set Up Your First Workspace

```bash
# Create your first workspace
nubifer-workspace create \
  --name "My First Workspace" \
  --provider aws \
  --account-id 123456789012 \
  --region us-east-1
```

See the [Quick Start Guide](/docs/getting-started/quick-start/) for detailed workspace setup.

## Security Architecture

Understanding what you just installed:

```
┌─────────────────────────────────────────────────────────┐
│  Boot Process                                           │
├─────────────────────────────────────────────────────────┤
│  1. BIOS/UEFI loads GRUB                               │
│  2. GRUB prompts for LUKS passphrase                   │
│  3. Disk decrypted, kernel loaded                      │
│  4. GNOME login screen appears                         │
│  5. User logs in with user password                    │
│  6. GPG key unlocked (may prompt for passphrase)       │
│  7. Credentials available via pass/GPG                 │
└─────────────────────────────────────────────────────────┘
```

### Why Full Disk Encryption?

- **Laptop theft**: If someone steals your laptop, they can't read your data
- **Credential protection**: Your cloud credentials are encrypted at rest
- **Compliance**: Many security frameworks require encryption at rest
- **Peace of mind**: Even if you lose your device, your data is safe

### The Encryption Stack

| Layer | Technology | What It Protects |
|-------|------------|------------------|
| Disk | LUKS (AES-256) | Everything on disk |
| Credentials | GPG (AES-256) | Cloud credentials specifically |
| Runtime | GNOME Keyring | Keys in memory during session |

## Troubleshooting

### Can't Boot After Installation

**Symptom**: System doesn't boot, goes straight to BIOS.

**Fix**: 
1. Boot from USB again
2. Select "Boot existing OS" if available
3. Or reinstall with "Install bootloader to /dev/sda" explicitly selected

### Forgot LUKS Passphrase

**Bad news**: There's no recovery. LUKS encryption is designed this way.

**Prevention**: 
- Write down your passphrase and store it securely
- Consider using a password manager for the passphrase
- Test your passphrase before completing installation

### Forgot User Password

**Fix**: Boot into recovery mode and reset:
1. At GRUB, select "Advanced options"
2. Select "Recovery mode"
3. Enter LUKS passphrase
4. Select "root shell"
5. Run: `passwd <username>`

### GPG Key Issues

**Symptom**: Can't access credentials, GPG errors.

**Fix**:
```bash
# Check GPG agent
gpg-agent --daemon

# List keys
gpg --list-keys

# If no keys, create one
gpg --full-generate-key
```

## Next Steps

- [Quick Start Guide](/docs/getting-started/quick-start/) - Set up your first workspace
- [Credential Security](/docs/security/credential-security/) - Understand how credentials are protected
- [Workspace Isolation](/docs/security/workspace-isolation/) - Learn about namespace isolation
