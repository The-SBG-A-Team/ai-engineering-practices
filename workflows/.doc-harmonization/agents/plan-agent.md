# PLAN AGENT

**Command:** `#sc-workflow --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: plan
input:
  - context/analysis-output.md
  - context/section-reference.md
output: context/plan-output.md
```

---

## Task

For each item in analysis, specify:
- WHAT to change
- WHERE (file:lines)
- HOW to change
- ORDER (dependencies)

---

## Cross-Ref Formats

```markdown
# PRD → Arch
> **Technical Details**: See [Section X.X](architecture.md#section) in Architecture.

# PRD → FE-Spec
> **UI/UX Design**: See [Section X.X](front-end-spec.md#section) in FE-Spec.

# Arch → PRD
> **Requirements**: See [Section X.X](prd.md#section) in PRD.

# Arch → FE-Spec
> **Visual Design**: See [Section X.X](front-end-spec.md#section) in FE-Spec.

# FE-Spec → PRD
> **Business Context**: See [Section X.X](prd.md#section) in PRD.

# FE-Spec → Arch
> **Implementation**: See [Section X.X](architecture.md#section) in Architecture.
```

---

## Output Format

Write to `context/plan-output.md`:

```markdown
# Plan: Batch {ID}

Mode: `2-DOC` | `3-DOC`

## Docs
- PRD: {path}
- Arch: {path}
- FE-Spec: {path | N/A}

## Steps

### Step 1: {ACTION}
- Action: MOVE | REMOVE | ADD_CROSSREF
- Source: {file}:{lines}
- Target: {file}:{location}
- Content:
```
{exact content}
```
- Cross-ref:
```
{exact cross-ref text}
```
- Depends: None | Step X

### Step 2: {ACTION}
...

## Order
1. Step X (no deps)
2. Step Y (after X)

## Validation
- [ ] No info lost
- [ ] Cross-refs correct format
- [ ] User stories preserved
```

---

## No Changes Template

```markdown
# Plan: Batch {ID}

Mode: `2-DOC` | `3-DOC`

## Status: NO_CHANGES

Sections properly separated.
```

---

## Rules

- Planning only, DO NOT edit docs
- Be precise: exact lines + content
- Use cross-ref formats above
- Specify execution order
- Skip FE-Spec in 2-DOC mode

---

## Return

```yaml
STATUS: COMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: context/plan-output.md
SUMMARY: "[N] steps planned. [M] moves. [K] cross-refs." OR "NO_CHANGES - sections properly separated."
BLOCKERS: none | description
```
