---
title: "Your First Workspace"
linkTitle: "First Workspace"
weight: 3
description: "Deep dive into workspace configuration"
---

## Understanding Workspaces

A workspace in NubiferOS is an isolated environment for a specific cloud account. When you switch workspaces:

- Environment variables change to match the workspace
- Credentials are loaded from encrypted storage
- Visual indicators update (prompt, panel, optionally wallpaper)
- Read-only mode can be enabled for additional safety

## Creating a Workspace

### AWS Workspace

```bash
nubifer-workspace create \
  --name aws-prod \
  --provider aws \
  --account-id 123456789012 \
  --region us-east-1 \
  --color red  # Visual indicator color
```

### Azure Workspace

```bash
nubifer-workspace create \
  --name azure-dev \
  --provider azure \
  --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
  --color blue
```

### GCP Workspace

```bash
nubifer-workspace create \
  --name gcp-staging \
  --provider gcp \
  --project-id my-project-staging \
  --color green
```

## Adding Credentials

After creating a workspace, add your credentials:

```bash
nubifer-creds add aws-prod
```

You'll be prompted to enter your access key and secret. These are encrypted with your GPG key and stored in `~/.password-store/`.

## Switching Workspaces

```bash
nubifer-workspace switch aws-prod
```

Your prompt will change to show the active workspace:

```
[☁️ aws-prod | us-east-1] $
```

## Workspace Colors

Colors help you visually distinguish workspaces:

| Color | Suggested Use |
|-------|---------------|
| Red | Production |
| Orange | Staging |
| Green | Development |
| Blue | Testing |
| Purple | Sandbox |

## Next Steps

- Learn about [read-only mode](/docs/user-guide/)
- Understand the [security model](/docs/security/)
