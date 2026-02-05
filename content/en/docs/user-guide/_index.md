---
title: "User Guide"
linkTitle: "User Guide"
weight: 20
description: "Learn how to use NubiferOS effectively"
---

This section covers day-to-day usage of NubiferOS, including workspace management, credential handling, and security features.

## Workspaces

Workspaces are isolated environments for different cloud accounts. Each workspace has its own:
- Credentials (stored encrypted)
- Environment variables
- Shell history
- Visual indicators

```bash
# Create a workspace
nubifer-workspace create --name aws-prod --provider aws

# Switch workspaces
nubifer-workspace switch aws-prod

# List workspaces
nubifer-workspace list
```

**Learn more**: [Workspace Isolation](/docs/security/workspace-isolation/)

## Credential Management

NubiferOS uses `pass` (the standard Unix password manager) with GPG encryption to store your cloud credentials securely.

```bash
# Add credentials
nubifer-creds add

# List credentials
nubifer-creds list

# Use AWS SSO (recommended)
nubifer-creds aws configure-sso
```

**Learn more**: [Credential Security](/docs/security/credential-security/)

## Context Indicators

Visual feedback helps you always know which workspace is active:
- Terminal prompt shows workspace name and region
- GNOME panel indicator
- Desktop wallpaper can change per workspace

**Source**: [Context Indicator Component](https://github.com/nubiferos/nubiferos/tree/trunk/components/context-indicator)

## Read-Only Mode

Lock workspaces to prevent destructive operations like `terraform destroy`. Enabling write mode requires sudo, creating an intentional barrier.

```bash
# Lock workspace
nubifer-workspace ro

# Unlock (requires sudo)
sudo nubifer-workspace rw

# Timed unlock (auto-reverts)
sudo nubifer-workspace rw -d 30
```

**Learn more**: [Workspace Isolation - Read-Only Mode](/docs/security/workspace-isolation/#read-only-mode)
