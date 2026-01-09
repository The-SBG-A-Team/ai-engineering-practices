#sc-analyze --seq --uc --serena --ultrathink

# Phase 1: Section Analysis

**Runs:** Once per section

Read `shared.md` first.

---

## Task

Analyze your assigned section in the target document and identify all issues that need to be addressed. Write a comprehensive analysis report for the Plan Agent.

---

## Output

`reports/[DOC_TYPE]/[section]/01-analysis.md`

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document, document type, section config |
| `reference/sections-[DOC_TYPE].md` | Section config (name, line range, search terms) |
| Target document | Section content and related content elsewhere |
| `reference/core.md` | Quality thresholds, content rules by document type |
| `reference/patterns.md` | Consolidation patterns |

---

## Document Context

The orchestrator provides document context:

```yaml
document: [filename]
type: [architecture | prd | front-end-spec | project-brief]
path: docs/[document]
section: [section_name]
section_number: [N]
```

Use the document type to apply appropriate content rules from `reference/core.md`.

---

## Process

### 1. Load Section Config
- Read your section's config from `reference/sections-[DOC_TYPE].md`
- Note: name, line range, search terms, complexity
- Review document-type-specific guidance in section config

### 2. Locate Your Section
- Find section in target document using config
- Determine status:
  - **FOUND**: Section exists as its own section
  - **ABSORBED**: Section was merged into another
  - **SCATTERED**: Content spread across multiple places
  - **NOT_FOUND**: Section doesn't exist

### 3. Apply Document-Type Rules
Read `reference/core.md` and apply rules for your document type:

**Architecture:**
- Flag implementation code (>10 lines) for streamlining
- Check for copy-pasted source code
- Verify diagrams, schemas, API contracts are present

**PRD:**
- Check requirement numbering (FR/NFR)
- Flag technical implementation details for removal
- Verify acceptance criteria presence

**Front-End Spec:**
- Check for user flow diagrams
- Verify component specifications
- Flag backend implementation details

**Project Brief:**
- Verify problem statement clarity
- Check success metrics presence
- Flag technical over-specification

### 4. Analyze (Scan ENTIRE Document)

Identify:

| Issue Type | What to Find |
|------------|--------------|
| Scattered Content | YOUR topic content in OTHER sections |
| Reader Completeness | Gaps that break reader understanding |
| Duplicates | Same info in multiple places |
| Placement Issues | Content in wrong section |
| Merge Opportunities | Overlapping sections |
| Fluff & Bloat | Unnecessary verbosity |
| Doc-Type Violations | Content that doesn't belong in this document type |
| Implementation Code | Copy-pasted source code (>10 lines) - Architecture only |
| Missing Requirements | FR/NFR without numbers - PRD only |
| Formatting Issues | Inconsistent formatting |
| Diagram Issues | Mermaid syntax errors, inaccuracies |

### 5. Write Analysis Report

---

## Report Format

```markdown
# Section [N]: [NAME] - Analysis Report

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Document Type | [type] |
| Template | [template_path] |

## Section Status
- **Location Status**: FOUND | ABSORBED | SCATTERED | NOT_FOUND
- **Current Heading**: [Section heading]
- **Line Range**: ~[X-Y]
- **Complexity**: [from config]

## Reader Completeness Assessment
- [ ] Section is self-contained
- [ ] No critical details scattered elsewhere
- [ ] Could stand alone as separate document

**Gaps Identified**: [List any completeness issues]

## Document-Type Compliance

### Content That Should Stay
| Content | Location | Compliant | Notes |
|---------|----------|-----------|-------|
| [desc] | ~X-Y | YES/NO | [notes] |

### Content That Should Move/Be Removed
| Content | Location | Issue | Recommendation |
|---------|----------|-------|----------------|
| [desc] | ~X-Y | [wrong doc type/too technical/etc] | Move to [doc] / Remove |

## Scattered Content Found

| Content | Found In | Lines | Should Be In |
|---------|----------|-------|--------------|
| [desc] | [section] | ~X-Y | This section / Other |

## Duplicate Content Found

| Content | Location 1 | Location 2 | Action |
|---------|------------|------------|--------|
| [desc] | [section] ~X-Y | [section] ~X-Y | Keep in X / Merge |

## Content Placement Issues

### Should Move Out
| Content | Currently In | Move To | Reason |
|---------|--------------|---------|--------|
| [desc] | This section | [other] | [why] |

### Should Pull In
| Content | Currently In | Pull To | Reason |
|---------|--------------|---------|--------|
| [desc] | [other] | This section | [why] |

## Merge Opportunities

| Section | Overlap | Recommendation |
|---------|---------|----------------|
| [name] | ~X% | Merge into this / Merge this into X |

## Fluff & Bloat

| Location | Lines | Issue | Action |
|----------|-------|-------|--------|
| [subsection] | ~X-Y | [desc] | Remove / Condense |

## Implementation Code to Streamline (Architecture Only)

| Code Block | Lines | Type | Action |
|------------|-------|------|--------|
| [desc] | ~X-Y | [class/method] | Replace with desc + file ref |

## Requirements Issues (PRD Only)

| Issue | Location | Details |
|-------|----------|---------|
| Missing FR number | ~X-Y | [requirement text] |
| Missing acceptance criteria | ~X-Y | [requirement] |

## Formatting Issues

| Issue | Location | Lines |
|-------|----------|-------|
| [desc] | [subsection] | ~X-Y |

## Diagram Issues

| Diagram | Location | Issue |
|---------|----------|-------|
| [name] | ~X-Y | [syntax error / inaccuracy] |

## Summary for Plan Agent

**Key Actions Needed**:
1. [Most important]
2. [Second priority]
3. [etc.]

**Estimated Impact**:
- Lines before: ~X
- Expected after: ~Y
- Change: [+/-]%

**Risks/Considerations**:
- [Any concerns]

---
*Analysis by Analyze Agent*
*Document: [DOCUMENT]*
*Document Type: [DOC_TYPE]*
*Timestamp: [YYYY-MM-DD HH:MM]*
```

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/[section]/01-analysis.md
SUMMARY: "Analyzed [section] in [document] ([doc_type]). Found [N] issues: [X] scattered content, [Y] duplicates, [Z] doc-type violations."
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
BLOCKERS: none | "description"
```

---

## Reference Documents

| Document | What to Use |
|----------|-------------|
| `reference/sections-[DOC_TYPE].md` | Your section config, merge candidates |
| `reference/core.md` | Restructuring rules, content guidelines by doc type |
| `reference/patterns.md` | Consolidation patterns |
| Target document | The document you're analyzing |
| `app/` | Source code for validation (architecture only) |
| Previous reports | Check what earlier agents found |

---

## Before You Start

1. Read your section config from `reference/sections-[DOC_TYPE].md`
2. Note merge candidates and dependencies
3. Check complexity rating - expect more issues for HIGH
4. Review document-type rules in `reference/core.md`
5. Apply document-type-specific analysis

---

## Notes

- Do NOT edit anything - analysis only
- Be specific - include line numbers and section names
- Scan ENTIRE document, not just your section
- Focus on reader experience
- Apply document-type-specific rules
- Be actionable - findings must be usable by Plan Agent
