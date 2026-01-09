# Doc Harmonization

An AI-orchestrated workflow that ensures consistency **across** your project's documentation files.

## What It Does

| Capability | Description |
|------------|-------------|
| **Cross-doc deduplication** | Removes content repeated across documents |
| **Terminology alignment** | Ensures consistent naming and terms |
| **Content placement** | Moves misplaced content to the correct document |
| **Cross-references** | Adds links between related sections |

## When to Use

Use **doc-harmonization** when you have multiple docs that need to work together:

- PRD has technical details that belong in Architecture
- Architecture has UI/UX content that belongs in Front-End-Spec
- Same concept described differently across documents
- Missing cross-references between related sections

> **Tip:** Run this workflow *before* running doc-optimize. Get content in the right documents first, then optimize each document individually.

## Supported Configurations

| Mode | Documents | UI/UX Location |
|------|-----------|----------------|
| **2-DOC** | PRD + Architecture | Stays in PRD |
| **3-DOC** | PRD + Architecture + Front-End-Spec | Moves to FE-Spec |

Mode is auto-detected from your config.

## How It Works

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

## Quick Start

1. Copy `workflows/.doc-harmonization/` to your project
2. Copy `progress.template.md` to `progress.md`
3. Edit `progress.md` — set your doc paths in Project Config
4. Say: **"Run the doc-harmonization workflow"**
5. Let it run — it pauses for confirmation at key points

## Key Files

| File | Purpose |
|------|---------|
| `workflow.md` | Orchestrator instructions and state machine |
| `progress.md` | State tracking (create from template) |
| `progress.template.md` | Template for new projects |
| `agents/*.md` | Specialized sub-agent prompts |
| `context/*.md` | Working files (auto-generated) |
| `reports/*.md` | Review reports (auto-generated) |

## Recovery

Context reset mid-workflow? Just say **"continue"** — the orchestrator reads `progress.md` and resumes.

## Requirements

- AI assistant with file read/write capability (Claude Code, Kiro, etc.)
- Project docs in `docs/` directory:
  - `docs/prd.md` (required)
  - `docs/architecture.md` (required)
  - `docs/front-end-spec.md` (optional, enables 3-DOC mode)

## Related Workflows

| Workflow | Purpose | Run Order |
|----------|---------|-----------|
| **doc-harmonization** | Cross-document consistency | 1st |
| **doc-optimize** | Per-document optimization | 2nd |
