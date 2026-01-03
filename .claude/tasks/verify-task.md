# Step 4: Define Task Verifications

## Context

You are executing step 4 of the add-and-triage workflow. Previous phases have:

1. **Phase 1 (Create)**: Generated initial task file with proper structure and complexity estimate
2. **Phase 2 (Refine)**: Added detailed specification, affected files, and implementation resources
3. **Phase 3 (Parallelize)**: Reorganized implementation steps for maximum parallel execution with explicit dependencies

Your role is to add LLM-as-Judge verification sections to each implementation step, ensuring quality control through automated evaluation.

## Goal

Add LLM-as-Judge verification sections to each implementation step in the task file. Each step must have a `#### Verification` section with appropriate verification level, custom rubrics, thresholds, and reference patterns.

## Input

- **Task File**: `$ARGUMENTS` - Path to the parallelized task file from Phase 3
- **Task Location**: `.specs/tasks/` directory

## Instructions

### 1. Load and Understand Task

1. Read the task file to get the full implementation process
2. Locate the `## Implementation Process` section
3. List all steps with their Expected Output and Success Criteria
4. Note artifact types being created/modified

### 2. Classify Each Step

For each step, determine:

**Artifact Type** (by category):

| Category | Examples |
|----------|----------|
| **Code & Logic** | Source code, API endpoints, business logic, data models, algorithms |
| **Infrastructure** | Configuration files (JSON, YAML), build scripts, migrations, Docker |
| **Tests** | Unit tests, integration tests, E2E tests, fixtures |
| **Documentation** | README, API docs, user guides, agent definitions, workflow commands, task files |
| **Simple Operations** | Directory creation, file renaming, file deletion, simple refactoring |

**Criticality Level** (based on impact of defects):

| Criticality | Impact if Defective | Examples |
|-------------|---------------------|----------|
| **HIGH** | Security vulnerabilities, data loss, system failures | Auth logic, payments, data migrations, core algorithms, API contracts, agent definitions |
| **MEDIUM-HIGH** | Broken functionality, poor UX | Business logic, UI components, workflow orchestration, task files |
| **MEDIUM** | Degraded quality, user confusion | Documentation, utilities, helpers, configuration |
| **LOW** | Minimal impact, easily fixed | Formatting, comments, non-critical config, logging |
| **NONE** | Binary success/failure, no judgment needed | Directory creation, file deletion, file moves |

**Item Count**:

- Single item → Single Judge or Panel
- Multiple similar items → Per-Item Judges

### 3. Determine Verification Level

Use this decision tree:

```
Is artifact type Directory/Deletion/Config?
├── Yes → Level: NONE
│
└── No → Is criticality HIGH?
    ├── Yes → Level: Panel of 2 Judges
    │
    └── No → Are there multiple similar items?
        ├── Yes → Level: Per-Item Judges (one per item)
        │
        └── No → Level: Single Judge
```

**Verification Levels Reference:**

| Level | When to Use | Configuration |
|-------|-------------|---------------|
| ❌ None | Simple operations (mkdir, delete, JSON update) | Skip verification |
| ✅ Single Judge | Non-critical single artifacts | 1 evaluation, threshold 4.0/5.0 |
| ✅ Panel (2) | Critical single artifacts | 2 evaluations, median voting, threshold 4.0/5.0 |
| ✅ Per-Item | Multiple similar items | 1 evaluation per item, parallel, threshold 4.0/5.0 |

### 4. Define Rubrics

For each step requiring verification, create a rubric with:

- **3-6 criteria** relevant to the artifact type
- **Weights summing to 1.0**
- **Clear descriptions** of what each criterion measures

Use these templates as starting points:

---

#### Source Code / Business Logic Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Correctness | 0.30 | Implements requirements correctly |
| Code Quality | 0.20 | Follows project conventions, readable |
| Error Handling | 0.20 | Handles edge cases, failures gracefully |
| Security | 0.15 | No vulnerabilities, proper validation |
| Performance | 0.15 | No obvious inefficiencies |

#### Documentation Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Accuracy | 0.30 | Content is factually correct |
| Completeness | 0.25 | All necessary information included |
| Clarity | 0.20 | Easy to understand |
| Examples | 0.15 | Helpful examples where needed |
| Consistency | 0.10 | Terminology matches codebase |

#### Agent Definition Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Pattern Conformance | 0.25 | Follows existing agent patterns (frontmatter, structure) |
| Frontmatter Completeness | 0.20 | Has name, description, tools fields |
| Domain Knowledge | 0.25 | Demonstrates domain-specific expertise |
| Documentation Quality | 0.15 | Clear role, process, output format sections |
| RFC 2119 Bindings | 0.15 | Uses MUST/SHOULD/MAY appropriately |

#### Task File Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Self-Containment | 0.25 | Sub-agent doesn't need external context |
| Context Section | 0.15 | Clear workflow position |
| Goal Clarity | 0.20 | Specific, measurable goal |
| Instructions Quality | 0.20 | Numbered, actionable steps |
| Success Criteria | 0.15 | Checkboxes with measurable outcomes |
| Input/Output Contract | 0.05 | Clear contracts defined |

#### Test Code Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Coverage | 0.25 | Tests cover requirements |
| Edge Cases | 0.25 | Edge cases and error paths tested |
| Isolation | 0.20 | Tests are independent, no side effects |
| Clarity | 0.15 | Test intent is clear from name/structure |
| Maintainability | 0.15 | Tests are not brittle |

#### Configuration Rubric

| Criterion | Weight | Description |
|-----------|--------|-------------|
| Correctness | 0.35 | Values are correct for environment |
| Security | 0.25 | No secrets exposed, proper permissions |
| Completeness | 0.20 | All required fields present |
| Consistency | 0.20 | Follows project config patterns |

---

**Custom Rubric Guidelines:**

1. Extract criteria from the step's own Success Criteria when possible
2. Weight by importance: critical aspects get 0.20-0.30, minor get 0.05-0.15
3. Be specific: "Documents hypothesis file format" not "Good documentation"
4. Match artifact type: code artifacts need different criteria than documentation

### 5. Add Verification Sections

For each step, add `#### Verification` section after `#### Success Criteria`:

---

**Template: No Verification**

```markdown
#### Verification

**Level:** ❌ NOT NEEDED
**Rationale:** [Why verification is unnecessary - e.g., "Simple file operation. Success is binary."]
```

---

**Template: Single Judge**

```markdown
#### Verification

**Level:** ✅ Single Judge
**Artifact:** `[path/to/artifact.md]`
**Threshold:** 4.0/5.0

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| [Criterion 1] | 0.XX | [Description] |
| [Criterion 2] | 0.XX | [Description] |
| ... | ... | ... |

**Reference Pattern:** `[path/to/reference.md]` (if applicable)
```

---

**Template: Panel of 2 Judges**

```markdown
#### Verification

**Level:** ✅ CRITICAL - Panel of 2 Judges with Aggregated Voting
**Artifact:** `[path/to/artifact.md]`
**Threshold:** 4.0/5.0

**Rubric:**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| [Criterion 1] | 0.XX | [Description] |
| [Criterion 2] | 0.XX | [Description] |
| ... | ... | ... |

**Reference Pattern:** `[path/to/reference.md]`
```

---

**Template: Per-Item Judges**

```markdown
#### Verification

**Level:** ✅ Per-[Item Type] Judges ([N] separate evaluations in parallel)
**Artifacts:** `[path/to/items/{item1,item2,...}.md]`
**Threshold:** 4.0/5.0

**Rubric (per [item type]):**

| Criterion | Weight | Description |
|-----------|--------|-------------|
| [Criterion 1] | 0.XX | [Description] |
| [Criterion 2] | 0.XX | [Description] |
| ... | ... | ... |

**Reference Pattern:** `[path/to/reference.md]` (if applicable)
```

---

### 6. Add Verification Summary

After all steps, add a summary table before `## Blockers`:

```markdown
---

## Verification Summary

| Step | Verification Level | Judges | Threshold | Artifacts |
|------|-------------------|--------|-----------|-----------|
| 1 | ❌ None | - | - | [Brief description] |
| 2a | ✅ Panel (2) | 2 | 4.0/5.0 | [Brief description] |
| 2b | ✅ Per-Item | N | 4.0/5.0 | [Brief description] |
| ... | ... | ... | ... | ... |

**Total Evaluations:** [Calculate total]
**Implementation Command:** `/implement-task $TASK_FILE`

---
```

### 7. Update Task File

Use Write tool to modify the task file:

1. Preserve everything before the first implementation step
2. Add `#### Verification` section to each step (after Success Criteria)
3. Add Verification Summary before Blockers section
4. Preserve Blockers and Notes sections unchanged

## Verification Principles

1. **Match Verification to Risk**: Higher risk artifacts need more thorough verification
2. **Custom Rubrics Over Generic**: Extract criteria from step's own Success Criteria
3. **Reference Patterns Enable Quality**: Always specify when one exists
4. **Standard Threshold**: 4.0/5.0 for most artifacts; 4.5/5.0 for high-stakes; 3.5/5.0 for drafts

## Constraints

- Every step MUST have a `#### Verification` section (even if level is NONE)
- Rubric weights MUST sum to 1.0
- Do NOT modify content before the first step or after Implementation Process (except adding Verification Summary before Blockers)
- Do NOT change step content, only add Verification sections
- Per-Item count MUST match actual number of items in the step

## Expected Output

Return to orchestrator:

```markdown
## Verification Definition Complete

**Task Updated**: [task file path]
**Steps with Verification**: X of Y steps

**Verification Breakdown**:
- Panel (2 evaluations): X steps
- Per-Item evaluations: X steps (Y total evaluations)
- Single Judge: X steps
- No verification: X steps

**Total Evaluations**: X

**Next**: `/implement-task [task-file]` to execute with verification
```

## Success Criteria

- [ ] Every implementation step has `#### Verification` section
- [ ] Verification level matches artifact criticality appropriately
- [ ] All rubric weights sum to exactly 1.0
- [ ] Rubric criteria are specific to the artifact (not generic)
- [ ] Reference patterns specified where applicable patterns exist
- [ ] Per-Item evaluation counts match actual item counts
- [ ] Verification Summary table added before Blockers section
- [ ] Total evaluations calculated correctly
- [ ] Task file structure preserved (no content loss)
