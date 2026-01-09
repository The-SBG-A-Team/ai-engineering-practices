# ANALYZE AGENT

**Command:** `#sc-analyze --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: analyze
input: 
  - context/section-reference.md (doc mode, paths)
  - batch sections from source docs
output: context/analysis-output.md
```

---

## 3-Way Separation

| Doc | Contains | Answers |
|-----|----------|---------|
| PRD | WHAT + WHY | What should it do? Why? |
| Arch | HOW (tech) | How built? What tech? |
| FE-Spec | HOW (visual) | How looks? UX? |

2-DOC mode: UI/UX stays in PRD

---

## Classification

| Content | → Doc | Examples |
|---------|-------|----------|
| Requirements, user stories, AC, KPIs | PRD | "AC1: When validation fails..." |
| Code, configs, infra, API schemas | Arch | "Lambda timeout: 10s" |
| Colors, fonts, spacing, layouts | FE-Spec | "#2563EB", "16px padding" |

---

## Decision Examples

| Content | Goes To | Reason |
|---------|---------|--------|
| "System shall support 1000 concurrent users" | PRD | Business target |
| "Use connection pooling with max 100 connections" | Arch | Technical impl |
| "Primary button color: #2563EB" | FE-Spec | Visual design |
| "User can export data as CSV" | PRD | Feature requirement |
| "Use Papa Parse library for CSV generation" | Arch | Technical choice |
| "Export button positioned bottom-right" | FE-Spec | UI layout |

---

## Acceptable Overlap

Same content CAN exist in multiple docs when serving different purposes:

| PRD | Arch | FE-Spec |
|-----|------|---------|
| "What steps occur" | "How services communicate" | "How user navigates" |
| Business process | Sequence diagram | Screen-to-screen flow |

**Example - Payment Feature, 3 Views:**
- PRD: User submits → System validates → Payment processed
- Arch: PaymentController → ValidationService → PaymentGateway
- FE-Spec: Payment Form → Loading State → Success Modal

All valid. Different perspectives.

---

## Red Flags

| In PRD | Move to |
|--------|---------|
| Code blocks | Arch |
| Config (YAML/JSON) | Arch |
| `#XXXXXX` colors | FE-Spec |
| `16px`, `font-weight` | FE-Spec |

| In Arch | Move to |
|---------|---------|
| "As a user..." | PRD |
| "System shall..." | PRD |
| Colors, fonts | FE-Spec |

---

## Output Format

Write to `context/analysis-output.md`:

```markdown
# Analysis: Batch {ID}

Mode: `2-DOC` | `3-DOC`

## Sections

| Doc | Section | Lines | Status |
|-----|---------|-------|--------|
| PRD | | | Clean|Needs Work |
| Arch | | | |
| FE-Spec | | | |

## Move

| Content | From | To | Reason |
|---------|------|----|--------|

## Duplicates

| Content | Locations | Keep | Remove |
|---------|-----------|------|--------|

## Cross-Refs Needed

| From | To | Purpose |
|------|-----|---------|

## Summary

| Metric | Count |
|--------|-------|
| Items to move | |
| Duplicates | |
| Cross-refs | |

## Notes for Plan Agent
```

---

## Rules

- Analysis only, DO NOT edit docs
- Read doc mode first
- Include line numbers
- Preserve user stories in PRD
- Skip FE-Spec in 2-DOC mode
- Acceptable overlap is OK (different perspectives)

---

## Return

```yaml
STATUS: COMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: context/analysis-output.md
SUMMARY: "[N] items to move. [M] duplicates. [K] cross-refs needed."
BLOCKERS: none | description
```
