# Shared Agent Protocol

> All agents in this workflow inherit these rules.

---

## Placeholders

| Placeholder | Replace With | Example |
|-------------|--------------|---------|
| `[DOCUMENT]` | Document filename | "architecture.md" |
| `[DOC_TYPE]` | Document type slug | "architecture", "prd", "front-end-spec" |
| `[SECTION]` | Title Case section name | "Data Models" |
| `[section]` | lowercase-hyphenated | "data-models" |
| `[N]` | Section/topic number | "4" |
| `[BATCH]` | Batch number | "2" |
| `[V]` | Iteration version | "v2" |

---

## Document Types

| Type | Slug | Template | Target File |
|------|------|----------|-------------|
| Architecture | `architecture` | `architecture-tmpl.yaml` | `docs/architecture.md` |
| PRD | `prd` | `prd-tmpl.yaml` | `docs/prd.md` |
| Front-End Spec | `front-end-spec` | `front-end-spec-tmpl.yaml` | `docs/front-end-spec.md` |
| Project Brief | `project-brief` | `project-brief-tmpl.yaml` | `docs/project-brief.md` |

---

## Return Protocol

**CRITICAL**: After completing your task and writing your report, return ONLY this YAML block:

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/[section]/[filename].md
SUMMARY: "2-3 sentences max describing what was done"
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
LOOP_CONTROL: CONTINUE | EXIT  # Only for loop phases
EDITS_MADE: true | false       # Only for loop phases
BLOCKERS: none | "description of blocker"
```

**Rules:**
- MAX 150 words in return message
- Orchestrator sees ONLY this return - be precise
- CONFIDENCE < 70 triggers debugger
- BLOCKED status stops workflow for user input

---

## Status Definitions

| Status | Meaning | Orchestrator Action |
|--------|---------|---------------------|
| COMPLETE | Task finished successfully | Proceed to next |
| INCOMPLETE | Partial progress, can continue | May retry or proceed |
| BLOCKED | Cannot proceed without help | Stop, notify user |

---

## Confidence Guidelines

| Range | Meaning | When to Use |
|-------|---------|-------------|
| 95-100 | High confidence, verified | All checks pass, no concerns |
| 80-94 | Good confidence, minor uncertainty | Most checks pass, small gaps |
| 70-79 | Moderate confidence | Some concerns, may need review |
| <70 | Low confidence | Significant issues, triggers debugger |

---

## File Paths

| Type | Path Pattern |
|------|--------------|
| Setup report | `reports/[DOC_TYPE]/00-setup.md` |
| Section reports | `reports/[DOC_TYPE]/[section]/[phase]-[name].md` |
| Section state | `reports/[DOC_TYPE]/[section]/.state.json` |
| Batch reviews | `reports/[DOC_TYPE]/batch-[N]/review.md` |
| Final review | `reports/[DOC_TYPE]/final/review.md` |
| Section config | `reference/sections-[DOC_TYPE].md` |

---

## Report Naming Convention

| Phase | Filename Pattern | Example |
|-------|------------------|---------|
| P0 Setup | `00-setup.md` | `reports/architecture/00-setup.md` |
| P1 Analyze | `01-analysis.md` | `reports/prd/requirements/01-analysis.md` |
| P2 Plan | `02-plan.md` | `reports/prd/requirements/02-plan.md` |
| P3 Execute | `03-execute.md` | `reports/prd/requirements/03-execute.md` |
| Review | `review.md` | `reports/architecture/batch-1/review.md` |
| Loop iterations | `[phase]-[name]-v[N].md` | `03-execute-v2.md` |

---

## Verification Commands

Before returning COMPLETE, verify your work:

```bash
# Check report was written
ls -la reports/[DOC_TYPE]/[section]/[filename].md

# For execute agents - verify document was modified
git diff docs/[DOCUMENT] | head -50

# For diagram fixes - test Mermaid renders
# (Visual check in preview)
```

---

## Document-Type Guidelines

Always read the document-type-specific guidelines from `workflow.md` before making changes.

### Quick Reference

**Architecture:**
- Keep: Diagrams, schemas, API contracts, interfaces
- Remove: Implementation code, copy-pasted source

**PRD:**
- Keep: Requirements (FR/NFR), user stories, acceptance criteria
- Remove: Technical implementation, code snippets

**Front-End Spec:**
- Keep: User flows, component specs, design tokens
- Remove: Backend code, API implementations

**Project Brief:**
- Keep: Problem statement, success metrics, scope
- Remove: Technical details, implementation specifics

---

## Content Tiers

When reading/writing reports, prioritize:

| Tier | Include | Skip |
|------|---------|------|
| MUST | Requirements, gaps with file:line, interfaces | - |
| INCLUDE | Patterns (ref patterns.md), non-standard logic | Standard patterns |
| REFERENCE | Working code, spec line ranges | Full code blocks |

---

## Context Efficiency Rules

1. **Tables over prose** - AI compresses prose, preserves tables
2. **YAML for structured data** - Machine-parseable
3. **Reference by location** - "See lines 245-280" not full content
4. **Progressive loading** - Scan headers first, deep dive only where needed

---

## Error Handling

If you encounter an error:

1. **Document the error** in your report
2. **Set STATUS: BLOCKED** if cannot proceed
3. **Describe the blocker** clearly in BLOCKERS field
4. **Do NOT guess** - ask for help via BLOCKED status

---

## Cross-Agent Communication

- Agents communicate via **files only** (reports, state)
- Never assume previous agent's context
- Always read required input files fresh
- Trust but verify previous agent's work
- Include DOCUMENT and DOCUMENT_TYPE in all returns
