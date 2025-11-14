# Plan Feature Development

Based on current git branch if it written in format `feature/<number-padded-to-3-digits>-<kebab-case-title>`, read feature specification from `specs/<number-padded-to-3-digits>-<kebab-case-title>/spec.md` file. It was written during 1 phase of SDD workflow (Discovery).

## Phase 2:Preform Codebase Exploration

**Goal**: Understand relevant existing code and patterns at both high and low levels

**Actions**:

1. Launch 2-3 code-explorer agents in parallel. Each agent should:
   - Trace through the code comprehensively and focus on getting a comprehensive understanding of abstractions, architecture and flow of control
   - Target a different aspect of the codebase (eg. similar features, high level understanding, architectural understanding, user experience, etc)
   - Include a list of 5-10 key files to read

   **Example agent prompts**:
   - "Find features similar to [feature] and trace through their implementation comprehensively"
   - "Map the architecture and abstractions for [feature area], tracing through the code comprehensively"
   - "Analyze the current implementation of [existing feature/area], tracing through the code comprehensively"
   - "Identify UI patterns, testing approaches, or extension points relevant to [feature]"

2. Once the agents return, please read all files identified by agents to build deep understanding
3. Write research report in `reports/<number-padded-to-3-digits>-<kebab-case-title>/research.md` file
4. Present comprehensive summary of findings and patterns discovered

---

## Phase 3: Clarifying Questions

**Goal**: Fill in gaps and resolve all ambiguities before designing

**CRITICAL**: This is one of the most important phases. DO NOT SKIP.

**Actions**:

1. Review the codebase findings and original feature request
2. Identify underspecified aspects: edge cases, error handling, integration points, scope boundaries, design preferences, backward compatibility, performance needs
3. **Present all questions to the user in a clear, organized list**
4. **Wait for answers before proceeding to architecture design**

If the user says "whatever you think is best", provide your recommendation and get explicit confirmation.

---

## Phase 4: Architecture Design

**Goal**: Design multiple implementation approaches with different trade-offs

**CRITICAL**: Do not write code during this phase, use only high level planing and architecture diagrams.

**Actions**:

1. Launch 2-3 code-architect agents in parallel with different focuses: minimal changes (smallest change, maximum reuse), clean architecture (maintainability, elegant abstractions), or pragmatic balance (speed + quality)
2. Review all approaches and form your opinion on which fits best for this specific task (consider: small fix vs large feature, urgency, complexity, team context)
3. Write architecture design doc in `spec/<number-padded-to-3-digits>-<kebab-case-title>/desgin.md` file. Include all found approaches, trade-offs comparison, **your recommendation with reasoning** and concrete implementation differences.
4. Present to user: brief summary of each approach, trade-offs comparison, **your recommendation with reasoning**, concrete implementation differences
5. **Ask user which approach they prefer**

## Phase 5: Plan

**Goal**: Plan the implementation based on approach choosen by the user and clarify all unclear or uncertain areas.

**CRITICAL**: Do not write code during this phase, use only high level planing and architecture diagrams.

**Actions**:

1. Based on approach choosen by the user, plan the implementation.
2. Write implementation plan in `spec/<number-padded-to-3-digits>-<kebab-case-title>/plan.md` file.
3. Write contracts for data models and API if it applicable in `spec/<number-padded-to-3-digits>-<kebab-case-title>/contracts.md` file.
4. Present implementation plan summary to the user.
5. Review implementation plan and present unclear or unceartan areas to the user for clarification.
