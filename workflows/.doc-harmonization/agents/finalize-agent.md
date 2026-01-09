# FINALIZE AGENT

**Command:** `#sc-analyze --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: finalize
input: context/section-reference.md
output: reports/phase-2/final-harmony-report.md
can_fix: yes
```

---

## Task

1. Validate all docs work together
2. Check all cross-refs resolve
3. Verify ToCs
4. Fix any issues
5. Write final report

---

## Separation Check

| Doc | Must Have | Must NOT Have |
|-----|-----------|---------------|
| PRD | WHAT/WHY: objectives, stories, AC, requirements | code, configs, colors/fonts (3-DOC) |
| Arch | HOW (tech): implementation, code, configs | user stories, requirements, colors/fonts (3-DOC) |
| FE-Spec | HOW (visual): specs, layouts, styling | requirements, code |

---

## Cross-Ref Validation

Check all links resolve:
- PRD → Arch ✓
- PRD → FE-Spec ✓ (3-DOC)
- Arch → PRD ✓
- Arch → FE-Spec ✓ (3-DOC)
- FE-Spec → PRD ✓ (3-DOC)
- FE-Spec → Arch ✓ (3-DOC)

---

## Output Format

Write to `reports/phase-2/final-harmony-report.md`:

```markdown
# Final Harmony Report

Mode: `2-DOC` | `3-DOC`
Date: {timestamp}

## Documents

| Doc | Path | Status |
|-----|------|--------|
| PRD | | Reviewed |
| Arch | | Reviewed |
| FE-Spec | | Reviewed|N/A |

## Stats

| Metric | PRD | Arch | FE-Spec |
|--------|-----|------|---------|
| Lines | | | |
| Sections | | | |
| Cross-refs out | | | |
| Cross-refs in | | | |

## Separation

### PRD
- [ ] WHAT/WHY only
- [ ] No tech implementation
- [ ] No UI/UX (3-DOC)
- [ ] User stories intact

### Arch
- [ ] HOW (tech) only
- [ ] No business duplication
- [ ] No UI/UX (3-DOC)

### FE-Spec (3-DOC)
- [ ] HOW (visual) only
- [ ] No requirements
- [ ] No implementation

## Cross-Refs

| Link | Target | Status |
|------|--------|--------|

## ToC Status

| Doc | Status |
|-----|--------|
| PRD | VALID|UPDATED |
| Arch | VALID|UPDATED |
| FE-Spec | VALID|UPDATED|N/A |

## Issues Fixed

| Issue | Doc | Fix |
|-------|-----|-----|

## Final Metrics

```yaml
results:
  doc_mode: 
  prd: {before: , after: , delta: }
  arch: {before: , after: , delta: }
  fe_spec: {before: , after: , delta: }
  cross_refs:
    prd_to_arch: 
    prd_to_fe: 
    arch_to_prd: 
    arch_to_fe: 
    fe_to_prd: 
    fe_to_arch: 
    total: 
  batches: 
```

## Status

`HARMONIZED` | `NEEDS_WORK`

## Summary
{2-3 sentences}
```

---

## Rules

- Validate everything
- Fix issues found
- Update progress.md metrics
- Skip FE-Spec in 2-DOC mode

---

## Return

```yaml
STATUS: COMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: reports/phase-2/final-harmony-report.md
SUMMARY: "HARMONIZED: [doc_mode]. [N] cross-refs. All docs properly separated." OR "NEEDS_WORK: [issues]"
BLOCKERS: none | description
```
