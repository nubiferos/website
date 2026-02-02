---
title: "Credentials"
linkTitle: "Credentials"
weight: 20
description: "Secure storage and management of cloud credentials, API keys, and secrets"
---

NubiferOS provides encrypted credential management using battle-tested tools (GPG + pass) rather than custom encryption. Your credentials are protected at rest and automatically injected into cloud CLIs when needed.

## Why Secure Credential Management?

Cloud credentials are the keys to your infrastructure. A leaked AWS access key can result in:
- Unauthorized resource creation (cryptomining)
- Data exfiltration
- Infrastructure destruction
- Massive cloud bills

**Common credential mistakes:**
- Storing credentials in plaintext `~/.aws/credentials`
- Exposing credentials in environment variables (visible in `/proc`)
- Committing credentials to git repositories
- Sharing credentials via Slack or email

**NubiferOS solves this by:**
- Encrypting all credentials with GPG (AES-256)
- Never exposing credentials in environment variables
- Isolating credentials per workspace
- Providing audit logging for all credential access

## How It Works

### No Plaintext Credentials

Unlike traditional setups, NubiferOS never stores credentials in plaintext files:

```bash
# Traditional (INSECURE) - ~/.aws/credentials
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE      # Plaintext!
aws_secret_access_key = wJalrXUtnFEMI/...     # Plaintext!

# NubiferOS - ~/.aws/credentials DOESN'T EXIST
# Credentials stored in GPG-encrypted pass store
```

### Credential Storage Architecture

```
┌─────────────────────────────────────────────────────────┐
│  System Keyring (GNOME Keyring / libsecret)            │
│  └── GPG Master Key (unlocked at login)                │
│      └── pass (password-store)                         │
│          ├── nubifer/workspace-abc/cloud/aws/prod     │
│          ├── nubifer/workspace-def/cloud/aws/dev      │
│          ├── nubifer/workspace-abc/cloud/azure/sub1   │
│          └── nubifer/workspace-abc/api/github         │
└─────────────────────────────────────────────────────────┘
```

### CLI Wrapper Flow

When you run a cloud command, here's what happens:

```
┌─────────────────────────────────────────────────────────┐
│  CLI Wrapper (e.g., aws, az, gcloud)                   │
│  1. Detects active workspace                           │
│  2. Calls credential_process helper                    │
│  3. Helper fetches from pass (GPG-encrypted)           │
│  4. Credentials injected into CLI process              │
│  5. Access logged to audit log                         │
└─────────────────────────────────────────────────────────┘
```

**Example AWS config:**
```ini
# ~/.aws/config
[profile production]
region = us-east-1
credential_process = /usr/local/bin/nubifer-aws-credential-helper production
```

**Key principle**: Credentials are fetched on-demand and injected directly into CLI processes. They're never exposed in environment variables or plaintext files.

## Quick Start

### Add AWS Credentials

```bash
# Interactive mode (recommended)
nubifer-creds add

# Or specify directly
nubifer-creds aws add-access-key \
  --profile production \
  --access-key-id AKIAIOSFODNN7EXAMPLE \
  --secret-access-key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Use AWS SSO (Recommended)

```bash
# Configure SSO
nubifer-creds aws configure-sso \
  --sso-start-url https://my-company.awsapps.com/start \
  --sso-region us-east-1 \
  --account-id 123456789012 \
  --role-name DevRole

# Login (opens browser)
nubifer-creds aws sso-login --profile production
```

### List Credentials

```bash
# List all
nubifer-creds list

# Filter by type
nubifer-creds list --type aws
nubifer-creds list --type api

# Filter by workspace
nubifer-creds list --workspace production
```

### Verify Credentials Work

```bash
# Test AWS credentials
nubifer-creds test aws

# Check what's loaded
nubifer-creds verify
```

## Credential Types

### AWS Credentials

#### Option 1: AWS SSO (Best)

No long-lived credentials. MFA enforced. Automatic rotation.

```bash
nubifer-creds aws configure-sso \
  --sso-start-url https://my-company.awsapps.com/start \
  --sso-region us-east-1 \
  --account-id 123456789012 \
  --role-name DevRole

# Login when needed
nubifer-creds aws sso-login --profile production
```

#### Option 2: IAM Access Keys (Secured)

Keys encrypted in vault. Never written to disk unencrypted.

```bash
nubifer-creds aws add-access-key \
  --profile production \
  --access-key-id AKIA... \
  --secret-access-key <secret>
```

#### Option 3: Temporary Credentials (STS)

Short-lived credentials that auto-expire.

```bash
nubifer-creds aws assume-role \
  --role-arn arn:aws:iam::123456789012:role/DevRole \
  --session-name dev-session \
  --duration 3600
```

### Azure Credentials

```bash
# Device code flow (MFA supported)
nubifer-creds azure login --use-device-code

# Service principal
nubifer-creds azure add-service-principal \
  --tenant-id <tenant-id> \
  --client-id <client-id> \
  --client-secret <secret>
```

### GCP Credentials

```bash
# User authentication
nubifer-creds gcp login

# Service account key (encrypted)
nubifer-creds gcp add-service-account \
  --key-file service-account.json \
  --project my-project
# Original key file is securely deleted after import
```

### API Keys & Tokens

```bash
# GitHub token
nubifer-creds api add-token \
  --service github \
  --token ghp_xxxxxxxxxxxx \
  --scopes "repo,workflow"

# GitLab token
nubifer-creds api add-token \
  --service gitlab \
  --token glpat-xxxxxxxxxxxx

# Datadog
nubifer-creds api add-token \
  --service datadog \
  --api-key <key> \
  --app-key <app-key>
```

### Database Credentials

```bash
# PostgreSQL
nubifer-creds db add \
  --type postgresql \
  --host db.example.com \
  --port 5432 \
  --database mydb \
  --username dbuser \
  --password <password>

# MongoDB
nubifer-creds db add \
  --type mongodb \
  --connection-string "mongodb://user:pass@host:27017/db"
```

### SSH Keys

```bash
nubifer-creds ssh add-key \
  --name github \
  --key-file ~/.ssh/id_rsa \
  --passphrase <passphrase>
```

## Automatic CLI Integration

Once credentials are stored, they're automatically available to cloud CLIs:

```bash
# AWS - credentials injected automatically
aws s3 ls
aws ec2 describe-instances

# Azure - credentials injected automatically
az vm list
az storage account list

# GCP - credentials injected automatically
gcloud compute instances list
gsutil ls

# Terraform - provider credentials injected
terraform plan
terraform apply

# Docker - registry credentials injected
docker pull registry.company.com/image
```

**No environment variables needed.** Credentials are fetched on-demand and injected directly into the CLI process.

## Use Cases

### Use Case 1: Multi-Account AWS Management

Store credentials for multiple AWS accounts:

```bash
# Add production credentials
nubifer-creds aws add-access-key \
  --profile prod \
  --access-key-id AKIA... \
  --secret-access-key <secret>

# Add development credentials
nubifer-creds aws add-access-key \
  --profile dev \
  --access-key-id AKIA... \
  --secret-access-key <secret>

# Link to workspaces
nubifer-workspace create --name "AWS Prod" --provider aws \
  --credential-id prod

nubifer-workspace create --name "AWS Dev" --provider aws \
  --credential-id dev
```

Now switching workspaces automatically switches credentials.

### Use Case 2: Migrate from Existing Setup

Import credentials from your current configuration:

```bash
# Import from ~/.aws/credentials
nubifer-creds import aws-cli

# Import from ~/.azure/
nubifer-creds import azure-cli

# Import from ~/.config/gcloud/
nubifer-creds import gcloud

# Original files are backed up and can be deleted
```

### Use Case 3: Team Credential Sharing

Export encrypted credentials for team members:

```bash
# Export (encrypted with password)
nubifer-creds export \
  --output team-creds.enc \
  --password <shared-password>

# Team member imports
nubifer-creds import \
  --input team-creds.enc \
  --password <shared-password>
```

### Use Case 4: Credential Rotation

Rotate credentials before they expire:

```bash
# Check what needs rotation
nubifer-creds check-rotation

# Rotate AWS access keys
nubifer-creds rotate aws --profile production
# Creates new key, updates vault, deletes old key

# Set rotation policy
nubifer-creds config set rotation-days 90
nubifer-creds config set rotation-reminders true
```

## Password Manager Integration

### Bitwarden

```bash
# Install Bitwarden CLI
nubifer-creds install-bitwarden

# Login
bw login

# Sync credentials
nubifer-creds bitwarden sync

# Import specific item
nubifer-creds bitwarden import --item-id <id>
```

### 1Password

```bash
# Install 1Password CLI
nubifer-creds install-1password

# Connect
op signin

# Import vault
nubifer-creds 1password import --vault "Cloud Credentials"
```

### KeePassXC

```bash
nubifer-creds keepassxc import --database ~/credentials.kdbx
```

## Security Architecture

### Encryption Layers

| Layer | Technology | What It Protects |
|-------|------------|------------------|
| Disk | LUKS (AES-256) | All data at rest |
| Vault | GPG (AES-256) | Credential database |
| Runtime | GNOME Keyring | Master key in memory |
| Workspace | Firejail | Credential isolation |

### Storage Location

```
~/.password-store/           # GPG-encrypted credentials
~/.config/nubifer/
├── vault/
│   ├── master.key          # Encrypted by system keyring
│   ├── credentials.db      # Encrypted credential metadata
│   └── audit.log           # Encrypted access log
└── config.json             # Vault configuration
```

### Audit Logging

All credential access is logged:

```bash
# View audit log
nubifer-creds audit-log

# Output:
# 2025-01-31T10:30:00 | user | ACCESS | aws/prod | aws s3 ls
# 2025-01-31T10:31:00 | user | ACCESS | aws/prod | terraform plan
# 2025-01-31T10:35:00 | user | ROTATE | aws/prod | manual rotation
```

## Best Practices

### 1. Use Short-Lived Credentials

✅ **Recommended:**
- AWS SSO (temporary tokens)
- Azure device code flow
- GCP user authentication
- STS assume-role with MFA

❌ **Avoid:**
- Long-lived IAM access keys
- Service principal secrets without rotation
- Permanent service account keys

### 2. Rotate Regularly

```bash
# Set 90-day rotation policy
nubifer-creds config set rotation-days 90

# Enable reminders
nubifer-creds config set rotation-reminders true

# Check status weekly
nubifer-creds check-rotation
```

### 3. Use Workspace Isolation

Link credentials to workspaces so they're only accessible in the right context:

```bash
nubifer-workspace create --name "Prod" --provider aws \
  --credential-id prod-creds
```

### 4. Enable Audit Logging

```bash
nubifer-creds config set audit-logging true
```

### 5. Backup Encrypted

```bash
nubifer-creds backup create \
  --output ~/backups/creds-$(date +%Y%m%d).enc \
  --password <strong-password>
```

## Command Reference

| Command | Description |
|---------|-------------|
| `nubifer-creds add` | Add credentials (interactive) |
| `nubifer-creds list` | List all credentials |
| `nubifer-creds list --type aws` | List AWS credentials |
| `nubifer-creds test aws` | Test AWS credentials |
| `nubifer-creds verify` | Verify current credentials |
| `nubifer-creds rotate --all` | Rotate all credentials |
| `nubifer-creds check-rotation` | Check rotation status |
| `nubifer-creds audit-log` | View access log |
| `nubifer-creds import aws-cli` | Import from AWS CLI |
| `nubifer-creds export` | Export encrypted backup |

## Troubleshooting

### Credentials Not Working

```bash
# Verify credentials are loaded
nubifer-creds verify

# Test specific provider
nubifer-creds test aws

# Check environment
nubifer-creds env show
```

### Vault Locked

```bash
# Unlock vault
nubifer-creds vault unlock

# Reset password (requires recovery key)
nubifer-creds vault reset-password --recovery-key <key>
```

### Import Failed

```bash
# Check source files exist
ls -la ~/.aws/credentials

# Import with verbose output
nubifer-creds import aws-cli --verbose
```

## Source Code

- [Credential Manager Component](https://github.com/nubiferos/nubiferos/tree/main/components/credential-manager)
- [CREDENTIAL_SECURITY.md](https://github.com/nubiferos/nubiferos/blob/main/docs/CREDENTIAL_SECURITY.md)
- [GPG Setup Guide](https://github.com/nubiferos/nubiferos/blob/main/docs/guides/GPG_SETUP_GUIDE.md)
