# Shared Agent Protocol

All agents inherit these rules.

---

## Placeholders

| Placeholder | Replace With |
|-------------|--------------|
| `{ID}` | Batch number (1, 2, 3) |
| `{Name}` | Batch name |

---

## Return Protocol

After completing task, return ONLY:

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: context/[file].md OR reports/[file].md
SUMMARY: 2-3 sentences
BLOCKERS: none | description
```

**MAX 100 words.** Orchestrator sees only this.

---

## File Paths

| Type | Path |
|------|------|
| Context | `workflows/.doc-harmonization/context/` |
| Reports | `workflows/.doc-harmonization/reports/` |
| Source Docs | `docs/prd.md`, `docs/architecture.md`, `docs/front-end-spec.md` |

---

## Doc Mode

| Mode | Docs | UI/UX Location | Detection |
|------|------|----------------|-----------|
| 2-DOC | PRD + Arch | PRD (embedded) | `fe_spec: null` |
| 3-DOC | PRD + Arch + FE-Spec | FE-Spec (dedicated) | `fe_spec: <path>` |

**Check `context/section-reference.md` for mode after Phase 0.**

### Mode-Specific Rules

| In 2-DOC Mode | In 3-DOC Mode |
|---------------|---------------|
| UI/UX stays in PRD | UI/UX moves to FE-Spec |
| Skip FE-Spec operations | Process all 3 docs |
| 2-way cross-refs (PRD↔Arch) | 6-way cross-refs |
| FE-Spec columns = N/A | FE-Spec columns filled |

---

## Rules

1. Read your agent file fully before starting
2. Follow output format exactly
3. Never skip steps
4. Document everything
5. Return structured YAML when done
