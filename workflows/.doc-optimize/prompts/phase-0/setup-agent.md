#sc-analyze --seq --uc --serena --ultrathink

# Phase 0: Document Setup & Section Discovery

**Runs:** Once per document at workflow start

Read `shared.md` first.

---

## Task

Analyze the target document to discover its structure, identify sections/topics for consolidation, and generate the workflow configuration. Use the corresponding template as a reference for expected structure.

---

## Output

`reports/[DOC_TYPE]/00-setup.md` - Setup report with discovered sections
`reference/sections-[DOC_TYPE].md` - Generated section configuration (for orchestrator)

---

## Inputs

| Source | What to Extract |
|--------|-----------------|
| Orchestrator spawn context | Document path, document type, template path |
| Template file | Expected section structure, content guidelines |
| Target document | Actual section structure, headings, line counts |
| `workflow.md` | Document-type-specific guidelines |

---

## Document Type Context

The orchestrator provides document context:

```yaml
document: [filename]
type: [architecture | prd | front-end-spec | project-brief]
path: docs/[document]
template: .bmad-core/templates/[template-name].yaml
```

Use the document type to apply appropriate consolidation rules from `workflow.md`.

---

## Process

### 1. Read Document Context
- Note document type from orchestrator context
- Read corresponding template for expected structure
- Read document-type guidelines from `workflow.md`

### 2. Analyze Template Structure
- Read the template file (YAML format)
- Extract expected sections from template
- Note required vs optional sections
- Understand content expectations per section

### 3. Analyze Document Structure
- Read the target document
- Extract all top-level sections (## headings)
- For each section, note:
  - Heading text
  - Line range (start-end)
  - Approximate line count
  - Key subsections (### headings)
  - Presence of code blocks, diagrams, tables

### 4. Compare to Template
- Map discovered sections to template sections
- Identify missing expected sections
- Identify unexpected/extra sections
- Note structural deviations

### 5. Identify Section Topics
For each section, determine:
- Primary topic/domain
- Search terms that would find related content
- Potential overlap with other sections
- Complexity estimate (HIGH/MEDIUM/LOW)
- Document-type-specific issues (see workflow.md)

### 6. Apply Document-Type Guidelines

**For Architecture:**
- Flag large code blocks for streamlining
- Check for copy-pasted implementations
- Verify diagram presence

**For PRD:**
- Verify requirement numbering (FR/NFR)
- Check for implementation details to remove
- Ensure acceptance criteria presence

**For Front-End Spec:**
- Check for user flow diagrams
- Verify component specifications
- Flag backend implementation details

**For Project Brief:**
- Verify problem statement clarity
- Check success metrics presence
- Flag technical over-specification

### 7. Propose Batching Strategy
Group sections into batches based on:
- Related topics (process together)
- Dependencies (dependent sections in same/later batch)
- Complexity balance (mix HIGH and LOW)
- Batch size: 3-4 sections per batch

### 8. Generate Configuration
Create `reference/sections-[DOC_TYPE].md` with discovered sections

### 9. Write Setup Report
Document findings for user review

---

## Report Format

```markdown
# Setup Report: [Document Name]

## Document Context

| Attribute | Value |
|-----------|-------|
| Document | [filename] |
| Type | [document_type] |
| Path | docs/[document] |
| Template | .bmad-core/templates/[template] |

## Document Overview

| Metric | Value |
|--------|-------|
| Total Lines | [N] |
| Sections Found | [N] |
| Expected Sections (Template) | [N] |
| Code Blocks | [N] |
| Diagrams | [N] |
| Tables | [N] |

## Template Comparison

| Template Section | Found | Document Section | Notes |
|------------------|-------|------------------|-------|
| [expected] | ✓/✗ | [actual or -] | [alignment notes] |

## Discovered Sections

| # | Section Name | Lines | Range | Complexity | Key Topics |
|---|--------------|-------|-------|------------|------------|
| 1 | [name] | [N] | [X-Y] | HIGH/MED/LOW | [topics] |
| 2 | [name] | [N] | [X-Y] | HIGH/MED/LOW | [topics] |
...

## Section Details

### Section 1: [Name]

**Line Range:** [X-Y] (~[N] lines)
**Complexity:** [HIGH/MEDIUM/LOW]
**Template Mapping:** [corresponding template section or "Not in template"]

**Subsections:**
- [subsection 1]
- [subsection 2]

**Key Topics:** [topic1], [topic2]
**Search Terms:** "[term1]", "[term2]", "[term3]"

**Content Types:**
- [ ] Code blocks: [N]
- [ ] Mermaid diagrams: [N]
- [ ] Tables: [N]
- [ ] External references: [N]

**Document-Type Issues:**
- [ ] [Issue specific to document type, e.g., "Large code block (50 lines) - needs streamlining"]

**Potential Overlaps:** [other sections that may overlap]

---

[Repeat for each section]

---

## Document-Type Analysis

### [Document Type] Specific Findings

**Content to Keep:**
- [List based on document type guidelines]

**Content to Remove:**
- [List based on document type guidelines]

**Content to Relocate:**
- [Content that belongs in different document]

## Proposed Batching

| Batch | Sections | Rationale |
|-------|----------|-----------|
| 1 | [1, 2, 3] | Foundation sections |
| 2 | [4, 5, 6] | [rationale] |
...

## Merge Candidates

| Section A | Section B | Overlap | Recommendation |
|-----------|-----------|---------|----------------|
| [name] | [name] | ~[X]% | Consider merging |

## Recommendations

1. [Key recommendation 1]
2. [Key recommendation 2]
3. [Key recommendation 3]

## User Confirmation Required

Before proceeding, please confirm:
- [ ] Section list is complete
- [ ] Batching strategy is acceptable
- [ ] Any sections to skip/prioritize
- [ ] Document-type-specific issues acknowledged

---
*Setup completed by Setup Agent*
*Document Type: [DOC_TYPE]*
*Timestamp: [YYYY-MM-DD HH:MM]*
```

---

## sections-[DOC_TYPE].md Format

Generate `reference/sections-[DOC_TYPE].md`:

```markdown
# Section Configuration: [Document Type]

> Auto-generated by Phase 0 Setup Agent
> Modify as needed before proceeding

---

## Document Context

```yaml
document: "[filename]"
document_type: "[type]"
document_path: "docs/[document]"
template_path: ".bmad-core/templates/[template].yaml"
```

---

## Sections

### Section 1: [Name]
```yaml
number: 1
name: "[Section Name]"
slug: "[section-slug]"
line_range: [X, Y]
complexity: HIGH | MEDIUM | LOW
search_terms: ["term1", "term2", "term3"]
batch: 1
template_section: "[corresponding template section or null]"
dependencies: []  # Section numbers this depends on
merge_candidates: []  # Sections that may overlap
expected_issues:
  - code_streamlining: HIGH | MEDIUM | LOW  # Expected amount of code to streamline
  - scattered_content: HIGH | MEDIUM | LOW  # Expected scattered content
  - doc_type_specific: "[specific issue for this document type]"
guidance: |
  [Section-specific hints based on document type, e.g.:]
  - Keep high-level, move technical details elsewhere
  - Watch for overlap with [other section]
  - High code streamlining expected - remove implementations
  - Should be definitive source for [topic]
notes: "[any special considerations]"
```

### Section 2: [Name]
```yaml
number: 2
name: "[Section Name]"
slug: "[section-slug]"
...
```

[Repeat for all sections]

---

## Batches

```yaml
batch_1:
  name: "Foundation"
  sections: [1, 2, 3]

batch_2:
  name: "[Name]"
  sections: [4, 5, 6]

...
```

---

## Merge Candidates

```yaml
merge_candidates:
  - sections: [X, Y]
    overlap: "~Z%"
    recommendation: "merge X into Y"
```
```

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[DOC_TYPE]/00-setup.md
SUMMARY: "Discovered [N] sections in [document] ([doc_type]). Proposed [M] batches. Generated reference/sections-[DOC_TYPE].md. Awaiting user confirmation."
BLOCKERS: none | "description"
DOCUMENT: [document_name]
DOCUMENT_TYPE: [doc_type]
SECTIONS_FOUND: [N]
BATCHES_PROPOSED: [M]
AWAITING_CONFIRMATION: true
```

---

## Notes

- This phase runs ONCE per document at workflow start
- User MUST confirm sections before Phase 1 begins
- If document structure is unclear, set STATUS: BLOCKED
- Generate sections file even if uncertain - user can modify
- Use template as guide, not strict requirement
- Different document types have different consolidation rules - follow workflow.md
