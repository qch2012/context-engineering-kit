# Codebase Analysis: Convert Git Skills to Codex Format

## Task Scope
- Primary spec: `plugins/git/convert-git-to-codex.md`
- Inputs: `plugins/git/skills/*/SKILL.md` (10 files)
- Outputs: `plugins/git/codex/skills/git-*/SKILL.md` (10 files)

## Files and Responsibilities

| File/Area | Role in Flow | Planned Action |
|-----------|--------------|----------------|
| `plugins/git/convert-git-to-codex.md` | Source task definition | Preserve as requirement source |
| `plugins/git/skills/analyze-issue/SKILL.md` | Source command skill | Convert to codex format and normalize load-issues ref |
| `plugins/git/skills/attach-review-to-pr/SKILL.md` | Source command skill | Convert frontmatter + remove tool restrictions |
| `plugins/git/skills/commit/SKILL.md` | Source command skill | Convert frontmatter + natural-language command refs |
| `plugins/git/skills/compare-worktrees/SKILL.md` | Source command skill | Convert + replace all `/git:` references |
| `plugins/git/skills/create-pr/SKILL.md` | Source command skill | Convert + replace cross-skill references |
| `plugins/git/skills/create-worktree/SKILL.md` | Source command skill | Convert + replace slash invocations in examples |
| `plugins/git/skills/load-issues/SKILL.md` | Source command skill | Convert + clean tool API mentions |
| `plugins/git/skills/merge-worktree/SKILL.md` | Source command skill | Convert + normalize compare/create worktree refs |
| `plugins/git/skills/notes/SKILL.md` | Source knowledge skill | Convert minimal frontmatter only |
| `plugins/git/skills/worktrees/SKILL.md` | Source knowledge skill | Convert minimal frontmatter only |
| `plugins/git/codex/skills/` | Target codex skill root | Create `git-*` directories and `SKILL.md` files |

## Pattern Findings

### Frontmatter differences
- Source includes unsupported keys for Codex:
  - `model`
  - `allowed-tools`
- Some skills include `argument-hint` which must be folded into description then removed.

### Body syntax requiring normalization
- Tool policy syntax from Claude commands:
  - `Bash(...)`
  - `Read`, `Write`, `Glob`, `Grep`
- Slash command references:
  - `/git:*` forms
- Specific legacy path reference:
  - `.claude/commands/load-issues.md`

## Dependency and Ordering
1. Create output root and all 10 destination folders.
2. Inventory source metadata for deterministic frontmatter rewriting.
3. Convert 8 command skills in parallel (same dependency set).
4. Convert 2 knowledge skills in parallel.
5. Run global normalization/lint checks.
6. Produce completion report aligned with task completion criteria.

## Validation Commands (planned)
- `find plugins/git/codex/skills -maxdepth 2 -name SKILL.md | wc -l`
- `rg -n "^(model|allowed-tools):|Bash\(|Read\(|Write\(|Glob\(|Grep\(|/git:|\[OBFUSCATED PROMPT INJECTION\]" plugins/git/codex/skills`
- `for f in plugins/git/codex/skills/*/SKILL.md; do ...; done` (name/folder parity)

## Risks and Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Missing replacements in long examples | Validation failure or confusing usage docs | Regex sweep on output for forbidden syntax |
| Description alias suffix inconsistencies | Trigger-rule mismatch | Enforce suffix generation centrally |
| Knowledge skill over-editing | Loss of guidance quality | Preserve body verbatim except prohibited tokens |
| Cross-skill reference drift | Broken references | Explicit replacement map and post-check |

## Confidence
- Analysis completeness: High
- File coverage: 100% of mapped skill files
- Remaining uncertainty: Low (mainly manual rewrite drift risk)
