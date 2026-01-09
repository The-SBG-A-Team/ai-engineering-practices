# Core Rules & Configuration

> Shared rules, thresholds, and paths for all agents.

---

## Workflow Identity

```yaml
workflow: doc-optimize
version: 4.0.0
purpose: Optimize documentation - consolidate, restructure, streamline, and improve writing quality
```

---

## Objectives

| Goal | Description |
|------|-------------|
| Professional Clarity | Easy to read, well-organized, scannable |
| Zero Duplication | No repetition within or across sections |
| Audience Separation | PRD for business, Architecture for technical |
| Optimal Density | Thorough without bloat |
| Improved Articulation | Clear English, professional tone, active voice |

---

## Supported Document Types

| Type | Slug | Template | Target |
|------|------|----------|--------|
| Architecture | `architecture` | `architecture-tmpl.yaml` | `docs/architecture.md` |
| PRD | `prd` | `prd-tmpl.yaml` | `docs/prd.md` |
| Front-End Spec | `front-end-spec` | `front-end-spec-tmpl.yaml` | `docs/front-end-spec.md` |
| Project Brief | `project-brief` | `project-brief-tmpl.yaml` | `docs/project-brief.md` |

---

## Quality Thresholds

| Metric | Threshold | Action if Exceeded |
|--------|-----------|-------------------|
| Agent confidence | < 70 | Spawn debugger |
| Loop iterations | > 5 | Force exit, ask user |
| Section line count | > 500 | Consider splitting |
| Section line count | < 50 | Consider merging |
| Code block length | > 10 lines | Streamline (architecture only) |

---

## Loop Rules

```yaml
loops:
  - P3_EXECUTE  # May need multiple passes
  - REVIEW      # May find issues requiring fixes

rules:
  minimum_iterations: 1
  maximum_iterations: 3

  continue_if:
    - agent.edits_made == true
    - agent.confidence < 90

  exit_if:
    - agent.edits_made == false AND agent.confidence >= 90

  force_exit_if:
    - iteration >= 3  # STOP, ask user
```

---

## Report Paths

| Report Type | Path Pattern |
|-------------|--------------|
| Setup | `reports/[DOC_TYPE]/00-setup.md` |
| Section analysis | `reports/[DOC_TYPE]/[section]/01-analysis.md` |
| Section plan | `reports/[DOC_TYPE]/[section]/02-plan.md` |
| Section execute | `reports/[DOC_TYPE]/[section]/03-execute.md` |
| Section state | `reports/[DOC_TYPE]/[section]/.state.json` |
| Batch review | `reports/[DOC_TYPE]/batch-[N]/review.md` |
| Final review | `reports/[DOC_TYPE]/final/review.md` |
| Section config | `reference/sections-[DOC_TYPE].md` |

---

## Content Rules by Document Type

### Architecture Documents

**KEEP:**
- Mermaid diagrams
- Schema DDL / ERD
- Configuration examples (YAML, JSON)
- API contracts / OpenAPI refs
- Interface definitions
- TypeScript/Java interfaces (contracts only)
- Short pseudocode (≤10 lines)
- File references (`See: app/...`)

**REMOVE:**
- Copy-pasted source code
- Method implementations
- Full class bodies
- Code duplicating `app/` source

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| 50-line code block | Description + file reference |
| Full implementation | Pattern name + behavior description |
| Copy-pasted class | Class responsibility + `See: app/.../File.java` |

---

### PRD Documents

**KEEP:**
- Numbered requirements (FR1, FR2, NFR1, etc.)
- User stories with acceptance criteria
- Decision records
- Scope boundaries
- Success metrics
- Stakeholder definitions

**REMOVE:**
- Technical implementation details
- Code snippets
- Database schemas (→ architecture)
- API specifications (→ architecture)
- System design details (→ architecture)

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| Technical specs | Reference: "See architecture.md" |
| Implementation notes | Acceptance criteria |
| System design | High-level requirement description |

---

### Front-End Spec Documents

**KEEP:**
- User flows (Mermaid diagrams)
- Component specifications
- Design tokens / style definitions
- Accessibility requirements (WCAG)
- Interaction patterns
- Wireframe references

**REMOVE:**
- Backend implementation details
- API implementation code
- Database schemas
- Server-side logic
- Full component implementations

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| API implementation | API contract reference |
| Backend logic | Data flow description |
| Full component code | Component spec + props interface |

---

### Project Brief Documents

**KEEP:**
- Problem statement
- Target users / personas
- Success metrics
- MVP scope
- Constraints
- High-level goals

**REMOVE:**
- Technical details
- Implementation specifics
- Code examples
- Database designs
- API specifications

**REPLACE:**
| Instead of... | Use... |
|---------------|--------|
| Technical specs | "Technical details in architecture.md" |
| Implementation plan | High-level milestones |
| System design | Goals and outcomes |

---

## Reader Completeness Criteria

For each section, verify:

1. **Reader expectation**: Would a reader looking for this info expect it here?
2. **Self-containment**: After reading, does reader have complete understanding?
3. **Context completeness**: Are critical details elsewhere that would leave gaps?
4. **Standalone viability**: Could section be extracted as separate document?

---

## Restructuring Guidelines

### When to Merge Sections
- Two sections cover overlapping topics (>50% overlap)
- A section is too small to stand alone (<50 lines)
- Content naturally belongs together but was artificially separated
- Merging eliminates significant duplication

### When to Split Sections
- A section is too large (>500 lines) and covers multiple distinct topics
- A subsection has grown important enough to warrant its own section
- Splitting improves navigation and discoverability

### When to Rename Sections
- Current name doesn't reflect actual content
- A more descriptive name would help readers find information
- After merging, a new name better captures combined content

### When to Delete Sections
- Content is entirely duplicated elsewhere
- Content provides no value (pure fluff)
- Content is outdated/irrelevant to current system

---

## Cross-Document References

When content belongs in a different document, add a reference:

```markdown
> **See also:** [architecture.md - Data Models](#data-models) for database schema details.
```

| Content Type | Primary Home | Reference Format |
|--------------|--------------|------------------|
| Database schemas | architecture.md | "Schema details in architecture.md" |
| API specs | architecture.md | "API contract in architecture.md" |
| User flows | front-end-spec.md | "User flow in front-end-spec.md" |
| Requirements | prd.md | "Requirement FR-X in prd.md" |
| Problem statement | project-brief.md | "Context in project-brief.md" |

---

## Stop Conditions

```yaml
mandatory_stop:
  - Any agent BLOCKED after debugger attempt
  - Any agent confidence < 70 after debugger
  - Max loop iterations reached
  - User input required
  - Phase 0 awaiting confirmation
  - Document complete (before next document)

on_stop:
  - Document in progress.md blockers section
  - Notify user with specifics
  - Wait for guidance
```

---

## Verification Commands

```bash
# Check document syntax
cat docs/[DOCUMENT] | head -100

# Verify Mermaid diagrams (visual check)
# Preview in markdown viewer

# Check for remaining code blocks (architecture only)
grep -n '```java\|```typescript\|```python' docs/[DOCUMENT]

# Check line count
wc -l docs/[DOCUMENT]

# Git diff for changes
git diff docs/[DOCUMENT] | head -100
```
