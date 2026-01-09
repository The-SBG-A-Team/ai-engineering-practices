# Document Optimization Workflow

**Version:** 4.0.0 | **Updated:** 2026-01-09

> **Start here**: Read `prompts/orchestrator.md` for execution instructions.

---

## Quick Start

1. **Start workflow**: Orchestrator detects available documents
2. **Select documents**: User chooses which docs to consolidate
3. **Run Phase 0**: Setup agent analyzes selected document(s), discovers sections
4. **Confirm sections**: Review `reference/sections-[doc].md`, modify if needed
5. **Execute workflow**: Orchestrator manages agents through phases per document
6. **Check progress**: `progress.md`

---

## Supported Documents

> The workflow auto-detects available documents in `docs/` and asks user which to consolidate.

| Document Type | File | Template | Purpose |
|---------------|------|----------|---------|
| Architecture | `docs/architecture.md` | `architecture-tmpl.yaml` | Technical architecture |
| PRD | `docs/prd.md` | `prd-tmpl.yaml` | Product requirements |
| Front-End Spec | `docs/front-end-spec.md` | `front-end-spec-tmpl.yaml` | UI/UX specification |
| Project Brief | `docs/project-brief.md` | `project-brief-tmpl.yaml` | Project overview |

### Document-Type Guidelines

```yaml
architecture:
  focus:
    - Technical accuracy
    - Code streamlining (remove implementations)
    - Diagram consistency
    - Cross-references to source code
  keep:
    - Mermaid diagrams
    - Schema DDL
    - API contracts
    - Interface definitions
  remove:
    - Copy-pasted source code
    - Full implementations

prd:
  focus:
    - Requirement completeness
    - Traceability (FR/NFR numbering)
    - Scope clarity
    - Stakeholder alignment
  keep:
    - Numbered requirements (FR/NFR)
    - User stories
    - Acceptance criteria
    - Decision records
  remove:
    - Implementation details
    - Technical specifications (belongs in architecture)

front-end-spec:
  focus:
    - User experience clarity
    - Component specifications
    - Design system consistency
    - Accessibility requirements
  keep:
    - User flows (Mermaid)
    - Component specs
    - Design tokens
    - Accessibility notes
  remove:
    - Implementation code
    - Backend details

project-brief:
  focus:
    - Vision clarity
    - Stakeholder alignment
    - Success metrics
    - Scope definition
  keep:
    - Problem statement
    - Target users
    - Success criteria
    - MVP scope
  remove:
    - Technical details
    - Implementation specifics
```

---

## Workflow Overview

### Phase -1: Document Selection (Orchestrator)

```
┌─────────────────────────────────────────────────────────────┐
│  ORCHESTRATOR                                               │
│  - Scans docs/ for available documents                      │
│  - Presents document options to user                        │
│  - Accepts user selection (single or multiple)              │
│  - Configures workflow for selected documents               │
└─────────────────────────────────────────────────────────────┘
```

### Phase 0: Dynamic Setup (Per Document)

```
┌─────────────────────────────────────────────────────────────┐
│  SETUP AGENT                                                │
│  - Analyzes target document                                 │
│  - Reads corresponding template for structure guidance      │
│  - Discovers sections/topics                                │
│  - Proposes batching strategy                               │
│  - Generates reference/sections-[doc].md                    │
│  - Awaits user confirmation                                 │
└─────────────────────────────────────────────────────────────┘
```

### Per Section: 3 Phases

```
┌─────────────────────────────────────────────────────────────┐
│  P1: ANALYZE AGENT                                          │
│  - Prompt: prompts/phase-1/analyze-agent.md                 │
│  - Output: reports/[doc]/[section]/01-analysis.md           │
├─────────────────────────────────────────────────────────────┤
│  P2: PLAN AGENT                                             │
│  - Prompt: prompts/phase-2/plan-agent.md                    │
│  - Input:  reports/[doc]/[section]/01-analysis.md           │
│  - Output: reports/[doc]/[section]/02-plan.md               │
├─────────────────────────────────────────────────────────────┤
│  P3: EXECUTE AGENT                                          │
│  - Prompt: prompts/phase-3/execute-agent.md                 │
│  - Input:  reports/[doc]/[section]/02-plan.md               │
│  - Edits:  docs/[document].md                               │
│  - Output: reports/[doc]/[section]/03-execute.md            │
└─────────────────────────────────────────────────────────────┘
```

### Per Batch: Review

```
┌─────────────────────────────────────────────────────────────┐
│  REVIEW AGENT                                               │
│  - Prompt: prompts/utility/review-agent.md                  │
│  - Input:  All section reports in batch                     │
│  - May edit: docs/[document].md                             │
│  - Output: reports/[doc]/batch-[N]/review.md                │
└─────────────────────────────────────────────────────────────┘
```

### Per Document: Final Review

```
┌─────────────────────────────────────────────────────────────┐
│  FINAL REVIEW AGENT                                         │
│  - Prompt: prompts/utility/final-review-agent.md            │
│  - Input:  All batch review reports for document            │
│  - Edits:  docs/[document].md (final polish)                │
│  - Output: reports/[doc]/final/review.md                    │
└─────────────────────────────────────────────────────────────┘
```

---

## Directory Structure

```
workflows/.doc-optimize/
├── workflow.md              # This file - Configuration & Overview
├── progress.md              # Progress tracking & state
│
├── prompts/
│   ├── orchestrator.md      # Orchestration instructions
│   ├── shared.md            # Common protocol for all agents
│   ├── phase-0/
│   │   └── setup-agent.md   # Dynamic document analysis
│   ├── phase-1/
│   │   └── analyze-agent.md # Section analysis
│   ├── phase-2/
│   │   └── plan-agent.md    # Execution planning
│   ├── phase-3/
│   │   └── execute-agent.md # Document editing
│   └── utility/
│       ├── review-agent.md  # Batch review
│       ├── final-review-agent.md  # Final quality check
│       └── debugger-agent.md      # Issue diagnosis
│
├── reference/
│   ├── core.md              # Rules, thresholds, paths
│   ├── patterns.md          # Consolidation patterns (all doc types)
│   ├── state.md             # Current blockers, status
│   └── sections-[doc].md    # [Generated by Phase 0 per document]
│
└── reports/
    ├── 00-setup.md          # Phase 0 setup report
    └── [doc-type]/          # Per document type
        ├── [section-slug]/
        │   ├── .state.json  # Section checkpoint
        │   ├── 01-analysis.md
        │   ├── 02-plan.md
        │   └── 03-execute.md
        ├── batch-[N]/
        │   └── review.md
        └── final/
            └── review.md
```

---

## Purpose

This workflow consolidates and optimizes documentation by:

1. **Finding scattered content** - Content about a topic spread across sections
2. **Removing duplicates** - Repeated information
3. **Streamlining content** - Remove inappropriate content for doc type
4. **Ensuring reader completeness** - Each section stands alone
5. **Preparing for sharding** - Sections can be extracted as separate files
6. **Improving articulation** - Clear English, active voice, professional tone

**Goal**: Each section must be **self-contained, reader-complete, and professionally written**.

> **Writing Style:** See `reference/patterns.md` → "Writing Style & Articulation" for dos/don'ts and transformation patterns.

---

## Core Rules

1. **Dynamic discovery** - Phase 0 discovers sections, no hardcoded topics
2. **Document-type awareness** - Rules adapt based on document type
3. **Template-guided** - Use bmad-core templates as structure reference
4. **Sequential execution** - One sub-agent at a time per section
5. **File-based communication** - Agents read previous agent's output files
6. **Section ownership** - Agents own sections, not arbitrary line ranges
7. **Reader-centric** - Optimize for reader experience

---

## Reader-Centric Consolidation Criteria

**Ask these questions when deciding where content belongs:**

1. **Reader expectation**: Would a reader looking for this info expect it here?
2. **Self-containment**: After reading this section, does the reader have complete understanding?
3. **Context completeness**: Are there critical details elsewhere that would leave the reader with gaps?
4. **Standalone viability**: Could this section be extracted as a separate document and still make sense?

---

## Restructuring Guidelines

**Sections are not sacred. The goal is a well-organized, readable document.**

### When to Merge Sections
- Two sections cover overlapping topics (>50% overlap)
- A section is too small to stand alone (<50 lines of valuable content)
- Content naturally belongs together but was artificially separated
- Merging eliminates significant duplication

### When to Split Sections
- A section is too large (>500 lines) and covers multiple distinct topics
- A subsection has grown important enough to warrant its own section
- Splitting improves navigation and discoverability

### When to Rename Sections
- Current name doesn't reflect actual content
- A more descriptive name would help readers find information
- After merging, a new name better captures combined content

### When to Delete Sections
- Content is entirely duplicated elsewhere
- Content provides no value (pure fluff)
- Content is outdated/irrelevant to current system

---

## Content Guidelines by Document Type

### Architecture Documents

**KEEP:**
- Mermaid diagrams
- Schema DDL
- Configuration examples (YAML)
- API contracts
- File references (`See: app/...`)
- Interface definitions
- TypeScript/Java interfaces (contracts, not implementations)
- Short pseudocode (≤10 lines for complex logic)

**REMOVE:**
- Copy-pasted source code
- Method implementations
- Full class bodies
- Code that duplicates what's in `app/`

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| 50-line code block | Description + file reference |
| Full implementation | Pattern name + behavior description |
| Copy-pasted class | Class responsibility + `See: app/.../File.java` |

### PRD Documents

**KEEP:**
- Numbered requirements (FR1, NFR1)
- User stories with acceptance criteria
- Decision records
- Scope boundaries
- Success metrics

**REMOVE:**
- Technical implementation details
- Code snippets
- Database schemas (belongs in architecture)
- API specifications (belongs in architecture)

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| Technical specs | Reference to architecture doc |
| Implementation notes | Acceptance criteria |
| System design | High-level requirements |

### Front-End Spec Documents

**KEEP:**
- User flows (Mermaid)
- Component specifications
- Design tokens
- Accessibility requirements
- Interaction patterns

**REMOVE:**
- Backend implementation details
- API implementation code
- Database schemas
- Server-side logic

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| API implementation | API contract reference |
| Backend logic | Data flow description |
| Full component code | Component spec + props |

---

## Using on Other Projects

To reuse this workflow:

1. **Copy the workflow folder** to new project's `workflows/` directory
2. **Ensure docs exist** in `docs/` directory
3. **Ensure templates exist** in `.bmad-core/templates/`
4. **Clear state files**:
   - Reset `progress.md` (use template)
   - Clear `reference/state.md`
   - Delete `reference/sections-*.md` (will be regenerated)
5. **Clear reports/** - Remove old output files
6. **Run the workflow** following `prompts/orchestrator.md`

### Template Mapping

The workflow automatically maps documents to templates:

| Document Pattern | Template |
|------------------|----------|
| `*architecture*.md` | `architecture-tmpl.yaml` or `fullstack-architecture-tmpl.yaml` |
| `*prd*.md` | `prd-tmpl.yaml` |
| `*front-end-spec*.md` | `front-end-spec-tmpl.yaml` |
| `*project-brief*.md` | `project-brief-tmpl.yaml` |

---

## Status

- **Current Phase**: See `progress.md`
- **Last Updated**: See `progress.md`
