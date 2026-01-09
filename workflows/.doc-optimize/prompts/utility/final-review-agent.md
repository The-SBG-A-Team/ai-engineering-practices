#sc-analyze --seq --uc --serena --morph --ultrathink

# Final Review Agent

**Runs:** Once per document (after all batches complete)

Read `shared.md` first.

---

## Task

Perform comprehensive document-wide review after all batches complete. Ensure document is ready for use and meets all quality standards for its document type.

---

## Output

`reports/[DOC_TYPE]/final/review.md`

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document, document type |
| `reports/[DOC_TYPE]/batch-[N]/review.md` | All batch review reports |
| Target document | Final document state |
| `reference/core.md` | Quality standards by document type |
| `reference/sections-[DOC_TYPE].md` | Original section configs |

---

## Document Context

The orchestrator provides document context:

```yaml
document: [filename]
type: [architecture | prd | front-end-spec | project-brief]
path: docs/[document]
batches_completed: [N]
```

---

## Process

### 1. Read All Batch Reviews
- Understand what was done across all batches
- Note any flagged concerns

### 2. Document Structure Assessment
- Map final section structure
- Note merged/absorbed sections
- Verify logical ordering
- Check section numbering

### 3. Table of Contents
- Regenerate ToC to match current structure
- Update section numbers
- Fix anchor links

### 4. Reader Completeness (PRIMARY)
For EACH section verify:
- Self-contained
- No critical details scattered
- Could be extracted as standalone

### 5. Document-Type Compliance
Verify document follows its type-specific rules:

**Architecture:**
- [ ] No implementation code (only contracts, diagrams, schemas)
- [ ] File references for implementations
- [ ] Diagrams present for key flows

**PRD:**
- [ ] All requirements numbered (FR/NFR)
- [ ] Acceptance criteria present
- [ ] No technical implementation details
- [ ] Cross-refs to architecture for technical details

**Front-End Spec:**
- [ ] User flows documented
- [ ] Component specs complete
- [ ] Accessibility requirements present
- [ ] No backend implementation details

**Project Brief:**
- [ ] Problem statement clear
- [ ] Success metrics defined
- [ ] MVP scope bounded
- [ ] No implementation details

### 6. Cross-References
- Fix broken internal links
- Update refs to merged sections
- Verify anchor links
- Check cross-document references

### 7. Final Quality Check
- No remaining duplicates
- No remaining fluff
- Document-type rules followed
- Consistent formatting
- All diagrams render

---

## Report Format

```markdown
# Final Review Report: [Document Name]

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Document Type | [type] |
| Path | docs/[document] |
| Template | [template_path] |

## Summary

[Overall quality assessment - 2-3 sentences]

**Final Status**: APPROVED | NEEDS_WORK

---

## Document Statistics

| Metric | Original | Final | Change |
|--------|----------|-------|--------|
| Total Lines | ~X | Y | +/-Z% |
| Sections | X | Y | [merged/split] |
| Code Blocks | X | Y | +/-Z |
| Diagrams | X | Y | +/-Z |

---

## Final Document Structure

| # | Section Name | Original Sections | Status |
|---|--------------|-------------------|--------|
| 1 | [name] | Section [N] | PASS |
| 2 | [name] | Sections [N, M] (merged) | PASS |

---

## Section Outcome Summary

| Section | Name | Outcome | Final Location |
|---------|------|---------|----------------|
| 1 | [name] | ACTIVE | Section 1 |
| 4 | [name] | MERGED | Section 3 |

---

## Reader Completeness Assessment

| Section | Self-Contained | Standalone-Ready | Notes |
|---------|----------------|------------------|-------|
| [name] | YES/NO | YES/NO | [notes] |

---

## Document-Type Compliance

### [Document Type] Requirements

| Requirement | Status | Notes |
|-------------|--------|-------|
| [Req 1 for this doc type] | ✓/✗ | [notes] |
| [Req 2 for this doc type] | ✓/✗ | [notes] |
| [Req 3 for this doc type] | ✓/✗ | [notes] |

### Content Appropriateness

| Check | Status |
|-------|--------|
| Contains only appropriate content for [doc_type] | ✓/✗ |
| Inappropriate content removed/relocated | ✓/✗ |
| Cross-refs added for relocated content | ✓/✗ |

---

## Table of Contents

**Action**: Regenerated | Updated | No changes
**Changes**: [list]

---

## Cross-References

**Broken Links Found**: [N]
**Links Fixed**: [N]

| Original | Updated To | Reason |
|----------|------------|--------|
| [old] | [new] | [merged/renamed] |

### Cross-Document References

| From Section | Reference | To Document | Status |
|--------------|-----------|-------------|--------|
| [section] | [ref text] | [doc] | VALID/FIXED |

---

## Technical Accuracy (Architecture Only)

| Area | Status | Issues | Fixed |
|------|--------|--------|-------|
| Function names | ✓/✗ | [list] | YES/NO |
| Entity names | ✓/✗ | [list] | YES/NO |
| API specs | ✓/✗ | [list] | YES/NO |

---

## Final Quality Check

| Check | Status | Notes |
|-------|--------|-------|
| No duplicates | ✓/✗ | |
| No fluff | ✓/✗ | |
| Document-type compliant | ✓/✗ | |
| Consistent formatting | ✓/✗ | |
| All diagrams render | ✓/✗ | |

---

## Changes Made

### Structure Changes
| Change | Details |
|--------|---------|
| [type] | [desc] |

### Content Fixes
| Section | Fix |
|---------|-----|
| [name] | [fix] |

---

## Validation Results

- [ ] All sections reader-complete
- [ ] Document-type rules followed
- [ ] Technical accuracy verified (architecture only)
- [ ] All diagrams render
- [ ] ToC accurate
- [ ] All cross-refs valid
- [ ] Formatting consistent

---

## Outstanding Issues (if any)

| Issue | Severity | Recommendation |
|-------|----------|----------------|
| [desc] | HIGH/MED/LOW | [action] |

---

## Recommendations

### For Future Maintenance
- [Areas to watch]
- [Patterns established]

### Cross-Document Considerations
- [How this doc relates to others]
- [Dependencies on other docs]

---

## Final Status

**Status**: APPROVED | NEEDS_WORK
**Sign-off**: Final Review Agent
**Document**: [DOCUMENT]
**Document Type**: [DOC_TYPE]
**Timestamp**: [YYYY-MM-DD HH:MM]

---
*Final review by Final Review Agent*
```

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/final/review.md
SUMMARY: "Final review complete for [document] ([doc_type]). Document [APPROVED/NEEDS_WORK]. [X] sections, [Y] lines. [Z] issues fixed."
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
DOCUMENT_STATUS: APPROVED | NEEDS_WORK
BLOCKERS: none | "description"
```
