---
title: "Available Tools"
linkTitle: "Tools"
weight: 10
description: "Complete list of 50+ cloud tools available in NubiferOS"
---

NubiferOS offers 50+ cloud tools that you can select during installation. This page lists all available tools organized by category.

## Installation Options

During installation, you choose which tools to install:

| Installation Type | Size | What's Included |
|-------------------|------|-----------------|
| **Minimal** | ~2GB | Core CLIs, VS Code, essential utilities |
| **Standard** | ~4-5GB | Popular cloud tools, 2-3 IDEs |
| **Full** | ~7-8GB | Everything available |
| **Custom** | Varies | Pick exactly what you need |

## Quick Navigation

- [Cloud CLI Tools](#cloud-cli-tools) - AWS, Azure, GCP
- [Infrastructure as Code](#infrastructure-as-code) - Terraform, Pulumi, Ansible
- [Container & Kubernetes](#container--kubernetes-tools) - Docker, kubectl, Helm
- [Monitoring & Observability](#monitoring--observability) - Grafana, Prometheus, Trivy
- [CI/CD & GitOps](#cicd--gitops-tools) - GitHub CLI, ArgoCD, Flux
- [Database Clients](#database-clients) - PostgreSQL, MySQL, MongoDB
- [Testing Frameworks](#testing-frameworks) - k6, Playwright, Selenium
- [Secrets Management](#secrets-management) - Vault, SOPS, age
- [Development Tools](#development-tools) - Languages, utilities
- [IDEs](#ides) - VS Code, IntelliJ, PyCharm

---

## Cloud CLI Tools

### AWS Tools (11 tools)

| Tool | Description | Use Case |
|------|-------------|----------|
| **AWS CLI v2** | Primary AWS command-line interface | All AWS operations |
| **SAM CLI** | Serverless Application Model | Lambda development |
| **eksctl** | Amazon EKS cluster management | Kubernetes on AWS |
| **AWS CDK** | Cloud Development Kit | Infrastructure as code |
| **Session Manager Plugin** | Secure shell access | EC2 access without SSH keys |
| **AWS Copilot** | Container deployment | ECS/Fargate deployments |
| **AWS Amplify CLI** | Full-stack app development | Web/mobile apps |
| **EB CLI** | Elastic Beanstalk deployment | PaaS deployments |
| **AWS NoSQL Workbench** | DynamoDB GUI tool | Database design |
| **AWS Vault** | Secure credential storage | Credential management |
| **Lightsail CLI** | Lightsail management | Simple cloud hosting |

### Azure Tools (3 tools)

| Tool | Description | Use Case |
|------|-------------|----------|
| **Azure CLI** | Primary Azure command-line interface | All Azure operations |
| **Azure Functions Core Tools** | Serverless function development | Azure Functions |
| **Bicep** | Azure infrastructure as code | ARM template alternative |

### Google Cloud Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| **gcloud SDK** | Google Cloud CLI (includes gcloud, gsutil, bq) | All GCP operations |

---

## Infrastructure as Code

| Tool | Description | Use Case |
|------|-------------|----------|
| **Terraform** | Multi-cloud infrastructure provisioning | Infrastructure automation |
| **Pulumi** | Modern infrastructure as code | TypeScript/Python IaC |
| **Ansible** | Configuration management | Server configuration |

---

## Container & Kubernetes Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| **Docker** | Container runtime and CLI | Container development |
| **Podman** | Daemonless container engine | Rootless containers |
| **kubectl** | Kubernetes command-line tool | Cluster management |
| **Helm** | Kubernetes package manager | Application deployment |
| **k9s** | Terminal UI for Kubernetes | Cluster monitoring |
| **Dive** | Docker image layer explorer | Image optimization |
| **Lazydocker** | Docker terminal UI | Container management |
| **Stern** | Kubernetes log tailing | Log aggregation |

---

## Monitoring & Observability

| Tool | Description | Use Case |
|------|-------------|----------|
| **Grafana** | Visualization and dashboards | Metrics visualization |
| **Prometheus (promtool)** | Metrics collection | Alerting rules |
| **Loki (logcli)** | Log aggregation | Log querying |
| **Trivy** | Security scanner | Container/IaC scanning |

---

## CI/CD & GitOps Tools

| Tool | Description | Use Case |
|------|-------------|----------|
| **GitHub CLI (gh)** | GitHub command-line interface | GitHub workflows |
| **GitLab CLI (glab)** | GitLab command-line interface | GitLab workflows |
| **ArgoCD** | GitOps continuous delivery | Kubernetes deployments |
| **Flux** | GitOps toolkit | Kubernetes GitOps |
| **Tekton CLI (tkn)** | Cloud-native CI/CD | Pipeline management |
| **Jenkins CLI** | Jenkins server connection | CI/CD automation |
| **Kustomize** | Kubernetes configuration | YAML management |
| **Skaffold** | Kubernetes development | Dev workflow |

---

## Database Clients

| Tool | Description | Use Case |
|------|-------------|----------|
| **psql** | PostgreSQL client | PostgreSQL databases |
| **mysql** | MySQL/MariaDB client | MySQL databases |
| **mongosh** | MongoDB shell | MongoDB databases |
| **redis-cli** | Redis client | Redis caching |

---

## Testing Frameworks

### Load & Performance Testing

| Tool | Description | Use Case |
|------|-------------|----------|
| **k6** | Modern load testing | Performance testing |
| **Locust** | Python-based load testing | Distributed load tests |
| **Newman** | Postman CLI | API testing |

### Browser & E2E Testing

| Tool | Description | Use Case |
|------|-------------|----------|
| **Selenium** | Browser automation | Web testing |
| **Playwright** | Modern browser testing | Cross-browser testing |

### Unit & Integration Testing

| Tool | Description | Use Case |
|------|-------------|----------|
| **Pytest** | Python testing framework | Python tests |
| **Robot Framework** | Keyword-driven testing | Acceptance testing |
| **Behave** | BDD testing for Python | Behavior-driven tests |

### Security Testing

| Tool | Description | Use Case |
|------|-------------|----------|
| **SonarQube Scanner** | Code quality and security | Code analysis |
| **OWASP ZAP** | Security testing | Vulnerability scanning |
| **Trivy** | Container/IaC security | Security scanning |

---

## Secrets Management

These tools are available for managing secrets in your cloud infrastructure. For how NubiferOS manages your local credentials, see [Credential Security](/en/docs/security/credential-security/).

| Tool | Description | Use Case |
|------|-------------|----------|
| **HashiCorp Vault** | Secrets management | Enterprise secrets |
| **SOPS** | Encrypted secrets in Git | GitOps secrets |
| **age** | Modern file encryption | Simple encryption |

---

## Development Tools

### Languages & Runtimes

| Tool | Description |
|------|-------------|
| **Python 3** | With pip and venv |
| **Node.js** | With npm |
| **Go** | Available via apt |
| **Java** | Available via apt |

### Utilities

| Tool | Description |
|------|-------------|
| **Git** | Version control |
| **curl/wget** | Data transfer |
| **jq** | JSON processor |
| **yq** | YAML processor |
| **httpie** | User-friendly HTTP client |

---

## IDEs

### Default (Pre-installed)

| IDE | Size | Best For |
|-----|------|----------|
| **Visual Studio Code** | ~200MB | General development, best cloud extensions |
| **Vim/Neovim** | ~10MB | Terminal-based editing, always available |

### Optional (Selectable during installation)

| IDE | Size | Best For |
|-----|------|----------|
| **VSCodium** | ~200MB | VS Code without telemetry |
| **IntelliJ IDEA CE** | ~800MB | Java, Kotlin, Scala |
| **PyCharm CE** | ~600MB | Python, data science |
| **Emacs** | ~50MB | Highly extensible editing |
| **Kate** | ~30MB | Lightweight KDE editor |
| **Eclipse** | ~500MB | Java enterprise |

### Automatic IDE Plugin Installation

NubiferOS automatically installs cloud-related IDE plugins:

```bash
# Run after installation to configure plugins
/usr/local/bin/install-ide-plugins
```

**Pre-configured extensions:** AWS Toolkit, Azure Tools, Cloud Code (GCP), Terraform, Docker, Kubernetes, Python, Go

---

## Required Security Tools

These tools are always installed and form the security foundation of NubiferOS. They cannot be disabled.

| Tool | Purpose | Learn More |
|------|---------|------------|
| **GPG + pass** | Credential encryption | [Credential Security](/en/docs/security/credential-security/) |
| **Firejail** | Workspace isolation | [Workspace Isolation](/en/docs/security/workspace-isolation/) |
| **AppArmor** | Mandatory access control | [Security Architecture](/en/security/) |
| **Wayland** | Display isolation (prevents keylogging) | [Security Architecture](/en/security/) |
| **UFW** | Firewall | [Security Architecture](/en/security/) |
| **fail2ban** | Intrusion prevention | [Security Architecture](/en/security/) |
| **auditd** | Security audit logging | [Security Architecture](/en/security/) |
| **LUKS** | Full disk encryption | [Security Architecture](/en/security/) |

---

## Post-Installation Management

```bash
# List available tools
nubifer-tools list-available

# Install additional tool
nubifer-tools install aws-copilot
nubifer-tools install pycharm

# Remove tool
nubifer-tools remove eclipse

# Update all tools
nubifer-tools update-all

# Verify installations
verify-cloud-tools
```

## Tool Updates

### Automatic Updates
- Security updates via unattended-upgrades
- System packages updated automatically

### Manual Updates
```bash
# Update cloud CLIs
nubifer-tools update-cloud-tools

# Update IDEs
nubifer-tools update-ides

# Update everything
nubifer-tools update-all
```

## Source Code

- [INCLUDED_TOOLS.md](https://github.com/nubiferos/nubiferos/blob/main/docs/guides/INCLUDED_TOOLS.md)
- [IDE_PLUGINS.md](https://github.com/nubiferos/nubiferos/blob/main/docs/guides/IDE_PLUGINS.md)
