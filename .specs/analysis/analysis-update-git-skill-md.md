# Codebase Analysis: update-git-skill-md

Task: .specs/tasks/draft/update-git-skill-md.docs.md
Date: 2026-02-17

## Problem Definition

Analyze all references impacted by changing create-worktree worktree path convention from sibling directories to `.worktree/<name>`.

## Affected Files

### Modify

1. `plugins/git/skills/create-worktree/SKILL.md`
- Path convention and examples currently use sibling paths.
- Must update instruction step 5b/5c references, examples, important notes, cleanup, troubleshooting.

### Validate (no mandatory edit)

1. `.gitignore`
- Ensure docs requirement to ignore `.worktree/` is accurate.

## Integration Points

1. Command instruction section and "Worktree Path Convention" section must be aligned.
2. Example commands and cleanup commands must match new convention.
3. Troubleshooting snippets must reference `.worktree/<name>` paths.

## Similar Patterns

- Existing documentation tasks in `.specs/tasks/done/*.feature.md` follow focused single-file updates with explicit scope and acceptance checks.

## Risk Assessment

- Risk Level: Low
- Primary risk is inconsistency across sections in same markdown file.

## Pre-Implementation Reading

1. `plugins/git/skills/create-worktree/SKILL.md`
2. `.gitignore`
3. `CLAUDE.md`

## Verification Summary

- Files to modify: 1
- Files to create: 0
- Files to delete: 0
- Key integration points: 3
