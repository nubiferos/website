---
title: "Credential Security"
linkTitle: "Credential Security"
weight: 20
description: "How NubiferOS protects your cloud credentials with multi-layer encryption"
---

NubiferOS provides multi-layer credential protection using battle-tested tools rather than custom encryption. Your credentials are protected at rest and automatically injected into cloud CLIs when needed.

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

## Security Architecture

### Multi-Layer Protection

| Layer | Technology | What It Protects |
|-------|------------|------------------|
| **Disk** | LUKS (AES-256) | All data at rest |
| **Vault** | GPG (AES-256) | Credential database |
| **Runtime** | GNOME Keyring | Master key in memory |
| **Workspace** | Firejail | Credential isolation |

### Why GPG + pass?

- **Battle-tested**: Decades of security audits
- **Proven**: Used by security professionals worldwide
- **No custom crypto**: Avoids implementation bugs
- **Auditable**: Open source, large community
- **Git integration**: Built-in backup and sync

### No Environment Variable Exposure

**We never do this:**
```bash
# FORBIDDEN - visible in /proc, logs, crash dumps
export AWS_ACCESS_KEY_ID="AKIA..."
export AWS_SECRET_ACCESS_KEY="..."
```

**We always do this:**
```bash
# Credentials pulled on-demand from encrypted store
aws s3 ls  # Wrapper fetches from vault
```

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

### List and Verify Credentials

```bash
# List all
nubifer-creds list

# Test AWS credentials
nubifer-creds test aws

# Check what's loaded
nubifer-creds verify
```

## Credential Types Supported

### Cloud Provider Credentials

| Provider | Credential Types |
|----------|-----------------|
| **AWS** | Access keys, SSO, STS temporary credentials |
| **Azure** | Service principals, managed identities, device code flow |
| **GCP** | Service accounts, Application Default Credentials |

### Other Secrets

| Type | Examples |
|------|----------|
| **API Keys** | GitHub, GitLab, Datadog, monitoring services |
| **Database** | PostgreSQL, MySQL, MongoDB connection strings |
| **SSH** | Private keys and passphrases |
| **Registry** | Container registry credentials |

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
```

**No environment variables needed.** Credentials are fetched on-demand and injected directly into the CLI process.

## Audit Logging

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
- Azure device code flow with MFA
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

# Check status
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
- [GPG Setup Guide](https://github.com/nubiferos/nubiferos/blob/main/docs/GPG_KEY_SETUP.md)
