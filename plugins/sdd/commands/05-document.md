# Document Feature Complition

Based on current git branch if it written in format `specs/<number-padded-to-3-digits>-<kebab-case-title>`, read feature specification from `specs/<number-padded-to-3-digits>-<kebab-case-title>/**.md` files. It was written during previus phases of SDD workflow (Discovery, Research, Planining, etc.).

## Phase 10: Document Feature Complition

**Goal**: Document feature complition based on tasks list written in `specs/<number-padded-to-3-digits>-<kebab-case-title>/tasks.md` file and implementation results.

**Actions**:

1. Verify all tasks are completed based on `specs/<number-padded-to-3-digits>-<kebab-case-title>/tasks.md` file.
2. Review codebase for any missing or incomplete functionality.
3. Present to user all missing or incomplete functionality and ask if they want to fix it now or later.
4. If there no missing or incomplete functionality or user accepts the results as is, proceed to the next phase.
5. Read existing documentation in `docs/` to identify missing areas.
6. Document feature in `docs/` folder. Include API, usage guide, and architecture updates.
7. Add or update README.md files to folder that was affected by implementation, if it need. Include there development specific and overral module summary to be used by LLMs during codebase undesrstanding and navigation.
8. Present summary of written documentation to the user.
