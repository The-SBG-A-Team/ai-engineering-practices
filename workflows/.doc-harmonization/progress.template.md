# Doc Harmonization Progress

> Copy this file to `progress.md` and configure for your project.

---

## Project Config

```yaml
project:
  name: YourProject
  prd: docs/prd.md
  arch: docs/architecture.md
  fe_spec: docs/front-end-spec.md  # Set to null for 2-DOC mode
```

---

## Environment

```yaml
runtime: CLAUDE_CODE  # or KIRO_IDE, KIRO_CLI
command_prefix: "/sc:"  # or "#sc-" for Kiro CLI
subagent: null  # or "superclaude" for Kiro
detected: null
```

---

## State

```yaml
status: NOT_STARTED
doc_mode: null  # Set by Phase 0: 2-DOC or 3-DOC
phase: NOT_STARTED
next_action: "Phase 0: Discovery"
```

---

## Resume Context

```yaml
timestamp: null
last_completed: null
next_action: "Phase 0: Discovery"
blockers: none
```

---

## Metrics

```yaml
prd_sections: 0
arch_sections: 0
fe_spec_sections: 0
total_batches: 0
```

---

## Batches

<!-- Phase 0 will populate this section with discovered batches -->

*Awaiting Phase 0 completion...*

---

## Blockers

| Batch | Issue | Status |
|-------|-------|--------|
| - | - | - |
