---
title: "NubiferAI"
linkTitle: "NubiferAI"
weight: 25
description: "AI-native cloud operations platform for NubiferOS"
---

{{% alert title="Auto-synced" color="info" %}}
This documentation is synced from the [NubiferAI repository](https://github.com/nubiferos/nubiferai). It refreshes automatically every 30 minutes.
{{% /alert %}}

NubiferAI is an AI-native cloud operations platform. Describe what you want to build, deploy, or change in natural language, and NubiferAI generates infrastructure-as-code, deployment scripts, and operational plans.

## Documentation

- [Getting Started](getting-started/) — Installation, first launch, and your first nucleation
- [Providers](providers/) — Setting up Claude, OpenAI, Ollama, and Bedrock
- [GUI Application](gui/) — Walkthrough of the GTK desktop application
- [Seeds](seeds/) — AI personas for different cloud tasks
- [NubiferOS Integration](nubiferos-integration/) — Using NubiferAI within NubiferOS

## Quick Start

```bash
# Install (standalone)
pip install nubiferai

# Or via NubiferOS
nubi addon install nubiferai

# Launch the GUI
nubiferai-gtk

# Or use the CLI
nubiferai nucleate "Deploy a Python Flask app to AWS ECS with a load balancer"
```

## Source Code

- [GitHub Repository](https://github.com/nubiferos/nubiferai)
- [Issue Tracker](https://github.com/nubiferos/nubiferai/issues)
