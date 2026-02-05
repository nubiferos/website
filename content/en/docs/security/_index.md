---
title: "Security"
linkTitle: "Security"
weight: 30
description: "Understanding NubiferOS security architecture"
---

Security is the foundation of NubiferOS, not an afterthought. We publish our [threat model](/docs/security/threat-model/) so you can verify our claims.

## Security Layers

NubiferOS implements defense in depth with eight security layers:

```
┌─────────────────────────────────────────────────────┐
│  Application Sandboxing (Firejail)                 │
├─────────────────────────────────────────────────────┤
│  Credential Encryption (GPG/pass)                  │
├─────────────────────────────────────────────────────┤
│  Desktop Isolation (Wayland)                       │
├─────────────────────────────────────────────────────┤
│  Mandatory Access Control (AppArmor)               │
├─────────────────────────────────────────────────────┤
│  Kernel Hardening & Firewall                       │
├─────────────────────────────────────────────────────┤
│  CPU Security Mitigations                          │
├─────────────────────────────────────────────────────┤
│  Full Disk Encryption (LUKS)                       │
└─────────────────────────────────────────────────────┘
```

## Documentation

- [Threat Model](/docs/security/threat-model/) - What we protect against and explicit boundaries
- [Credential Security](/docs/security/credential-security/) - How credentials are encrypted and managed
- [Workspace Isolation](/docs/security/workspace-isolation/) - Firejail namespaces and read-only mode

## Quick Summary

### What We Protect Against
✅ Credential theft from disk  
✅ Cross-account credential access  
✅ Accidental destructive operations  
✅ Keylogging between applications  
✅ Supply chain attacks  

### What We Don't Protect Against
⚠️ Hardware-level attacks (Spectre, cold boot)  
⚠️ Nation-state actors  
⚠️ Social engineering  
⚠️ Compromised cloud providers  

## Security Tools

We use battle-tested, audited tools rather than custom solutions:

| Tool | Purpose | In Use Since |
|------|---------|--------------|
| GPG | Credential encryption | 1999 |
| pass | Password management | 2012 |
| Firejail | Application sandboxing | 2014 |
| AppArmor | Mandatory access control | 2007 |
| Wayland | Display isolation | 2012 |
| LUKS | Disk encryption | 2004 |

## Source Code

- [Security Documentation](https://github.com/nubiferos/nubiferos/tree/main/docs)
- [SECURITY_SUMMARY.md](https://github.com/nubiferos/nubiferos/blob/main/docs/SECURITY_SUMMARY.md)
- [THREAT_MODEL.md](https://github.com/nubiferos/nubiferos/blob/main/docs/THREAT_MODEL.md)
