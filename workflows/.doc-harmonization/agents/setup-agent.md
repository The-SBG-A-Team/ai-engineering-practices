# SETUP AGENT

**Command:** `#sc-analyze --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: setup
input: progress.md config
output: context/section-reference.md
updates: progress.md metrics
```

---

## Task

1. Read doc paths from progress.md Config section
2. **Detect mode:**
   - If `fe_spec: null` or file doesn't exist → **2-DOC mode**
   - If `fe_spec: <path>` and file exists → **3-DOC mode**
3. Analyze each doc's sections (## headers, line ranges)
4. Map topics across docs (skip FE-Spec mapping in 2-DOC)
5. Generate batches (adjust for doc count)
6. Write context/section-reference.md
7. Update progress.md metrics (set `doc_mode: 2-DOC | 3-DOC`)

### Mode Detection Logic

```yaml
# Read from progress.md
fe_spec_config: progress.md → project.fe_spec

if: fe_spec_config == null OR fe_spec_config == "null"
then: doc_mode = "2-DOC"

if: fe_spec_config is a path
then:
  if: file exists at that path
  then: doc_mode = "3-DOC"
  else: doc_mode = "2-DOC" (warn: configured but missing)
```

---

## Output Format

Write to `context/section-reference.md`:

```markdown
# Section Reference

## Mode
`2-DOC` | `3-DOC`

## Documents

| Doc | Path | Lines | Sections |
|-----|------|-------|----------|
| PRD | | | |
| Arch | | | |
| FE-Spec | | | |

## PRD Sections

| # | Name | Lines | Domain | Type |
|---|------|-------|--------|------|

## Arch Sections

| # | Name | Lines | Domain | Type |
|---|------|-------|--------|------|

## FE-Spec Sections (if 3-DOC)

| # | Name | Lines | Domain | Type |
|---|------|-------|--------|------|

## Topic Map

| Domain | PRD | Arch | FE-Spec |
|--------|-----|------|---------|

## Batches

### Batch 1: {Name}
- PRD: {sections}
- Arch: {sections}
- FE-Spec: {sections | N/A}
- Load: Light|Medium|Heavy

### Batch 2: {Name}
...

## Analysis Notes

### Content Requiring Movement
- {sections with mixed content needing harmonization}

### Potential Challenges
- {complex areas or dependencies}
```

---

## Domain Examples

Foundation, Requirements, Data/Models, API, Workflows, UI/UX, Security, Performance, Deployment

---

## Content Types

| Type | Indicators |
|------|------------|
| Business | user stories, AC, KPIs, compliance |
| Technical | code, configs, infra, API specs |
| UI/UX | colors, fonts, spacing, layouts |
| Mixed | multiple types → needs work |

---

## Output Checklist

Before completing:
- [ ] Doc paths from progress.md verified to exist
- [ ] Doc mode determined (2-DOC or 3-DOC)
- [ ] All docs analyzed
- [ ] Sections identified with line numbers
- [ ] Topic domains mapped
- [ ] Batching recommendations provided
- [ ] section-reference.md written
- [ ] progress.md metrics updated

---

## Rules

- DO NOT edit source docs
- Detect mode from FE-Spec existence
- Capture all sections with line numbers
- Map cross-doc relationships
- ADAPT TO PROJECT - section names vary by project

---

## Return

```yaml
STATUS: COMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: context/section-reference.md
SUMMARY: "[mode] detected. [N] sections mapped. [M] batches recommended."
BLOCKERS: none | description
```
