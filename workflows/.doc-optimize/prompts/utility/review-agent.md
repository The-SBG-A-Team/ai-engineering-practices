#sc-analyze --seq --uc --serena --morph --ultrathink

# Batch Review Agent

**Runs:** Once per batch (after all sections complete)

Read `shared.md` first.

---

## Task

Review all sections in your assigned batch. Validate changes, fix any issues, and ensure quality standards are met for the specific document type.

---

## Output

`reports/[DOC_TYPE]/batch-[N]/review.md`

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document, document type, batch info |
| `reports/[DOC_TYPE]/[section]/03-execute.md` | All execute reports in batch |
| Target document | Current document state |
| `reference/core.md` | Quality thresholds, document-type rules |
| `reference/sections-[DOC_TYPE].md` | Section configs |

---

## Document Context

The orchestrator provides document context:

```yaml
document: [filename]
type: [architecture | prd | front-end-spec | project-brief]
path: docs/[document]
batch: [N]
sections: [list of section numbers in batch]
```

---

## Process

### 1. Read All Execute Reports
- Read each report in the batch
- Understand what changes were made
- Note any flagged concerns

### 2. Review Each Section
For each section:
- Read section in target document
- Verify changes applied correctly
- Check reader completeness
- Verify document-type compliance
- Identify remaining issues

### 3. Fix Issues
- Fix problems directly
- Document all fixes

### 4. Write Review Report

---

## Review Checklist by Document Type

### All Documents
| Category | Checks |
|----------|--------|
| Reader Completeness | Self-contained, no scattered content, standalone viable |
| Content Quality | Duplicates removed, content moved correctly, no loss |
| Formatting | Heading hierarchy, consistency, flow |
| Diagrams | Render correctly, accurate, no syntax errors |
| Cross-References | Links work, refs updated, no broken anchors |

### Architecture Additional Checks
| Category | Checks |
|----------|--------|
| Code Streamlining | No implementations, reasonable length, file refs added |
| Technical Content | Diagrams present, schemas documented, API contracts clear |

### PRD Additional Checks
| Category | Checks |
|----------|--------|
| Requirements | All numbered (FR/NFR), acceptance criteria present |
| No Technical Details | Implementation details removed, cross-refs added |

### Front-End Spec Additional Checks
| Category | Checks |
|----------|--------|
| User Flows | Diagrams present, flows complete |
| Component Specs | Props, states, accessibility documented |
| No Backend Details | Backend code removed, cross-refs added |

### Project Brief Additional Checks
| Category | Checks |
|----------|--------|
| High-Level Focus | No implementation details |
| Metrics | Success metrics clearly defined |
| Scope | MVP scope clearly bounded |

---

## Report Format

```markdown
# Batch [N] Review Report - [Document Type]

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Document Type | [type] |
| Path | docs/[document] |

## Batch Overview

| Section | Name | Execute Report | Status |
|---------|------|----------------|--------|
| [N] | [name] | [path] | ACTIVE/ABSORBED |

---

## Summary

[2-3 sentence assessment]

**Overall Status**: ALL_PASS | ISSUES_FIXED | ISSUES_REMAINING

---

## Section [N]: [Name] Review

### Status
- **Outcome**: ACTIVE | ABSORBED | MERGED
- **Location**: [Heading]
- **Quality**: PASS | ISSUES_FIXED

### Reader Completeness
- [ ] Self-contained
- [ ] No scattered content
- [ ] Standalone viable

### Document-Type Compliance
- [ ] [Check 1 for this doc type]
- [ ] [Check 2 for this doc type]
- [ ] [Check 3 for this doc type]

### Issues Found
| # | Issue | Severity | Location |
|---|-------|----------|----------|
| 1 | [desc] | HIGH/MED/LOW | ~X-Y |

### Fixes Applied
| # | Issue | Fix | Lines |
|---|-------|-----|-------|
| 1 | [issue] | [fix] | ~X-Y |

### Remaining Concerns
- [Issues not fixed and why]

---

[Repeat for each section]

---

## Cross-Section Issues

| Issue | Affects | Fix Applied |
|-------|---------|-------------|
| [desc] | [sections] | [fix] |

---

## Verification Summary

| Check | Sec [N] | Sec [N+1] | Sec [N+2] |
|-------|---------|-----------|-----------|
| Reader complete | ✓/✗ | ✓/✗ | ✓/✗ |
| No duplicates | ✓/✗ | ✓/✗ | ✓/✗ |
| Doc-type compliant | ✓/✗ | ✓/✗ | ✓/✗ |
| Formatting OK | ✓/✗ | ✓/✗ | ✓/✗ |
| Diagrams OK | ✓/✗ | ✓/✗ | ✓/✗ |

---

## Recommendations for Next Batch

- [Things to watch for]
- [Patterns established]

---

## Batch Status

**Status**: ALL_PASS | ISSUES_FIXED | NEEDS_USER_INPUT
**Approved for next batch**: YES | NO

---
*Review by Review Agent*
*Document: [DOCUMENT]*
*Document Type: [DOC_TYPE]*
*Timestamp: [YYYY-MM-DD HH:MM]*
```

---

## When to Fix vs Flag

| FIX Immediately | FLAG for User |
|-----------------|---------------|
| Missing content that should have been moved | Major structural changes needed |
| Duplicate content that should have been removed | Unclear requirements |
| Formatting inconsistencies | Content of uncertain value |
| Broken diagrams with obvious fixes | Significant accuracy concerns |
| Minor cross-reference issues | Issues requiring domain knowledge |
| Document-type violations with clear fixes | Ambiguous document-type boundaries |

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/batch-[N]/review.md
SUMMARY: "Reviewed [N] sections in batch [M] for [document] ([doc_type]). [X] issues found, [Y] fixed. Status: [ALL_PASS/ISSUES_FIXED/ISSUES_REMAINING]."
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
LOOP_CONTROL: EXIT | CONTINUE
EDITS_MADE: true | false
BLOCKERS: none | "description"
```
