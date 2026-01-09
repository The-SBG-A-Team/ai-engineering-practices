# Document Consolidation Progress

> **Orchestrator**: Update this file after EVERY sub-agent completes.
> **Recovery**: Read this file + check `reports/` directory after context reset.

---

## Environment

```yaml
runtime: null  # KIRO_CLI | KIRO_IDE | CLAUDE_CODE
command_prefix: null  # "#sc-" | "/sc:"
subagent: null  # "superclaude" | null
detected: null
```

---

## State

```yaml
updated: null
status: NOT_STARTED
phase: DOC_SELECT_PENDING
current_document: null
current_document_type: null
current_section: null
current_batch: null
current_agent: null
```

---

## Handover

```yaml
timestamp: null
reason: null  # CONTEXT_EXHAUSTION | PHASE_COMPLETE | BATCH_COMPLETE | DOC_COMPLETE | AWAITING_CONFIRMATION
last_completed: null
current_document: null
next_action: "Run document selection to choose which docs to consolidate"
context: |
  Workflow not yet started. Document selection will scan docs/ directory,
  present available documents, and accept user selection.
blockers: none
pending_decisions: none
```

---

## Document Selection

> Populated by orchestrator during Phase -1

```yaml
available_documents: []
selected_documents: []
document_queue: []  # Remaining documents to process
```

### Available Documents

| # | Document | Type | Lines | Template | Status |
|---|----------|------|-------|----------|--------|
| - | *Run document selection first* | - | - | - | - |

### Selected Documents

| Order | Document | Type | Status |
|-------|----------|------|--------|
| - | *Awaiting selection* | - | - |

---

## Current Document Configuration

> Populated after Phase 0 completes for current document

```yaml
document: null
document_type: null
document_path: null
template_path: null
sections_discovered: 0
batches_planned: 0
setup_confirmed: false
```

---

## Section Progress (Current Document)

> Populated after Phase 0 and user confirmation

| # | Section | Slug | P1 | P2 | P3 | Status |
|---|---------|------|----|----|----|----|
| - | *Run Phase 0 first* | - | - | - | - | - |

**Legend:** ✓ Complete | ▶ In Progress | ✗ Failed | - Pending

---

## Batch Progress (Current Document)

| Batch | Sections | Status | Review |
|-------|----------|--------|--------|
| - | *Run Phase 0 first* | - | - |

---

## Document Progress (All Selected)

| # | Document | Type | Sections | Batches | Status | Final Review |
|---|----------|------|----------|---------|--------|--------------|
| - | *Awaiting selection* | - | - | - | - | - |

**Legend:** ✓ Complete | ▶ In Progress | - Pending

---

## Blockers

| # | Type | Description | Document | Since | Resolution |
|---|------|-------------|----------|-------|------------|
| - | - | No blockers | - | - | - |

---

## Completion Log

```
[YYYY-MM-DD HH:MM] [Document] [Agent] [Section] [Status] [Notes]
```

---

## Session History

| Session | Started | Ended | Documents | Agents Run | Progress |
|---------|---------|-------|-----------|------------|----------|
| - | - | - | - | - | - |
