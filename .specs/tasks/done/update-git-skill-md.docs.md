---
title: Update create-worktree skill path convention to .worktree
issue_type: docs
complexity: S
---

## Initial User Prompt

## Overview

Change the `create-worktree` skill's path convention from **sibling directories** (`../myproject-<name>`) to a **local `.worktree/` subfolder** inside the repo (`.worktree/<name>`).

**Source file:** `plugins/git/skills/create-worktree/SKILL.md`

## Changes Required

1. Worktree Path Convention section:
- Replace sibling layout with `.worktree/` layout.
- Add rule that `.worktree/` must be listed in `.gitignore`.

2. Step 5c path convention:
- Change `../<project>-<name>` to `.worktree/<name>`.
- Keep branch naming unchanged (`<type>/<name>`), path uses normalized `<name>`.

3. All `git worktree add` command examples:
- Replace `../myproject-<name>` with `.worktree/<name>`.

4. Examples section:
- Update all `Creates:` outputs to `.worktree/<name>`.

5. Cleanup section:
- Update `git worktree remove` examples to `.worktree/<name>`.

6. Important Notes:
- Replace sibling-directory note with local-subfolder note.

## Description

This task updates the Git plugin documentation for `create-worktree` so every path example and path-convention rule consistently uses `.worktree/<name>` inside the repository. The current document mixes or relies on sibling-directory guidance, which can cause users to create worktrees outside the intended local workspace model.

The change is documentation-only and targets `plugins/git/skills/create-worktree/SKILL.md`. The business value is reduced user error and clearer operational guidance for maintainers and users creating feature, fix, refactor, hotfix, and review worktrees.

Key constraints are preserving existing branch naming semantics (`<type>/<name>`), preserving setup behavior descriptions, and updating only path-convention related content.

**Scope**:
- Included: path convention section, step instructions, command examples, cleanup examples, troubleshooting text, and important notes that reference worktree paths.
- Excluded: runtime code changes, command parser behavior changes, branch naming logic changes, and dependency-install behavior changes.

**User Scenarios**:
1. **Primary Flow**: User reads `create-worktree` docs and executes commands that create worktrees at `.worktree/<name>`.
2. **Alternative Flow**: User reads cleanup/troubleshooting sections and correctly removes or navigates `.worktree/<name>` worktrees.
3. **Error Handling**: If path already exists or setup fails, troubleshooting guidance still uses the same `.worktree/<name>` convention.

## Acceptance Criteria

### Functional Requirements

- [ ] **Path convention is updated**:
  - Given: `plugins/git/skills/create-worktree/SKILL.md` path-convention guidance
  - When: the document is reviewed
  - Then: all convention text specifies `.worktree/<name>` and no sibling-directory convention remains.

- [ ] **Instruction step references are aligned**:
  - Given: step instructions for creating worktrees
  - When: path examples are inspected
  - Then: `git worktree add` path targets are `.worktree/<name>` throughout.

- [ ] **Examples and cleanup commands are aligned**:
  - Given: examples and cleanup sections
  - When: commands and expected outputs are reviewed
  - Then: all path references use `.worktree/<name>`.

- [ ] **Important notes are aligned**:
  - Given: "Important Notes" section
  - When: local path guidance is reviewed
  - Then: it explicitly states worktrees are under `.worktree/` and mentions `.gitignore`.

- [ ] **Troubleshooting guidance is aligned**:
  - Given: troubleshooting snippets involving paths
  - When: user-facing commands are reviewed
  - Then: paths no longer reference sibling directories.

### Non-Functional Requirements

- [ ] **Consistency**: No conflicting path convention appears in the target file.
- [ ] **Clarity**: Updated guidance remains concise and unambiguous for command execution.
- [ ] **Documentation Integrity**: Existing non-path behavior descriptions remain intact.

### Definition of Done

- [ ] All acceptance criteria pass.
- [ ] Manual review confirms no `../myproject-` path remains in the target skill file.
- [ ] Task file contains architecture, implementation steps, and verification definitions.

## Architecture Overview

### References

- **Research**: `.specs/research/research-update-git-skill-md.md`
- **Codebase Analysis**: `.specs/analysis/analysis-update-git-skill-md.md`
- **Scratchpads**:
  - `.specs/scratchpad/3ecc6d27-research.md`
  - `.specs/scratchpad/30ad019b-code.md`
  - `.specs/scratchpad/08523ea9-business.md`
  - `.specs/scratchpad/47d7e9f2-architecture.md`

### Solution Strategy

Use a single-file documentation refactor strategy:
1. Update the normative convention first (`.worktree/<name>`).
2. Update all dependent command snippets and examples.
3. Perform a consistency audit to remove stale sibling-directory references.

### Key Decisions

1. **Single-source edit**: Limit direct edits to `plugins/git/skills/create-worktree/SKILL.md` to avoid unnecessary scope expansion.
2. **Convention-first ordering**: Update rule text before examples so all examples derive from one canonical convention.
3. **Consistency gate**: Require a final text audit for old path patterns before marking complete.

### Expected Changes

- `plugins/git/skills/create-worktree/SKILL.md` (modify)
  - Path convention section
  - Step instruction path-convention bullet
  - `git worktree add` examples
  - Cleanup examples
  - Important notes bullet
  - Troubleshooting path snippets

### Trade-offs

- **Accepted**: docs-only update without command runtime enforcement.
- **Rejected**: partial section edits that leave mixed path conventions.

## Implementation Process

You MUST launch for each step a separate agent, instead of performing all steps yourself. And for each step marked as parallel, you MUST launch separate agents in parallel.

**CRITICAL:** For each agent you MUST:
1. Use the **Agent** type specified in the step (e.g., `haiku`, `sonnet`, `tech-writer`)
2. Provide path to task file and prompt which step to implement
3. Require agent to implement exactly that step, not more, not less, not other steps

### Parallelization Overview

```text
Step 1 [tech-writer]
    │
    ├───────────────┬───────────────┐
    ▼               ▼               │
Step 2 [tech-writer]  Step 3 [tech-writer]
    │               │
    └───────┬───────┘
            ▼
      Step 4 [tech-writer]
```

### Step 1: Inventory path-convention references

**Model:** opus
**Agent:** tech-writer
**Depends on:** None
**Parallel with:** None

Build a complete checklist of all path-convention references in `plugins/git/skills/create-worktree/SKILL.md` that must be updated to `.worktree/<name>`.

#### Expected Output

- A verified list of all sibling-path references and their target replacement style.

#### Success Criteria

- [ ] Every section that contains sibling-path text is identified.
- [ ] Replacement style is defined consistently as `.worktree/<name>`.
- [ ] No non-path behavior changes are introduced at this stage.

#### Subtasks

- [ ] Locate path references in convention, examples, cleanup, notes, troubleshooting.
- [ ] Confirm required `.gitignore` mention is present in planned edits.
- [ ] Prepare edit checklist for parallel steps.

#### Verification

- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric (weights sum 1.0):**
  - Completeness (0.35): all relevant sections discovered.
  - Accuracy (0.30): identified references are truly path-related.
  - Clarity (0.20): checklist is actionable.
  - Scope Control (0.15): avoids unrelated edits.

---

### Step 2: Update normative convention and core instructions

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 1
**Parallel with:** Step 3

Update authoritative convention text and core instruction bullets to `.worktree/<name>`, including the dedicated path-convention section and step 5c-style guidance.

#### Expected Output

- Updated convention definition in `plugins/git/skills/create-worktree/SKILL.md`.
- Updated instruction bullets referencing `.worktree/<name>`.

#### Success Criteria

- [ ] Path-convention section uses local `.worktree/` layout.
- [ ] Instruction text no longer prescribes sibling directories.
- [ ] `.gitignore` expectation for `.worktree/` is documented in notes/convention.

#### Subtasks

- [ ] Replace sibling layout tree with `.worktree/` structure.
- [ ] Update path-convention instruction bullet to `.worktree/<name>`.
- [ ] Ensure branch naming semantics remain unchanged.

#### Verification

- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric (weights sum 1.0):**
  - Correctness (0.35): canonical convention is correct.
  - Consistency (0.30): no conflicting normative text remains.
  - Readability (0.20): instructions remain clear and concise.
  - Boundary Adherence (0.15): no behavior changes beyond docs.

---

### Step 3: Update examples, cleanup, and troubleshooting references

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 1
**Parallel with:** Step 2
**Note:** Individual command-example groups MUST be updated in parallel by multiple agents when available.

Synchronize all command examples and operational snippets with `.worktree/<name>`, including creation outputs, cleanup commands, and troubleshooting commands.

#### Expected Output

- Updated examples and snippets in `plugins/git/skills/create-worktree/SKILL.md`.

#### Success Criteria

- [ ] All `git worktree add` examples use `.worktree/<name>`.
- [ ] Cleanup examples use `.worktree/<name>`.
- [ ] Troubleshooting snippets no longer use sibling paths.

#### Subtasks

| Sub-task | Description | Agent | Can Parallel |
|----------|-------------|-------|--------------|
| examples | Update examples section commands/outputs | tech-writer | Yes |
| cleanup | Update cleanup command snippets | tech-writer | Yes |
| troubleshooting | Update troubleshooting path snippets | tech-writer | Yes |

#### Verification

- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric (weights sum 1.0):**
  - Coverage (0.35): all example/snippet groups updated.
  - Command Accuracy (0.30): syntax and paths are valid.
  - Consistency (0.20): examples match normative convention.
  - Documentation Quality (0.15): snippets remain easy to follow.

---

### Step 4: Consistency audit and final validation

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 2, Step 3
**Parallel with:** None

Perform final document audit to ensure no stale sibling-directory references remain and all acceptance criteria are satisfied.

#### Expected Output

- Final validated `plugins/git/skills/create-worktree/SKILL.md`.
- Validation notes mapped to acceptance criteria.

#### Success Criteria

- [ ] No `../myproject-` or sibling path convention remains in the target file.
- [ ] All acceptance criteria can be checked as satisfied.
- [ ] Final wording is internally consistent across sections.

#### Subtasks

- [ ] Run full-text search for stale sibling path patterns.
- [ ] Re-read modified sections for consistency.
- [ ] Confirm docs-only scope remained intact.

#### Verification

- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric (weights sum 1.0):**
  - Validation Rigor (0.35): checks are explicit and complete.
  - Defect Detection (0.30): catches any stale references.
  - Traceability (0.20): links outcomes to acceptance criteria.
  - Scope Safety (0.15): confirms no off-scope edits.

## Implementation Summary

| Step | Goal | Output | Est. Effort |
|------|------|--------|-------------|
| 1 | Build full edit inventory | Path-reference checklist | S |
| 2 | Update convention and core instructions | Canonical `.worktree` guidance | S |
| 3 | Update examples/cleanup/troubleshooting | Aligned operational snippets | S |
| 4 | Validate consistency and finalize | Ready-to-implement doc spec | S |

**Total Steps**: 4
**Critical Path**: Steps 1 -> (2,3) -> 4
**Parallel Opportunities**: Steps 2 and 3 MUST run in parallel.

## Verification Summary

- Steps with verification: 4/4
- Verification type:
  - Single Judge: 4
  - Panel: 0
  - Per-Item: 0
  - None: 0
- Total evaluations expected: 4

## Risks & Blockers Summary

### High Priority

| Risk/Blocker | Impact | Likelihood | Mitigation |
|--------------|--------|------------|------------|
| Missing one stale sibling-path reference | Med | Med | Mandatory grep-based consistency audit in Step 4 |
| Over-editing non-path behavior text | Med | Low | Enforce docs-only boundary and scope checks in each step |

## Definition of Done (Task Level)

- [ ] All implementation steps completed.
- [ ] All acceptance criteria verified.
- [ ] Final target file has consistent `.worktree/<name>` path convention.
- [ ] No high-priority risks remain unaddressed.
