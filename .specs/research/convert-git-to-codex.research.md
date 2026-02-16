# Research: Convert Git Skills to Codex Format

## Context
- Task file: `plugins/git/convert-git-to-codex.md`
- Source skills: `plugins/git/skills/*/SKILL.md` (10 files)
- Output root: `plugins/git/codex/skills/`

## Key Findings

### Source inventory
- Total skills: 10
- Command-like skills (8): `commit`, `create-pr`, `analyze-issue`, `load-issues`, `attach-review-to-pr`, `create-worktree`, `merge-worktree`, `compare-worktrees`
- Knowledge skills (2): `notes`, `worktrees`

### Frontmatter variability
- `description`: present in all source skills.
- `argument-hint`: present in command-like skills.
- `model`: present in a subset (e.g., `commit`, `create-worktree`, `merge-worktree`, `compare-worktrees`).
- `allowed-tools`: present in command-like skills.

### Deterministic conversion rules
1. Map output to `plugins/git/codex/skills/git-<name>/SKILL.md`.
2. Output frontmatter contains lowercase keys only:
- `name: git-<name>`
- `description: "<rewritten text>. Also invoked as $git:<name>."`
3. Remove unsupported keys from output:
- `model`
- `allowed-tools`
- `argument-hint`
4. Body normalization:
- Convert tool restrictions to natural-language command directions.
- Replace `/git:<name>` with `$git-<name>`.
- Replace `.claude/commands/load-issues.md` with `$git-load-issues` skill reference.
- Remove `Read/Write/Glob/Grep` tool API mentions.
- Remove `[OBFUSCATED PROMPT INJECTION]` markers when present.

## Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| Incorrect alias formatting in description | Skill discovery inconsistency | Enforce terminal phrase: `Also invoked as $git:<name>.` |
| Missed slash-invocation replacement in examples | Runtime ambiguity | Regex scan for `/git:` after conversion |
| Over-aggressive body rewrite degrades guidance | Behavior drift | Preserve markdown structure and only normalize restricted syntax |
| Cross-skill reference mismatch (`load-issues`) | Broken workflow links | Force replacement to `$git-load-issues` |

## Validation Checklist
- 10 destination folders exist, each with one `SKILL.md`.
- Each file has frontmatter keys: `name`, `description` only.
- `name` equals folder name exactly.
- No forbidden tokens remain:
  - `model:`
  - `allowed-tools:`
  - `Bash(`
  - `Read(`
  - `Write(`
  - `Glob(`
  - `Grep(`
  - `/git:`
  - `[OBFUSCATED PROMPT INJECTION]`

## Output Artifact Targets
- `plugins/git/codex/skills/git-commit/SKILL.md`
- `plugins/git/codex/skills/git-create-pr/SKILL.md`
- `plugins/git/codex/skills/git-analyze-issue/SKILL.md`
- `plugins/git/codex/skills/git-load-issues/SKILL.md`
- `plugins/git/codex/skills/git-attach-review-to-pr/SKILL.md`
- `plugins/git/codex/skills/git-create-worktree/SKILL.md`
- `plugins/git/codex/skills/git-merge-worktree/SKILL.md`
- `plugins/git/codex/skills/git-compare-worktrees/SKILL.md`
- `plugins/git/codex/skills/git-notes/SKILL.md`
- `plugins/git/codex/skills/git-worktrees/SKILL.md`
