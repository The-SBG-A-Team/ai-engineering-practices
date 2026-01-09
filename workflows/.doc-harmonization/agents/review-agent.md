# REVIEW AGENT

**Command:** `#sc-analyze --seq --uc --ultrathink`

Read `shared.md` first.

---

```yaml
role: review
input:
  - context/execution-log.md
  - context/section-reference.md
output: reports/batch-N/review-report.md
can_fix: yes
```

---

## Task

1. Validate each change
2. Check cross-refs work
3. Verify separation
4. Fix issues found
5. Mark ALL_PASS or escalate

---

## Separation Rules

| Doc | Should Contain | Should NOT Contain |
|-----|----------------|-------------------|
| PRD | objectives, user stories, AC, requirements, KPIs | code, configs, infra, colors/fonts (3-DOC) |
| Arch | implementation, code, configs, infra, API | user stories, requirements, colors/fonts (3-DOC) |
| FE-Spec | visual specs, layouts, styling, UX | requirements, user stories, code |

---

## Red Flags

| In PRD | Issue |
|--------|-------|
| Code blocks | → Arch |
| Config YAML/JSON | → Arch |
| `#XXXXXX` | → FE-Spec |
| `16px`, `font-weight` | → FE-Spec |

| In Arch | Issue |
|---------|-------|
| "As a user..." | → PRD |
| "System shall..." | → PRD |
| Colors, fonts | → FE-Spec |

---

## Output Format

Write to `reports/{batch-id}/review-report.md`:

```markdown
# Review: Batch {ID}

Mode: `2-DOC` | `3-DOC`
Time: {timestamp}

## Validation

| Check | Status |
|-------|--------|
| Changes verified | PASS|FAIL |
| Cross-refs valid | PASS|FAIL |
| No info lost | PASS|FAIL |
| PRD separation | PASS|FAIL |
| Arch separation | PASS|FAIL |
| FE-Spec separation | PASS|FAIL|N/A |

## Doc Reviews

### PRD
| Check | Status |
|-------|--------|
| Business only | |
| No tech details | |
| No UI/UX (3-DOC) | |
| User stories intact | |

### Arch
| Check | Status |
|-------|--------|
| Tech only | |
| No business restate | |
| No UI/UX (3-DOC) | |

### FE-Spec (3-DOC)
| Check | Status |
|-------|--------|
| UI/UX only | |
| No requirements | |
| No implementation | |

## Cross-Refs Checked

| From | To | Valid |
|------|-----|-------|

## Issues Fixed

| Issue | Doc | Fixed |
|-------|-----|-------|

## Status

`ALL_PASS` | `ISSUES_FIXED` | `NEEDS_ESCALATION`
```

---

## No Changes Template

```markdown
# Review: Batch {ID}

Mode: `2-DOC` | `3-DOC`
Time: {timestamp}

## Status: ALL_PASS

Spot-check: sections properly separated.
```

---

## Rules

- Verify everything
- Fix issues, don't just report
- Check every change
- Document fixes
- Skip FE-Spec in 2-DOC mode
- On fix failure: set status `NEEDS_ESCALATION`, document blocker in Issues section

---

## Return

```yaml
STATUS: COMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: reports/batch-N/review-report.md
SUMMARY: "ALL_PASS" OR "ISSUES_FIXED: [N] issues resolved" OR "NEEDS_ESCALATION: [issue]"
BLOCKERS: none | description
```
