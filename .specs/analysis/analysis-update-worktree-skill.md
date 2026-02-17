# Codebase Analysis: update-worktree-skill

Task: .specs/tasks/todo/update-worktree-skill.md
Date: 2026-02-17

## Affected Files

### Modify

1. `plugins/git/skills/create-worktree/SKILL.md`
- Branch resolution commands
- Path convention subsection
- Worktree path convention diagram and naming rules
- Examples section
- Important Notes bullet
- Cleanup section
- Troubleshooting navigation path

### Validate

1. `.gitignore`
- Include `worktree/` if missing.

## Risks

- Inconsistent path convention left in one section after edits.
- Accidental behavioral edits outside documentation scope.

## Integration Points

- Canonical convention text must match all command examples.
- Important Notes must align with path convention and ignore rule.
- Cleanup/troubleshooting snippets must follow same directory pattern.

## Risk Level

Low
