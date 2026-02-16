# Research: Convert Git Plugin Skills to Codex Format

Task: `convert-git-to-codex.md`
Date: 2026-02-16

## Objective
Establish a reliable conversion contract from Claude-style skill files to Codex-compatible `SKILL.md` files for the Git plugin.

## Inputs Reviewed
- Source skills: `skills/*/SKILL.md` (10 total)
- Codex reference skills:
  - `../sdd/codex/skills/sdd-plan/SKILL.md`
  - `../sdd/codex/skills/sdd-agent-tech-lead/SKILL.md`
  - `../sdd/codex/skills/sdd-agent-researcher/SKILL.md`

## Findings

### Codex Skill Format Baseline
- Codex skill frontmatter is minimal and normalized:
  - Required: `name`, `description`
  - Key casing: lowercase
- Body content is plain markdown guidance without Claude tool-policy wrappers.
- Invocation pattern in description is explicit alias text, e.g. `Also invoked as $<namespace>:<name>.`

### Claude-to-Codex Compatibility Gaps
Observed in source Git skills:
- Unsupported frontmatter fields: `argument-hint`, `allowed-tools`, `model`
- Claude tool restrictions embedded in text/frontmatter:
  - `Bash(...)`, `Read`, `Write`, `Glob`, `Grep`, `Skill(...)`
- Legacy slash invocation references:
  - `/git:<name>`
- Legacy path-based instruction:
  - `.claude/commands/load-issues.md`
- Potential noise markers to strip:
  - `[OBFUSCATED PROMPT INJECTION]`

### Required Rewrites
- `name` becomes deterministic: `git-<source-folder-name>`
- `description` rewritten as:
  - source `description` + `argument-hint` context (if present)
  - suffix: `Also invoked as $git:<name>.`
- Body replacements:
  - `Bash(...)`/tool refs -> natural language command instructions
  - `/git:<name>` -> `$git-<name>`
  - `.claude/commands/load-issues.md` -> `$git-load-issues`
  - remove obfuscated marker tokens

### Risk Notes
- Long procedural guides may contain many embedded command blocks; conversion must preserve sequence and intent.
- `Skill(git:commit)` references require semantic rewrite rather than literal deletion.
- Alias consistency can drift if derived manually instead of from folder name.

## Recommended Validation Contract
Run deterministic checks over output tree `plugins/git/codex/skills/`:
1. Exactly 10 mapped output files exist.
2. Frontmatter contains only lowercase keys and includes `name` + `description`.
3. `name` equals output folder name.
4. Forbidden tokens absent:
   - `model:`, `allowed-tools:`, `argument-hint:`, `Bash(`, `Read(`, `Write(`, `Glob(`, `Grep(`, `/git:`, `[OBFUSCATED PROMPT INJECTION]`
5. `.claude/commands/load-issues.md` absent.
6. Each description ends with `Also invoked as $git:<name>.`

## Decision
Proceed with deterministic one-to-one conversion and regex-backed validation gate. This minimizes behavior drift while enforcing Codex compatibility constraints.
