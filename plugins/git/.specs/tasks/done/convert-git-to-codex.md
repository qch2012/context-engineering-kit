---
title: Convert Git Plugin Skills to Codex Format
status: todo
issue_type: refactor
complexity: L
---

## Initial User Prompt

## Task

Convert the Git plugin from Claude Code format to OpenAI Codex CLI skills.

- **Source:** `/Users/andy/github/claude/context-engineering-kit/plugins/git/`
- **Output:** `plugins/git/codex/skills/`

---

## Step 1 — Read Source

Read all `SKILL.md` files under `plugins/git/skills/`. There are 10 skills total (8 command, 2 knowledge).

## Step 2 — Create Output Directory

```bash
mkdir -p plugins/git/codex/skills
```

## Step 3 — Convert Each Skill

For each `plugins/git/skills/<name>/SKILL.md`, create `plugins/git/codex/skills/git-<name>/SKILL.md`.

**Front-matter rules (lowercase keys only):**

- `name:` → `git-<name>` (must match folder name)
- `description:` → rewrite from original `description` + `argument-hint`, append `Also invoked as $git:<name>.`
- **Drop** `model:` and `allowed-tools:` — not supported in Codex

**Body rules:**

- Copy markdown body after front-matter
- Replace `Bash(git status:*)` style tool restrictions → natural language ("Run `git status`")
- Replace `/git:<name>` slash invocation → `$git-<name>`
- Replace `.claude/commands/load-issues.md` → "use the `$git-load-issues` skill"
- Replace `Read`, `Write`, `Glob`, `Grep` tool refs → natural language
- Remove all `[OBFUSCATED PROMPT INJECTION]` markers
- Preserve file system paths (`./specs/issues/`, etc.)

**Skill mapping (10 total):**

```
skills/commit/           → git-commit/SKILL.md
skills/create-pr/        → git-create-pr/SKILL.md
skills/analyze-issue/    → git-analyze-issue/SKILL.md
skills/load-issues/      → git-load-issues/SKILL.md
skills/attach-review-to-pr/ → git-attach-review-to-pr/SKILL.md
skills/create-worktree/  → git-create-worktree/SKILL.md
skills/merge-worktree/   → git-merge-worktree/SKILL.md
skills/compare-worktrees/ → git-compare-worktrees/SKILL.md
skills/notes/            → git-notes/SKILL.md
skills/worktrees/        → git-worktrees/SKILL.md
```

**Cross-skill reference replacements:**

```
/git:commit                          → $git-commit
/git:create-worktree                 → $git-create-worktree
/git:compare-worktrees               → $git-compare-worktrees
.claude/commands/load-issues.md      → $git-load-issues
```

**Example output — `git-commit/SKILL.md`:**

```markdown
---
name: git-commit
description: "Create well-formatted commits with conventional commit messages and emoji. Optional flags like --no-verify to skip pre-commit checks. Also invoked as $git:commit."
---

(Body from original SKILL.md with Bash() restrictions replaced by natural language.)
```

## Step 4 — Validate

For each of the 10 output skills, verify:

- `SKILL.md` exists with `name` and `description` in lowercase front-matter
- `name` matches folder name exactly
- No `model:`, `allowed-tools:`, `Bash()`, `Read()`, `Write()`, `Glob()`, `Grep()` remain
- No `/git:` slash syntax remains
- No `[OBFUSCATED PROMPT INJECTION]` markers remain
- Each `description` ends with colon-variant alias

## Completion Criteria

```
plugins/git/codex/skills/
├── git-commit/SKILL.md
├── git-create-pr/SKILL.md
├── git-analyze-issue/SKILL.md
├── git-load-issues/SKILL.md
├── git-attach-review-to-pr/SKILL.md
├── git-create-worktree/SKILL.md
├── git-merge-worktree/SKILL.md
├── git-compare-worktrees/SKILL.md
├── git-notes/SKILL.md
└── git-worktrees/SKILL.md
```

10 directories, each containing one `SKILL.md` with valid front-matter and cleaned body. No other files needed.

## Description

This task migrates the Git plugin skill definitions from Claude-specific format to Codex-compatible skill format, producing 10 output skills under `plugins/git/codex/skills/` with exact one-to-one mapping and deterministic naming (`git-<skill-name>`). The migration keeps the operational meaning of each skill while removing unsupported metadata and tool-restriction syntax.

The business value is cross-runtime portability and consistency. Codex consumers need skill files with stable frontmatter, normalized invocation syntax, and clean cross-skill references. The migration reduces maintenance overhead by codifying a repeatable compatibility contract and objective validation checks.

Primary stakeholders are plugin maintainers and users invoking Git skills in Codex. The task is constrained to file-based migration in the Git plugin scope only; no source behavior changes, no non-git plugin conversion, and no docs/plugin manifest updates are included.

**Scope**:
- Included: Conversion of all 10 mapped source skills, frontmatter rewrite, syntax normalization, cross-reference replacement, and completion validation.
- Excluded: Changes to source skill intent/behavior, changes outside `plugins/git/skills` and `plugins/git/codex/skills`, and repo-wide migration automation.

**User Scenarios**:
1. **Primary Flow**: A maintainer converts all 10 skills and confirms output passes all structural and content checks.
2. **Alternative Flow**: Knowledge skills with minimal frontmatter are converted using the same naming and alias conventions.
3. **Error Handling**: Validation detects missing outputs or forbidden syntax and reports exact files for correction.

## Acceptance Criteria

### Functional Requirements

- [ ] **Exact output mapping is complete**
  - Given: The 10 source skills exist under `plugins/git/skills/`
  - When: Migration is executed
  - Then: Exactly 10 output files exist at the mapped paths under `plugins/git/codex/skills/git-*/SKILL.md`

- [ ] **Codex frontmatter format is enforced**
  - Given: An output skill file exists
  - When: Its frontmatter is inspected
  - Then: It contains lowercase keys only and includes `name` and `description` fields, with no `model`, `allowed-tools`, or `argument-hint`

- [ ] **Output naming is deterministic**
  - Given: An output folder `plugins/git/codex/skills/git-<name>/`
  - When: `SKILL.md` is inspected
  - Then: Frontmatter `name` equals `git-<name>` exactly

- [ ] **Required compatibility replacements are applied**
  - Given: Converted output files
  - When: Content is scanned
  - Then: `/git:*` slash invocations are replaced with `$git-*` forms and `.claude/commands/load-issues.md` references are replaced with `$git-load-issues`

- [ ] **Unsupported syntax is fully removed**
  - Given: Converted output files
  - When: Content is scanned for forbidden patterns
  - Then: No `Bash(`, `Read(`, `Write(`, `Glob(`, `Grep(`, or `[OBFUSCATED PROMPT INJECTION]` markers remain

- [ ] **Description aliases are consistent**
  - Given: Every output skill description
  - When: Trailing alias text is checked
  - Then: The description ends with `Also invoked as $git:<name>.`

### Non-Functional Requirements

- [ ] **Consistency**: 100% of output files follow the same frontmatter and alias convention.
- [ ] **Preservation**: Body guidance remains semantically equivalent except required compatibility rewrites.
- [ ] **Validation Performance**: Full output validation completes in under 5 seconds on local workspace.

### Definition of Done

- [ ] All acceptance criteria pass
- [ ] Validation evidence is captured in task artifacts
- [ ] Output tree contains only the 10 required skill directories and `SKILL.md` files
- [ ] Review confirms no unsupported syntax remains

## Architecture Overview

### References
- Research: `.specs/research/convert-git-to-codex.research.md`
- Codebase analysis: `.specs/analysis/convert-git-to-codex.analysis.md`
- Scratchpads:
  - `.specs/scratchpad/4e86fde2.md`
  - `.specs/scratchpad/d9c9d228.md`
  - `.specs/scratchpad/f591bb7a.md`

### Solution Strategy

Use a deterministic two-stage migration pipeline:
1. **Conversion stage**: Build output directory tree and transform each source skill into Codex format with exact mapping, frontmatter normalization, and body compatibility rewrites.
2. **Validation stage**: Run objective structural and token-level checks to guarantee naming parity, forbidden syntax removal, and alias consistency.

This architecture minimizes behavior drift by preserving body semantics and limiting edits to known compatibility boundaries.

### Architecture Decomposition

| Component | Responsibility | Input | Output |
|-----------|----------------|-------|--------|
| Mapping Engine | Resolve 10 fixed source->target paths | source skill path list | output path list |
| Metadata Normalizer | Convert frontmatter to Codex schema | source frontmatter | `name` + `description` |
| Content Normalizer | Apply required replacements and cleanup | source body markdown | codex-compatible body |
| Validation Gate | Enforce completion criteria | converted output tree | pass/fail evidence |

### Architecture Decisions

1. **One-to-one file mapping with namespace prefix**
- Decision: output folder names always use `git-<source-name>`.
- Rationale: ensures stable, unambiguous skill namespace in Codex.

2. **Conservative body rewrite policy**
- Decision: only compatibility-driven lexical rewrites are allowed.
- Rationale: preserves original operational guidance and reduces migration risk.

3. **Regex-backed quality gate**
- Decision: enforce no-forbidden-token contract using deterministic text scans.
- Rationale: objective pass/fail avoids subjective review gaps.

### High-Level Structure

```text
Source Skills (10)
      |
      v
Path + Metadata + Body Transformation
      |
      v
Codex Skills (10)
      |
      v
Validation Gate (structure + syntax + alias)
```

### Workflow Steps

1. Create output root and required destination folders.
2. Build source metadata inventory from all 10 source skills.
3. Convert command-like skills in parallel.
4. Convert knowledge skills in parallel.
5. Apply cross-file normalization and run forbidden-token sweeps.
6. Validate completion and produce migration evidence summary.

## Implementation Process

You MUST launch for each step a separate agent, instead of performing all steps yourself. And for each step marked as parallel, you MUST launch separate agents in parallel.

**CRITICAL:** For each agent you MUST:
1. Use the **Agent** type specified in the step (e.g., `haiku`, `sonnet`, `tech-writer`)
2. Provide path to task file and prompt which step to implement
3. Require agent to implement exactly that step, not more, not less, not other steps

### Implementation Strategy

**Approach**: Mixed
**Rationale**: Foundational setup and inventory are bottom-up prerequisites; conversion and validation run top-down with explicit dependency gates.

### Parallelization Overview

```text
Step 1 [haiku]
    │
Step 2 [haiku]
    │
    ├───────────┬───────────┐
    ▼           ▼
Step 3 [opus] Step 4 [opus]
    │           │
    └─────┬─────┘
          ▼
      Step 5 [opus]
          │
          ▼
      Step 6 [haiku]
```

### Phase Overview

```text
Phase 1: Setup + Inventory
    │
    ▼
Phase 2: Parallel Conversion
    │
    ▼
Phase 3: Normalization + Validation
```

### Step 1: Create Output Structure

**Model:** haiku
**Agent:** developer
**Depends on:** None
**Parallel with:** None

**Goal**: Create deterministic destination root and all 10 target skill directories.

#### Expected Output

- `plugins/git/codex/skills/` exists.
- 10 directories exist with names matching `git-<skill-name>` mapping.

#### Success Criteria

- [ ] Output root is created.
- [ ] All 10 mapped destination directories exist.

#### Verification

**Level:** ❌ NOT NEEDED
**Rationale:** Simple directory operations with binary success/failure.

#### Subtasks

- [ ] Create `plugins/git/codex/skills`.
- [ ] Create `git-commit`, `git-create-pr`, `git-analyze-issue`, `git-load-issues`, `git-attach-review-to-pr`, `git-create-worktree`, `git-merge-worktree`, `git-compare-worktrees`, `git-notes`, `git-worktrees` directories.

---

### Step 2: Build Source Metadata Inventory

**Model:** haiku
**Agent:** code-explorer
**Depends on:** Step 1
**Parallel with:** None

**Goal**: Collect per-skill source metadata and replacement requirements used by conversion steps.

#### Expected Output

- Inventory table for 10 source skills: source name, description, argument-hint, unsupported keys present.
- Replacement checklist per source file for `/git:*`, tool syntax, and legacy path references.

#### Success Criteria

- [ ] Inventory includes all 10 source skills.
- [ ] Each row captures whether `argument-hint`, `model`, and `allowed-tools` exist.
- [ ] Replacement checklist identifies files requiring cross-skill/path rewrites.

#### Verification

**Level:** ✅ Single Judge
**Artifact:** `.specs/analysis/convert-git-to-codex.analysis.md`
**Threshold:** 4.0/5.0

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Coverage | 0.30 | Includes all mapped source skills |
| Accuracy | 0.30 | Metadata and rewrite needs are correctly captured |
| Clarity | 0.20 | Table and findings are understandable and actionable |
| Consistency | 0.20 | Naming and path conventions align with task mapping |

**Reference Pattern:** `plugins/sdd/codex/skills/sdd-plan/SKILL.md`

#### Subtasks

- [ ] Enumerate all source skill files.
- [ ] Extract frontmatter fields required for conversion.
- [ ] Capture per-file rewrite triggers.

---

### Step 3: Convert Command Skills (8 files)

**Model:** opus
**Agent:** developer
**Depends on:** Step 1, Step 2
**Parallel with:** Step 4
**Note:** Individual command skills MUST be converted in parallel by multiple agents.

**Goal**: Convert the 8 command-like Git skills into Codex format with compliant frontmatter and cleaned body content.

#### Expected Output

- `plugins/git/codex/skills/git-commit/SKILL.md`
- `plugins/git/codex/skills/git-create-pr/SKILL.md`
- `plugins/git/codex/skills/git-analyze-issue/SKILL.md`
- `plugins/git/codex/skills/git-load-issues/SKILL.md`
- `plugins/git/codex/skills/git-attach-review-to-pr/SKILL.md`
- `plugins/git/codex/skills/git-create-worktree/SKILL.md`
- `plugins/git/codex/skills/git-merge-worktree/SKILL.md`
- `plugins/git/codex/skills/git-compare-worktrees/SKILL.md`

#### Success Criteria

- [ ] All 8 output files exist at mapped locations.
- [ ] Each file has `name` matching folder name and description with alias suffix.
- [ ] No unsupported frontmatter fields remain.
- [ ] Body syntax is normalized for Codex compatibility.

#### Verification

**Level:** ✅ Per-Item Judges (8 separate evaluations in parallel)
**Artifacts:** `plugins/git/codex/skills/{git-commit,git-create-pr,git-analyze-issue,git-load-issues,git-attach-review-to-pr,git-create-worktree,git-merge-worktree,git-compare-worktrees}/SKILL.md`
**Threshold:** 4.0/5.0

**Rubric (per skill file):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Frontmatter Correctness | 0.30 | Name/description schema is valid and complete |
| Instruction Preservation | 0.25 | Core operational guidance remains semantically intact |
| Compatibility Cleanup | 0.25 | Unsupported syntax is fully removed/replaced |
| Cross-Reference Accuracy | 0.20 | Skill references use required `$git-*` forms |

**Reference Pattern:** `plugins/sdd/codex/skills/sdd-agent-tech-lead/SKILL.md`

#### Subtasks

| Sub-task | Description | Agent | Can Parallel |
|----------|-------------|-------|--------------|
| cmd-1 | Convert `commit` | opus | Yes |
| cmd-2 | Convert `create-pr` | opus | Yes |
| cmd-3 | Convert `analyze-issue` | opus | Yes |
| cmd-4 | Convert `load-issues` | opus | Yes |
| cmd-5 | Convert `attach-review-to-pr` | opus | Yes |
| cmd-6 | Convert `create-worktree` | opus | Yes |
| cmd-7 | Convert `merge-worktree` | opus | Yes |
| cmd-8 | Convert `compare-worktrees` | opus | Yes |

---

### Step 4: Convert Knowledge Skills (2 files)

**Model:** opus
**Agent:** developer
**Depends on:** Step 1, Step 2
**Parallel with:** Step 3
**Note:** Individual knowledge skills MUST be converted in parallel by multiple agents.

**Goal**: Convert `notes` and `worktrees` skill documents to Codex format while preserving knowledge content.

#### Expected Output

- `plugins/git/codex/skills/git-notes/SKILL.md`
- `plugins/git/codex/skills/git-worktrees/SKILL.md`

#### Success Criteria

- [ ] Both knowledge output files exist at mapped locations.
- [ ] Frontmatter matches Codex format with alias-ending descriptions.
- [ ] No forbidden syntax remains in either file.

#### Verification

**Level:** ✅ Per-Item Judges (2 separate evaluations in parallel)
**Artifacts:** `plugins/git/codex/skills/{git-notes,git-worktrees}/SKILL.md`
**Threshold:** 4.0/5.0

**Rubric (per skill file):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Frontmatter Correctness | 0.30 | Name/description schema is valid |
| Content Integrity | 0.30 | Knowledge guidance remains intact |
| Compatibility Cleanup | 0.25 | Unsupported syntax removed |
| Invocation Consistency | 0.15 | Alias and invocation conventions match task |

**Reference Pattern:** `plugins/sdd/codex/skills/sdd-create-ideas/SKILL.md`

#### Subtasks

| Sub-task | Description | Agent | Can Parallel |
|----------|-------------|-------|--------------|
| know-1 | Convert `notes` | opus | Yes |
| know-2 | Convert `worktrees` | opus | Yes |

---

### Step 5: Run Global Normalization and Compatibility Sweep

**Model:** opus
**Agent:** developer
**Depends on:** Step 3, Step 4
**Parallel with:** None

**Goal**: Enforce global consistency and perform final compatibility cleanup across all output files.

#### Expected Output

- Output tree with all 10 files cleaned and consistent.
- Sweep results confirming zero forbidden syntax matches.

#### Success Criteria

- [ ] No output file contains forbidden tokens (`model:`, `allowed-tools:`, `Bash(`, `Read(`, `Write(`, `Glob(`, `Grep(`, `/git:`).
- [ ] No output file contains `.claude/commands/load-issues.md`.
- [ ] All descriptions end with required alias form.
- [ ] All `name` fields match folder names exactly.

#### Verification

**Level:** ✅ CRITICAL - Panel of 2 Judges with Aggregated Voting
**Artifact:** `plugins/git/codex/skills/`
**Threshold:** 4.0/5.0

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Completeness | 0.30 | All 10 outputs exist and are in expected paths |
| Compatibility | 0.30 | No unsupported syntax remains |
| Consistency | 0.20 | Naming/alias formatting is uniform |
| Fidelity | 0.20 | Converted guidance remains aligned with source intent |

**Reference Pattern:** `plugins/sdd/codex/skills/`

#### Subtasks

- [ ] Run forbidden-token scans over output tree.
- [ ] Run name/folder parity check for all outputs.
- [ ] Run alias suffix consistency check.
- [ ] Apply fixes for any violations found.

---

### Step 6: Final Validation and Completion Report

**Model:** haiku
**Agent:** tech-writer
**Depends on:** Step 5
**Parallel with:** None

**Goal**: Produce final completion evidence aligned with task completion criteria.

#### Expected Output

- Validation summary listing 10 output paths and check status.
- Brief completion note with pass/fail for each required rule.

#### Success Criteria

- [ ] Completion report confirms 10/10 required outputs exist.
- [ ] Completion report confirms all rule checks pass.
- [ ] Report includes any remediation done to resolve validation failures.

#### Verification

**Level:** ✅ Single Judge
**Artifact:** `plugins/git/convert-git-to-codex.md`
**Threshold:** 4.0/5.0

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Accuracy | 0.35 | Summary matches actual output state |
| Completeness | 0.30 | All required checks/results are reported |
| Clarity | 0.20 | Result is concise and unambiguous |
| Traceability | 0.15 | Report references concrete paths/checks |

**Reference Pattern:** `.specs/research/convert-git-to-codex.research.md`

#### Subtasks

- [ ] Compile final results for each acceptance criterion.
- [ ] Document pass/fail status per validation check.
- [ ] Capture unresolved issues (if any) with actionable follow-up.

## Implementation Summary

| Step | Goal | Output | Est. Effort |
|------|------|--------|-------------|
| 1 | Create output structure | 10 destination folders | S |
| 2 | Build source inventory | metadata and rewrite matrix | S |
| 3 | Convert command skills | 8 codex `SKILL.md` files | M |
| 4 | Convert knowledge skills | 2 codex `SKILL.md` files | S |
| 5 | Normalize + sweep | globally consistent outputs | M |
| 6 | Validate + report | completion evidence | S |

**Total Steps**: 6
**Critical Path**: Steps 1 -> 2 -> (3+4) -> 5 -> 6
**Parallel Opportunities**: Step 3 and Step 4 MUST be done in parallel.

## Verification Summary

| Step | Verification Level | Judges | Threshold | Artifacts |
|------|-------------------|--------|-----------|-----------|
| 1 | ❌ None | - | - | Output directory creation |
| 2 | ✅ Single Judge | 1 | 4.0/5.0 | `.specs/analysis/convert-git-to-codex.analysis.md` |
| 3 | ✅ Per-Item | 8 | 4.0/5.0 | 8 command skill files |
| 4 | ✅ Per-Item | 2 | 4.0/5.0 | 2 knowledge skill files |
| 5 | ✅ Panel (2) | 2 | 4.0/5.0 | `plugins/git/codex/skills/` |
| 6 | ✅ Single Judge | 1 | 4.0/5.0 | `plugins/git/convert-git-to-codex.md` |

**Total Evaluations:** 14
**Implementation Command:** `$sdd-implement $TASK_FILE`

## Risks & Blockers Summary

### High Priority

| Risk/Blocker | Impact | Likelihood | Mitigation |
|--------------|--------|------------|------------|
| Residual forbidden syntax in long skill documents | High | Medium | Run regex sweep and fix iteratively before final validation |
| Incorrect cross-skill replacements | High | Medium | Use explicit replacement table and targeted scans for `/git:` and legacy paths |
| Name/alias mismatch across files | Medium | Medium | Add deterministic checks for `name`/folder parity and description suffix |

## Definition of Done (Task Level)

- [ ] All implementation steps completed
- [ ] All acceptance criteria verified
- [ ] Validation checks pass with zero critical findings
- [ ] Output tree contains all 10 expected codex skills and no forbidden syntax
- [ ] Migration evidence is documented and review-ready

## Execution Results (2026-02-16)

### Implemented Outputs

- `codex/skills/git-commit/SKILL.md`
- `codex/skills/git-create-pr/SKILL.md`
- `codex/skills/git-analyze-issue/SKILL.md`
- `codex/skills/git-load-issues/SKILL.md`
- `codex/skills/git-attach-review-to-pr/SKILL.md`
- `codex/skills/git-create-worktree/SKILL.md`
- `codex/skills/git-merge-worktree/SKILL.md`
- `codex/skills/git-compare-worktrees/SKILL.md`
- `codex/skills/git-notes/SKILL.md`
- `codex/skills/git-worktrees/SKILL.md`

### Validation Outcomes

- Output count check: pass (`10` skill files)
- Frontmatter schema check (`name`, `description` only): pass
- Folder/name parity check: pass
- Forbidden token sweep (`model`, `allowed-tools`, `argument-hint`, `Bash(`, `Read(`, `Write(`, `Glob(`, `Grep(`, `/git:`, obfuscated marker, legacy `.claude/commands/load-issues.md`): pass
- Cross-reference replacement checks (`$git-*` skill refs and `$git-load-issues`): pass
- Description alias suffix checks (`Also invoked as $git:<name>.`): pass

### Notes

- Judge evaluations were not run in this execution.
- No automated test suite was run; verification was file-structure and content-rule based per task criteria.
