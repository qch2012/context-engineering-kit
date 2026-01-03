# Step 2: Refine Task

## Context

You are executing step 2 of the add-and-triage workflow. This step transforms an initial task into an actionable specification with comprehensive implementation details that enable developers to start work immediately with full context.

## Goal

Analyze and enhance the task file created in Phase 1 by:

1. Clarifying requirements and filling in missing details
2. Identifying all files that will be affected (modified, created, or deleted)
3. Gathering useful implementation resources (pattern references, documentation, related code)
4. Creating a detailed implementation process with steps, success criteria, and risks

## Input

- **Task File Path**: `$TASK_FILE` (provided by orchestrator)
- **Initial Complexity**: Complexity estimate from Phase 1 (S/M/L/XL)

## Instructions

### Phase 1: Load and Understand Task

1. **Read the task file**:
   ```bash
   ls .specs/tasks/
   Read .specs/tasks/$TASK_FILE
   ```

2. **Analyze current state**:
   - What is the task about?
   - What details are missing?
   - What assumptions need clarification?

3. **Identify clarification needs**:
   - What is the expected behavior?
   - What triggers this functionality?
   - What are the success criteria?

### Phase 2: Analyze Codebase Impact

1. **Search for related code**:
   - Use Glob to find relevant plugin/module directories
   - Use Grep to find related patterns, keywords, similar implementations
   - Read existing files to understand current structure

2. **Identify files to be modified/created**:
   - **Primary changes**: Core files that implement the feature
   - **Configuration changes**: Manifests, settings, config files
   - **Documentation updates**: READMEs, guides, examples

3. **Find similar implementations**:
   - Look for existing patterns in the codebase
   - Check how similar features were implemented
   - Note reusable patterns and conventions

### Phase 3: Gather Implementation Resources

1. **Pattern references**:
   - Similar commands/features in the codebase
   - Related plugin implementations
   - Existing hooks, agents, or workflows

2. **Documentation**:
   - Relevant guides in `docs/guides/`
   - Plugin documentation in `docs/plugins/`
   - Contributing guidelines

3. **External resources** (if applicable):
   - Official documentation URLs
   - API references
   - Related specifications

### Phase 4: Update Task Description

1. **Read the current task file** to get frontmatter and existing content

2. **CRITICAL: Preserve the Initial User Prompt section** - NEVER delete it

3. **Write the updated file** using this structure:

```markdown
---
title: [KEEP EXISTING]
status: [KEEP EXISTING]
issue_type: [KEEP EXISTING]
complexity: [S/M/L/XL - update if analysis reveals different scope]
---

# Initial User Prompt

[PRESERVE ORIGINAL USER REQUEST - NEVER DELETE THIS]

# Description

[Brief summary of what needs to be implemented]

**Acceptance Criteria:**
- Requirement 1
- Requirement 2
- Requirement 3

**Example Flow:** (if applicable)
1. Step 1
2. Step 2
3. Expected outcome

---

## Files to be Modified/Created

### Primary Changes

```
path/to/plugin/
├── agents/
│   └── agent-name.md              # NEW: Agent description
├── commands/
│   ├── new-command.md             # NEW: Command description
│   ├── existing-command.md        # UPDATE: What to change
│   └── old-command.md             # DELETE: Merged into new-command
└── tasks/
    ├── task-one.md                # NEW: Task description
    └── task-two.md                # NEW: Task description
```

### Documentation Updates

```
docs/
├── plugins/
│   └── plugin-name/
│       └── README.md              # UPDATE: Document the feature
└── guides/
    └── relevant-guide.md          # UPDATE: Update guide
```

---

## Useful Resources for Implementation

### Pattern References

```
plugins/
├── similar-plugin/
│   └── commands/
│       └── similar-command.md     # Similar pattern to follow
└── other-plugin/
    └── agents/
        └── example-agent.md       # Agent definition pattern
```

### Documentation

```
docs/
├── guides/
│   └── relevant-guide.md          # Key concepts
└── reference/
    └── relevant-ref.md            # Reference documentation
```

### Related Code

```
plugins/
├── related-plugin/
│   └── agents/                    # Multiple agent examples
└── another-plugin/
    └── commands/                  # Command examples
```

---

## Implementation Process

### Step 1: [Step title]
[Step description]

#### Expected output
[List of expected output]

#### Success criteria
[List of success criteria]

#### Subtasks
[List of subtasks]

#### Blockers
[List of blockers]

#### Risks
[List of risks]
```

## Constraints

- **NEVER delete the Initial User Prompt section** - this preserves original user intent and is essential for acceptance criteria validation
- **Preserve frontmatter** - keep existing title, status, issue_type fields
- **Use proper tools** for codebase analysis:
  - Glob for finding files by pattern
  - Grep for searching content
  - Read for examining file contents
- **Do not invent files** - only list files you have confirmed exist or that clearly need to be created
- **Keep descriptions actionable** - avoid vague language like "improve" or "enhance"
- **Do not start implementation** - this step is for analysis and planning only

## Expected Output

Report to the orchestrator:

```
## Refinement Complete

**Task Updated**: $TASK_FILE

**Summary**:
- Files Identified: X files (Y to create, Z to modify)
- Implementation Steps: N steps defined
- Useful Resources: R references gathered
- Complexity: [S/M/L/XL] (updated from initial if changed)

**Files to Modify/Create**:
- path/to/file1.md (NEW)
- path/to/file2.md (UPDATE)
- ...

**Key Resources**:
- path/to/reference1.md - [why it's useful]
- path/to/reference2.md - [why it's useful]

**Ready for Phase 3**: Yes/No
- [Any blockers or questions that need resolution]
```

## Success Criteria

Before completing refinement, verify:

- [ ] Initial user prompt is preserved in `# Initial User Prompt` section
- [ ] Task has clear, actionable description
- [ ] Implementation requirements are specific (no ambiguous language)
- [ ] All affected files are identified with action (NEW/UPDATE/DELETE) and descriptions
- [ ] Useful resources are listed with context on why they're helpful
- [ ] Similar patterns in codebase have been identified
- [ ] Implementation process has detailed steps with success criteria
- [ ] Complexity estimate is accurate (update if analysis reveals different scope)
- [ ] No ambiguous requirements remain
