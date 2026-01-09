# SuperClaude Guide

A configuration framework that enhances AI coding assistants with specialized commands, behavioral modes, and MCP server integrations.

## What is SuperClaude?

SuperClaude is a meta-programming framework that transforms AI assistants into structured development platforms. It provides:
- **30 slash commands** covering the full development lifecycle
- **16 specialized agents** with domain expertise
- **7 behavioral modes** for different contexts
- **8 MCP server integrations** for enhanced capabilities

**Why we use it:**
- Structured commands produce consistent, high-quality output
- Flags control reasoning depth and output format
- MCP servers add capabilities (web search, code analysis, browser automation)
- Works with Kiro CLI, Kiro IDE, and Claude Code

---

## Installation

### For Kiro CLI

```bash
# Install SuperClaude
npx superclaude-kiro install

# With MorphLLM for fast file editing
npx superclaude-kiro install --with-morph

# Check status
npx superclaude-kiro status

# Update to latest
npx superclaude-kiro update
```

### What Gets Installed

| Component | Location | Purpose |
|-----------|----------|---------|
| Steering files | `~/.kiro/steering/superclaude/` | 30 command definitions |
| Agents | `~/.kiro/agents/` | 4 specialized agents |
| MCP servers | `~/.kiro/settings/mcp.json` | Tool integrations |
| Default agent | `~/.kiro/settings/cli.json` | Set to `superclaude` |

### MCP Server Selection

During installation, select which MCP servers to install:

| Server | Description | Default |
|--------|-------------|---------|
| `sequential-thinking` | Structured reasoning | Yes |
| `context7` | Library documentation lookup | Yes |
| `playwright` | Browser automation | Yes |
| `serena` | Semantic code analysis | Yes |
| `morphllm-fast-apply` | Ultra-fast file editing | No |

---

## Command Syntax

### Environment-Specific Syntax

| Environment | Syntax | Example |
|-------------|--------|---------|
| **Kiro CLI** | `#sc-command` | `#sc-implement --seq` |
| **Kiro IDE** | `/sc:command` | `/sc:implement --seq` |
| **Claude Code** | `/sc:command` | `/sc:implement --seq` |

---

## Commands Cheatsheet

### Planning & Design

| Command | Description |
|---------|-------------|
| `#sc-brainstorm` | Structured brainstorming |
| `#sc-design` | System architecture design |
| `#sc-estimate` | Time/effort estimation |

### Development

| Command | Description |
|---------|-------------|
| `#sc-implement` | Code implementation |
| `#sc-build` | Build workflows |
| `#sc-improve` | Code improvements |
| `#sc-cleanup` | Refactoring and cleanup |
| `#sc-explain` | Code explanations |

### Testing & Quality

| Command | Description |
|---------|-------------|
| `#sc-test` | Test generation |
| `#sc-analyze` | Code analysis |
| `#sc-troubleshoot` | Debugging and diagnosis |
| `#sc-reflect` | Retrospectives |

### Documentation

| Command | Description |
|---------|-------------|
| `#sc-document` | Documentation generation |
| `#sc-help` | Command help |

### Project Management

| Command | Description |
|---------|-------------|
| `#sc-pm` | Project management |
| `#sc-task` | Task tracking |
| `#sc-workflow` | Workflow generation |
| `#sc-git` | Git operations |

### Research & Analysis

| Command | Description |
|---------|-------------|
| `#sc-research` | Deep web research |

### Session Management

| Command | Description |
|---------|-------------|
| `#sc-save` | Save session state |
| `#sc-load` | Load session state |

### Utilities

| Command | Description |
|---------|-------------|
| `#sc-sc` | General SuperClaude command |
| `#sc-spawn` | Parallel task orchestration |
| `#sc-index` | Repository indexing |

---

## Flags Reference

### MCP Server Flags

| Flag | MCP Server | Purpose |
|------|------------|---------|
| `--seq` | sequential-thinking | Structured reasoning for every step |
| `--serena` | serena | Semantic code analysis tools |
| `--c7` / `--context7` | context7 | Library documentation lookup |
| `--play` / `--playwright` | playwright | Browser automation |
| `--all-mcp` | All servers | Enable all MCP servers |

### Analysis Depth Flags

| Flag | Thinking Steps | When to Use |
|------|----------------|-------------|
| `--think` | ~5-8 steps | Standard complex tasks |
| `--think-hard` | ~10-15 steps | Difficult problems |
| `--ultrathink` | 15-25+ steps | Maximum depth analysis |

### Output Flags

| Flag | Effect |
|------|--------|
| `--uc` / `--ultracompressed` | Minimal tokens, symbols/abbreviations |
| `--token-efficient` | Concise responses, reduced context |

### Execution Flags

| Flag | Effect |
|------|--------|
| `--morph` | Use MorphLLM for fast file editing |

---

## Recommended Usage Patterns

### Default Pattern (Most Tasks)

```bash
#sc-sc --seq --uc --ultrathink "your task description"
```

This combination provides:
- `--seq`: Structured step-by-step reasoning
- `--uc`: Token-efficient output
- `--ultrathink`: Deep analysis (15-25 thought steps)

### Implementation Tasks

```bash
#sc-implement --seq --serena --uc --morph "implement user authentication"
```

- `--serena`: Code analysis for understanding existing code
- `--morph`: Fast file editing

### Code Analysis

```bash
#sc-analyze --seq --serena --ultrathink "review this code for security issues"
```

### Research Tasks

```bash
#sc-research --seq --c7 --ultrathink "latest React patterns for state management"
```

- `--c7`: Library documentation lookup

### Workflow Generation

```bash
#sc-workflow --seq --uc --ultrathink "create a workflow for API integration testing"
```

---

## Behavioral Modes

SuperClaude supports natural language mode activation:

| Mode | Trigger Phrase | Behavior |
|------|----------------|----------|
| Think deeply | "think through this step by step" | Activates `--think-hard` |
| Brainstorm | "let's brainstorm this" | Collaborative discovery |
| Delegate | "delegate this to sub-tasks" | Break into parallel tasks |
| Safe mode | "validate carefully before executing" | Maximum validation |
| Token efficient | "be concise" | Compressed output |

---

## MCP Server Details

### Sequential Thinking
**Purpose:** Structured reasoning and problem-solving

Enables step-by-step thinking for complex tasks. Activated with `--seq` flag.

### Serena
**Purpose:** Semantic code analysis and editing

Provides tools for:
- `find_symbol` — Find code symbols
- `get_symbols_overview` — File symbol overview
- `find_referencing_symbols` — Find references
- `replace_symbol_body` — Edit code

Activated with `--serena` flag.

### Context7
**Purpose:** Library documentation lookup

Fetches up-to-date documentation for programming libraries. Activated with `--c7` or `--context7` flag.

### Playwright
**Purpose:** Browser automation and testing

Enables browser control for:
- E2E testing
- Web scraping
- UI automation

Activated with `--play` or `--playwright` flag.

### MorphLLM Fast Apply
**Purpose:** Ultra-fast file editing (10,500+ tokens/sec)

Requires API key from [morphllm.com](https://www.morphllm.com). Activated with `--morph` flag.

---

## Agent Switching

SuperClaude installs 4 specialized agents:

| Agent | Purpose |
|-------|---------|
| `superclaude` | Main framework agent (default) |
| `sc-pm` | Project Manager agent |
| `sc-implement` | Implementation agent |
| `sc-analyze` | Analysis agent |

### Switch Agents

```bash
# In Kiro session
/agent swap
# Select from: superclaude, sc-pm, sc-implement, sc-analyze

# Or start with specific agent
kiro-cli chat --agent sc-pm
```

---

## Troubleshooting

### SuperClaude Not Loading

```bash
# Check setting
kiro-cli settings chat.defaultAgent

# Set manually
kiro-cli settings chat.defaultAgent superclaude
```

### MCP Servers Not Working

```bash
# List MCP servers
kiro-cli mcp list

# Check config
cat ~/.kiro/settings/mcp.json

# Check status
npx superclaude-kiro status
```

### Reinstall from Scratch

```bash
npx superclaude-kiro uninstall
npx superclaude-kiro install --force
```

---

## Quick Reference Card

```
RECOMMENDED DEFAULT:
#sc-sc --seq --uc --ultrathink "task"

IMPLEMENTATION:
#sc-implement --seq --serena --uc --morph "task"

ANALYSIS:
#sc-analyze --seq --serena --ultrathink "task"

RESEARCH:
#sc-research --seq --c7 --ultrathink "topic"

WORKFLOW:
#sc-workflow --seq --uc --ultrathink "workflow needs"

FLAGS:
--seq         Sequential thinking (always use)
--uc          Ultra-compressed output (always use)
--ultrathink  Deep reasoning (15-25 steps)
--serena      Code analysis tools
--morph       Fast file editing
--c7          Documentation lookup
--play        Browser automation
```

---

## References

- [SuperClaude GitHub](https://github.com/SuperClaude-Org/SuperClaude_Framework)
- [SuperClaude Docs](https://superclaude.netlify.app/docs/)
