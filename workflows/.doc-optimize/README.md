# Doc Optimize

An AI-orchestrated workflow that optimizes your project documentation for clarity, structure, and professional quality.

## What It Does

| Capability | Description |
|------------|-------------|
| **Consolidate** | Finds scattered content and brings it together |
| **Deduplicate** | Eliminates repeated information |
| **Restructure** | Merges, splits, or reorganizes sections as needed |
| **Streamline** | Removes content that doesn't belong (e.g., code in PRDs) |
| **Improve Writing** | Applies active voice, concise phrasing, professional tone |

## Supported Documents

| Type | File | Focus |
|------|------|-------|
| Architecture | `docs/architecture.md` | Technical design, diagrams, schemas, API contracts |
| PRD | `docs/prd.md` | Requirements, user stories, acceptance criteria |
| Front-End Spec | `docs/front-end-spec.md` | User flows, component specs, design tokens |
| Project Brief | `docs/project-brief.md` | Vision, problem statement, success metrics |

## How It Works

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

## Quick Start

1. Copy `workflows/.doc-optimize/` to your project
2. Ensure target docs exist in `docs/`
3. Say: **"Run the doc-optimize workflow"**
4. Select documents when prompted
5. Confirm section breakdown in Phase 0
6. Let it run

## Key Files

| File | Purpose |
|------|---------|
| `workflow.md` | Full configuration and rules |
| `progress.md` | State tracking (enables pause/resume) |
| `prompts/` | Agent instructions by phase |
| `reference/patterns.md` | Writing style guide, consolidation patterns |
| `reports/` | All outputs (analysis, plans, reviews) |

## Recovery

Context reset mid-workflow? Just say **"continue"** — the orchestrator reads `progress.md` and resumes.

## Writing Quality

The workflow applies these articulation rules automatically:

| Before | After |
|--------|-------|
| "is responsible for handling" | "handles" |
| "In order to" | "To" |
| "Due to the fact that" | "Because" |
| Passive voice | Active voice |
| Vague ("various", "some") | Specific counts |

See `reference/patterns.md` for the full style guide.

## Requirements

- AI assistant with file read/write capability (Claude Code, Kiro, etc.)
- Project docs in `docs/` directory
- Optional: bmad-core templates in `.bmad-core/templates/` for structure guidance
