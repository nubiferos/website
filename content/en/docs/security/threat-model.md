---
title: "Threat Model"
linkTitle: "Threat Model"
weight: 10
description: "What NubiferOS protects against and explicit security boundaries"
---

NubiferOS publishes its threat model so you can make informed decisions about your security requirements. Most operating systems make vague security claims—we tell you exactly what we protect against and what we don't.

## Why a Public Threat Model?

Security through obscurity doesn't work. By publishing our threat model, we:
- Help you assess if NubiferOS meets your security requirements
- Enable security researchers to identify gaps
- Build trust through transparency
- Set clear expectations for users

## User Persona

**Primary User**: Cloud Engineer / DevOps Professional

**Characteristics**:
- Manages multiple cloud accounts (AWS, Azure, GCP, and others)
- Handles sensitive credentials and API keys daily
- Works with infrastructure as code (Terraform, Ansible)
- Requires secure credential management and isolation
- Uses workstation for cloud administration and development
- May work remotely or in shared office environments

**Use Cases**:
- Managing cloud infrastructure across multiple accounts
- Storing and using cloud credentials securely
- Preventing accidental cross-account operations
- Protecting credentials from malware and keyloggers
- Secure remote work with sensitive cloud access

## Assets We Protect

### Primary Assets
| Asset | Examples | Protection |
|-------|----------|------------|
| **Cloud Credentials** | AWS access keys, Azure service principals, GCP service accounts | GPG encryption, workspace isolation |
| **Infrastructure Code** | Terraform state, Ansible playbooks, Kubernetes configs | Encrypted disk, access controls |
| **Sensitive Data** | SSH keys, database credentials, certificates | GPG encryption, audit logging |

### Secondary Assets
| Asset | Examples | Protection |
|-------|----------|------------|
| **System Access** | User authentication, sudo privileges | Strong passwords, fail2ban |
| **Work Product** | Code repositories, documentation | Encrypted disk, backups |

## In-Scope Adversaries

### 1. Opportunistic Attackers
**Motivation**: Financial gain, credential theft

**Capabilities**: Basic malware, phishing, social engineering

**Attack Vectors**:
- Malicious downloads and email attachments
- Credential harvesting malware
- Keyloggers and screen capture tools
- Browser-based attacks

**Our Mitigations**: Wayland isolation, AppArmor profiles, Firejail sandboxing

### 2. Insider Threats
**Motivation**: Financial gain, revenge, espionage

**Capabilities**: Physical access, legitimate system access

**Attack Vectors**:
- Physical device theft
- Unauthorized credential access
- Privilege escalation
- Intentional data exfiltration

**Our Mitigations**: LUKS encryption, audit logging, workspace isolation

### 3. Targeted Attackers
**Motivation**: Espionage, competitive advantage

**Capabilities**: Advanced persistent threats, custom malware

**Attack Vectors**:
- Spear phishing and social engineering
- Zero-day exploits
- Supply chain attacks
- Advanced malware and rootkits

**Our Mitigations**: SBOM tracking, GPG signing, vulnerability scanning

### 4. Human Error (Operator Mistakes)
**Motivation**: Accidental self-harm, operational confusion

**Capabilities**: Legitimate system access, authorized credentials

**Attack Vectors**:
- Wrong cloud account selection
- Commands executed in wrong terminal/workspace
- Operations in wrong region or environment
- Accidental deletion or modification of resources
- Credential confusion between accounts

**Our Mitigations**: Workspace isolation, visual context indicators, read-only mode, separate command histories

## Attack Scenarios Addressed

| Scenario | Attack | Mitigation | Result |
|----------|--------|------------|--------|
| **Supply Chain** | Compromised build pipeline | SBOM + GPG signing + vulnerability scanning | Tampered releases detected |
| **Credential Theft** | Malware steals credentials | GPG encryption + Wayland isolation + AppArmor | Credentials encrypted at rest |
| **Cross-Account Confusion** | Wrong account credentials used | Firejail workspace isolation | Blast radius limited to single workspace |
| **Device Theft** | Laptop stolen | LUKS full disk encryption | Data inaccessible without passphrase |
| **Keylogger** | App captures keystrokes | Wayland display isolation | Apps isolated from each other |
| **Privilege Escalation** | Compromised app gains root | AppArmor mandatory access control | Blast radius limited by profiles |
| **Compromised ISO** | Tampered download | GPG signature + SHA256 checksums | Tampered ISO fails verification |

## Partially Mitigated Threats

These threats cannot be fully prevented at the OS level, but NubiferOS actively reduces their impact:

### 1. CPU Side-Channel Attacks (Spectre, Meltdown, MDS)
**Risk**: Malicious processes exploit CPU speculative execution to read sensitive memory.

**Our Mitigations**: Kernel CPU mitigations enabled by default (Layer 2), ASLR, restricted `/proc` access, configurable enhanced mitigations with performance trade-offs.

**Limitation**: Software mitigations reduce but cannot eliminate hardware-level vulnerabilities. New variants may appear before patches are available.

### 2. Cold Boot Attacks
**Risk**: Attacker extracts LUKS encryption keys from RAM on a running or recently powered-off machine.

**Our Mitigations**: LUKS full disk encryption (data is useless without the key), kernel hardening restricts memory access, limited LUKS passphrase attempts with forced reboot on failure.

**Limitation**: Encryption keys must reside in RAM while the system is running. No RAM wiping on shutdown or hardware memory encryption is currently implemented. Future TPM+PIN support will further reduce this risk.

### 3. Network-Based Attacks
**Risk**: Man-in-the-middle attacks, DNS poisoning, BGP hijacking, ISP surveillance.

**Our Mitigations**: UFW firewall with deny-by-default, fail2ban brute force protection, DNS hardening, VPN compatibility.

**Limitation**: NubiferOS cannot control network infrastructure between your machine and cloud providers. We harden the endpoint but cannot prevent upstream network attacks.

### 4. Application-Level Vulnerabilities
**Risk**: Vulnerabilities in cloud CLIs, browser exploits, IDE vulnerabilities.

**Our Mitigations**: AppArmor profiles limit what compromised apps can access, Firejail sandboxing isolates workspaces, Wayland prevents cross-application keylogging, workspace isolation contains blast radius.

**Limitation**: NubiferOS cannot patch third-party application vulnerabilities. We limit the damage a compromised application can cause, but cannot prevent the vulnerability itself.

## Explicit Out-of-Scope Threats

These threats are outside what NubiferOS can meaningfully address:

### 1. Hardware Keyloggers and DMA Attacks
**Examples**: Physical keylogging devices, Thunderbolt/PCIe DMA attacks, hardware implants

**Rationale**: Physical hardware attacks cannot be detected or prevented by software.

### 2. Nation-State Actors
**Examples**: Advanced persistent threats, hardware supply chain attacks, sophisticated zero-day chains

**Rationale**: Defending against nation-state actors requires specialized security measures beyond a general-purpose workstation OS.

### 3. Firmware and UEFI Attacks
**Examples**: UEFI rootkits, firmware-level persistence, Secure Boot bypass, TPM attacks

**Rationale**: Firmware security requires hardware vendor cooperation and specialized tools.

### 4. Social Engineering
**Examples**: Phishing, pretexting, impersonation, insider recruitment

**Rationale**: Human factors are outside the technical scope of the operating system.

### 5. Cloud Provider Security
**Examples**: AWS/Azure/GCP infrastructure vulnerabilities, cloud provider insider threats

**Rationale**: Cloud infrastructure security is the provider's responsibility.

## Security Layers

NubiferOS implements defense-in-depth with 8 security layers:

```
┌─────────────────────────────────────────────────────┐
│  Layer 8: User Education & Policies                 │
├─────────────────────────────────────────────────────┤
│  Layer 7: Application Sandboxing (Firejail)        │
├─────────────────────────────────────────────────────┤
│  Layer 6: Credential Encryption (GPG/pass)         │
├─────────────────────────────────────────────────────┤
│  Layer 5: Desktop Isolation (Wayland)              │
├─────────────────────────────────────────────────────┤
│  Layer 4: Mandatory Access Control (AppArmor)      │
├─────────────────────────────────────────────────────┤
│  Layer 3: Kernel Hardening & Firewall              │
├─────────────────────────────────────────────────────┤
│  Layer 2: CPU Security Mitigations                 │
├─────────────────────────────────────────────────────┤
│  Layer 1: Full Disk Encryption (LUKS)              │
└─────────────────────────────────────────────────────┘
```

### Layer Details

| Layer | Technology | What It Protects |
|-------|------------|------------------|
| **Disk Encryption** | LUKS (AES-256-XTS) | Data at rest, physical theft |
| **CPU Mitigations** | Kernel parameters | Spectre, Meltdown, MDS |
| **Kernel Hardening** | sysctl, UFW, fail2ban | Network attacks, brute force |
| **Access Control** | AppArmor | Privilege escalation |
| **Desktop Isolation** | Wayland | Keylogging, screen capture |
| **Credential Encryption** | GPG + pass | Credential theft |
| **App Sandboxing** | Firejail | Cross-workspace leakage |
| **User Education** | Documentation | Social engineering |

## Build & Release Security

Every NubiferOS release includes comprehensive supply chain security:

### Software Bill of Materials (SBOM)
- **CycloneDX format** - Machine-readable component list
- **SPDX format** - Industry-standard software package data
- **Dependency tracking** - Full dependency tree for all packages

**Why it matters**: You can verify exactly what's in your OS.

### Automated Vulnerability Scanning
- **Grype scanner** - Checks all packages against CVE databases
- **Severity thresholds** - Critical vulnerabilities block releases
- **Continuous monitoring** - Scans run on every commit

**Why it matters**: Known vulnerabilities are caught before release.

### Cryptographic Signing
- **GPG signatures** - Detached signatures for ISO verification
- **Public key distribution** - Verification key on website and GitHub
- **Tamper detection** - Modified ISOs fail verification

**Why it matters**: You can verify the ISO hasn't been tampered with.

### Secret & Code Scanning
- **Gitleaks** - Detects accidentally committed secrets
- **ShellCheck** - Static analysis of all shell scripts
- **Pre-commit hooks** - Catches issues before commit

## Risk Assessment

### High Risk (Actively Mitigated)
✅ Credential theft and exposure
✅ Cross-account operational mistakes
✅ Physical device compromise
✅ Supply chain attacks

### Medium Risk (Partially Mitigated)
⚠️ CPU side-channel attacks (kernel mitigations enabled)
⚠️ Cold boot attacks (LUKS encryption, kernel hardening)
⚠️ Application-level exploits (sandboxing, AppArmor, workspace isolation)
⚠️ Network-based attacks (firewall, fail2ban, DNS hardening)
⚠️ System-level exploits (kernel hardening, ASLR)
⚠️ Brute force attacks (fail2ban, password policies)

### Out of Scope (Accepted)
⚡ Hardware keyloggers and DMA attacks
⚡ Nation-state threats
⚡ Firmware and UEFI attacks
⚡ Social engineering
⚡ Cloud provider compromise  

## Trust Boundaries

### Pre-Install Trust Boundary

**Trusted**: NubiferOS installer ISO (when verified), UEFI/BIOS firmware, hardware platform

**Untrusted**: Network during installation, unverified installation media

**Note**: Pre-install environment should not be used for entering credentials.

### Post-Install Trust Boundary

**Trusted**: NubiferOS kernel, LUKS encryption, GPG/pass storage, AppArmor, Firejail, Wayland

**Untrusted**: User applications, network traffic, USB devices, browser content

## Assumptions

### Security Assumptions
1. Hardware platform is trusted
2. UEFI/BIOS firmware is not compromised
3. NubiferOS ISO is properly verified before installation
4. Users follow basic security practices
5. Security updates are applied promptly

### Operational Assumptions
1. Workstations are used in reasonably secure environments
2. Basic network security measures are in place
3. Recovery keys are stored securely
4. Users can respond appropriately to security incidents

## Best Practices

### For Maximum Security

1. **Verify ISO before installation**
   ```bash
   gpg --verify nubiferos.iso.sig nubiferos.iso
   sha256sum -c SHA256SUMS
   ```

2. **Use strong LUKS passphrase** (16+ characters)

3. **Enable read-only mode for production workspaces**
   ```bash
   nubifer-workspace create --name "Prod" --read-only
   ```

4. **Use short-lived credentials** (AWS SSO, Azure device code)

5. **Keep system updated**
   ```bash
   sudo apt update && sudo apt upgrade
   ```

6. **Store recovery key securely** (physical safe/vault)

## Source Code

View the complete security documentation:
- [THREAT_MODEL.md](https://github.com/nubiferos/nubiferos/blob/trunk/docs/THREAT_MODEL.md)
- [SECURITY_SUMMARY.md](https://github.com/nubiferos/nubiferos/blob/trunk/docs/SECURITY_SUMMARY.md)
- [SECURITY_NON_GOALS.md](https://github.com/nubiferos/nubiferos/blob/trunk/docs/SECURITY_NON_GOALS.md)
