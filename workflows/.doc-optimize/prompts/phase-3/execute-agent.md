#sc-implement --seq --uc --serena --morph --ultrathink

# Phase 3: Execute Plan

**Runs:** Once per section (after P2), may loop

Read `shared.md` first.

---

## Task

Read the Plan Agent's execution plan and implement all changes to the target document. Write a report of what was done.

---

## Output

`reports/[DOC_TYPE]/[section]/03-execute.md`

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document, document type, section info |
| `reports/[DOC_TYPE]/[section]/02-plan.md` | Execution plan (PRIMARY) |
| Target document | Document to edit |
| `reference/core.md` | Content rules by document type |
| `reference/patterns.md` | Replacement templates |

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

---

## Process

### 1. Read the Plan
- Open and read entire plan file
- Understand all steps before starting
- Note execution order
- Review document-type-specific guidance

### 2. Execute Each Step
- Follow steps in specified order
- Use edit tools efficiently
- Verify each step after completion
- Apply document-type rules

### 3. Verify Results
- Re-read modified section
- Check nothing valuable lost
- Verify formatting consistent
- Test diagrams render

### 4. Write Report

---

## Editing Guidelines

### Content Rules by Document Type

**Architecture:**
| Action | Guideline |
|--------|-----------|
| Preserve | Diagrams, schemas, API contracts, interfaces |
| Remove | Copy-pasted implementation code |
| Replace | Long code blocks → descriptions + file refs |
| Keep | Configuration examples (≤10 lines) |

**PRD:**
| Action | Guideline |
|--------|-----------|
| Preserve | Requirements (FR/NFR), user stories, acceptance criteria |
| Remove | Technical implementation details, code snippets |
| Replace | System design details → cross-ref to architecture.md |
| Keep | Decision records, scope boundaries |

**Front-End Spec:**
| Action | Guideline |
|--------|-----------|
| Preserve | User flows, component specs, design tokens |
| Remove | Backend implementation, API implementation code |
| Replace | Backend details → cross-ref to architecture.md |
| Keep | Accessibility requirements, wireframe refs |

**Project Brief:**
| Action | Guideline |
|--------|-----------|
| Preserve | Problem statement, success metrics, MVP scope |
| Remove | Technical details, implementation specifics |
| Replace | System design → cross-ref to architecture.md |
| Keep | Goals, constraints, user personas |

### Code Streamlining Template (Architecture Only)

**Before** (copy-pasted implementation):
```java
@Service
public class SomeService {
    // 50 lines of implementation
}
```

**After** (architectural description):
```markdown
**SomeService** handles [responsibility].

| Aspect | Details |
|--------|---------|
| Trigger | [what initiates it] |
| Input | [what it receives] |
| Output | [what it produces] |
| Key Logic | [brief description] |

**Implementation:** `app/path/to/SomeService.java`
```

### Cross-Document Reference Format

```markdown
> **See also:** [architecture.md - Section Name](#section-anchor) for technical details.
```

---

## Report Format

```markdown
# Section [N]: [NAME] - Execution Report

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Document Type | [type] |
| Path | docs/[document] |

## Plan Reference
**Source**: reports/[DOC_TYPE]/[section]/02-plan.md
**Plan Steps**: [X total]
**Executed**: [Y steps]

---

## Section Status

**Outcome**: ACTIVE | ABSORBED | MERGED | NOT_FOUND
**Final Location**: [Section heading]
**Final Line Range**: ~X-Y

---

## Execution Summary

| Step | Action | Status | Notes |
|------|--------|--------|-------|
| 1 | [action] | DONE | [notes] |
| 2 | [action] | DONE | [notes] |
| N | [action] | DONE/SKIPPED | [reason] |

---

## Changes Made

### Content Pulled In
| What | From | Lines Added |
|------|------|-------------|
| [desc] | [section] | +X |

### Content Moved Out
| What | To | Lines Removed |
|------|-----|---------------|
| [desc] | [section] | -X |

### Content Removed
| What | Reason | Lines |
|------|--------|-------|
| [desc] | [duplicate/fluff/wrong doc type] | -X |

### Code Streamlined (Architecture Only)
| Original | Replaced With | Lines Changed |
|----------|---------------|---------------|
| [class/method] | [desc + ref] | -X |

### Cross-Document References Added
| Content Removed | Reference Added |
|-----------------|-----------------|
| [content] | "See [section] in [doc]" |

### Formatting Fixes
| Issue | Location | Fix |
|-------|----------|-----|
| [issue] | ~X | [fix] |

---

## Verification Checklist

- [ ] All plan steps executed
- [ ] Scattered content consolidated
- [ ] No duplicate content remains
- [ ] Document-type rules followed
- [ ] Code blocks streamlined (architecture only)
- [ ] Technical details removed (prd/brief only)
- [ ] Cross-refs added where content moved
- [ ] Formatting consistent
- [ ] Diagrams render correctly
- [ ] Section is reader-complete

---

## Metrics

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Lines | X | Y | +/-Z% |
| Code blocks | X | Y | +/-Z |

---

## Deviations from Plan

| Step | Plan Said | I Did | Reason |
|------|-----------|-------|--------|
| [N] | [planned] | [actual] | [why] |

---

## Impact on Other Sections

**Sections affected**: [list]
**Cross-references updated**: [list]

---

## Recommendations for Review

- [Things to verify]
- [Potential issues]

---
*Execution by Execute Agent*
*Document: [DOCUMENT]*
*Document Type: [DOC_TYPE]*
*Timestamp: [YYYY-MM-DD HH:MM]*
```

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/[section]/03-execute.md
SUMMARY: "Executed [N] steps for [section] in [document] ([doc_type]). [X] lines removed, [Y] changes made."
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
LOOP_CONTROL: EXIT | CONTINUE
EDITS_MADE: true | false
BLOCKERS: none | "description"
```

---

## Notes

- Follow the plan - Plan Agent already thought through approach
- Execute in order - step order matters
- Verify as you go - check each change worked
- Document deviations - explain if you change from plan
- Don't over-edit - only make planned changes
- Apply document-type rules from `reference/core.md`
