## Objective

Convert the **Spec-Driven Development (SDD)** plugin from the [Context Engineering Kit](https://github.com/NeoLabHQ/context-engineering-kit/tree/master/plugins/sdd) (Claude Code format) into **OpenAI Codex CLI** compatible skills, following Codex's flat skill namespace and conversational invocation model.

---

## Source Plugin Overview

The SDD plugin is a comprehensive spec-driven development workflow for Claude Code. It lives at:

```
plugins/sdd/
├── agents/                          # Flat .md files (9 agent roles)
│   ├── business-analyst.md
│   ├── code-explorer.md
│   ├── developer.md
│   ├── qa-engineer.md
│   ├── researcher.md
│   ├── software-architect.md
│   ├── team-lead.md
│   ├── tech-lead.md
│   └── tech-writer.md
├── prompts/
│   └── judge.md                     # Quality evaluation prompt
├── scripts/
│   ├── create-folders.sh            # .specs/ directory setup
│   └── create-scratchpad.sh
├── skills/                          # All skills (including orchestrators)
│   ├── add-task/SKILL.md
│   ├── brainstorm/SKILL.md
│   ├── create-ideas/SKILL.md
│   ├── implement/SKILL.md
│   └── plan/
│       ├── SKILL.md
│       └── analyse-business-requirements.md
└── README.md
```

### Component Roles

| **Claude Code Concept** | **File** | **Purpose** |
| --- | --- | --- |
| **Skill** | `skills/<name>/SKILL.md` | User-invocable workflow or reusable prompt. All 5 entry points (add-task, plan, implement, create-ideas, brainstorm) are skills. Orchestrator skills (plan, implement) chain agent roles inline. |
| **Agent** | `agents/<name>.md` | Sub-agent role definition with system prompt, responsibilities, and output format. Flat `.md` files (not subdirectories). |
| **Prompt** | `prompts/judge.md` | Quality evaluation prompt used for verification gates. |
| **Script** | `scripts/*.sh` | Shell scripts for `.specs/` directory setup and scratchpad creation. |

### SDD Workflow Phases

The `/sdd-plan` command orchestrates agents in this order:

1. **Phase 2a** — `researcher` → technology research, dependency analysis
2. **Phase 2b** — `code-explorer` → codebase analysis, pattern identification
3. **Phase 2c** — `business-analyst` → requirements, specification writing
4. **Phase 3** — `software-architect` → architecture design, implementation planning
5. **Phase 4** — `tech-lead` → task decomposition, dependency mapping
6. **Phase 5** — `team-lead` → parallelization, agent assignment
7. **Phase 6** — `qa-engineer` → verification rubrics, quality gates

The `/sdd-implement` command uses:

- `developer` → code implementation, TDD, quality review
- `tech-writer` → documentation, API guides, lessons learned

---

## Target: Codex CLI Skill Format

### Codex Constraints

<aside>
⚠️

**Critical differences from Claude Code:**

- Codex has a **flat skill namespace** — no nested plugins or namespaces
- **Invocation is via `$skill-name`** in prompts, or `/skills` to browse — no `/plugin:skill` slash syntax
- **Implicit invocation** — Codex can auto-select a skill when the task matches its `description`
- **No native sub-agent orchestration** — Codex cannot launch isolated sub-agents like Claude Code
- Skills live in **`SKILL.md`** (uppercase) inside a named directory
- Skill locations (scoped): `.agents/skills/` (repo), `$HOME/.agents/skills/` (user), `/etc/codex/skills/` (admin)
- **Progressive disclosure**: Codex reads only `name` + `description` at startup; full `SKILL.md` loaded on invocation
</aside>

### Codex Skill Format (per [official docs](https://developers.openai.com/codex/skills))

A skill is a **directory** with a `SKILL.md` file plus optional subdirectories:

```
my-skill/
├── SKILL.md              # Required: instructions + metadata
├── scripts/              # Optional: executable code
├── references/           # Optional: documentation
├── assets/               # Optional: templates, resources
└── agents/
    └── openai.yaml       # Optional: UI metadata, invocation policy, dependencies
```

`SKILL.md` format (note: **lowercase** front-matter keys, **no** `## Instructions` / `## Examples` sections required):

```markdown
---
name: skill-name-here
description: Explain exactly when this skill should and should not trigger.
---

Skill instructions for Codex to follow.
(Write imperative steps with explicit inputs and outputs.)
```

Optional `agents/openai.yaml` for UI and invocation control:

```yaml
interface:
  display_name: "User-facing name"
  short_description: "User-facing description"
  icon_small: "./assets/small-logo.svg"
  brand_color: "#3B82F6"
policy:
  allow_implicit_invocation: true   # set false to require explicit $skill invocation
dependencies:
  tools:
    - type: "mcp"
      value: "some-mcp-server"
```

---

## Conversion Instructions

Follow these steps in order. Complete each step fully before moving to the next.

### Step 1 — Read Source Plugin

The source repo is already cloned locally. Read all files under `/Users/andy/github/claude/context-engineering-kit/plugins/sdd/`.

For each file, note:

- Filename and path
- YAML front-matter (description, argument-hint, etc.)
- Main body content (instructions, prompts)
- Any references to other agents/skills
- Any file system operations (e.g. `.specs/tasks/` folder management)

### Step 2 — Create Codex Skills Directory

Create the target directory inside the source plugin:

```bash
mkdir -p plugins/sdd/codex/skills
```

<aside>
📂

**Scope hierarchy (Codex scans all):**

- `$CWD/.agents/skills/` — current working directory (repo)
- `$REPO_ROOT/.agents/skills/` — repo root (repo)
- `$HOME/.agents/skills/` — user home (personal)
- `/etc/codex/skills/` — system-wide (admin)
</aside>

### Step 3 — Convert Skills

For each directory in `plugins/sdd/skills/<name>/`:

1. Create directory `plugins/sdd/codex/skills/sdd-<name>/`
2. Create `SKILL.md` (uppercase) inside it
3. Map the YAML front-matter (use **lowercase** keys):
    - `name:` — set to `sdd-<name>` (must match folder name exactly)
    - `description:` — prepend `(SDD)`, include argument-hint context, and append the colon-variant alias (e.g. "Also invoked as `$sdd:<name>`.")
4. Copy the markdown body directly after the front-matter (no `## Instructions` heading needed)
5. For `plan/`, also read `analyse-business-requirements.md` and incorporate its content into the skill body
6. For orchestrator skills (`plan`, `implement`) that reference agent roles via sub-agent calls, rewrite as **sequential inline phases** — embed the orchestration logic directly, referencing agent-skills (e.g. "follow instructions from `$sdd-agent-researcher`")

**Expected output — 5 skills:**

| **Source** | **Codex Skill** | **Notes** |
| --- | --- | --- |
| `skills/add-task/SKILL.md` | `sdd-add-task` | Creates `.specs/tasks/draft/<name>.feature.md` |
| `skills/plan/SKILL.md`  • `analyse-business-requirements.md` | `sdd-plan` | Orchestrator — rewrite sub-agent calls as inline phases |
| `skills/implement/SKILL.md` | `sdd-implement` | Orchestrator — rewrite sub-agent calls as inline phases |
| `skills/create-ideas/SKILL.md` | `sdd-create-ideas` | Direct conversion |
| `skills/brainstorm/SKILL.md` | `sdd-brainstorm` | Direct conversion |

**Example — create-ideas:**

```
plugins/sdd/skills/create-ideas/SKILL.md
→ plugins/sdd/codex/skills/sdd-create-ideas/SKILL.md
```

```markdown
---
name: sdd-create-ideas
description: "(SDD) Generate diverse ideas using creative sampling techniques. Use when brainstorming approaches or exploring solution spaces. Also invoked as $sdd:create-ideas."
---

(Content from original SKILL.md body — written as imperative instructions
with explicit inputs and outputs. No section headings required.)
```

### Step 4 — Convert Agents to Skills (role prompts)

For each file `plugins/sdd/agents/<name>.md`:

1. Create directory `plugins/sdd/codex/skills/sdd-agent-<name>/`
2. Create `SKILL.md` inside it
3. Use the agent's system prompt / role definition as the skill body (after front-matter)
4. Set front-matter:
    - `name: sdd-agent-<name>`
    - `description:` — prefix with `(SDD Agent)`, include which phase uses it, describe when to trigger, and append the colon-variant alias (e.g. "Also invoked as `$sdd-agent:<name>`.")
5. Preserve the agent's output format requirements in the skill body

<aside>
💡

**Why convert agents to skills?** Codex cannot launch isolated sub-agents. By converting each agent role into a skill, you can invoke them sequentially ("use sdd-agent-researcher") to simulate the multi-agent workflow. The orchestration logic moves to the command-level skills (Step 5).

</aside>

**Naming convention:**

| **Source File** | **Codex Skill Name** |
| --- | --- |
| `agents/researcher.md` | `sdd-agent-researcher` |
| `agents/code-explorer.md` | `sdd-agent-code-explorer` |
| `agents/business-analyst.md` | `sdd-agent-business-analyst` |
| `agents/software-architect.md` | `sdd-agent-software-architect` |
| `agents/tech-lead.md` | `sdd-agent-tech-lead` |
| `agents/team-lead.md` | `sdd-agent-team-lead` |
| `agents/qa-engineer.md` | `sdd-agent-qa-engineer` |
| `agents/developer.md` | `sdd-agent-developer` |
| `agents/tech-writer.md` | `sdd-agent-tech-writer` |

### Step 5 — Convert Remaining Files

**`prompts/judge.md`** — If it contains a quality evaluation prompt used by the workflow, convert to `plugins/sdd/codex/skills/sdd-agent-judge/SKILL.md` — implicit invocation is enabled by default. If it's only used internally by another skill, inline its content into that skill instead.

**`scripts/create-folders.sh`** — Review the directory structure it creates. Ensure each skill that writes to those paths includes `mkdir -p` in its instructions. Do not create a separate skill for this script.

**`scripts/create-scratchpad.sh`** — Same approach: inline the behavior into the relevant skill(s) rather than creating a standalone script skill.

### Step 6 — Validate the Conversion

For each converted skill, verify:

- [ ]  `SKILL.md` (uppercase) has valid YAML front-matter with `name` and `description` (lowercase keys)
- [ ]  `name` matches the folder name exactly
- [ ]  `description` clearly explains when the skill should and should not trigger
- [ ]  Skill body contains the full original prompt content as imperative instructions
- [ ]  No references to Claude Code-specific APIs (e.g. `/plugin`, `TaskTool`, `Bash()`)
- [ ]  File system paths (`.specs/`) are preserved correctly
- [ ]  Orchestrator skills (plan, implement) correctly reference agent-skill names
- [ ]  Each `description` includes the colon-variant alias (e.g. "Also invoked as `$sdd:plan`")

**Test each command-level skill:**

```bash
# Test add-task (explicit invocation via $skill-name)
codex "$sdd-add-task create a task for implementing user auth with JWT"
# Verify: .specs/tasks/draft/implement-user-auth.feature.md created

# Test plan
codex "$sdd-plan for @.specs/tasks/draft/implement-user-auth.feature.md"
# Verify: Task moved to .specs/tasks/todo/ with full spec

# Test implement
codex "$sdd-implement @.specs/tasks/todo/implement-user-auth.feature.md"
# Verify: Working code produced, task moved to .specs/tasks/done/

# You can also use /skills slash command to browse and select skills interactively
```

---

## Final Directory Structure

```
plugins/sdd/codex/                           # Build output (skills only, no AGENTS.md)
└── skills/
    ├── sdd-add-task/SKILL.md                # Skill: create task
    ├── sdd-plan/SKILL.md                    # Skill: multi-phase planning
    ├── sdd-implement/SKILL.md               # Skill: implementation
    ├── sdd-create-ideas/SKILL.md            # Skill: idea generation
    ├── sdd-brainstorm/SKILL.md              # Skill: collaborative brainstorm
    ├── sdd-agent-researcher/SKILL.md        # Agent role
    ├── sdd-agent-code-explorer/SKILL.md     # Agent role
    ├── sdd-agent-business-analyst/SKILL.md  # Agent role
    ├── sdd-agent-software-architect/SKILL.md # Agent role
    ├── sdd-agent-tech-lead/SKILL.md         # Agent role
    ├── sdd-agent-team-lead/SKILL.md         # Agent role
    ├── sdd-agent-qa-engineer/SKILL.md       # Agent role
    ├── sdd-agent-developer/SKILL.md         # Agent role
    └── sdd-agent-tech-writer/SKILL.md       # Agent role

To deploy, copy skills:
  cp -r plugins/sdd/codex/skills/* ~/.agents/skills/
  # or: cp -r plugins/sdd/codex/skills/* .agents/skills/
  # No AGENTS.md needed — aliases are in each skill's description.

Project root (after deployment):
└── .specs/
    ├── tasks/
    │   ├── draft/
    │   ├── todo/
    │   └── done/
    ├── research/
    ├── analysis/
    └── plans/
```

---

## Key Adaptation Notes

<aside>
🔑

**Context isolation trade-off:** Claude Code's SDD strength is launching each agent as an isolated sub-agent with clean context. In Codex, all phases run in a single session. To mitigate context rot:

- Write each phase's output to disk (`.specs/`) before starting the next
- Instruct the model to "clear your working assumptions" between phases
- For complex tasks, consider running each phase as a separate `codex` invocation
</aside>

<aside>
📎

**Claude-specific syntax to remove/replace:**

- `TaskTool`, `Bash()`, `Read()` → replace with natural language instructions ("read the file", "run the command")
- `/plugin`, `/command` references → replace with "use skill `sdd-<name>`"
- `subagent:` directives → replace with inline role-switching instructions
- `[OBFUSCATED PROMPT INJECTION]` markers → skip, these are anti-jailbreak guards
</aside>
