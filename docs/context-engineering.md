# Context Engineering

Principles for effective AI collaboration in software development.

## The Evolution: Prompt → Context Engineering

The narrow skill of "prompt engineering" is evolving into something more robust: **context engineering**.

| Prompt Engineering | Context Engineering |
|--------------------|---------------------|
| Crafting single prompts | Managing entire information payloads |
| One-shot interactions | Persistent context across sessions |
| Clever wording | Systematic architecture |
| Ad-hoc requests | Spec-driven development |
| Focus on the question | Focus on what you feed the AI |

**Key insight:** What you provide to the AI (specs, architecture docs, examples, conversation history) matters as much as—or more than—how you phrase your request.

---

## Why Context Engineering Matters

Research suggests that companies adopting context engineering practices see:
- **Up to 94% improvement** in AI reliability
- **~60% reduction** in AI mistakes
- **Significantly better** output quality and consistency

The difference is treating AI not as a calculator that solves isolated problems, but as a **strategic partner** that needs the bigger picture.

---

## Core Principles

### 1. Spec-Driven Development

**Don't start coding. Start with specs.**

```
❌ "Build a user authentication system"

✅ "Build a user authentication system based on:
   - PRD: docs/prd.md (requirements, user stories)
   - Architecture: docs/architecture.md (tech stack, patterns)
   - Story: docs/stories/1.1.auth.md (specific tasks, acceptance criteria)"
```

Specs serve as persistent context that:
- Defines requirements clearly
- Establishes constraints and patterns
- Provides acceptance criteria
- Enables consistent implementation across sessions

### 2. Context as Architecture

Treat the information you provide as a system requiring careful design:

| Component | Purpose | Example |
|-----------|---------|---------|
| System instructions | Define behavior and constraints | Agent persona, rules |
| Conversation history | Maintain continuity | Previous decisions, context |
| Retrieved documents | Provide domain knowledge | PRD, architecture, specs |
| Tool definitions | Enable capabilities | MCP servers, functions |
| Memory | Persist across sessions | State files, progress tracking |

### 3. Progressive Disclosure

Don't dump everything at once. Load context progressively:

```yaml
Stage 1 - Scan:
  - Read headers and summaries
  - Identify relevant sections
  - Note file locations

Stage 2 - Deep Dive:
  - Read only sections with gaps/issues
  - Skip "working" or "aligned" items

Stage 3 - Consolidate:
  - Reference by location, don't copy
  - Summarize findings
```

### 4. Focused Scope

AI performs better with bounded focus areas than broad "fix everything" tasks:

```yaml
# BAD: Broad scope
"Fix all type errors, lint issues, and test failures"

# GOOD: Focused scope
"Fix type errors in the authentication module"
```

**Why:** Narrow scope enables deeper analysis, reduces cognitive load, and produces more consistent output.

---

## Spec-Driven Development in Practice

### The Planning → Implementation Flow

```
1. PLAN FIRST
   ├── Create PRD (requirements, user stories)
   ├── Create Architecture (tech stack, patterns)
   └── Create Stories (specific tasks, acceptance criteria)

2. IMPLEMENT FROM SPECS
   ├── Story contains everything dev needs
   ├── Don't load PRD/architecture during implementation
   └── Specs are the persistent context

3. ITERATE
   ├── Update specs when requirements change
   ├── Specs become source of truth
   └── AI always has consistent context
```

### Why Specs Beat Ad-Hoc Prompting

| Ad-Hoc Prompting | Spec-Driven Development |
|------------------|-------------------------|
| Context lost between sessions | Specs persist across sessions |
| Inconsistent implementations | Consistent patterns from specs |
| Repeated explanations | Specs explain once |
| Drift from requirements | Specs anchor to requirements |
| Hard to review | Specs are reviewable artifacts |

### Practical Example

**Without specs:**
```
"Add user authentication with JWT tokens, password hashing, 
and session management. Use bcrypt for passwords and store 
sessions in Redis. Add login, logout, and refresh endpoints..."
```

**With specs:**
```
"Implement story 1.1 from docs/stories/1.1.auth.md"
```

The story file contains:
- User story and acceptance criteria
- Technical notes from architecture
- Specific tasks and subtasks
- Testing requirements
- File locations and patterns to follow

---

## Prompting Best Practices

### 1. Be Specific About Core Features

```yaml
# BAD
"Build a social media app"

# GOOD
"Build a social media app for photographers with:
- Portfolio galleries
- Image tagging
- Follow system
- Commenting"
```

### 2. Include Business Context

```yaml
# BAD
"Build an e-commerce site"

# GOOD
"Build an e-commerce site for handmade crafts with:
- Artist profiles
- Custom product options
- Commission tracking
- Marketplace model"
```

### 3. Reference Existing Patterns

```yaml
# BAD
"Create a new API endpoint"

# GOOD
"Create a new API endpoint following the pattern in:
- src/api/users.ts (structure)
- docs/architecture/api-patterns.md (conventions)"
```

### 4. Use Structured Flags

```bash
# Structured thinking + efficient output + deep analysis
#sc-sc --seq --uc --ultrathink "your task"
```

| Flag | Purpose |
|------|---------|
| `--seq` | Sequential thinking (step-by-step reasoning) |
| `--uc` | Ultra-compressed output (token efficient) |
| `--ultrathink` | Deep reasoning (15-25 thought steps) |

---

## Context Management Strategies

### 1. State Files for Long Tasks

For complex workflows, track state in files:

```yaml
# progress.md
status: IN_PROGRESS
phase: P4
section: authentication
last_completed: analyze-agent
next_action: Run plan-agent
```

**Benefits:**
- Resume after context resets
- Human-readable progress
- Any agent can continue from state

### 2. Document Sharding

Split large documents into focused sections:

```
docs/architecture.md (large)
    ↓
docs/architecture/
├── index.md
├── tech-stack.md
├── data-models.md
├── api-patterns.md
└── security.md
```

**Benefits:**
- Load only relevant sections
- Reduce token usage
- Faster context loading

### 3. Reference by Location

```yaml
# BAD: Copying content
"The user model has these fields: id, email, password_hash, 
created_at, updated_at, last_login, is_active, role..."

# GOOD: Reference by location
"See user model in src/models/user.ts lines 10-45"
```

### 4. Tables Over Prose

AI compresses prose but preserves structured data:

```yaml
# BAD: Prose
"The first endpoint is GET /api/users which returns a list of users.
The second endpoint is POST /api/users which creates a new user..."

# GOOD: Table
| Endpoint | Method | Description |
|----------|--------|-------------|
| /api/users | GET | List users |
| /api/users | POST | Create user |
```

---

## The Context Engineering Mindset

### Every Context Decision Involves Tradeoffs

| Dimension | Tradeoff |
|-----------|----------|
| Latency | More context = slower responses |
| Autonomy | More context = more independent action |
| Oversight | Less context = more user interaction needed |
| Pre-work | Retrieve/verify/compute before answering |
| Evidence | How much proof before acting |
| Cost of being wrong | Higher stakes = more context needed |

### Questions to Ask

1. **What does the AI need to know?** — Not everything, just what's relevant
2. **What patterns should it follow?** — Provide examples, not just rules
3. **What constraints exist?** — Tech stack, conventions, requirements
4. **What's the acceptance criteria?** — How do we know it's done?
5. **What can go wrong?** — Edge cases, error handling, security

---

## Recommended Reading

### Articles

| Title | Source | Key Insight |
|-------|--------|-------------|
| [Context Engineering: The Definitive 2025 Guide](https://www.flowhunt.io/blog/context-engineering/) | FlowHunt | Comprehensive overview of context engineering principles |
| [My LLM Coding Workflow Going into 2026](https://addyosmani.com/blog/ai-coding-workflow/) | Addy Osmani | Practical workflow patterns from a senior engineer |
| [Context Engineering for Reliable AI Agents](https://techcommunity.microsoft.com/blog/appsonazureblog/context-engineering-lessons-from-building-azure-sre-agent/4481200) | Microsoft | Lessons from building production AI agents |
| [Prompt Engineering Is Dead](https://www.neuralbuddies.com/p/prompt-engineering-is-dead) | Neural Buddies | The evolution from prompts to context |
| [Context Engineering Will Define AI's Next Chapter](https://ephor.substack.com/p/context-engineering-will-define-ais) | Substack | Strategic perspective on context engineering |

### Frameworks

| Framework | Focus | Link |
|-----------|-------|------|
| BMAD Method | Agile AI development | [GitHub](https://github.com/bmad-code-org/BMAD-METHOD) |
| SuperClaude | Commands and MCP integration | [GitHub](https://github.com/SuperClaude-Org/SuperClaude_Framework) |

---

## Quick Reference

### Context Engineering Checklist

- [ ] **Specs exist** — PRD, architecture, stories before coding
- [ ] **Context is focused** — Only relevant information loaded
- [ ] **Patterns are referenced** — Examples, not just rules
- [ ] **State is tracked** — Progress files for long tasks
- [ ] **Output is structured** — Tables, YAML, not prose
- [ ] **Scope is bounded** — One domain/task at a time

### Recommended Prompt Pattern

```bash
#sc-sc --seq --uc --ultrathink "task description"
```

### Spec-Driven Flow

```
Plan (PRD + Architecture) → Story → Implement → Review → Done
```

### Context Hierarchy

```
1. System instructions (agent persona, rules)
2. Specs (PRD, architecture, story)
3. Patterns (examples, conventions)
4. State (progress, decisions)
5. Request (current task)
```
