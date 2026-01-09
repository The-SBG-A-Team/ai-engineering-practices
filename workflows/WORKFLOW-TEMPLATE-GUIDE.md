# AI Workflow Template & Best Practices Guide

**Version:** 1.0.0 | **Created:** 2026-01-09

A comprehensive guide for building reusable, long-running AI orchestration workflows. Derived from production-tested patterns in the front-end-integration-alignment workflow.

---

## Table of Contents

1. [Core Principles](#core-principles)
2. [Directory Structure](#directory-structure)
3. [Orchestrator Design](#orchestrator-design)
4. [State Management](#state-management)
5. [Prompt Engineering](#prompt-engineering)
6. [Sub-Agent Protocol](#sub-agent-protocol)
7. [Context Efficiency](#context-efficiency)
8. [Loop & Iteration Patterns](#loop--iteration-patterns)
9. [Error Handling](#error-handling)
10. [Handover Protocol](#handover-protocol)
11. [SC Commands & Flags](#sc-commands--flags)
12. [Templates](#templates)

---

## Environment Detection

### Runtime Detection (CRITICAL - Do First)

Before executing any workflow, the orchestrator MUST determine the runtime environment:

```yaml
DETECT ENVIRONMENT:
  check_for:
    - "Claude Code" or "Claude Desktop" → CLAUDE_CODE
    - "Kiro IDE" → KIRO_IDE
    - "kiro-cli" or "Kiro CLI" → KIRO_CLI
    - Unknown → ASK_USER

  if: cannot determine
  then: |
    ASK: "Which environment are you running in?
    1. Claude Code / Claude Desktop
    2. Kiro IDE
    3. Kiro CLI
    Please specify so I can use the correct command syntax."
```

### Command Syntax by Environment

| Environment | SC Command Syntax | Example | Sub-Agent |
|-------------|-------------------|---------|-----------|
| Claude Code | `/sc:command --flags` | `/sc:implement --seq --serena` | *(not specified)* |
| Kiro IDE | `/sc:command --flags` | `/sc:implement --seq --serena` | **superclaude** |
| Kiro CLI | `#sc-command --flags` | `#sc-implement --seq --serena` | **superclaude** |

**CRITICAL:** When running in Kiro IDE or Kiro CLI, you MUST spawn sub-agents using the `superclaude` agent. Only SuperClaude agents understand and can execute `/sc:` and `#sc-` commands with their associated flags. In Claude Code, do not specify a sub-agent type.

### Orchestrator Startup Protocol

```yaml
on_workflow_start:
  1_detect_environment:
    - Check system context for runtime indicators
    - If unclear, ask user before proceeding
    
  2_set_command_prefix:
    if: CLAUDE_CODE or KIRO_IDE
    then: command_prefix = "/sc:"
    
    if: KIRO_CLI
    then: command_prefix = "#sc-"
    
  3_set_subagent_type:
    if: KIRO_IDE or KIRO_CLI
    then: subagent = "superclaude"  # REQUIRED - only superclaude understands SC commands
    
    if: CLAUDE_CODE
    then: subagent = null  # Do not specify - use default behavior
    
  4_store_in_state:
    - Record environment in progress.md
    - All subsequent agent spawns use correct syntax AND agent type
```

### Progress.md Environment Block

```yaml
## Environment
runtime: KIRO_CLI  # or CLAUDE_CODE, KIRO_IDE
command_prefix: "#sc-"  # or "/sc:"
subagent: superclaude  # REQUIRED for KIRO_IDE/KIRO_CLI, omit for CLAUDE_CODE
detected: 2026-01-09T12:00:00Z
```

---

## Core Principles

### 1. Separation of Concerns

```yaml
ORCHESTRATOR:
  - Reads state files only (progress.md, .state.json)
  - Spawns sub-agents with task instructions
  - Updates state based on agent returns
  - NEVER executes work directly

SUB-AGENTS:
  - Execute specific tasks
  - Read prompts and reference files
  - Write reports
  - Return structured YAML status
```

### 2. Reusability Through Abstraction

- Prompts in separate files → orchestrator doesn't need to know details
- Reference files for patterns → agents share common knowledge
- Section configs → same workflow applies to different domains
- Shared protocol → all agents speak the same language

### 3. State as Source of Truth

- All progress tracked in files (not conversation memory)
- Any agent can resume from state files
- Context resets don't lose progress
- Human-readable state for debugging

### 4. Progressive Disclosure

- Agents read only what they need
- Reports summarize for downstream agents
- Reference by location, not full content
- Tables over prose

### 5. Focused Scope per Agent

AI performs better with bounded focus areas than broad "fix everything" tasks.

```yaml
WHY:
  - Narrow scope enables deeper analysis
  - Reduces cognitive load and error rate
  - Produces higher quality, more consistent output
  - Easier to verify and review results

RULES:
  - One domain/section per agent invocation
  - Batch related work by theme, not "all work"
  - Prefer multiple focused passes over one comprehensive pass
  - Let agents go deep on narrow scope vs shallow on broad scope

ANTI-PATTERN:
  # BAD: "Fix all type errors, lint issues, and test failures"
  # GOOD: "Fix type errors in the authentication module"

  # BAD: "Review the entire codebase for issues"
  # GOOD: "Review API layer alignment for PolicyHolds domain"
```

---

## Directory Structure

```
workflows/[workflow-name]/
├── orchestrator.md           # Main orchestration logic
├── progress.md               # Global state tracker
│
├── prompts/                  # Agent instructions (separated by phase)
│   ├── shared.md             # Common protocol all agents inherit
│   ├── phase-0/              # Preflight/setup
│   ├── phase-1/              # Context gathering
│   ├── phase-2/              # Analysis/assessment
│   ├── phase-3/              # Planning
│   ├── phase-4/              # Implementation
│   ├── phase-5/              # Review
│   ├── phase-6/              # Verification
│   ├── phase-7/              # Fix/signoff
│   └── utility/              # Debugger, summarizer, verifier
│
├── reference/                # Shared knowledge base
│   ├── core.md               # Rules, thresholds, report paths
│   ├── patterns.md           # Implementation patterns
│   ├── sections.md           # Section/domain configs
│   └── state.md              # Current blockers, status matrix
│
└── reports/                  # Output organized by section
    └── [section]/
        ├── .state.json       # Per-section checkpoint
        ├── .preflight.json   # Environment check results
        └── *.md              # Phase reports
```

### Why This Structure Works

| Component | Purpose | Benefit |
|-----------|---------|---------|
| `prompts/` separate from orchestrator | Orchestrator delegates without knowing details | Prompts can evolve independently |
| `reference/` shared files | Single source of truth for patterns | Consistency across agents |
| `reports/[section]/` | Organized output per domain | Easy to find, review, resume |
| `.state.json` per section | Checkpoint for long workflows | Resume from any point |

---

## Orchestrator Design

### Identity Block

Define what the orchestrator IS and IS NOT:

```yaml
YOU ARE:
  - A coordinator who decides WHAT needs to be done next
  - A state tracker who updates progress files
  - A traffic controller who routes work to specialized agents

YOU ARE NOT:
  - A developer who reads or writes code
  - A reviewer who analyzes implementations
  - A debugger who investigates issues
```

### The Self-Check Test

Before ANY action, the orchestrator asks:

> "Am I about to look at code, run a command, or analyze something?"
> - If YES → STOP → That's a sub-agent's job
> - If NO → Proceed

### Allowed vs Forbidden Actions

```yaml
ALLOWED:
  - Read state files: progress.md, .state.json
  - Write state files: progress.md, .state.json (updates only)
  - Spawn sub-agents with task instructions
  - Run git commit after phases complete
  - Decide next action based on state machine

FORBIDDEN:
  - Reading source code (*.tsx, *.ts, *.js, etc.)
  - Running analysis commands (npm, grep, find)
  - Editing any code files
  - Using code analysis tools
  - Making implementation decisions
```

### State Machine Pattern

Define clear phase transitions:

```
| Current | Condition | Next | Notes |
|---------|-----------|------|-------|
| START | section selected | PREFLIGHT | Always run preflight |
| PREFLIGHT | return: PROCEED | P1 | Start context gathering |
| PREFLIGHT | return: FIX_FIRST | DEBUG | Fix then re-run |
| P1 | all agents done | P2 | Context → Assessment |
| P2 | loop exit | P3 | Assessment → Planning |
...
```

### Agent Registry

Maintain a table of all agents with their commands:

```
| Phase | Agent | Command | Prompt File | Parallel? |
|-------|-------|---------|-------------|-----------|
| 0.5 | preflight | /sc:build --seq --uc | prompts/phase-0/preflight.md | - |
| 1 | 1a | /sc:sc --seq --uc --serena | prompts/phase-1/1a-*.md | first |
| 1 | 1b | /sc:sc --seq --uc --serena | prompts/phase-1/1b-*.md | parallel |
```

---

## State Management

### Global Progress File (progress.md)

```yaml
## State
updated: 2026-01-09T09:55:00Z
status: IN_PROGRESS
run_mode: FULL
section: [current_section]
phase: P4
next_action: Run 4a agent

## Handover
timestamp: [last_handover_time]
reason: [CONTEXT_EXHAUSTION | PHASE_COMPLETE | etc.]
last_completed: [agent_id]
next_action: [what to do next]
context: |
  [Summary of what was accomplished]
blockers: none

## Sections
| # | Name | Status | Phase | Updated |
|---|------|--------|-------|---------|
| 1 | section-a | ✓ | P9 | 01-07 |
| 2 | section-b | ▶ | P4 | 01-09 |
| 3 | section-c | - | - | - |
```

### Per-Section State (.state.json)

```json
{
  "section": "section-name",
  "sectionNumber": 1,
  "timestamp": "2026-01-09T09:00:00Z",
  "status": "PASS",
  "recommendation": "PROCEED",
  "checks": {
    "check1": { "status": "PASS", "details": "..." },
    "check2": { "status": "PASS", "details": "..." }
  },
  "summary": {
    "passed": 9,
    "failed": 0,
    "total": 9
  },
  "lastAgentReturn": {
    "agent": "2f",
    "status": "COMPLETE",
    "confidence": 95
  }
}
```

### Why JSON + Markdown Hybrid

| Format | Use For | Reason |
|--------|---------|--------|
| JSON | Machine state (.state.json, .preflight.json) | Parseable, structured |
| Markdown | Human-readable state (progress.md) | Easy to read/edit |
| YAML blocks in MD | Structured data in readable docs | Best of both |

---

## Prompt Engineering

### Prompt File Structure

The SC command MUST be the first line of every prompt file:

```markdown
/sc:sc --seq --uc --serena --ultrathink

# [Phase]: [Task Name]

**Runs:** [Sequential order or LOOP]

Read `shared.md` first.

---

## Task
[Clear, single-sentence task description]

## Output
`reports/[section]/[filename].md`

## Inputs
| Source | What to Extract |
|--------|-----------------|
| [file] | [specific data] |

## Process
1. [Step 1]
2. [Step 2]
3. [Step 3]

## Report Format
```markdown
[Template for output report]
```

## Return
```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: path/to/report.md
SUMMARY: 2-3 sentences
```
```

**Command Position Rule:** The `/sc:*` command on line 1 ensures the agent activates with correct flags before reading instructions.

### Shared Protocol (shared.md)

All agents inherit common rules:

```markdown
# Shared Agent Protocol

## Placeholders
| Placeholder | Replace With |
|-------------|--------------|
| [SECTION] | Title Case |
| [section] | lowercase |
| [N] | Iteration number |

## Return Protocol
After writing report, return ONLY:
```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[section]/[filename].md
SUMMARY: 2-3 sentences
LOOP_CONTROL: CONTINUE | EXIT  # loops only
EDITS_MADE: true | false       # loops only
BLOCKERS: none | description
```
**MAX 150 words.** Orchestrator sees only this.

## Verification
```bash
[verification commands]
```
All must pass before COMPLETE.
```

### Prompt Design Principles

1. **Single Responsibility**: One task per prompt
2. **Clear Inputs/Outputs**: Explicit file paths
3. **Structured Return**: YAML for machine parsing
4. **Verification Built-in**: Commands to validate work
5. **Loop-Aware**: CONTINUE/EXIT for iterative phases

---

## Sub-Agent Protocol

### Task Delegation Template

When orchestrator spawns an agent, the SC command MUST be the very first characters:

```markdown
/sc:implement --seq --serena --uc --morph --ultrathink

## Task: [PHASE_NAME] - [SECTION]

**Section:** [section_name]
**Iteration:** [N] (for loops)

### Instructions
1. Read: workflows/[workflow]/prompts/shared.md
2. Read: workflows/[workflow]/[PROMPT_FILE]
3. Execute the task defined in the prompt
4. Output report to: workflows/[workflow]/reports/[section]/[REPORT_FILE]

### Context
[Any relevant context from previous iterations]

### Expected Return
Return structured YAML per shared.md protocol.
```

**CRITICAL: Command Position Rule**
- The `/sc:*` or `#sc-*` command MUST start at character position 0
- No text, whitespace, or headers before the command
- The command line triggers the correct agent behavior
- Everything after the command line is the task instruction

```yaml
# CORRECT - Command first
/sc:implement --seq --serena --uc --morph

## Task: Fix API types
...

# WRONG - Text before command
## Task: Fix API types

/sc:implement --seq --serena --uc --morph
...
```

### Return Message Contract

```yaml
# Required fields
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: path/to/report.md
SUMMARY: "2-3 sentences max"

# Loop-only fields
LOOP_CONTROL: CONTINUE | EXIT
EDITS_MADE: true | false

# Always include
BLOCKERS: none | "description"
```

### Processing Agent Returns

```yaml
on_agent_return:
  if: STATUS == BLOCKED
  then: spawn debugger with agent context

  if: STATUS == INCOMPLETE AND CONFIDENCE < 70
  then: spawn debugger

  if: LOOP_CONTROL == CONTINUE AND iteration < max
  then: spawn same agent for next iteration

  if: LOOP_CONTROL == EXIT OR iteration >= max
  then: proceed to next phase

  always: update .state.json with lastAgentReturn
```

---

## Context Efficiency

### YAML and Tables for Structured Data

AI models compress prose but preserve structured data. Use:

```yaml
# GOOD: Tables
| Endpoint | Method | Status |
|----------|--------|--------|
| /api/v1/items | GET | Aligned |
| /api/v1/items | POST | Gap |

# GOOD: YAML blocks
gaps:
  - id: GAP-01
    severity: HIGH
    fix: Add validation

# BAD: Prose
The first endpoint is GET /api/v1/items which is aligned with the spec.
The second endpoint is POST /api/v1/items which has a gap...
```

### Progressive Loading Pattern

When reading multiple reports:

```yaml
Stage 1 - Scan:
  - Read ONLY headers and summary sections
  - Skip prose, focus on tables
  - Note file:line references

Stage 2 - Deep Dive:
  - Read only sections with gaps/findings
  - Skip "working" or "aligned" items

Stage 3 - Consolidate:
  - Reference by location, don't copy
  - Output ~40% shorter than sum of inputs
```

### Reference by Location

```markdown
# GOOD
See openapi.yaml lines 245-280 for schema definition.
Pattern: reference/patterns.md#api-client

# BAD
[Copying 50 lines of code into the report]
```

### Content Tiers

| Tier | Include | Skip |
|------|---------|------|
| MUST | Requirements, gaps with file:line, interfaces | - |
| INCLUDE | Patterns (ref patterns.md), non-standard logic | Standard patterns |
| REFERENCE | Working code, spec line ranges | Full code blocks |

---

## Loop & Iteration Patterns

### Loop Configuration

```yaml
loops: [P2_LOOP, P3_LOOP, P5, P8, P9]

rules:
  minimum_iterations: 2
  maximum_iterations: 5

  continue_if:
    - iteration < 2  # always run at least twice
    - agent.edits_made == true
    - agent.confidence < 95

  exit_if:
    - iteration >= 2 AND agent.edits_made == false AND agent.confidence >= 95

  force_exit_if:
    - iteration >= 5  # STOP, ask user
```

### Loop Report Naming

```
reports/[section]/
├── 05-review-v1.md    # First iteration
├── 05-review-v2.md    # Second iteration
├── 05-review-v3.md    # Third iteration (if needed)
```

### Loop Agent Return

```yaml
STATUS: COMPLETE
CONFIDENCE: 97
REPORT_PATH: reports/section/05-review-v2.md
SUMMARY: "Fixed 2 type errors. All patterns aligned."
LOOP_CONTROL: EXIT      # No more work needed
EDITS_MADE: false       # No changes this iteration
BLOCKERS: none
```

---

## Error Handling

### Utility Agents

| Agent | Trigger | Purpose |
|-------|---------|---------|
| debugger | BLOCKED, confidence <70%, FIX_FIRST | Diagnose and fix issues |
| verifier | After implementation phases | Run verification commands |
| summarizer | After 3+ agents, before handover | Create session summary |

### Debugger Protocol

```markdown
## Diagnosis Protocol

### Step 1: Reproduce
[Run verification commands]

### Step 2: Classify Error
| Type | Symptoms | Fix Approach |
|------|----------|--------------|
| TypeScript | TS2xxx codes | Fix types |
| Build | Webpack errors | Fix imports |
| Runtime | Console errors | Add guards |

### Step 3: Trace Dependency Chain
API type error → check schema
Hook error → check API client
Page error → check hook usage

### Step 4: Apply & Verify
[Run verification again]
```

### Stop Conditions

```yaml
mandatory_stop:
  - Any agent BLOCKED after debugger attempt
  - Any agent confidence < 70 after debugger
  - Max loop iterations reached
  - User input required

on_stop:
  - Document in progress.md blockers section
  - Notify user with specifics
  - Wait for guidance
```

---

## Handover Protocol

### When to Handover

```yaml
triggers:
  - Context exhaustion approaching
  - 4+ agents spawned in session
  - User requests pause
  - Phase/section complete
```

### Handover Process

```yaml
steps:
  1: STOP - don't start new agents
  2: Let current agent finish
  3: Spawn summarizer agent
  4: Copy summarizer return to progress.md handover_state
  5: Notify user with next steps
  6: WAIT - do not continue
```

### Handover State Format

```yaml
timestamp: 2026-01-09T08:50:00Z
reason: CONTEXT_EXHAUSTION | PHASE_COMPLETE | SECTION_COMPLETE
last_completed: [agent_id]
next_action: [what to do next]
orchestrator_confidence: 100
agent_confidence: 97
context: |
  [2-5 sentences summarizing session state]
blockers: none
pending_decisions: none
```

### Context Recovery

```yaml
recovery_triggers:
  - User says "continue", "resume"
  - New conversation
  - Uncertainty about state

recovery_steps:
  1: Read orchestrator.md fully
  2: Read progress.md for current state
  3: Read reports/{section}/.state.json if exists
  4: Announce what you're resuming before proceeding
```

---

## SC Commands & Flags

### Command Selection by Task Type

| Task Type | Command | When to Use |
|-----------|---------|-------------|
| Analysis | `/sc:sc` | Context gathering, assessment |
| Implementation | `/sc:implement` | Code changes |
| Build/Verify | `/sc:build` | Preflight, verification |
| Troubleshoot | `/sc:troubleshoot` | Debugging |
| Workflow | `/sc:workflow` | Planning, orchestration |

### Flag Combinations

| Flags | Purpose | Use Case |
|-------|---------|----------|
| `--seq` | Sequential thinking | All complex tasks |
| `--uc` | Ultra-compressed output | Token efficiency |
| `--serena` | Code analysis tools | Reading/analyzing code |
| `--morph` | Code editing tools | Making changes |
| `--ultrathink` | Deep reasoning (15-25 steps) | Complex analysis |
| `--play` | Playwright browser automation | E2E testing |

### Standard Combinations

```yaml
# Analysis (read-only)
/sc:sc --seq --uc --serena --ultrathink

# Implementation (read + write)
/sc:implement --seq --serena --uc --morph --ultrathink

# Verification (build/test)
/sc:build --seq --uc

# E2E Testing
/sc:sc --seq --play --serena --uc --ultrathink

# Debugging
/sc:troubleshoot --seq --serena --uc --morph --ultrathink
```

---

## Templates

### New Workflow Checklist

```markdown
## Workflow Setup Checklist

- [ ] Create directory: `workflows/[workflow-name]/`
- [ ] Create orchestrator.md with:
  - [ ] Identity block (IS/IS NOT)
  - [ ] Allowed/Forbidden actions
  - [ ] State machine transitions
  - [ ] Agent registry table
  - [ ] Loop rules
  - [ ] Handover protocol
- [ ] Create progress.md with:
  - [ ] State section
  - [ ] Handover section
  - [ ] Sections table
  - [ ] Blockers table
- [ ] Create prompts/shared.md with:
  - [ ] Placeholders table
  - [ ] Return protocol
  - [ ] Verification commands
- [ ] Create prompts/phase-N/ for each phase
- [ ] Create reference/ files:
  - [ ] core.md (rules, thresholds)
  - [ ] patterns.md (implementation patterns)
  - [ ] sections.md (domain configs)
  - [ ] state.md (current status)
- [ ] Create reports/ directory structure
```

### Orchestrator Template

```markdown
# [Workflow Name] Orchestrator

**Version:** 1.0.0 | **Updated:** [DATE]

---

## Identity

**You are a DELEGATION-ONLY orchestrator.**

```yaml
YOU ARE:
  - A coordinator who decides WHAT needs to be done next
  - A state tracker who updates progress files
  - A traffic controller who routes work to agents

YOU ARE NOT:
  - [List forbidden roles for this workflow]
```

---

## Allowed Actions

```yaml
ALLOWED:
  - Read state files: progress.md, .state.json
  - Write state files (updates only)
  - Spawn sub-agents with task instructions
  - [Workflow-specific allowed actions]

FORBIDDEN:
  - [Workflow-specific forbidden actions]
```

---

## State Machine

| Current | Condition | Next |
|---------|-----------|------|
| START | [condition] | PHASE_1 |
| PHASE_1 | [condition] | PHASE_2 |
...

---

## Agent Registry

| Phase | Agent | Command | Prompt File |
|-------|-------|---------|-------------|
| 1 | agent-1a | [command] | prompts/phase-1/1a.md |
...

---

## Loop Rules

```yaml
loops: [PHASE_X, PHASE_Y]
minimum_iterations: 2
maximum_iterations: 5
```

---

## Handover Protocol

[Copy from guide]
```

### Prompt Template

```markdown
/sc:sc --seq --uc --serena --ultrathink

# [Phase Number]: [Task Name]

**Runs:** [Sequential/Parallel/LOOP]

Read `shared.md` first.

---

## Task

[Single sentence describing what this agent does]

## Output

`reports/[section]/[filename].md`

## Inputs

| Source | What to Extract |
|--------|-----------------|
| [file] | [data] |

## Process

1. [Step 1]
2. [Step 2]
3. [Step 3]

## Report Format

```markdown
## [Report Title]: [SECTION]

### Section 1
[content]

### Section 2
| Column | Column |
|--------|--------|
```

## Return

```yaml
STATUS: COMPLETE | INCOMPLETE | BLOCKED
CONFIDENCE: 0-100
REPORT_PATH: reports/[section]/[filename].md
SUMMARY: 2-3 sentences
BLOCKERS: none | description
```
```

**Remember:** SC command on line 1, no text before it.

### Section Config Template

```yaml
## [Section Number]: [Section Name]
domain: [DomainName]
directory: [path/to/files/]
files: [list of files]
priority: HIGH | MEDIUM | LOW
complexity: HIGH | MEDIUM | LOW
dependencies: [list of other sections]
notes: [any special considerations]
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| SC command not first | Command not activated | Put `/sc:*` at character 0 |
| Orchestrator doing work | Context pollution, inconsistency | Delegate everything |
| Prose-heavy reports | AI compacts, loses detail | Use tables and YAML |
| No state files | Can't resume after context reset | Always track in files |
| Monolithic prompts | Hard to maintain, reuse | Split by phase/task |
| Hardcoded values | Can't reuse for other domains | Use placeholders |
| No verification | Silent failures | Built-in verification commands |
| No loop limits | Infinite loops | Min 2, max 5 iterations |
| No handover protocol | Lost progress on context reset | Explicit handover state |
| No environment detection | Wrong command syntax | Detect runtime first |
| Wrong subagent in Kiro | SC commands not understood | Use `superclaude` agent in Kiro IDE/CLI |

---

## Workflow Evolution Tips

1. **Start Simple**: Begin with fewer phases, add as needed
2. **Version Your Orchestrator**: Track changes in version header
3. **Document Decisions**: Add notes to state.md for why things are done
4. **Review Reports**: Check if agents are producing useful output
5. **Tune Loop Thresholds**: Adjust min/max based on task complexity
6. **Add Utility Agents**: Debugger, summarizer as patterns emerge
7. **Cross-Reference**: Link related workflows in reference files

---

## Quick Reference Card

```
ENVIRONMENT DETECTION (DO FIRST)
├── Claude Code / Kiro IDE → /sc:command
├── Kiro CLI → #sc-command
└── Unknown → ASK USER

SUBAGENT SELECTION (CRITICAL)
├── Kiro IDE → use "superclaude" agent
├── Kiro CLI → use "superclaude" agent
├── Claude Code → do not specify agent type
└── Why: Only superclaude understands SC commands

SC COMMAND POSITION (CRITICAL)
├── Must be first characters (position 0)
├── No text/whitespace before command
├── Command line activates agent behavior
└── Instructions follow after command

ORCHESTRATOR RULES
├── Never read source code
├── Never run analysis commands
├── Never edit code files
├── Only read/write state files
└── Spawn agents for ALL work

AGENT RETURN FORMAT
├── STATUS: COMPLETE | INCOMPLETE | BLOCKED
├── CONFIDENCE: 0-100
├── REPORT_PATH: path/to/report.md
├── SUMMARY: 2-3 sentences
├── LOOP_CONTROL: CONTINUE | EXIT (loops)
├── EDITS_MADE: true | false (loops)
└── BLOCKERS: none | description

LOOP RULES
├── Minimum: 2 iterations
├── Maximum: 5 iterations
├── Exit: confidence >= 95 AND no edits AND iteration >= 2
└── Force exit: iteration >= 5

CONTEXT EFFICIENCY
├── Tables over prose
├── YAML for structured data
├── Reference by location
└── Progressive loading (scan → deep dive → consolidate)

SC FLAGS
├── --seq: Sequential thinking
├── --uc: Ultra-compressed
├── --serena: Code analysis
├── --morph: Code editing
├── --ultrathink: Deep reasoning
└── --play: Browser automation
```
