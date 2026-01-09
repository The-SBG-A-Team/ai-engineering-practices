# BMAD Method Guide

**B**reakthrough **M**ethod for **A**gile AI-**D**riven Development — A structured methodology for AI-driven agile development.

## What is BMAD?

BMAD Method is an open-source framework that provides specialized AI agents and guided workflows for software development. Instead of generic AI assistance, BMAD gives you domain-expert personas (PM, Architect, Developer, QA) that understand their roles and follow proven methodologies.

**Why we use it:**
- Structured planning produces better code than ad-hoc prompting
- Specialized agents provide domain expertise
- Workflows ensure nothing gets missed
- Documents (PRD, Architecture) serve as persistent context

---

## Installation

### New Project Setup

```bash
# Install BMAD Method (v6 recommended)
npx bmad-method@alpha install

# Or stable v4
npx bmad-method install
```

The installer will:
1. Create `.bmad-core/` directory with agents, tasks, templates
2. Generate `AGENTS.md` file for IDE integration
3. Set up `core-config.yaml` for project configuration

### IDE Integration

We use **Kiro CLI** which automatically loads `AGENTS.md` as a steering file. This gives access to BMAD agents and commands using the `*` syntax.

```bash
# Kiro automatically loads AGENTS.md
# Reference agents naturally:
"As dev, implement the user authentication"
"As pm, create a PRD for the task management feature"
```

### Refresh After Updates

```bash
# Reinstall and regenerate AGENTS.md
npx bmad-method install -f -i codex
```

---

## Agents Cheatsheet

### Quick Reference

| Agent | ID | When to Use |
|-------|-----|-------------|
| Business Analyst | `analyst` | Market research, brainstorming, project briefs |
| Product Manager | `pm` | PRDs, product strategy, feature prioritization |
| Product Owner | `po` | Backlog management, story validation, sprint planning |
| Architect | `architect` | System design, architecture docs, tech selection |
| UX Expert | `ux-expert` | UI/UX design, wireframes, front-end specs |
| Scrum Master | `sm` | Story creation, epic management, agile guidance |
| Developer | `dev` | Code implementation, debugging, refactoring |
| QA / Test Architect | `qa` | Test architecture, quality gates, code review |
| BMad Master | `bmad-master` | General tasks, any agent capability |
| BMad Orchestrator | `bmad-orchestrator` | Workflow coordination, agent switching |

### Agent Activation

```bash
# Natural language activation
"As pm, create a PRD for..."
"As architect, design the system for..."
"As dev, implement..."

# Or use the orchestrator
"*agent pm"
"*agent architect"
```

### Agent Details

#### Product Manager (pm) — John
**Focus:** PRDs, product strategy, roadmap planning

**Commands:**
- `*create-prd` — Create PRD from template
- `*create-brownfield-prd` — PRD for existing projects
- `*shard-prd` — Split PRD into sections
- `*correct-course` — Handle change requests

#### Architect (architect) — Winston
**Focus:** System design, technology selection, API design

**Commands:**
- `*create-backend-architecture` — Backend architecture doc
- `*create-full-stack-architecture` — Full stack architecture
- `*create-front-end-architecture` — Frontend architecture
- `*document-project` — Document existing project
- `*execute-checklist` — Run architecture checklist

#### Developer (dev) — James
**Focus:** Code implementation, debugging, testing

**Commands:**
- `*develop-story` — Implement story tasks sequentially
- `*run-tests` — Execute linting and tests
- `*review-qa` — Apply QA fixes
- `*explain` — Explain what was done and why

**Critical Rules:**
- Story has ALL info needed — don't load PRD/architecture unless directed
- Only update Dev Agent Record sections in story files
- Follow `develop-story` command when implementing

#### Scrum Master (sm) — Bob
**Focus:** Story preparation, sprint management

**Commands:**
- `*draft` — Create next story from epic
- `*story-checklist` — Validate story draft
- `*correct-course` — Handle mid-sprint changes

#### QA / Test Architect (qa) — Quinn
**Focus:** Test strategy, quality gates, code review

**Commands:**
- `*risk {story}` — Risk assessment before development
- `*design {story}` — Create test strategy
- `*trace {story}` — Verify test coverage
- `*nfr {story}` — Check non-functional requirements
- `*review {story}` — Full assessment + quality gate
- `*gate {story}` — Update quality gate status

---

## Commands Cheatsheet

All commands use `*` prefix (e.g., `*help`, `*agent pm`).

### Universal Commands

| Command | Description |
|---------|-------------|
| `*help` | Show available commands |
| `*agent [name]` | Switch to specialized agent |
| `*task [name]` | Run specific task |
| `*checklist [name]` | Execute checklist |
| `*yolo` | Toggle skip confirmations mode |
| `*exit` | Exit current agent |

### Document Commands

| Command | Description |
|---------|-------------|
| `*create-doc {template}` | Create document from template |
| `*doc-out` | Output full document |
| `*shard-doc {doc} {dest}` | Split document into sections |

### Workflow Commands

| Command | Description |
|---------|-------------|
| `*workflow-init` | Analyze project and recommend workflow |
| `*workflow [name]` | Start specific workflow |
| `*workflow-guidance` | Get help selecting workflow |

### Development Commands

| Command | Description |
|---------|-------------|
| `*develop-story` | Implement story tasks |
| `*run-tests` | Execute tests |
| `*review-qa` | Apply QA fixes |

---

## Workflow Overview

### Planning Phase (Web UI or IDE)

```
Project Idea
    ↓
[Optional] *analyst → *brainstorm {topic}, *create-project-brief
    ↓
*pm → *create-prd (or *create-brownfield-prd for existing projects)
    ↓
[Optional] *ux-expert → *create-front-end-spec, *generate-ui-prompt
    ↓
*architect → *create-full-stack-architecture (or *create-backend-architecture)
    ↓
*po → *execute-checklist-po (runs master checklist)
    ↓
*po → *shard-doc {document} {destination}
    ↓
Ready for Development
```

### Development Phase (IDE)

```
*sm → *draft (creates next story from epic + architecture)
    ↓
[Optional] *qa → *risk {story}, *design {story}
    ↓
[Optional] *po → *validate-story-draft {story}
    ↓
*dev → *develop-story
    ↓
*dev → *run-tests
    ↓
[Optional] *qa → *review {story} → creates quality gate
    ↓
COMMIT YOUR CHANGES
    ↓
Mark Story Done
    ↓
Repeat from *sm → *draft
```

### Command Quick Reference by Phase

| Phase | Agent | Command | Purpose |
|-------|-------|---------|---------|
| **Analysis** | `*analyst` | `*brainstorm {topic}` | Structured brainstorming |
| | | `*create-project-brief` | Initial project brief |
| | | `*research-prompt {topic}` | Deep research prompt |
| **Planning** | `*pm` | `*create-prd` | Create PRD from template |
| | | `*create-brownfield-prd` | PRD for existing projects |
| | | `*shard-prd` | Split PRD into sections |
| **UX** | `*ux-expert` | `*create-front-end-spec` | UI/UX specification |
| | | `*generate-ui-prompt` | AI frontend prompt for v0/Lovable |
| **Architecture** | `*architect` | `*create-full-stack-architecture` | Full stack design |
| | | `*create-backend-architecture` | Backend only |
| | | `*create-front-end-architecture` | Frontend only |
| | | `*document-project` | Document existing project |
| **Validation** | `*po` | `*execute-checklist-po` | Run master checklist |
| | | `*validate-story-draft {story}` | Validate story before dev |
| | | `*shard-doc {doc} {dest}` | Split document |
| **Story Prep** | `*sm` | `*draft` | Create next story |
| | | `*story-checklist` | Validate story draft |
| | | `*correct-course` | Handle changes |
| **Development** | `*dev` | `*develop-story` | Implement story tasks |
| | | `*run-tests` | Execute tests |
| | | `*review-qa` | Apply QA fixes |
| | | `*explain` | Explain implementation |
| **QA** | `*qa` | `*risk {story}` | Risk assessment |
| | | `*design {story}` | Test strategy |
| | | `*trace {story}` | Requirements traceability |
| | | `*nfr {story}` | Non-functional requirements |
| | | `*review {story}` | Full review + quality gate |
| | | `*gate {story}` | Update gate status |

### Key Artifacts

| Artifact | Location | Purpose |
|----------|----------|---------|
| PRD | `docs/prd.md` | Requirements, epics, stories |
| Architecture | `docs/architecture.md` | Technical design |
| Sharded Epics | `docs/epics/` | Individual epic files |
| Stories | `docs/stories/` | Implementation-ready stories |
| QA Gates | `docs/qa/gates/` | Quality gate decisions |

---

## Configuration

### core-config.yaml

The `.bmad-core/core-config.yaml` file configures BMAD for your project:

```yaml
# Key settings
devStoryLocation: docs/stories
prdSharded: true
architectureSharded: true

# Files dev agent always loads
devLoadAlwaysFiles:
  - docs/architecture/coding-standards.md
  - docs/architecture/tech-stack.md
  - docs/architecture/project-structure.md
```

### Developer Context Files

The `devLoadAlwaysFiles` list defines what the dev agent always loads. Keep these lean and focused on rules the agent needs to follow.

---

## Best Practices

### 1. Plan Before Coding
Use PM and Architect agents to create specs before implementation. The specs become persistent context for the dev agent.

### 2. Use Specialized Agents
Don't use a generic agent for everything. Each agent has domain expertise:
- PM understands product requirements
- Architect understands system design
- Dev understands implementation patterns

### 3. Follow the Story Workflow
Stories contain everything the dev needs. Don't load PRD/architecture during implementation unless the story directs you to.

### 4. Commit After Each Story
The workflow explicitly reminds you to commit before proceeding. This prevents losing work and creates clean history.

### 5. Use QA for High-Risk Stories
Run `*risk` and `*design` before development on complex stories. Use `*review` after implementation for quality gates.

---

## References

- [BMAD Method GitHub](https://github.com/bmad-code-org/BMAD-METHOD)
- [BMAD Documentation](http://docs.bmad-method.org/)
- [BMadCode YouTube](https://www.youtube.com/@BMadCode)
- [BMAD Discord](https://discord.gg/gk8jAdXWmj)
