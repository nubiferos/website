---
title: "Built with AI"
linkTitle: "Built with AI"
weight: 40
description: "How we built NubiferOS using AI-assisted development with Kiro, Claude, and spec-driven workflows"
---

# Built with AI

NubiferOS was built using AI-assisted development tools. This page documents our journey, the tools we used, and the lessons we learned building a complete Linux distribution with AI collaboration.

## The Tools

We used three primary AI tools throughout development:

| Tool | Role | Why We Chose It |
|------|------|-----------------|
| **Kiro** | IDE + Spec-driven development | Structured workflow from requirements → design → tasks |
| **Claude Opus** | Primary AI model | Deep reasoning, complex problem-solving, long context |
| **Claude Sonnet** | Initial development | Faster iteration during early prototyping |

## The Timeline

| Phase | Period | Focus |
|-------|--------|-------|
| **Initial Prototyping** | Oct-Nov 2025 | Architecture decisions, component design |
| **Core Development** | Dec 2025 - Jan 2026 | Build system, installer, security hardening |
| **Model Upgrade** | January 2026 | Switched from Sonnet to Opus |
| **Polish & Documentation** | Jan-Feb 2026 | Website, documentation, testing |

**Total development time**: ~3-4 months from first commit to alpha release.

## Spec-Driven Development

Kiro's spec-driven workflow was central to our development process:

```
┌─────────────────────────────────────────────────────────┐
│  1. Requirements                                        │
│     User stories + acceptance criteria                  │
├─────────────────────────────────────────────────────────┤
│  2. Design                                              │
│     Architecture, components, interfaces                │
├─────────────────────────────────────────────────────────┤
│  3. Tasks                                               │
│     Ordered implementation checklist                    │
├─────────────────────────────────────────────────────────┤
│  4. Implementation                                      │
│     AI executes tasks with human review                 │
└─────────────────────────────────────────────────────────┘
```

### Why Spec-Driven Works

| Benefit | How It Helped |
|---------|---------------|
| **Structured thinking** | Forced us to define requirements before coding |
| **Traceable decisions** | Every feature links back to a user story |
| **Incremental progress** | Clear task list with checkboxes |
| **Context preservation** | Specs survive session boundaries |
| **Human oversight** | Review points at each phase |

### Example: Credential Manager Spec

Our credential manager started as a user story:

> "As a cloud engineer, I want my credentials encrypted at rest, so that a stolen laptop doesn't expose my AWS keys."

This became acceptance criteria:
- Credentials encrypted with GPG
- Master key stored in system keyring
- No plaintext credentials on disk
- Automatic credential injection into CLIs

Which became a design with specific components, then tasks we could execute one by one.

## Challenges We Faced

Building a Linux distribution with AI assistance wasn't without obstacles:

### Calamares Installer Issues

| Problem | What Happened | Solution |
|---------|---------------|----------|
| **LUKS configuration** | Installer didn't properly configure encrypted boot | Custom preseed scripts + manual GRUB configuration |
| **Partition detection** | Calamares struggled with complex partition schemes | Simplified to single encrypted root + swap |
| **Post-install hooks** | Scripts ran in wrong order | Explicit dependency ordering in modules |

### GRUB Bootloader Challenges

| Problem | What Happened | Solution |
|---------|---------------|----------|
| **Encrypted /boot** | GRUB couldn't find kernel after encryption | LUKS1 for /boot (GRUB limitation) |
| **Initramfs generation** | Missing cryptsetup modules | Custom initramfs hooks |
| **EFI vs BIOS** | Different code paths for each | Unified installer with detection |

### Session Length Limits

| Problem | What Happened | Solution |
|---------|---------------|----------|
| **Context loss** | Long sessions lost earlier context | Spec files as persistent context |
| **Repeated errors** | AI would make same mistakes after context reset | Document solutions in steering files |
| **Complex debugging** | Multi-file issues hard to track across sessions | Detailed error logs + reproduction steps |

### Model Limitations

| Problem | What Happened | Solution |
|---------|---------------|----------|
| **Sonnet repetition** | Would repeat same incorrect approach | Switched to Opus for complex problems |
| **Credit limits** | Hit API limits during intensive development | Batch similar tasks, prioritize complex work |
| **Shell script errors** | Subtle bash syntax issues | Explicit shellcheck validation |

## What Worked Well

### AI Strengths

| Area | Why AI Excelled |
|------|-----------------|
| **Documentation** | Generated comprehensive docs from code |
| **Boilerplate** | Systemd units, AppArmor profiles, config files |
| **Research** | Comparing approaches, finding best practices |
| **Refactoring** | Consistent changes across many files |
| **Testing** | Generating test cases from requirements |

### Human Strengths

| Area | Why Humans Were Essential |
|------|---------------------------|
| **Architecture decisions** | High-level design choices |
| **Security review** | Validating security claims |
| **User experience** | What feels right to use |
| **Edge cases** | Real-world scenarios AI missed |
| **Final approval** | Every change reviewed before merge |

## Lessons Learned

### For AI-Assisted Development

| Lesson | Details |
|--------|---------|
| **Specs are essential** | Without structured requirements, AI wanders |
| **Small tasks work better** | Break complex work into focused chunks |
| **Context is king** | Provide relevant files, not everything |
| **Verify, don't trust** | AI makes confident mistakes |
| **Document solutions** | Prevent repeating solved problems |

### For Building with AI

| Lesson | Details |
|--------|---------|
| **Start with architecture** | AI can't design systems from scratch well |
| **Use proven tools** | AI excels at integrating existing solutions |
| **Test incrementally** | Catch issues before they compound |
| **Keep humans in the loop** | Review every significant change |
| **Embrace iteration** | First attempt rarely perfect |

## The Numbers

| Metric | Value |
|--------|-------|
| **Development time** | ~3-4 months |
| **Spec documents** | 15+ requirement/design docs |
| **Components built** | 8 custom components |
| **Tools integrated** | 50+ cloud tools |
| **Documentation pages** | 30+ pages |
| **Security layers** | 8 defense-in-depth layers |

## Why We're Sharing This

Transparency is a core value of NubiferOS. We believe in:

| Principle | Application |
|-----------|-------------|
| **Honest about our process** | AI helped build this—we're not hiding it |
| **Reproducible methods** | Others can learn from our approach |
| **Continuous improvement** | Sharing invites feedback |
| **Demystifying AI development** | It's a tool, not magic |

## Try It Yourself

Interested in spec-driven AI development?

| Resource | Link |
|----------|------|
| **Kiro IDE** | [kiro.dev](https://kiro.dev) |
| **Claude** | [claude.ai](https://claude.ai) |
| **Our Specs** | [GitHub - custom-linux-distro tasks](https://github.com/nubiferos/nubiferos/blob/trunk/.kiro/specs/custom-linux-distro/tasks.md) |
| **NubiferOS Source** | [GitHub Repository](https://github.com/nubiferos/nubiferos) |

## Questions?

| Channel | Link |
|---------|------|
| **Discord** | [Join our community](https://discord.gg/nubiferos) |
| **GitHub Discussions** | [Ask questions](https://github.com/nubiferos/nubiferos/discussions) |
| **Issues** | [Report problems](https://github.com/nubiferos/nubiferos/issues) |
