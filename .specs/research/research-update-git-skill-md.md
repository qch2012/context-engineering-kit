# Research: Update create-worktree skill path convention

Task: .specs/tasks/draft/update-git-skill-md.docs.md
Date: 2026-02-17

## Existing Skills Discovery

No related local reusable skills found in `.agents/skills/` for this repository.

## Problem Definition

The `create-worktree` skill currently documents sibling-directory paths (`../<project>-<name>`). The requested behavior is to standardize documentation and command examples around `.worktree/<name>` inside the repository.

## Resources

| Resource | Type | Relevance |
|---|---|---|
| `plugins/git/skills/create-worktree/SKILL.md` | Source of truth | Primary file to update |
| `.gitignore` | Repository conventions | Needed to document `.worktree/` ignore requirement |
| `CLAUDE.md` | Project conventions | Confirms docs/plugin consistency expectations |

## Alternatives Considered

1. Keep sibling layout and only adjust examples.
- Rejected: leaves path convention contradictory in core instructions.

2. Switch to `.worktree/` in commands but keep old troubleshooting and notes.
- Rejected: creates mixed guidance and user confusion.

3. Update all relevant sections to `.worktree/<name>` and explicitly mention `.gitignore` requirement.
- Selected: consistent and actionable for users.

## Key Recommendation

Adopt `.worktree/<name>` as the single documented path convention across instruction, examples, cleanup, notes, and troubleshooting text in `plugins/git/skills/create-worktree/SKILL.md`.

## Risk Notes

- Risk: Missing one old `../myproject-...` example can reintroduce ambiguity.
- Mitigation: Perform full-text replacement audit and validate all path examples.

## Verification Summary

- Source checked: `plugins/git/skills/create-worktree/SKILL.md`
- Scope validated: docs-only change request
- Output artifact: updated skill markdown + planned implementation steps
