---
title: "NubiferAI"
linkTitle: "NubiferAI"
weight: 15
description: "AI-native cloud operations - describe what you want, get infrastructure-as-code"
type: page-toc
---

Describe what you want to build, deploy, or change in natural language. NubiferAI generates infrastructure-as-code, deployment scripts, and operational plans using AI providers you control.

<div class="text-center my-4">
  <a href="/download/" class="btn btn-warning btn-lg"><i class="fas fa-download me-2"></i>Download NubiferOS</a>
  <a href="/docs/nubiferai/getting-started/" class="btn btn-outline-primary btn-lg ms-2">Get Started with NubiferAI</a>
</div>

---

## How It Works

```
You describe it    -->    Seed interprets    -->    AI generates    -->    You review    -->    Execute
"Deploy Flask to       aws-deployer seed         Terraform +            Check the           One-click
 ECS with ALB"         focuses the AI            ECS task def           generated code      deploy
```

1. **Describe** what you want in plain English
2. **Seeds** (AI personas) focus the AI on the right domain
3. **AI generates** infrastructure-as-code, scripts, or operational plans
4. **You review** every action before anything runs
5. **Execute** with confidence — nothing runs without your approval

---

## The Desktop App

NubiferAI includes a native GTK4 desktop application with a prompt-driven workflow and one-click quick actions.

{{< screenshot src="img/nubiferai/home-page.png" alt="NubiferAI home page with prompt and quick actions" caption="Home page — describe what you want or pick a quick action" >}}

### Quick Actions by Cloud Provider

Pre-built actions organized by category: AWS, Google Cloud, Azure, FinOps, DevOps, and Security. Click one to start immediately.

---

## 35+ AI Seeds

Seeds are AI personas — specialized system prompts that give NubiferAI deep expertise in specific cloud domains. Each seed shapes how the AI thinks, what tools it uses, and what output format it produces.

<div class="row my-4">
  <div class="col-md-6 mb-3">
    {{< screenshot src="img/nubiferai/seeds-browser.png" alt="NubiferAI seeds browser with tag filters" caption="Seeds browser — search and filter by tag" >}}
  </div>
  <div class="col-md-6 mb-3">
    {{< screenshot src="img/nubiferai/seed-cards-detail.png" alt="NubiferAI seed cards with descriptions and tags" caption="Seed cards — each specialist with its tools and output formats" >}}
  </div>
</div>

| Category | Seeds | Examples |
|----------|-------|---------|
| **AWS** | 12 | Cloud Engineer, Solutions Architect, Lambda Specialist, IAM Specialist |
| **Google Cloud** | 5 | GCP Engineer, BigQuery Analyst, GKE Specialist |
| **Azure** | 5 | Azure Engineer, AKS Specialist, Cosmos DB Expert |
| **FinOps** | 4 | Cost Optimizer, Budget Analyst, Reserved Instance Planner |
| **DevOps** | 4 | Ansible Expert, Kubernetes Expert, CI/CD Specialist |
| **Security** | 1 | Security Auditor |
| **General** | 1 | General Cloud Assistant |

You can also [create custom seeds](/docs/nubiferai/seeds/#creating-custom-seeds) for your team's specific workflows.

---

## Multiple AI Providers

Switch providers at any time — use free local models for testing, or premium APIs for production quality.

<div class="row my-4">
  <div class="col-md-6 mx-auto">
    {{< screenshot src="img/nubiferai/provider-dropdown.png" alt="NubiferAI provider selection dropdown" caption="Switch providers and models from the header bar" >}}
  </div>
</div>

| Provider | Cost | Best For |
|----------|------|----------|
| **Ollama** | Free | Testing, privacy, offline use |
| **Claude** (Anthropic) | Paid | Best quality for infrastructure planning |
| **OpenAI** | Paid | Alternative premium option |
| **AWS Bedrock** | Paid | Enterprise, uses existing AWS credentials |

---

## NubiferOS Integration

When running on NubiferOS, NubiferAI automatically inherits your workspace context — no extra configuration needed.

| Feature | Standalone | On NubiferOS |
|---------|-----------|--------------|
| **Workspace detection** | Manual | Automatic |
| **AWS credentials** | API key required | Inherited from workspace |
| **Safety classification** | User-configured | Workspace-enforced |
| **Bedrock access** | Separate credentials | Uses workspace AWS credentials |
| **Provider switching** | All available | All available + Bedrock auto-configured |

---

## Install

```bash
# On NubiferOS (recommended)
nubi addon install nubiferai

# Standalone
pip install nubiferai

# Launch
nubiferai-gtk
```

---

## Documentation

| Topic | Description |
|-------|-------------|
| [Getting Started](/docs/nubiferai/getting-started/) | Installation, first launch, your first nucleation |
| [AI Providers](/docs/nubiferai/providers/) | Setup guides for all 4 providers |
| [GUI Application](/docs/nubiferai/gui/) | Complete desktop app walkthrough |
| [Seeds](/docs/nubiferai/seeds/) | All 35 seeds, custom seed creation |
| [NubiferOS Integration](/docs/nubiferai/nubiferos-integration/) | Auto-detection, credentials, safety enforcement |

## Source Code

NubiferAI is open source under GPL-3.0.

| Resource | Link |
|----------|------|
| **GitHub Repository** | [github.com/nubiferos/nubiferai](https://github.com/nubiferos/nubiferai) |
| **Issue Tracker** | [Report a bug](https://github.com/nubiferos/nubiferai/issues) |
| **Discussions** | [Ask questions](https://github.com/nubiferos/nubiferai/discussions) |
