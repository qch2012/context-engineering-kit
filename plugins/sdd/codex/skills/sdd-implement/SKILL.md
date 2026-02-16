---
name: sdd-implement
description: "(SDD) Implement a task with automated LLM-as-Judge verification for critical steps  Argument hint: Task file [options] (e.g., \"add-validation.feature.md --continue --human-in-the-loop\"). Also invoked as $sdd:implement."
---

Use this skill to implement from a planned task file, step by step, with quality gates.

Preparation:
1. Resolve task file and flags (`--continue`, `--refine`, `--target-quality`, `--max-iterations`, `--skip-judges`, `--human-in-the-loop`).
2. Ensure directories exist:
`mkdir -p .specs/tasks/todo .specs/tasks/in-progress .specs/tasks/done .specs/scratchpad .specs/analysis .specs/research .specs/plans`
3. Move task to `.specs/tasks/in-progress/` when execution starts.

Execution loop per step:
1. Implement using `$sdd-agent-developer` guidance and task success criteria.
2. Run project checks/tests for changed artifacts.
3. If judge checks are enabled, evaluate with `$sdd-agent-judge`.
4. On failure, revise and re-run until pass or max iterations.
5. Mark step done only after passing criteria and verification.

Finalize:
1. Complete definition-of-done verification.
2. Move task to `.specs/tasks/done/` only when all steps pass.
3. Return summary of changed files, verification outcomes, tests, and residual risks.
