---
title: "Quick Start"
linkTitle: "Quick Start"
weight: 2
description: "Get up and running with NubiferOS in 10 minutes"
---

This guide gets you from first boot to managing cloud resources in under 10 minutes.

## Prerequisites

- NubiferOS installed (see [Installation](/docs/getting-started/installation/))
- At least one cloud account (AWS, Azure, or GCP)
- Your cloud credentials ready

## First Boot

After installing NubiferOS, you'll be greeted with the GNOME desktop. The workspace indicator in the top panel shows "No Workspace" until you create one.

## Step 1: Create Your First Workspace

Open a terminal (`Ctrl+Alt+T`) and create a workspace:

### AWS Workspace

```bash
nubifer-workspace create \
  --name "AWS Production" \
  --provider aws \
  --account-id 123456789012 \
  --account-name "prod-account" \
  --region us-east-1
```

### Azure Workspace

```bash
nubifer-workspace create \
  --name "Azure Dev" \
  --provider azure \
  --account-id "subscription-id" \
  --account-name "dev-subscription" \
  --region eastus
```

### GCP Workspace

```bash
nubifer-workspace create \
  --name "GCP Staging" \
  --provider gcp \
  --account-id "project-id" \
  --account-name "staging-project" \
  --region us-central1
```

## Step 2: Add Credentials

### Option A: AWS SSO (Recommended)

```bash
nubifer-creds aws configure-sso \
  --sso-start-url https://my-company.awsapps.com/start \
  --sso-region us-east-1 \
  --account-id 123456789012 \
  --role-name DevRole

# Login (opens browser)
nubifer-creds aws sso-login --profile production
```

### Option B: AWS Access Keys

```bash
nubifer-creds aws add-access-key \
  --profile production \
  --access-key-id AKIAIOSFODNN7EXAMPLE \
  --secret-access-key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Option C: Interactive Mode

```bash
nubifer-creds add
# Follow the prompts
```

## Step 3: Switch to the Workspace

```bash
nubifer-workspace switch "aws production"
```

Your prompt changes to show the active workspace:

```
[☁️ prod-account | us-east-1] user@nubiferos:~$
```

## Step 4: Verify It Works

```bash
# AWS
aws sts get-caller-identity

# Azure
az account show

# GCP
gcloud config list
```

You should see your account information.

## Step 5: Enable Read-Only Mode (Recommended)

For production workspaces, enable read-only mode:

```bash
# Lock the workspace
nubifer-workspace ro
```

Your prompt now shows the lock indicator:

```
[🔒 RO][☁️ prod-account | us-east-1] user@nubiferos:~$
```

To make changes, temporarily unlock:

```bash
# Unlock for 15 minutes (requires sudo)
sudo nubifer-workspace rw -d 15

# Make your changes...

# Lock again (or wait for auto-lock)
nubifer-workspace ro
```

## Quick Reference

| Command | Shortcut | Description |
|---------|----------|-------------|
| `nubifer-workspace create` | `nw create` | Create a new workspace |
| `nubifer-workspace list` | `nw list` | List all workspaces |
| `nubifer-workspace switch <name>` | `nw-switch <name>` | Switch to a workspace |
| `nubifer-workspace current` | `nw-context` | Show current workspace |
| `nubifer-workspace ro` | `nw ro` | Enable read-only mode |
| `sudo nubifer-workspace rw` | `sudo nw rw` | Enable read-write mode |
| `nubifer-creds add` | - | Add credentials (interactive) |
| `nubifer-creds list` | - | List all credentials |
| `nubifer-creds test aws` | - | Test AWS credentials |

**Tip:** Use `nw` as a shortcut for `nubifer-workspace` in all commands.

## Common Workflows

### Managing Multiple AWS Accounts

```bash
# Create workspaces for each account
nubifer-workspace create --name "AWS Prod" --provider aws --account-id 111111111111
nubifer-workspace create --name "AWS Dev" --provider aws --account-id 222222222222
nubifer-workspace create --name "AWS Staging" --provider aws --account-id 333333333333

# Switch between them
nubifer-workspace switch "aws prod"
terraform plan

nubifer-workspace switch "aws dev"
terraform apply
```

### Multi-Cloud Management

```bash
# Morning: AWS infrastructure
nubifer-workspace switch "aws prod"
aws s3 ls

# Afternoon: Azure deployment
nubifer-workspace switch "azure staging"
az deployment group create ...

# Evening: GCP monitoring
nubifer-workspace switch "gcp logging"
gcloud logging read ...
```

### Safe Production Changes

```bash
# 1. Switch to production
nubifer-workspace switch "aws prod"

# 2. Verify read-only mode
nubifer-workspace current
# Mode: 🔒 Read-Only

# 3. Unlock for 15 minutes
sudo nubifer-workspace rw -d 15

# 4. Make changes
terraform apply

# 5. Lock immediately (or wait for auto-lock)
nubifer-workspace ro
```

## Troubleshooting

### Prompt Not Updating

```bash
# Reload shell integration
source /etc/nubifer/shell-integration.sh

# Or restart your shell
exec bash
```

### Credentials Not Working

```bash
# Verify credentials are loaded
nubifer-creds verify

# Test specific provider
nubifer-creds test aws
```

### Can't Switch Workspaces

```bash
# List available workspaces
nubifer-workspace list

# Check workspace ID
nubifer-workspace list --verbose
```

## Next Steps

- [Learn about workspaces in depth](/docs/user-guide/workspaces/)
- [Understand credential management](/docs/user-guide/credentials/)
- [Review the security model](/docs/security/threat-model/)
- [See all included tools](/features/)
