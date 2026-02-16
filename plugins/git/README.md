# Git Plugin

Git workflows for commits, pull requests, issue analysis, reviews, and worktrees.

This plugin now supports both:
- Claude Code command-style usage (`/git:*`)
- Codex skill-style usage (`codex/skills/git-*`)

## Plugin Target

- Maintain consistent commit history with conventional commits
- Reduce PR creation friction with standardized guidance
- Improve issue-to-code workflow with structured analysis
- Standardize team Git operations

## Overview

The Git plugin provides reusable workflows for:
- commit creation
- PR creation and review comments
- issue loading and analysis
- multi-worktree development
- git notes metadata

Most GitHub-related flows require GitHub CLI (`gh`).

## Claude Code Usage

### Install (Claude plugin)

```bash
/plugin install git@NeoLabHQ/context-engineering-kit
```

### Quick Start (Claude commands)

```bash
/git:commit
/git:create-pr
/git:load-issues
/git:analyze-issue 123
```

## Codex Skills Usage

### Install (Codex skills)

From this plugin directory (`plugins/git`), install all git skills into your personal Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -R codex/skills/git-* ~/.codex/skills/
```

Verify install:

```bash
find ~/.codex/skills -maxdepth 2 -name SKILL.md | rg 'git-'
```

### Quick Start (Codex)

Use the skill names directly in Codex prompts:
- `git-commit`
- `git-create-pr`
- `git-analyze-issue`
- `git-load-issues`
- `git-attach-review-to-pr`
- `git-create-worktree`
- `git-merge-worktree`
- `git-compare-worktrees`
- `git-notes`
- `git-worktrees`

Examples:
- "Use `git-commit` to create a conventional commit for my staged changes."
- "Use `git-create-pr` to prepare a draft PR from this branch."
- "Use `git-compare-worktrees` to compare `src/` between my current branch and `feature/auth-system`."

Note: Skill descriptions include alias text like `$git:<name>`, while file/folder skill IDs are `git-<name>`.

## Command Docs (Claude)

- [/git:commit](./commit.md) - Create well-formatted commits with conventional commit messages and emoji.
- [/git:create-pr](./create-pr.md) - Create pull requests using GitHub CLI with proper templates and formatting.
- [/git:analyze-issue](./analyze-issue.md) - Analyze a GitHub issue and create a detailed technical specification.
- [/git:load-issues](./load-issues.md) - Load all open issues from GitHub and save them as markdown files.
- [/git:attach-review-to-pr](./attach-review-to-pr.md) - Add line-specific review comments to pull requests using GitHub CLI API.
- [/git:create-worktree](./create-worktree.md) - Create and setup git worktrees for parallel development with automatic dependency installation.
- [/git:compare-worktrees](./compare-worktrees.md) - Compare files and directories between git worktrees or worktree and current branch.
- [/git:merge-worktree](./merge-worktree.md) - Merge changes from worktrees into current branch with selective file checkout, cherry-picking, interactive patch selection, or manual merge.

## Knowledge Skills

- [worktrees](./worktrees.md) - Patterns for parallel branch development with git worktrees.
- [notes](./notes.md) - Patterns for attaching metadata to commits with git notes.

[Usage Examples](./usage-examples.md)
