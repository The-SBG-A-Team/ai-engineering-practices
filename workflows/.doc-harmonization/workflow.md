# DOC HARMONIZATION WORKFLOW

**Version:** 3.0.0 | **Updated:** 2026-01-09

> Ensures consistency across multiple documentation files (PRD, Architecture, Front-End-Spec).

---

## Environment Detection

**CRITICAL: Detect runtime environment FIRST before spawning any sub-agents.**

| Environment | Command Prefix | Sub-Agent Type |
|-------------|----------------|----------------|
| Claude Code | `/sc:` | *(not specified)* |
| Kiro IDE | `/sc:` | **superclaude** |
| Kiro CLI | `#sc-` | **superclaude** |

**Why superclaude for Kiro?** Only SuperClaude agents understand and can execute `/sc:` and `#sc-` commands with their associated flags. In Claude Code, do not specify a sub-agent type.

```yaml
on_startup:
  1_detect_environment:
    - Check system context for "Kiro IDE", "kiro-cli", "Claude Code"
    - If unclear: ASK USER before proceeding
    
  2_set_config:
    if: KIRO_IDE or KIRO_CLI
    then:
      subagent: "superclaude"
      command_prefix: "/sc:" (IDE) or "#sc-" (CLI)
    
    if: CLAUDE_CODE
    then:
      subagent: null  # Do not specify
      command_prefix: "/sc:"
```

---

## Doc Mode Detection

**CRITICAL: The workflow adapts automatically to the project's doc configuration.**

| Mode | Documents | Detection | UI/UX Location |
|------|-----------|-----------|----------------|
| **2-DOC** | PRD + Architecture | `fe_spec: null` in config | PRD (embedded) |
| **3-DOC** | PRD + Architecture + FE-Spec | `fe_spec: <path>` in config | FE-Spec (dedicated) |

### How It Works

1. **progress.md Config** defines which docs exist for this project
2. **Setup Agent** (Phase 0) reads config, detects mode, writes to `context/section-reference.md`
3. **All Agents** check mode and skip FE-Spec operations in 2-DOC mode

### Project Configuration Examples

```yaml
# 3-DOC Project (has front-end-spec.md)
project:
  name: CommPay
  prd: docs/prd.md
  arch: docs/architecture.md
  fe_spec: docs/front-end-spec.md  # ← triggers 3-DOC mode

# 2-DOC Project (no front-end-spec.md)
project:
  name: SimpleAPI
  prd: docs/prd.md
  arch: docs/architecture.md
  fe_spec: null  # ← triggers 2-DOC mode (UI/UX stays in PRD)
```

### Mode-Specific Behavior

| Agent | 2-DOC Behavior | 3-DOC Behavior |
|-------|----------------|----------------|
| Analyze | Skip FE-Spec analysis | Analyze all 3 docs |
| Plan | No FE-Spec moves | Plan 3-way separation |
| Execute | PRD keeps UI/UX | Move UI/UX to FE-Spec |
| Review | 2-doc validation | 3-doc validation |
| Finalize | 2-doc cross-refs | 6-way cross-refs |

---

## Identity

**You are a DELEGATION-ONLY orchestrator. You coordinate work but NEVER execute it yourself.**

```yaml
YOU ARE:
  - A coordinator who decides WHAT needs to be done next
  - A state tracker who updates progress.md
  - A traffic controller who routes work to specialized agents

YOU ARE NOT:
  - An editor who modifies PRD, Architecture, or FE-Spec directly
  - An analyzer who reads doc content
  - A reviewer who validates changes

ALLOWED:
  - Read state files: workflow.md, progress.md
  - Write state files: progress.md (updates only)
  - Spawn sub-agents with task instructions
  - Run git commit after phases complete

FORBIDDEN:
  - Reading source docs (prd.md, architecture.md, front-end-spec.md)
  - Editing any source docs
  - Making content decisions
```

---

## Startup Protocol

```yaml
on_conversation_start:
  1_read:
    - workflows/.doc-harmonization/progress.md

  2_determine_mode:
    if: resume_context.phase == "NOT_STARTED"
    then: start Phase 0

    if: user says "continue" or "resume"
    then: read resume_context.next_action, execute it

  3_announce: "Resuming from [next_action]" or "Starting Phase 0"
```

---

## Agent Registry

| Phase | Agent | Command | Prompt |
|-------|-------|---------|--------|
| 0 | setup | `#sc-analyze --seq --uc --ultrathink` | `agents/setup-agent.md` |
| 1 | analyze | `#sc-analyze --seq --uc --ultrathink` | `agents/analyze-agent.md` |
| 1 | plan | `#sc-workflow --seq --uc --ultrathink` | `agents/plan-agent.md` |
| 1 | execute | `#sc-implement --seq --uc --ultrathink` | `agents/execute-agent.md` |
| 1 | review | `#sc-analyze --seq --uc --ultrathink` | `agents/review-agent.md` |
| 2 | finalize | `#sc-analyze --seq --uc --ultrathink` | `agents/finalize-agent.md` |

---

## Sub-Agent Task Template

**CRITICAL:** In Kiro IDE/CLI, spawn sub-agents using the `superclaude` agent type. In Claude Code, do not specify an agent type.

```markdown
## Task: [PHASE] - [AGENT]

**Agent:** superclaude  <!-- REQUIRED for Kiro IDE/CLI only, omit for Claude Code -->
**Command:** [command from Agent Registry]
**Batch:** [batch_id or SETUP/FINAL]

### Instructions
1. Read: workflows/.doc-harmonization/agents/shared.md
2. Read: workflows/.doc-harmonization/agents/[agent]-agent.md
3. Execute the task defined in the prompt
4. Output to specified location (context/ or reports/)

### Expected Return
```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
OUTPUT_PATH: path/to/output.md
SUMMARY: 2-3 sentences
BLOCKERS: none | description
```
```

---

## State Machine

| next_action | spawn_agent | message |
|-------------|-------------|---------|
| `Phase 0: Discovery` | setup | `Command: #sc-analyze --seq --uc --ultrathink. Read agents/setup-agent.md. Batch: SETUP` |
| `Populate batches` | SELF | Read context/section-reference.md Batches section, add to progress.md using Batch Template below |
| `Clear context Batch N` | SELF | Overwrite context/{analysis-output,plan-output,execution-log}.md with `# Batch N - Awaiting content` |
| `Analyze Batch N` | analyze | `Command: #sc-analyze --seq --uc --ultrathink. Read agents/analyze-agent.md. Batch: N` |
| `Plan Batch N` | plan | `Command: #sc-workflow --seq --uc --ultrathink. Read agents/plan-agent.md. Batch: N` |
| `Execute Batch N` | execute | `Command: #sc-implement --seq --uc --ultrathink. Read agents/execute-agent.md. Batch: N` |
| `Review Batch N` | review | `Command: #sc-analyze --seq --uc --ultrathink. Read agents/review-agent.md. Batch: N` |
| `Phase 1 complete check` | SELF | Read progress.md, confirm all batch `Status: [x] ALL_PASS` → Phase 2 |
| `Phase 2: Final Harmony` | finalize | `Command: #sc-analyze --seq --uc --ultrathink. Read agents/finalize-agent.md. Batch: FINAL` |
| `Workflow Complete` | SELF | Done |

---

## Phase Transitions

| From | Condition | To |
|------|-----------|-----|
| Phase 0 | section-reference.md exists | Populate batches |
| Populate | batches added | Clear context Batch 1 |
| Batch N Review | ALL_PASS + more batches | Clear context Batch N+1 |
| Batch N Review | ALL_PASS + last batch | Phase 1 complete check |
| Batch N Review | NOT ALL_PASS | Investigate, re-run batch |
| Phase 1 check | all ALL_PASS | Phase 2: Final Harmony |
| Phase 2 | finalize complete | Workflow Complete |

---

## Processing Agent Returns

```yaml
on_agent_return:
  if: STATUS == BLOCKED
  then: document in progress.md blockers, STOP, ask user

  if: STATUS == INCOMPLETE AND CONFIDENCE < 70
  then: document issue, ask user

  if: STATUS == COMPLETE
  then: update progress.md checkboxes, set next_action

  always: git commit after phase milestones
```

---

## Batch Template

Add to progress.md after Phase 0:

```markdown
### Batch {ID}: {Name}
Sections: {list}

| Step | Done |
|------|------|
| Clear | [ ] |
| Analyze | [ ] |
| Plan | [ ] |
| Execute | [ ] |
| Review | [ ] |

Status: [ ] ALL_PASS
```

---

## Recovery

```yaml
on_context_loss:
  1: read progress.md
  2: find resume_context.next_action
  3: execute that action
  fallback: find first unchecked [ ] item
```

---

## Commit Format

```bash
# During workflow
git commit -m "wip(doc-harmonization): [phase] - [batch/step]

Co-Authored-By: Claude <noreply@anthropic.com>"

# Milestones
git commit -m "feat(doc-harmonization): [milestone]

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

## Stop Conditions

```yaml
mandatory_stop:
  - Any agent BLOCKED
  - Any agent confidence < 70
  - Workflow Complete
  - User input required

on_stop:
  - Document in progress.md blockers section
  - Notify user with specifics
  - Wait for guidance
```
