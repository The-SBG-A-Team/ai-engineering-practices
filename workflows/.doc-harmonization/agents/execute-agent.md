# EXECUTE AGENT

**Command:** `#sc-implement --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: execute
input:
  - context/plan-output.md
  - context/section-reference.md
output: context/execution-log.md
modifies: source docs per plan
```

---

## Task

1. Read plan step-by-step
2. For each step:
   - Read current content
   - Make change
   - Verify success
   - Log change
3. Next step

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

## Move Protocol

1. Copy to target first
2. Verify in target
3. Remove from source
4. Add cross-ref in source
5. Log all

---

## Output Format

Write to `context/execution-log.md`:

```markdown
# Execution: Batch {ID}

Mode: `2-DOC` | `3-DOC`
Time: {timestamp}

## Modified
- PRD: YES|NO
- Arch: YES|NO
- FE-Spec: YES|NO|N/A

## Changes

### Change 1: {ACTION}
- Status: SUCCESS | FAILED
- File: {path}
- Lines: {old} → {new}
- Before:
```
{snippet}
```
- After:
```
{snippet}
```

### Change 2: {ACTION}
...

## Summary

| Metric | Count |
|--------|-------|
| Steps | |
| Success | |
| Failed | |

## Cross-Refs Added

| In | Points To | Line |
|----|-----------|------|

## Content Moved

| Content | From | To |
|---------|------|-----|

## Issues
{any problems}

## Notes for Review
{what to verify}
```

---

## No Changes Template

```markdown
# Execution: Batch {ID}

Mode: `2-DOC` | `3-DOC`
Time: {timestamp}

## Status: NO_CHANGES

Plan indicated sections already separated.
```

---

## Rules

- Follow plan exactly
- Log everything
- Stop on error
- Never delete without move/cross-ref
- Use cross-ref formats above
- Skip FE-Spec in 2-DOC mode

---

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: context/execution-log.md
SUMMARY: "[N] steps executed. [M] success. [K] failed." OR "NO_CHANGES - plan indicated no work needed."
BLOCKERS: none | description
```
