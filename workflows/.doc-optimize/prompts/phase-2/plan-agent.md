#sc-task --seq --uc --ultrathink

# Phase 2: Execution Planning

**Runs:** Once per section (after P1)

Read `shared.md` first.

---

## Task

Read the Analyze Agent's report and create a detailed, step-by-step execution plan for the Execute Agent to follow.

---

## Output

`reports/[DOC_TYPE]/[section]/02-plan.md`

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document, document type, section info |
| `reports/[DOC_TYPE]/[section]/01-analysis.md` | Analysis findings (PRIMARY) |
| `reference/core.md` | Content rules by document type, thresholds |
| `reference/patterns.md` | Consolidation patterns |
| Target document | Current state for line references |

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

Use the document type to apply appropriate consolidation rules.

---

## Process

### 1. Read Analysis Report
- Load the analysis file completely
- Understand all findings before planning
- Note priorities and risks
- Review document-type compliance issues

### 2. Plan Each Change
For each issue found:
- Determine exact action
- Specify source and target locations
- Provide before/after examples where helpful
- Consider dependencies between changes
- Apply document-type-specific rules

### 3. Order Operations
- Group related changes
- Order to avoid conflicts
- Plan verification steps

### 4. Write Execution Plan

---

## Planning Guidelines

| Guideline | Why |
|-----------|-----|
| Be specific | Execute Agent follows exactly |
| Include line numbers | Precise locations |
| Order matters | Avoid conflicts |
| Plan verification | Catch errors early |
| Document-type rules | Different docs have different rules |

---

## Document-Type Considerations

**Architecture:**
- Plan code streamlining (replace implementations with descriptions + refs)
- Keep diagrams, schemas, API contracts
- Plan file references for implementations

**PRD:**
- Plan requirement numbering fixes
- Plan removal of technical details (add cross-refs to architecture)
- Ensure acceptance criteria are present

**Front-End Spec:**
- Keep user flows, component specs, design tokens
- Plan removal of backend details
- Ensure accessibility requirements present

**Project Brief:**
- Keep high-level, non-technical
- Plan removal of implementation details
- Ensure success metrics are present

---

## Report Format

```markdown
# Section [N]: [NAME] - Execution Plan

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Document Type | [type] |
| Template | [template_path] |

## Analysis Summary

**Source**: reports/[DOC_TYPE]/[section]/01-analysis.md
**Key Findings**:
- [Top finding 1]
- [Top finding 2]
- [Top finding 3]

**Section Status**: FOUND | ABSORBED | SCATTERED | NOT_FOUND
**Current Location**: [Heading], lines ~X-Y

---

## Document-Type Rules Applied

**Document Type**: [type]
**Key Rules**:
- [Rule 1 from core.md]
- [Rule 2 from core.md]

---

## Execution Steps

### Step 1: [Action Category]

**Action**: PULL_IN | MOVE_OUT | REMOVE | MERGE | STREAMLINE | FORMAT | FIX_DIAGRAM | ADD_CROSS_REF
**Description**: [What to do]

**Source**:
- Section: [section name]
- Lines: ~X-Y
- Content: [brief description]

**Target**:
- Section: [section name]
- Insert after: [line/heading reference]

**Verification**: [How to verify]

---

### Step 2: [Action Category]

[Same format]

---

## Code Streamlining Plan (Architecture Only)

### Code Block 1: [Name]

**Location**: Lines ~X-Y
**Current**: [What it contains]
**Replace With**:
```markdown
[Exact replacement text]
```

---

## Cross-Document References to Add

| Content Being Removed | Add Reference To | Reference Text |
|-----------------------|------------------|----------------|
| [content] | [target doc] | "See [section] in [doc]" |

---

## Content to Remove

| # | Location | Lines | Content | Reason |
|---|----------|-------|---------|--------|
| 1 | [section] | ~X-Y | [desc] | [duplicate/fluff/wrong doc type] |

---

## Content to Pull In

| # | From | Lines | To | Insert After |
|---|------|-------|----|--------------|
| 1 | [section] | ~X-Y | This section | [heading] |

---

## Sections to Merge (if applicable)

**Merge**: [Section X] INTO [Section Y]
**New Name**: [if renamed]
**Reason**: [why]

**Process**:
1. [Step]
2. [Step]
3. [Update refs]

---

## Execution Order

1. **First**: Steps [N] - [Why first]
2. **Second**: Steps [N] - [Dependencies]
3. **Finally**: Steps [N] - [Verification]

---

## Post-Execution Verification

- [ ] All scattered content consolidated
- [ ] No duplicate content remains
- [ ] Code blocks streamlined (architecture only)
- [ ] Technical details removed (prd/project-brief)
- [ ] Formatting consistent
- [ ] Diagrams render correctly
- [ ] Section is reader-complete
- [ ] Cross-document references added where needed

---

## Estimated Metrics

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Lines | ~X | ~Y | +/-Z% |

---

## Notes for Execute Agent

- [Special considerations]
- [Edge cases]
- [Areas requiring care]
- [Document-type-specific guidance]

---
*Plan by Plan Agent*
*Document: [DOCUMENT]*
*Document Type: [DOC_TYPE]*
*Timestamp: [YYYY-MM-DD HH:MM]*
```

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/[section]/02-plan.md
SUMMARY: "Created plan with [N] steps for [section] in [document] ([doc_type]). Key actions: [list top 2-3]."
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
BLOCKERS: none | "description"
```

---

## Notes

- Do NOT edit anything - planning only
- Read analysis first - your plan depends on it
- Be precise - Execute Agent follows exactly
- Consider order - some changes must happen before others
- Reference `reference/patterns.md` for replacement templates
- Apply document-type-specific rules from `reference/core.md`
