# Codebase Analysis: Convert Git Plugin Skills to Codex Format

Task: `convert-git-to-codex.md`
Date: 2026-02-16

## Scope
Analyze source Git skills under `skills/` and define exact conversion inventory for target tree `plugins/git/codex/skills/`.

## Source Inventory (10/10)

| Source Skill | Source Path | Target Name | Target Path | argument-hint | model | allowed-tools | Notes |
|---|---|---|---|---|---|---|---|
| commit | `skills/commit/SKILL.md` | `git-commit` | `plugins/git/codex/skills/git-commit/SKILL.md` | Yes | Yes | Yes | Includes pre-commit/lint flow |
| create-pr | `skills/create-pr/SKILL.md` | `git-create-pr` | `plugins/git/codex/skills/git-create-pr/SKILL.md` | Yes | No | Yes | Contains `Skill(git:commit)` reference |
| analyze-issue | `skills/analyze-issue/SKILL.md` | `git-analyze-issue` | `plugins/git/codex/skills/git-analyze-issue/SKILL.md` | Yes | No | Yes | References `.claude/commands/load-issues.md` |
| load-issues | `skills/load-issues/SKILL.md` | `git-load-issues` | `plugins/git/codex/skills/git-load-issues/SKILL.md` | Yes | No | Yes | Command-heavy procedural template |
| attach-review-to-pr | `skills/attach-review-to-pr/SKILL.md` | `git-attach-review-to-pr` | `plugins/git/codex/skills/git-attach-review-to-pr/SKILL.md` | Yes | No | Yes | MCP fallback guidance included |
| create-worktree | `skills/create-worktree/SKILL.md` | `git-create-worktree` | `plugins/git/codex/skills/git-create-worktree/SKILL.md` | Yes | Yes | Yes | Long decision tree and setup logic |
| merge-worktree | `skills/merge-worktree/SKILL.md` | `git-merge-worktree` | `plugins/git/codex/skills/git-merge-worktree/SKILL.md` | Yes | Yes | Yes | Multiple merge strategies |
| compare-worktrees | `skills/compare-worktrees/SKILL.md` | `git-compare-worktrees` | `plugins/git/codex/skills/git-compare-worktrees/SKILL.md` | Yes | Yes | Yes | Argument classification logic |
| notes | `skills/notes/SKILL.md` | `git-notes` | `plugins/git/codex/skills/git-notes/SKILL.md` | No | No | No | Knowledge skill |
| worktrees | `skills/worktrees/SKILL.md` | `git-worktrees` | `plugins/git/codex/skills/git-worktrees/SKILL.md` | No | No | No | Knowledge skill |

## Rewrite Trigger Matrix

| Rule | Triggered In |
|---|---|
| Remove `model:` | `commit`, `create-worktree`, `merge-worktree`, `compare-worktrees` |
| Remove `allowed-tools:` | `commit`, `create-pr`, `analyze-issue`, `load-issues`, `attach-review-to-pr`, `create-worktree`, `merge-worktree`, `compare-worktrees` |
| Fold `argument-hint` into description | all command skills (8) |
| Replace `Skill(git:commit)` style references | `create-pr` |
| Replace `.claude/commands/load-issues.md` with `$git-load-issues` | `analyze-issue` |
| Replace `/git:<name>` with `$git-<name>` | any body occurrence (global scan required) |
| Remove tool-token mentions (`Read`, `Write`, `Glob`, `Grep`, `Bash(`) | command skills where present |
| Remove `[OBFUSCATED PROMPT INJECTION]` marker | global scan required |

## Conversion Constraints
- Preserve markdown structure and operational sequence.
- Keep filesystem paths and templates intact unless explicitly remapped.
- Do not alter semantic intent of skill guidance.

## Validation Commands (Post-Conversion)
```bash
find plugins/git/codex/skills -mindepth 2 -maxdepth 2 -name SKILL.md | wc -l
rg -n "^(model|allowed-tools|argument-hint):" plugins/git/codex/skills
rg -n "Bash\(|Read\(|Write\(|Glob\(|Grep\(|/git:|\.claude/commands/load-issues\.md|\[OBFUSCATED PROMPT INJECTION\]" plugins/git/codex/skills
```

## Conclusion
The codebase supports a deterministic mapping with low architectural risk. Primary implementation complexity is consistency of textual normalization across long command skills.
