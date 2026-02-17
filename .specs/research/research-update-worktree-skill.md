# Research: Update create-worktree skill to local worktree/ convention

Task: .specs/tasks/todo/update-worktree-skill.md
Date: 2026-02-17

## Existing Skills Discovery

No related local reusable skills found in `.agents/skills/`.

## Problem Definition

The target skill currently documents sibling-directory worktree paths. The requested outcome is a self-contained local `worktree/<name>` convention inside the repository, with all examples and operational snippets aligned.

## Sources Reviewed

| Source | Type | Why it matters |
|---|---|---|
| `plugins/git/skills/create-worktree/SKILL.md` | Primary source | Contains all path-convention references to change |
| `.gitignore` | Repo policy | Must include `worktree/` ignore guidance |
| `.specs/tasks/todo/update-worktree-skill.md` | Task input | Defines required edits and expected outputs |

## Alternatives Considered

1. Keep sibling convention in core steps and update examples only.
- Rejected: leaves contradictory instructions.

2. Update path convention and examples but skip cleanup/troubleshooting.
- Rejected: operational docs become inconsistent.

3. Update all path-related sections and enforce a final stale-reference audit.
- Selected.

## Key Recommendation

Adopt `worktree/<name>` as canonical path convention in all relevant sections of `plugins/git/skills/create-worktree/SKILL.md`, and ensure `.gitignore` includes `worktree/`.
