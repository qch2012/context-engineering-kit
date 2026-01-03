# Step 3: Parallelize Implementation Steps

## Context

You are executing step 3 of the add-and-triage workflow. The task file has been created (Phase 1) and refined with detailed specifications, affected files, and implementation resources (Phase 2). Your job is to reorganize the implementation steps for maximum parallel execution.

## Goal

Reorganize implementation steps in the refined task file for maximum parallel execution with explicit dependency tracking. Transform sequential plans into parallelized execution plans that enable multiple sub-agents to work simultaneously.

## Input

- **Task File**: Path provided by orchestrator (e.g., `.specs/tasks/task-{feature-name}.md`)

## Instructions

### 1. Load and Understand the Task

1. Read the task file to get the full implementation process
2. Locate the `## Implementation Process` section (or similar heading)
3. List all current steps
4. Note any existing dependencies mentioned

### 2. Analyze Step Dependencies

For each step, determine:

1. **Input requirements**: What files/artifacts must exist before this step starts? What information from previous steps is needed?
2. **Output artifacts**: What does this step produce? What files are created/modified?
3. **True dependencies vs. artificial sequencing**: Does step B truly need step A's output, or were they just listed sequentially by habit?
4. **Parallel opportunities**: Steps with the same dependencies can run in parallel. Independent utility work often parallelizes with main work.

### 3. Group Tightly Coupled Work

Identify steps that should be merged:

1. **Sync relationships**: If step A produces X and step B syncs X to Y, merge them
2. **Atomic operations**: Steps that must succeed together or fail together
3. **Same-file edits**: Multiple small edits to the same file should become a single step

**Example merge candidates:**
- "Update Plugin README" + "Sync Docs README from Plugin README" -> Single step
- "Create config file" + "Update config file" -> Single step

### 4. Build Dependency Graph

Create a visual ASCII diagram showing the dependency structure:

```
Step 1 (Foundation)
    |
    +---------------+---------------+
    v               v               v
Step 2a          Step 2b         Step 2c
(Can parallel)  (Can parallel)  (Can parallel)
    |               |               |
    +-------+-------+               |
            v                       |
         Step 3                     |
    (Needs 2a, 2b)                  |
            |                       |
            +-----------+-----------+
                        v
                     Step 4
                 (Needs 3, 2c)
```

**Diagram rules:**
- Vertical lines (|) show sequential dependency
- Horizontal branches (+--+--+) show parallel opportunities
- Merge points (+--+--+) show synchronization barriers
- Include agent type in brackets [agent-type] for each step

### 5. Restructure Implementation Steps

Rewrite each step with the following structure:

```markdown
### Step N: [Title]

**Agent:** [Agent type - see Agent Selection Guide below]
**Depends on:** [List of step numbers, or "None"]
**Parallel with:** [List of step numbers that share same dependencies]
**Note:** [If contains parallelizable sub-tasks] Individual [items] MUST be [action] in parallel by multiple agents

[Step description]

#### Expected Output
- [Artifact 1]
- [Artifact 2]

#### Success Criteria
- [Criterion 1]
- [Criterion 2]

---
```

**Important formatting rules:**
- Use "MUST be done in parallel" not "can be done in parallel"
- Be explicit about what enables parallelization
- Add tables for sub-tasks that parallelize:

| Sub-task | Description | Agent | Can Parallel |
|----------|-------------|-------|--------------|
| task-1   | Description | opus | Yes |
| task-2   | Description | opus | Yes |

### 6. Update Task File

Use Write tool to modify the task file directly:

1. **Preserve everything before Implementation Process** (title, context, description, etc.)
2. **Add execution directive** (EXACTLY this text, right after `## Implementation Process` heading):

```
You MUST launch for each step separate agent, instead of performing all steps by self. And for each step that mentioned as parallel, you MUST launch separate agents in parallel.

**CRITICAL:** For each agent you MUST:
1. Use the **Agent** type specified in the step (e.g., `haiku`, `sonnet`, `sdd:tech-writer`)
2. Provide path to task file and prompt which step to implement
3. Require agent to implement exactly that step, not more, not less, not other steps
```

3. **Add Parallelization Overview diagram** (with agent types in brackets)
4. **Rewrite steps with dependency properties** (Agent, Depends on, Parallel with, Note)
5. **Add horizontal rules (---) between steps** for clarity
6. **Preserve everything after Implementation Process** (Blockers, Notes, etc.)

## Agent Selection Guide

### Selection Principle: OUTPUT TYPE DETERMINES AGENT

Choose agent STRICTLY based on what the step produces, NOT what it reads or analyzes.

### Specialized Agents (USE ONLY WHEN OUTPUT EXACTLY MATCHES)

| Agent | ONLY Use When Output Is | NEVER Use For |
|-------|------------------------|---------------|
| `sdd:tech-writer` | Documentation files (README, guides, .md docs) | Code, configs, analysis |
| `sdd:developer` | Source code, implementation files | Docs, configs, planning |
| `sdd:software-architect` | Architecture plans, design documents | Implementation, docs |
| `sdd:tech-lead` | Task breakdowns, technical specifications | Code, docs |
| `sdd:business-analyst` | Requirements documents, user stories | Code, technical docs |
| `sdd:researcher` | Research reports, technology evaluations | Code, implementation |
| `sdd:code-explorer` | Codebase analysis reports | Code changes, docs |
| `code-review:code-reviewer` | Code review feedback | Code changes |
| `code-review:bug-hunter` | Bug analysis reports | Bug fixes (code) |

### General Agents (USE FOR EVERYTHING ELSE)

| Agent | When to Use | Examples |
|-------|-------------|----------|
| `opus` | **Default/standard choice**. Safe for any task. Use when correctness matters, decisions are nuanced, or you're unsure. | Most implementation, code writing, complex logic, architectural decisions |
| `sonnet` | Task is **not complex but high volume** - many similar steps, large context to process, repetitive work. | Bulk file updates, processing many similar items, large refactoring with clear patterns |
| `haiku` | **Trivial operations only**. Simple, mechanical tasks with no decision-making. | Directory creation, file deletion, simple config edits, file copying/moving |

### Decision Flow

```
1. What is the PRIMARY OUTPUT of this step?
   |
   +-> Documentation (.md, README, guides)
   |   --> sdd:tech-writer
   |
   +-> Source code implementation
   |   --> sdd:developer
   |
   +-> Architecture/design document
   |   --> sdd:software-architect
   |
   +-> Task breakdown/specs
   |   --> sdd:tech-lead
   |
   +-> Requirements/user stories
   |   --> sdd:business-analyst
   |
   +-> Research/evaluation report
   |   --> sdd:researcher
   |
   +-> Code review feedback
   |   --> code-review:code-reviewer
   |
   +-> Mixed/Other outputs
       |
       --> Is it trivial/mechanical?
           |
           +-> YES (no decisions, just file ops) --> haiku
           |
           --> NO --> Is it high-volume but simple pattern?
               |
               +-> YES (many similar items, bulk work) --> sonnet
               |
               --> NO or UNSURE --> opus (default)
```

### Common Mistakes to AVOID

| Wrong | Why | Correct |
|-------|-----|---------|
| `sdd:tech-writer` for updating plugin.json | JSON config is NOT documentation | `haiku` or `opus` |
| `sdd:developer` for writing README | README is documentation | `sdd:tech-writer` |
| `sonnet` for complex decisions | Sonnet is for volume, not complexity | `opus` |
| `haiku` for anything requiring judgment | Haiku is for mechanical tasks only | `opus` |
| `sdd:code-explorer` for fixing bugs | Explorer analyzes, doesn't implement | `sdd:developer` |

### Examples by Step Type

| Step Type | Output | Agent | Rationale |
|-----------|--------|-------|-----------|
| Create directories | Folders | `haiku` | Trivial, mechanical |
| Create single config file | JSON/YAML | `haiku` | Simple, no decisions |
| Write utility function | Code | `sdd:developer` | Source code output |
| Write complex algorithm | Code | `sdd:developer` | Source code output |
| Update README | Documentation | `sdd:tech-writer` | Documentation output |
| Write API docs | Documentation | `sdd:tech-writer` | Documentation output |
| Update manifest | JSON config | `opus` | Requires understanding structure |
| Refactor architecture | Code | `opus` | Complex decisions |
| Create workflow command | Markdown command | `opus` | Requires careful design |
| Clean up old files | File deletions | `haiku` | Trivial, mechanical |
| Sync/copy files | Copy operations | `haiku` | Trivial, mechanical |
| Update 10+ similar files | Bulk edits | `sonnet` | High volume, simple pattern |

## Parallelization Principles

### 1. High-Level Structure First

Create orchestrating files (workflow commands, main configs) BEFORE detail files (tasks, sub-configs). This establishes the skeleton that parallel workers fill in.

### 2. Same-Dependency Parallelization

Steps that depend on the same prerequisite(s) MUST run in parallel.

### 3. Merge Tightly Coupled Steps

If Step A's output is immediately consumed by Step B with no other consumers, consider merging:
- BAD: Step 6a: Update plugin README, Step 6b: Sync docs README from plugin README
- GOOD: Step 6a: Update plugin README + sync to docs README

### 4. Sub-task Parallelization

When a step contains multiple independent items, make parallelization explicit with **Note:** annotation.

### 5. Dependency Notation

- `Depends on: None` - Can start immediately
- `Depends on: Step 1` - Single dependency
- `Depends on: Step 2a, Step 2b` - Multiple dependencies (waits for ALL)
- `Parallel with: Step 2b, Step 3` - Same dependencies, run together

## Constraints

- Use proper tools (Read, Write) for file operations - do NOT use echo or cat for file modifications
- Add horizontal rules (---) between steps for visual clarity
- Preserve ALL content before and after the Implementation Process section
- Do NOT add new sections to the task file beyond what parallelization requires
- Do NOT change the meaning or scope of implementation steps - only reorganize them
- Use ONLY agents that exist (refer to Agent Selection Guide)
- Agent selection must be based on OUTPUT type, not input analysis

## Expected Output

Report the following to the orchestrator:

1. **Task Updated**: [task file path]
2. **Steps Reorganized**: X steps (from Y original)
3. **Steps Merged**: X steps combined
4. **Max Parallelization Depth**: X steps can run simultaneously at peak
5. **Agent Distribution**:
   - `opus`: X steps (default)
   - `sonnet`: X steps (high-volume)
   - `haiku`: X steps (trivial)
   - `sdd:tech-writer`: X steps (docs)
   - `sdd:developer`: X steps (code)
   - [other specialized agents if used]

## Success Criteria

- [ ] Sub-agent execution directive added (exact text after `## Implementation Process`)
- [ ] All steps have `Agent:` property (following Agent Selection Guide)
- [ ] All steps have `Depends on:` property
- [ ] Parallel opportunities identified with `Parallel with:`
- [ ] Visual dependency diagram added (with agent types in brackets)
- [ ] "MUST" used for parallel execution requirements (not "can")
- [ ] Tightly coupled steps merged (no artificial splitting)
- [ ] Sub-task tables include `Agent` and `Can Parallel` columns where applicable
- [ ] High-level structure steps come before detail steps
- [ ] Horizontal rules (---) separate steps
- [ ] Agent selection verified: specialized agents ONLY for exact output matches, general agents by complexity
- [ ] All content before/after Implementation Process preserved
