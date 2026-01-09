# Custom Workflows Guide

Reusable AI orchestration patterns for complex, multi-step tasks.

## What Are Workflows?

Workflows are structured instructions that guide AI assistants through complex, multi-phase tasks. Instead of ad-hoc prompting, workflows provide:

- **Orchestrator logic** — Decides what to do next based on state
- **Sub-agent prompts** — Specialized instructions for each phase
- **State management** — Tracks progress across context resets
- **Recovery protocol** — Resume from any point

**Why we use them:**
- Complex tasks need structure to stay on track
- State files enable pause/resume across sessions
- Specialized prompts produce better results than generic instructions
- Reusable patterns save time on similar tasks

---

## Available Workflows

| Workflow | Purpose | When to Use |
|----------|---------|-------------|
| **doc-harmonization** | Cross-document consistency | Multiple docs need alignment |
| **doc-optimize** | Per-document optimization | Single doc needs cleanup |

### Recommended Order

For document improvement:
1. **doc-harmonization** first — Get content in the right documents
2. **doc-optimize** second — Optimize each document individually

---

## Doc Harmonization

Ensures consistency **across** your project's documentation files.

### What It Does

| Capability | Description |
|------------|-------------|
| Cross-doc deduplication | Removes content repeated across documents |
| Terminology alignment | Ensures consistent naming and terms |
| Content placement | Moves misplaced content to correct document |
| Cross-references | Adds links between related sections |

### When to Use

- PRD has technical details that belong in Architecture
- Architecture has UI/UX content that belongs in Front-End-Spec
- Same concept described differently across documents
- Missing cross-references between related sections

### Supported Modes

| Mode | Documents | UI/UX Location |
|------|-----------|----------------|
| **2-DOC** | PRD + Architecture | Stays in PRD |
| **3-DOC** | PRD + Architecture + Front-End-Spec | Moves to FE-Spec |

Mode is auto-detected from your config.

### How It Works

```
Phase 0: SETUP
└── Analyzes all docs, groups related sections into batches

Phase 1: BATCH PROCESSING (repeats per batch)
├── Analyze → Identifies cross-doc issues
├── Plan    → Designs content moves and fixes
├── Execute → Applies changes to documents
└── Review  → Validates batch results

Phase 2: FINALIZE
└── Cross-document validation, final cross-references
```

### Quick Start

1. Copy `workflows/.doc-harmonization/` to your project
2. Copy `progress.template.md` to `progress.md`
3. Edit `progress.md` — set your doc paths in Project Config
4. Say: **"Run the doc-harmonization workflow"**
5. Let it run — it pauses for confirmation at key points

### Key Files

| File | Purpose |
|------|---------|
| `workflow.md` | Orchestrator instructions and state machine |
| `progress.md` | State tracking (create from template) |
| `agents/*.md` | Specialized sub-agent prompts |

---

## Doc Optimize

Optimizes your project documentation for clarity, structure, and professional quality.

### What It Does

| Capability | Description |
|------------|-------------|
| Consolidate | Finds scattered content and brings it together |
| Deduplicate | Eliminates repeated information |
| Restructure | Merges, splits, or reorganizes sections |
| Streamline | Removes content that doesn't belong |
| Improve Writing | Active voice, concise phrasing, professional tone |

### Supported Documents

| Type | File | Focus |
|------|------|-------|
| Architecture | `docs/architecture.md` | Technical design, diagrams, schemas |
| PRD | `docs/prd.md` | Requirements, user stories, acceptance criteria |
| Front-End Spec | `docs/front-end-spec.md` | User flows, component specs |
| Project Brief | `docs/project-brief.md` | Vision, problem statement |

### How It Works

```
Phase 0: SETUP
└── Scans document, discovers sections, proposes batches

Per Section:
├── P1: Analyze  → Identifies issues
├── P2: Plan     → Creates execution plan
└── P3: Execute  → Edits the document

Per Batch:
└── Review → Quality check

Per Document:
└── Final Review → Polish, ToC, cross-references
```

### Quick Start

1. Copy `workflows/.doc-optimize/` to your project
2. Ensure target docs exist in `docs/`
3. Say: **"Run the doc-optimize workflow"**
4. Select documents when prompted
5. Confirm section breakdown in Phase 0
6. Let it run

### Writing Quality Rules

The workflow applies these automatically:

| Before | After |
|--------|-------|
| "is responsible for handling" | "handles" |
| "In order to" | "To" |
| "Due to the fact that" | "Because" |
| Passive voice | Active voice |
| Vague ("various", "some") | Specific counts |

---

## Building New Workflows

### Using the Workflow Template Guide

The `WORKFLOW-TEMPLATE-GUIDE.md` provides best practices for building reusable workflows.

**To create a new workflow:**

```bash
#sc-workflow --seq --uc --ultrathink "your workflow needs"
```

Then reference the template guide:

```
Reference workflows/WORKFLOW-TEMPLATE-GUIDE.md for best practices
```

### Core Principles

#### 1. Separation of Concerns

```yaml
ORCHESTRATOR:
  - Reads state files only
  - Spawns sub-agents with task instructions
  - Updates state based on agent returns
  - NEVER executes work directly

SUB-AGENTS:
  - Execute specific tasks
  - Read prompts and reference files
  - Write reports
  - Return structured YAML status
```

#### 2. State as Source of Truth

- All progress tracked in files (not conversation memory)
- Any agent can resume from state files
- Context resets don't lose progress
- Human-readable state for debugging

#### 3. Focused Scope per Agent

AI performs better with bounded focus areas:

```yaml
# BAD: "Fix all type errors, lint issues, and test failures"
# GOOD: "Fix type errors in the authentication module"
```

### Workflow Directory Structure

```
workflows/[workflow-name]/
├── orchestrator.md           # Main orchestration logic
├── progress.md               # Global state tracker
├── prompts/                  # Agent instructions by phase
│   ├── shared.md             # Common protocol
│   ├── phase-0/              # Setup
│   ├── phase-1/              # First phase
│   └── ...
├── reference/                # Shared knowledge
│   ├── core.md               # Rules, thresholds
│   ├── patterns.md           # Implementation patterns
│   └── state.md              # Current status
└── reports/                  # Output by section
```

### Agent Return Protocol

All agents return structured YAML:

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: path/to/report.md
SUMMARY: 2-3 sentences
LOOP_CONTROL: CONTINUE | EXIT  # loops only
EDITS_MADE: true | false       # loops only
BLOCKERS: none | description
```

### Loop Rules

```yaml
loops: [P2_LOOP, P3_LOOP, P5]

rules:
  minimum_iterations: 2
  maximum_iterations: 5

  continue_if:
    - iteration < 2
    - agent.edits_made == true
    - agent.confidence < 95

  exit_if:
    - iteration >= 2 AND agent.edits_made == false AND agent.confidence >= 95
```

### Environment Detection

Workflows must detect the runtime environment:

| Environment | SC Command Syntax | Sub-Agent |
|-------------|-------------------|-----------|
| Claude Code | `/sc:command --flags` | (default) |
| Kiro IDE | `/sc:command --flags` | `superclaude` |
| Kiro CLI | `#sc-command --flags` | `superclaude` |

### Handover Protocol

For long workflows that may exhaust context:

```yaml
triggers:
  - Context exhaustion approaching
  - 4+ agents spawned in session
  - Phase/section complete

steps:
  1: STOP - don't start new agents
  2: Let current agent finish
  3: Spawn summarizer agent
  4: Copy summary to progress.md
  5: Notify user with next steps
  6: WAIT - do not continue
```

### Recovery

Context reset mid-workflow? Just say **"continue"** — the orchestrator reads `progress.md` and resumes.

---

## Quick Reference

### Running Workflows

```bash
# Doc harmonization
"Run the doc-harmonization workflow"

# Doc optimize
"Run the doc-optimize workflow"

# Resume after context reset
"continue"
```

### Creating Workflows

```bash
# Generate new workflow
#sc-workflow --seq --uc --ultrathink "workflow for API testing"

# Reference best practices
"Use workflows/WORKFLOW-TEMPLATE-GUIDE.md for structure"
```

### Workflow Files

| File | Purpose |
|------|---------|
| `orchestrator.md` | Main logic and state machine |
| `progress.md` | State tracking (enables resume) |
| `prompts/shared.md` | Common agent protocol |
| `prompts/phase-N/*.md` | Phase-specific instructions |
| `reference/*.md` | Shared knowledge and patterns |
| `reports/*.md` | Generated outputs |

---

## References

- [WORKFLOW-TEMPLATE-GUIDE.md](../workflows/WORKFLOW-TEMPLATE-GUIDE.md) — Full template guide
- [doc-harmonization](../workflows/.doc-harmonization/) — Cross-doc workflow
- [doc-optimize](../workflows/.doc-optimize/) — Per-doc workflow
