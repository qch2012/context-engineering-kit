---
title: Update create-worktree skill path convention to worktree/
issue_type: docs
complexity: S
---

## Initial User Prompt

## Overview

Change the `create-worktree` skill's path convention from **sibling directories** (`../myproject-<name>`) to a **local `worktree/` subfolder** inside the repo (`worktree/<name>`).

**Source file:** `plugins/git/skills/create-worktree/SKILL.md`

## Changes Required

### 1. Worktree Path Convention section

Replace the sibling directory layout with:

```text
myproject/                          # Main worktree (current directory)
├── .git/
├── .gitignore                      # Must include "worktree/"
├── worktree/
│   ├── add-auth/                   # feature/add-auth
│   ├── critical-bug/               # hotfix/critical-bug
│   └── pr-456/                     # review/pr-456
├── src/
└── ...
```

**Naming rules:**

- Pattern: `worktree/<name>` (uses the name part, NOT the full branch)
- Branch name: `<type>/<name>` (e.g., `feature/add-auth`)
- Directory name uses only the `<name>` portion for brevity
- Add `worktree/` to `.gitignore` so worktree folders are never committed

### 2. Step 5c — Path convention

Change from:

```text
c. **Path convention**: Use sibling directory with pattern ../<project>-<name>
```

To:

```text
c. **Path convention**: Use `worktree/` subfolder with pattern `worktree/<name>`
   - Use the normalized name (NOT the full branch with prefix)
   - Example: `feature/auth-system` → `worktree/auth-system`
```

### 3. All `git worktree add` commands

Change `../myproject-<name>` -> `worktree/<name>`.

### 4. Examples section

Update all `Creates:` entries to `worktree/<name>`.

### 5. Cleanup section

Update `git worktree remove` examples to `worktree/<name>`.

### 6. Important Notes bullet

Replace sibling directory note with local subfolder note and `.gitignore` requirement.

## Description

This task standardizes the `create-worktree` skill documentation around a local `worktree/<name>` directory structure inside the repository. The existing guidance uses sibling-directory conventions that can produce confusing instructions and inconsistent user behavior when creating, cleaning up, or troubleshooting worktrees.

The intended outcome is a single canonical path convention used everywhere in `plugins/git/skills/create-worktree/SKILL.md`: instructions, path-convention section, command examples, cleanup commands, and troubleshooting snippets. This improves reliability of operational documentation and reduces user mistakes.

The task is documentation-only. It must preserve branch naming semantics (`<type>/<name>`) and existing setup behavior descriptions.

**Scope**:
- Included: path-convention text, examples, cleanup snippets, troubleshooting path references, and notes about `.gitignore`.
- Excluded: runtime command behavior changes, parser changes, branch naming logic changes, and dependency-install logic changes.

**User Scenarios**:
1. **Primary Flow**: A user follows command examples and creates worktrees at `worktree/<name>`.
2. **Alternative Flow**: A user removes worktrees using cleanup commands that target `worktree/<name>`.
3. **Error Handling**: A user troubleshooting failed setup can navigate directly to `worktree/<name>` using matching documentation.

## Acceptance Criteria

### Functional Requirements

- [ ] **Canonical convention updated**
  - Given: `plugins/git/skills/create-worktree/SKILL.md`
  - When: path convention sections are reviewed
  - Then: they define `worktree/<name>` and no sibling-directory convention remains.

- [ ] **Instruction commands updated**
  - Given: step instructions for branch resolution and path convention
  - When: command examples are reviewed
  - Then: `git worktree add` examples use `worktree/<name>`.

- [ ] **Examples updated**
  - Given: examples section
  - When: `Creates:` outputs are reviewed
  - Then: all use `worktree/<name>`.

- [ ] **Cleanup and troubleshooting updated**
  - Given: cleanup and troubleshooting sections
  - When: path references are reviewed
  - Then: all use `worktree/<name>`.

- [ ] **Important notes updated**
  - Given: Important Notes section
  - When: worktree location guidance is reviewed
  - Then: it states worktrees are under `worktree/` and mentions `.gitignore`.

### Non-Functional Requirements

- [ ] **Consistency**: No conflicting path patterns remain in the file.
- [ ] **Clarity**: Updated language is concise and unambiguous.
- [ ] **Scope Safety**: No non-documentation behavior is changed.

### Definition of Done

- [ ] All acceptance criteria pass.
- [ ] Manual grep confirms legacy sibling-path references are removed in target file.
- [ ] Task execution summary records modified sections.

## Architecture Overview

### References

- Research: `.specs/research/research-update-worktree-skill.md`
- Analysis: `.specs/analysis/analysis-update-worktree-skill.md`
- Scratchpads:
  - `.specs/scratchpad/cc5fb681-update-worktree-skill-research.md`
  - `.specs/scratchpad/1b3922c1-update-worktree-skill-code.md`
  - `.specs/scratchpad/e1327215-update-worktree-skill-business.md`
  - `.specs/scratchpad/61b51198-update-worktree-skill-architecture.md`

### Solution Strategy

Apply a convention-first documentation update:
1. Update normative path-convention instructions.
2. Synchronize all examples and operational snippets to the same convention.
3. Perform a final stale-reference audit for consistency.

### Key Decisions

1. Use `worktree/<name>` uniformly across all path-bearing sections.
2. Keep branch naming format unchanged (`<type>/<name>`).
3. Restrict changes to documentation language only.

### Expected Changes

- `plugins/git/skills/create-worktree/SKILL.md` (modify)
- `.gitignore` (validate/add `worktree/` if missing)

## Implementation Process

You MUST launch for each step a separate agent, instead of performing all steps yourself. And for each step marked as parallel, you MUST launch separate agents in parallel.

**CRITICAL:** For each agent you MUST:
1. Use the **Agent** type specified in the step.
2. Provide task-file path and exact step scope.
3. Require implementation of that step only.

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

### Step 1: Inventory all legacy path references

**Model:** opus
**Agent:** tech-writer
**Depends on:** None
**Parallel with:** None

#### Expected Output
- Section-by-section inventory of sibling-path references and target replacements.

#### Success Criteria
- [ ] Every path-bearing section is listed.
- [ ] Replacement format is explicitly `worktree/<name>`.
- [ ] No unrelated edits are introduced.

#### Subtasks
- [ ] Identify references in instructions and path-convention section.
- [ ] Identify references in examples, cleanup, and troubleshooting.
- [ ] Identify note(s) requiring `.gitignore` mention.

#### Verification
- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric:** Completeness (0.35), Accuracy (0.30), Clarity (0.20), Scope Control (0.15)

---

### Step 2: Update normative convention and instruction text

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 1
**Parallel with:** Step 3

#### Expected Output
- Updated path-convention and branch-resolution instruction blocks.

#### Success Criteria
- [ ] Normative convention uses `worktree/<name>`.
- [ ] Instruction examples no longer use sibling paths.
- [ ] `.gitignore` requirement for `worktree/` is present where appropriate.

#### Subtasks
- [ ] Update branch-resolution command examples.
- [ ] Update path convention subsection and naming rules.
- [ ] Preserve branch naming semantics.

#### Verification
- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric:** Correctness (0.35), Consistency (0.30), Readability (0.20), Boundary Adherence (0.15)

---

### Step 3: Update examples, cleanup, and troubleshooting

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 1
**Parallel with:** Step 2

#### Expected Output
- Updated operational examples and path snippets.

#### Success Criteria
- [ ] Examples `Creates:` lines use `worktree/<name>`.
- [ ] Cleanup commands use `worktree/<name>`.
- [ ] Troubleshooting path navigation uses `worktree/<name>`.

#### Subtasks
| Sub-task | Description | Agent | Can Parallel |
|----------|-------------|-------|--------------|
| examples | Update example output paths | tech-writer | Yes |
| cleanup | Update worktree remove paths | tech-writer | Yes |
| troubleshooting | Update manual navigation path | tech-writer | Yes |

#### Verification
- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric:** Coverage (0.35), Command Accuracy (0.30), Consistency (0.20), Documentation Quality (0.15)

---

### Step 4: Consistency audit and final validation

**Model:** opus
**Agent:** tech-writer
**Depends on:** Step 2, Step 3
**Parallel with:** None

#### Expected Output
- Final validated documentation update and checklist against acceptance criteria.

#### Success Criteria
- [ ] No stale sibling path references remain in target file.
- [ ] Acceptance criteria map to explicit evidence.
- [ ] Change remains documentation-only.

#### Subtasks
- [ ] Run grep for stale patterns.
- [ ] Re-read all edited sections for internal consistency.
- [ ] Confirm no off-scope changes.

#### Verification
- **Level:** Single Judge
- **Threshold:** 4.0/5.0
- **Rubric:** Validation Rigor (0.35), Defect Detection (0.30), Traceability (0.20), Scope Safety (0.15)

## Implementation Summary

| Step | Goal | Output | Effort |
|------|------|--------|--------|
| 1 | Inventory references | Replacement checklist | S |
| 2 | Update convention text | Canonical path rules | S |
| 3 | Update operational snippets | Aligned examples/cleanup/troubleshooting | S |
| 4 | Validate and finalize | Consistent final doc update | S |

**Total Steps:** 4
**Critical Path:** 1 -> (2,3) -> 4
**Parallel Opportunities:** Steps 2 and 3 MUST run in parallel.

## Verification Summary

- Steps with verification: 4/4
- Verification type:
  - Single Judge: 4
  - Panel: 0
  - Per-Item: 0
  - None: 0
- Total evaluations expected: 4

## Risks & Blockers Summary

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Missed stale path reference | Medium | Medium | Step 4 grep + section re-read |
| Scope creep into behavior changes | Medium | Low | Explicit docs-only boundary in each step |
