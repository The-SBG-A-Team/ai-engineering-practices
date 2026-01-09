# AI Engineering Practices

A comprehensive guide to AI-assisted development practices, frameworks, and workflows for building software with AI coding assistants.

## Quick Start

| Practice | What It Does | Get Started |
|----------|--------------|-------------|
| **BMAD Method** | Agile AI-driven development with specialized agents | [BMAD Guide](docs/bmad-guide.md) |
| **SuperClaude** | Enhanced commands, flags, and MCP integrations | [SuperClaude Guide](docs/superclaude-guide.md) |
| **Custom Workflows** | Reusable AI orchestration patterns | [Workflows Guide](docs/workflows-guide.md) |
| **Context Engineering** | Principles for effective AI collaboration | [Context Engineering](docs/context-engineering.md) |

---

## Why This Repository?

AI coding assistants are powerful, but using them effectively requires structure. This repository documents our team's best practices for:

- **Planning before coding** — Spec-driven development produces better results than ad-hoc prompting
- **Specialized agents** — Different tasks need different AI personas (PM, Architect, Developer, QA)
- **Reusable workflows** — Complex multi-step tasks benefit from orchestrated workflows
- **Context management** — What you feed the AI matters as much as what you ask

---

## Core Frameworks

### BMAD Method

**B**reakthrough **M**ethod for **A**gile AI-**D**riven Development — A structured methodology for AI-driven agile development.

```bash
# Install BMAD in a new project
npx bmad-method install
```

BMAD provides:
- **21 specialized agents** (PM, Architect, Developer, QA, UX, Scrum Master, etc.)
- **50+ guided workflows** for planning and development
- **4-phase methodology**: Analysis → Planning → Solutioning → Implementation
- **IDE integration** via `AGENTS.md` file (works with Kiro, Codex, Claude Code)

→ [Full BMAD Guide](docs/bmad-guide.md)

### SuperClaude Framework

A configuration framework that enhances AI assistants with specialized commands and MCP server integrations.

```bash
# Install SuperClaude for Kiro CLI
npx superclaude-kiro install
```

SuperClaude provides:
- **30 slash commands** covering the full development lifecycle
- **16 specialized agents** with domain expertise
- **7 behavioral modes** for different contexts
- **8 MCP server integrations** for enhanced capabilities

→ [Full SuperClaude Guide](docs/superclaude-guide.md)

---

## Recommended Prompt Pattern

For most prompts, use this pattern as a baseline:

**Kiro CLI:**
```
#sc-sc --seq --uc --ultrathink "your task description"
```

**Kiro IDE / Claude Code:**
```
/sc:sc --seq --uc --ultrathink "your task description"
```

| Flag | Purpose |
|------|---------|
| `--seq` | Sequential thinking (structured reasoning) |
| `--uc` | Ultra-compressed output (token efficient) |
| `--ultrathink` | Deep reasoning (15-25 thought steps) |

This combination provides structured thinking with efficient output for most tasks.

---

## Custom Workflows

We've built reusable workflows for complex, multi-step AI tasks:

| Workflow | Purpose | When to Use |
|----------|---------|-------------|
| **doc-harmonization** | Cross-document consistency | Multiple docs need alignment |
| **doc-optimize** | Per-document optimization | Single doc needs cleanup |

Workflows are especially useful for large document improvements (like BMAD docs in `/docs`).

→ [Full Workflows Guide](docs/workflows-guide.md)

---

## Context Engineering

The evolution from "prompt engineering" to "context engineering" represents a shift in how we work with AI:

| Prompt Engineering | Context Engineering |
|--------------------|---------------------|
| Crafting single prompts | Managing entire information payloads |
| One-shot interactions | Persistent context across sessions |
| Clever wording | Systematic architecture |
| Ad-hoc | Spec-driven |

**Key principle:** What you feed the AI (specs, architecture docs, examples) matters more than how you phrase your request.

→ [Full Context Engineering Guide](docs/context-engineering.md)

---

## Installation Summary

### For a New Project

```bash
# 1. Install BMAD Method
npx bmad-method install

# 2. Install SuperClaude for Kiro CLI
npx superclaude-kiro install
```

### What Gets Installed

| Framework | Files Created | Purpose |
|-----------|---------------|---------|
| BMAD | `.bmad-core/`, `AGENTS.md` | Agent definitions, tasks, templates |
| SuperClaude | `~/.kiro/steering/superclaude/` | Steering files, MCP configs |

---

## Documentation Index

| Document | Description |
|----------|-------------|
| [BMAD Guide](docs/bmad-guide.md) | Installation, agents cheatsheet, commands, workflow |
| [SuperClaude Guide](docs/superclaude-guide.md) | Installation, commands, flags, MCP servers |
| [Workflows Guide](docs/workflows-guide.md) | Custom workflow documentation and template guide |
| [Context Engineering](docs/context-engineering.md) | AI engineering principles and best practices |

### Optional Setup

| Document | Description |
|----------|-------------|
| [Atlassian MCP Setup](ATLASSIAN_MCP_SETUP.md) | Confluence/Jira integration (add when needed, remove after to save context) |

---

## References

### Official Documentation

- [BMAD Method GitHub](https://github.com/bmad-code-org/BMAD-METHOD)
- [BMAD Documentation](http://docs.bmad-method.org/)
- [SuperClaude GitHub](https://github.com/SuperClaude-Org/SuperClaude_Framework)
- [SuperClaude Docs](https://superclaude.netlify.app/docs/)

### Community

- [BMAD Discord](https://discord.gg/gk8jAdXWmj)
- [BMadCode YouTube](https://www.youtube.com/@BMadCode)

---

## Contributing

This repository documents our team's AI engineering practices. Contributions welcome:

1. **Add new workflows** — Create reusable patterns in `workflows/`
2. **Improve guides** — Enhance documentation in `docs/`
3. **Share learnings** — Document what works (and what doesn't)

---

## License

MIT
