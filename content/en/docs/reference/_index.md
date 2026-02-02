---
title: "Reference"
linkTitle: "Reference"
weight: 40
description: "Technical reference documentation"
---

Technical reference for NubiferOS commands, configuration, and tools.

## Command Shortcuts

NubiferOS provides shell aliases for common commands. These are loaded automatically via `/etc/nubifer/shell-integration.sh`.

| Full Command | Shortcut | Description |
|--------------|----------|-------------|
| `nubifer-workspace` | `nw` | Main workspace command |
| `nubifer-workspace switch` | `nw-switch` | Quick switch with activation |
| `nubifer-workspace current` | `nw-context` | Show current workspace |
| - | `nw-activate` | Activate workspace in current shell |

**Examples:**
```bash
# These are equivalent:
nubifer-workspace list
nw list

# Quick switch (switches and activates in one step)
nw-switch "aws prod"

# Check current context
nw-context

# Lock/unlock workspace
nw ro
sudo nw rw -d 15
```

## Workspace Commands

### nubifer-workspace (alias: nw)

Manage isolated workspaces for different cloud accounts.

```bash
# Create a new workspace
nubifer-workspace create --name <name> --provider <aws|azure|gcp> [options]

# List all workspaces
nubifer-workspace list

# Switch to a workspace
nubifer-workspace switch <name>

# Delete a workspace
nubifer-workspace delete <name>

# Lock workspace (read-only mode)
nubifer-workspace ro

# Unlock workspace (requires sudo)
sudo nubifer-workspace rw

# Timed unlock (auto-reverts after N minutes)
sudo nubifer-workspace rw -d 30
```

**Source**: [Context Manager Component](https://github.com/nubiferos/nubiferos/tree/main/components/context-manager)

## Credential Commands

### nubifer-creds

Manage credentials for workspaces.

```bash
# List all credentials
nubifer-creds list

# Add credentials (interactive)
nubifer-creds add

# Add AWS access keys
nubifer-creds aws add-access-key \
  --profile production \
  --access-key-id AKIA... \
  --secret-access-key <secret>

# Configure AWS SSO (recommended)
nubifer-creds aws configure-sso \
  --sso-start-url https://my-company.awsapps.com/start \
  --sso-region us-east-1

# Add Azure service principal
nubifer-creds azure add-service-principal \
  --tenant-id <tenant-id> \
  --client-id <client-id> \
  --client-secret <secret>

# Add GCP service account
nubifer-creds gcp add-service-account \
  --key-file service-account.json \
  --project my-project

# Check credentials needing rotation
nubifer-creds check-rotation

# Rotate credentials
nubifer-creds rotate --all

# Remove credentials
nubifer-creds remove <workspace-name>
```

**Source**: [Credential Manager Component](https://github.com/nubiferos/nubiferos/tree/main/components/credential-manager)

## Configuration Files

| File | Purpose |
|------|---------|
| `~/.config/nubiferos/config.yaml` | Main configuration |
| `~/.config/nubiferos/workspaces/` | Workspace definitions |
| `~/.password-store/` | Encrypted credentials (via pass) |
| `~/.nubifer/vault/` | Credential vault |

## Environment Variables

Credentials are automatically injected (never exposed in environment):

```bash
# AWS - injected on-demand
AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN, AWS_REGION

# Azure - injected on-demand
ARM_CLIENT_ID, ARM_CLIENT_SECRET, ARM_TENANT_ID, ARM_SUBSCRIPTION_ID

# GCP - injected on-demand
GOOGLE_APPLICATION_CREDENTIALS, GOOGLE_CLOUD_PROJECT
```

## Included Tools

See the [Features page](/en/features/) for a complete list of pre-installed tools.

**Source**: [INCLUDED_TOOLS.md](https://github.com/nubiferos/nubiferos/blob/main/docs/guides/INCLUDED_TOOLS.md)

## Source Code

- [NubiferOS Repository](https://github.com/nubiferos/nubiferos)
- [Components](https://github.com/nubiferos/nubiferos/tree/main/components)
- [Documentation](https://github.com/nubiferos/nubiferos/tree/main/docs)
