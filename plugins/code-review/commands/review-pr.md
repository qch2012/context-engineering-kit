---
description: Comprehensive pull request review using specialized agents
allowed-tools: ["Bash", "Glob", "Grep", "Read", "Task", "Bash(gh issue view:*)", "Bash(gh search:*)", "Bash(gh issue list:*)", "Bash(gh pr comment:*)", "Bash(gh pr diff:*)", "Bash(gh pr view:*)", "Bash(gh pr list:*)"]
disable-model-invocation: false
argument-hint: "[review-aspects]"
---

# Pull Request Review Instructions

You are an expert code reviewer conducting a thorough evaluation of this pull request. Your review must be structured, systematic, and provide actionable feedback.

**IMPORTANT**: Skip reviewing changes in `spec/` and `reports/` folders unless specifically asked.

## Steps

To do this, follow these steps precisely:

### Phase 1: Preparation

1. Use a Haiku agent to check if the pull request (a) is closed, (b) is a draft, (c) does not need a code review (eg. because it is an automated pull request, or is very simple and obviously ok), or (d) already has a code review from you from earlier. If so, do not proceed.
2. Use Haiku agent to give you a list of file paths to (but not the contents of) any relevant CLAUDE.md files from the codebase: the root CLAUDE.md file (if one exists), as well as any CLAUDE.md files in the directories whose files the pull request modified
3. Use a Haiku agent to view the pull request, and ask the agent to return a summary of the change
4. If PR missing description, ask Haiku agent to add a description to the PR with summary of changes in short and concise format.

### Phase 2: Searching for Issues

Launch 5 parallel Sonnet agents to independently code review the change. The agents should do the following, then return a list of issues and the reason each issue was flagged (eg. CLAUDE.md adherence, bug, historical git context, etc.):

#### Agent #1: Security Auditor

Pass the following prompt to the agent, prefilling `modified_files_list` variable based on phase 1:

```markdown
Read the file changes in the pull request, then audit changes to make sure they not have any security vulnerabilities.

List of modified files:
{modified_files_list}


Report back in the following format:

## 🔒 Security Analysis

### Security Checklist
- [ ] **SQL Injection**: All database queries use parameterized statements or ORMs, zero string concatenation
- [ ] **XSS Prevention**: All user input is HTML-escaped before rendering, zero innerHTML with user data
- [ ] **CSRF Protection**: All state-changing requests require CSRF token validation
- [ ] **Authentication Required**: All protected endpoints check authentication before processing
- [ ] **Authorization Enforced**: All resource access checks user permissions, not just authentication
- [ ] **No Hardcoded Secrets**: Zero passwords, API keys, tokens, or credentials in code
- [ ] **Input Validation**: All inputs validated for type, length, format before processing
- [ ] **Output Encoding**: All data encoded appropriately for context (HTML, URL, JS, SQL)
- [ ] **No Vulnerable Dependencies**: Zero dependencies with known CVEs (check package versions)
- [ ] **HTTPS Only**: All sensitive data transmission requires HTTPS, no HTTP fallback
- [ ] **Session Invalidation**: All logout operations invalidate server-side sessions
- [ ] **Rate Limiting Applied**: All authentication endpoints have rate limiting
- [ ] **File Upload Validation**: All file uploads check type, size, and scan content
- [ ] **No Stack Traces**: Error responses contain zero technical details/stack traces
- [ ] **No Sensitive Logs**: Zero passwords, tokens, SSNs, or credit cards in log files
- [ ] **Path Traversal Prevention**: All file operations validate paths, no "../" acceptance
- [ ] **Command Injection Prevention**: Zero shell command execution with user input
- [ ] **XXE Prevention**: XML parsing has external entity processing disabled
- [ ] **Insecure Deserialization**: Zero untrusted data deserialization without validation
- [ ] **Security Headers**: All responses include security headers (CSP, X-Frame-Options, etc.)

### Security Vulnerabilities Found

| Severity | File | Line | Vulnerability Type | Specific Risk | Required Fix |
|----------|------|------|-------------------|---------------|--------------|
| Critical | | | | | |
| High | | | | | |
| Medium | | | | | |
| Low | | | | | |

**Severity Classification**:
   - **Critical**: Can be misused by bad actors to gain unauthorized access to the system or fully shutdown the system
   - **High**: Can be misused to perform some actions without proper authorization or get access to some sensitive data
   - **Medium**: May cause issues in edge cases or degrade performance
   - **Low**: Not have real impact on the system, but violates security practices


**Security Score: X/Y** *(Passed security checks / Total applicable checks)*

```

#### Agent #2: Bug Hunter

Pass the following prompt to the agent, prefilling `modified_files_list` variable based on phase 1:

```markdown
Read the file changes in the pull request, then do a shallow scan for obvious bugs. Avoid reading extra context beyond the changes, focusing just on the changes themselves. Focus on large bugs, and avoid small issues and nitpicks. Ignore likely false positives.

List of modified files:
{modified_files_list}

Report back in the following format:

## 🐛 Potential Issues & Bugs

| File | Line | Issue | Evidence | Impact | Fix Required |
|------|------|-------|----------|--------|--------------|
| | | | | | |

Impact types:
- Critical: Will cause runtime errors, data loss, or system crash
- High: Will break core features or corrupt data under normal usage
- Medium: Will cause errors under edge cases or degrade performance
- Low: Will cause code smells that don't affect functionality but hurt maintainability

## Evaluation Instructions

1. **Evidence Required**: For every failed item, provide:
   - Exact file path
   - Line number(s)
   - Specific code snippet showing the violation
   - Concrete fix required

2. **No Assumptions**: Only mark items based on code present in the PR. Don't assume about code outside the diff.

```

#### Agent #3: Code Quality Reviewer

Pass the following prompt to the agent, prefilling `modified_files_list` variable based on phase 1:

```markdown
Read the file changes in the pull request, then review the code quality. Focus on large issues, and avoid small issues and nitpicks. Ignore likely false positives.

List of modified files:
{modified_files_list}

Report back in the following format:

## 📋 Code Quality Checklist

For each failed check provide explanation and path to the file and line number of the issue.

### Clean Code Principles
- [ ] **DRY (Don't Repeat Yourself)**: Zero duplicated logic - any logic appearing 2+ times is extracted into a reusable function/module
- [ ] **KISS (Keep It Simple)**: All solutions use the simplest possible approach - no over-engineering or unnecessary complexity exists
- [ ] **YAGNI (You Aren't Gonna Need It)**: Zero code written for future/hypothetical requirements - all code serves current needs only
- [ ] **Early Returns**: All functions/methods use early return pattern instead of nested if-else when possible
- [ ] **Function Length**: All functions are 80 lines or less (including comments and blank lines)
- [ ] **File Size**: All files contain 200 lines or less (including comments and blank lines)
- [ ] **Method Arguments**: All functions/methods have 3 or fewer parameters, and use objects when need more than 3
- [ ] **Cognitive Complexity**: All functions have cyclomatic complexity ≤ 10
- [ ] **No Magic Numbers**: Zero hardcoded numbers in logic - all numbers are named constants
- [ ] **No Dead Code**: Zero commented-out code, unused variables, or unreachable code blocks

### SOLID Principles
- [ ] **Single Responsibility (Classes)**: Every class has exactly one responsibility - no class handles multiple unrelated concerns
- [ ] **Single Responsibility (Functions)**: Every function/method performs exactly one task - no function does multiple unrelated operations
- [ ] **Open/Closed**: All classes can be extended without modifying existing code
- [ ] **Liskov Substitution**: All derived classes can replace base classes without breaking functionality
- [ ] **Interface Segregation**: All interfaces contain only methods used by all implementers
- [ ] **Dependency Inversion**: All high-level modules depend on abstractions, not concrete implementations

### Naming Conventions
- [ ] **Variable Names**: All variables use full words, no single letters except loop counters (i,j,k)
- [ ] **Function Names**: All functions start with a verb and describe what they do (e.g., `calculateTotal`, not `total`)
- [ ] **Class Names**: All classes are nouns/noun phrases in PascalCase (e.g., `UserAccount`)
- [ ] **Boolean Names**: All boolean variables/functions start with is/has/can/should/will
- [ ] **Constants**: All constants use UPPER_SNAKE_CASE 
- [ ] **No Abbreviations**: Zero unclear abbreviations - `userAccount` not `usrAcct`
- [ ] **Collection Names**: All arrays/lists use plural names (e.g., `users` not `userList`)
- [ ] **Consistency**: All naming follows the same convention throughout (no mixing camelCase/snake_case)

### Architecture Patterns
- [ ] **Layer Boundaries**: Zero direct database calls from presentation layer, zero UI logic in data layer
- [ ] **Dependency Direction**: All dependencies point inward (UI→Domain→Data) with zero reverse dependencies
- [ ] **No Circular Dependencies**: Zero bidirectional imports between any modules/packages
- [ ] **Proper Abstractions**: All external dependencies are accessed through interfaces/abstractions
- [ ] **Pattern Consistency**: Same pattern used throughout (all MVC or all MVVM, not mixed)
- [ ] **Domain Isolation**: Business logic contains zero framework-specific code

### Error Handling
- [ ] **No Empty Catch**: Zero empty catch blocks - all errors are logged/handled/re-thrown
- [ ] **Specific Catches**: All catch blocks handle specific exception types, no generic catch-all
- [ ] **Error Recovery**: All errors have explicit recovery strategy or propagate to caller
- [ ] **User Messages**: All user-facing errors provide actionable messages, not technical stack traces
- [ ] **Consistent Strategy**: Same error handling pattern used throughout (all try-catch)
- [ ] **No String Errors**: All errors are typed objects/classes, not plain strings

### Performance & Resource Management
- [ ] **No N+1 Queries**: All database operations use batch loading/joins where multiple records needed
- [ ] **Resource Cleanup**: All opened resources (files/connections/streams) have explicit cleanup/close
- [ ] **No Memory Leaks**: All event listeners are removed, all intervals/timeouts are cleared
- [ ] **Efficient Loops**: All loops that can be O(n) are O(n), not O(n²) or worse
- [ ] **Lazy Loading**: All expensive operations are deferred until actually needed
- [ ] **No Blocking Operations**: All I/O operations are async/non-blocking in event-loop environments

### Frontend Specific (if applicable)
- [ ] **No Inline Styles**: Zero style attributes in HTML/JSX - all styles in SCSS/styled-components
- [ ] **No Prop Drilling**: Props pass through maximum 2 levels - deeper uses context/state management
- [ ] **Memoization**: All expensive computations (loops, filters, sorts) are memoized/cached
- [ ] **Key Props**: All list items have unique, stable key props (not array indices)
- [ ] **Event Handler Naming**: All event handlers named `handle[Event]` or `on[Event]` consistently
- [ ] **Component File Size**: All components files are under 200 lines (excluding imports/exports)
- [ ] **No Direct DOM**: Zero direct DOM manipulation (getElementById, querySelector) in React/Vue/Angular
- [ ] **No render functions**: Zero render functions defined inside of component functions, create separate component and use composition instead
- [ ] **No nested compomponent definitions**: Zero component functions defined inside of other component functions, create component on first level of component file
- [ ] **No unreactive variables definitions inside of compomnent**: Unreactive variables, constants and functions allways defined outside of component functions on first level of component file
- [ ] **Input Validation**: All inputs are validated using class-validator or similar library, not in component functions

### Backend Specific (if applicable)  
- [ ] **Only GraphQL or gRPC**: Zero REST endpoints, only GraphQL or gRPC endpoints are allowed, except for health check and readiness check endpoints
- [ ] **RESTful practices**: If REST is used, follow RESTful practices (GET for read, POST for create, etc.)
- [ ] **Status Codes**: All responses use correct HTTP status codes (200 for success, 404 for not found, etc.)
- [ ] **Idempotency**: All PUT/DELETE operations produce same result when called multiple times 
- [ ] **Request Validation**: All requests are validated using graphql validation rules or grpc validation rules, not in controllers
- [ ] **No Business Logic in Controllers**: Controllers only handle HTTP, all logic in services/domain
- [ ] **Transaction Boundaries**: All multi-step database operations wrapped in transactions, sagas or workflows
- [ ] **API Versioning**: All breaking changes handled through version prefix (/v1, /v2) or headers

### Database & Data Access (if applicable)
- [ ] **Declarative Datbase Definitions**: Allways used prisma.schema or similar library for database definitions, not in SQL files
- [ ] **No SQL queries**: All database queries are done through prisma.schema or similar library, not through the SQL
- [ ] **Parameterized Queries**: All SQL/prisma queries use parameters, zero string concatenation for queries
- [ ] **Index Usage**: All WHERE/JOIN columns have indexes defined
- [ ] **Batch Operations**: All bulk operations use batch insert/update, not individual queries in loops
- [ ] **Pagination**: All queries use cursor pagination, not offset/limit
- [ ] **Sorting**: All queries use sorting, not hardcoded order by
- [ ] **Filtering**: All queries use filtering, not hardcoded where clauses
- [ ] **Joins**: All queries use joins, not hardcoded joins
- [ ] **Connection Pooling**: Database connections are pooled, not created per request
- [ ] **Migration Safety**: All schema changes are backwards compatible or versioned

**Quality Score: X/Y** *(Count of checked (correct) items / Total applicable items)*

## Evaluation Instructions

1. **Binary Evaluation**: Each checklist item must be marked as either passed (✓) or failed (✗). No partial credit.

2. **Evidence Required**: For every failed item, provide:
   - Exact file path
   - Line number(s)
   - Specific code snippet showing the violation
   - Concrete fix required

3. **No Assumptions**: Only mark items based on code present in the PR. Don't assume about code outside the diff.

4. **Language-Specific Application**: Apply only relevant checks for the language/framework:
   - Skip frontend checks for backend PRs
   - Skip database checks for static sites
   - Skip class-based checks for functional programming

5. **Context Awareness**: Check repository's existing patterns before flagging inconsistencies





```

### Agent #4: Test Coverage Reviewer

Pass the following prompt to the agent, prefilling `modified_files_list` variable based on phase 1:

```markdown
Read the file changes in the pull request, then review the test coverage. Focus on large issues, and avoid small issues and nitpicks. Ignore likely false positives.

List of modified files:
{modified_files_list}

Report back in the following format:

## 🧪 Test Coverage Analysis

### Test Coverage Checklist
- [ ] **All Public Methods Tested**: Every public method/function has at least one test
- [ ] **Happy Path Coverage**: All success scenarios have explicit tests
- [ ] **Error Path Coverage**: All error conditions have explicit tests  
- [ ] **Boundary Testing**: All numeric/collection inputs tested with min/max/empty values
- [ ] **Null/Undefined Testing**: All optional parameters tested with null/undefined
- [ ] **Integration Tests**: All external service calls have integration tests
- [ ] **No Test Interdependence**: All tests can run in isolation, any order
- [ ] **Meaningful Assertions**: All tests verify specific values, not just "not null"
- [ ] **Test Naming Convention**: All test names describe scenario and expected outcome
- [ ] **No Hardcoded Test Data**: All test data uses factories/builders, not magic values
- [ ] **Mocking Boundaries**: External dependencies mocked, internal logic not mocked

### Missing Critical Test Coverage

| Component/Function | Test Type Missing | Business Risk | Importance |
|-------------------|------------------|---------------|------------|
| | | | High/Medium/Low |

### Test Quality Issues Found

| File | Issue | Impact |
|------|-------|--------|
| | | |

**Test Coverage Score: X/Y** *(Covered scenarios / Total critical scenarios)*

## Evaluation Instructions

1. **Binary Evaluation**: Each checklist item must be marked as either passed (✓) or failed (✗). No partial credit.

2. **Evidence Required**: For every failed item, provide:
   - Exact file path
   - Line number(s)
   - Specific code snippet showing the violation
   - Concrete fix required

3. **No Assumptions**: Only mark items based on code present in the PR. Don't assume about code outside the diff.

4. **Language-Specific Application**: Apply only relevant checks for the language/framework:
   - Skip frontend checks for backend PRs
   - Skip database checks for static sites
   - Skip class-based checks for functional programming

5. **Testing Focus**: Only flag missing tests for:
   - New functionality added
   - Bug fixes (regression tests)
   - Modified business logic

6. **Context Awareness**: Check repository's existing patterns before flagging inconsistencies

```

#### Agent #5: Historical Context Reviewer

Pass the following prompt to the agent, prefilling `modified_files_list` variable based on phase 1:

```markdown
1. Read the git blame and history of the code modified, to identify any bugs in light of that historical context.
2. Read previous pull requests that touched these files, and check for any comments on those pull requests that may also apply to the current pull request.
3. Provide a summary of the historical context and any issues that were found.

List of modified files:
{modified_files_list}

```

### Phase 3: Confidence Scoring

1. For each issue found in Phase 2, launch a parallel Haiku agent that takes the PR, issue description, and list of CLAUDE.md files (from step 2), and returns a score to indicate the agent's level of confidence for whether the issue is real or false positive. To do that, the agent should score each issue on a scale from 0-100, indicating its level of confidence. For issues that were flagged due to CLAUDE.md instructions, the agent should double check that the CLAUDE.md actually calls out that issue specifically. The scale is (give this rubric to the agent verbatim):
   a. 0: Not confident at all. This is a false positive that doesn't stand up to light scrutiny, or is a pre-existing issue.
   b. 25: Somewhat confident. This might be a real issue, but may also be a false positive. The agent wasn't able to verify that it's a real issue. If the issue is stylistic, it is one that was not explicitly called out in the relevant CLAUDE.md.
   c. 50: Moderately confident. The agent was able to verify this is a real issue, but it might be a nitpick or not happen very often in practice. Relative to the rest of the PR, it's not very important.
   d. 75: Highly confident. The agent double checked the issue, and verified that it is very likely it is a real issue that will be hit in practice. The existing approach in the PR is insufficient. The issue is very important and will directly impact the code's functionality, or it is an issue that is directly mentioned in the relevant CLAUDE.md.
   e. 100: Absolutely certain. The agent double checked the issue, and confirmed that it is definitely a real issue, that will happen frequently in practice. The evidence directly confirms this.
2. Filter out any issues with a score less than 80. If there are no issues that meet this criteria, do not proceed.
3. Use a Haiku agent to repeat the eligibility check from Phase 1, to make sure that the pull request is still eligible for code review. (In case if there was updates since review started)
4. Finally, use the gh bash command to comment back on the pull request with the result. When writing your comment, keep in mind to:
   a. Keep your output brief
   b. Avoid emojis
   c. Link and cite relevant code, files, and URLs

#### Examples of false positives, for Phase 3

- Pre-existing issues
- Something that looks like a bug but is not actually a bug
- Pedantic nitpicks that a senior engineer wouldn't call out
- Issues that a linter, typechecker, or compiler would catch (eg. missing or incorrect imports, type errors, broken tests, formatting issues, pedantic style issues like newlines). No need to run these build steps yourself -- it is safe to assume that they will be run separately as part of CI.
- General code quality issues (eg. lack of test coverage, general security issues, poor documentation), unless explicitly required in CLAUDE.md
- Issues that are called out in CLAUDE.md, but explicitly silenced in the code (eg. due to a lint ignore comment)
- Changes in functionality that are likely intentional or are directly related to the broader change
- Real issues, but on lines that the user did not modify in their pull request

Notes:

- Use build, lint and tests commands if you have access to them. They can help you find potential issues that are not obvious from the code changes.
- Use `gh` to interact with Github (eg. to fetch a pull request, or to create inline comments), rather than web fetch
- Make a todo list first
- You must cite and link each bug (eg. if referring to a CLAUDE.md, you must link it)

### Report review to pull request

#### If you found issues

Add a comment to the pull request, follow the following format precisely:

```markdown
# PR Review Report

**Quality Gate**: ⬜ PASS (Can merge) / ⬜ FAIL (Requires fixes)

**Blocking Issues Count**: X
- Security 
   - Score: X/Y** *(Passed security checks / Total applicable checks)*
   - Vulnerabilities: Critical: X, High: X, Medium: X, Low: X
- Test Coverage
   - Score: X/Y** *(Covered scenarios / Total critical scenarios)*
- Code Quality
   - Score: X/Y** *(Count of checked (correct) items / Total applicable items)*

## 🔄 Required Actions

### 🚫 Must Fix Before Merge
*(Blocking issues that prevent merge)*

1. 

### ⚠️ Better to Fix Before Merge
*(Issues that can be addressed in this or in next PRs)*

1. 

### 💡 Consider for Future
*(Suggestions for improvement, not blocking)*

1. 

---

## 🐛 Found Issues & Bugs

Detailed list of issues and bugs found in the pull request:

| Link to file | Issue | Evidence | Impact | 
|--------------|-------|----------|--------|
| <link to file> | <brief description of bug or issue> | <evidence> | <impact> |

Impact types:
- Critical: Will cause runtime errors, data loss, or system crash
- High: Will break core features or corrupt data under normal usage
- Medium: Will cause errors under edge cases or degrade performance
- Low: Will cause code smells that don't affect functionality but hurt maintainability

### Security Vulnerabilities Found

Detailed list of security vulnerabilities found in the pull request:

| Severity | Link to file | Vulnerability Type | Specific Risk | Required Fix |
|----------|------|------|-------------------|---------------|--------------|
| <severity> | <link to file> | <brief description of vulnerability> | <specific risk> | <required fix> |


**Severity Classification**:
   - **Critical**: Can be misused by bad actors to gain unauthorized access to the system or fully shutdown the system
   - **High**: Can be misused to perform some actions without proper authorization or get access to some sensitive data
   - **Medium**: May cause issues in edge cases or degrade performance
   - **Low**: Not have real impact on the system, but violates security practices

## 📋 Failed checklist items

Detailed list of failed code quality and test coverage checklist items found in the pull request:

| Link to file | Issue | Description | Fix Required |
|------|------|-------|-----------|--------------|
| <link to file> | <brief description of issue> | <description of issue> | <required fix> |

```

Note:

- <link to file> - is a link to file and line with full sha1 + line range for context, note that you MUST provide the full sha and not use bash here, eg. https://github.com/anthropics/claude-code/blob/1d54823877c4de72b2316a64032a54afc404e619/README.md#L13-L17
- When linking to code, follow the following format precisely, otherwise the Markdown preview won't render correctly: <https://github.com/anthropics/claude-cli-internal/blob/c21d3c10bc8e898b7ac1a2d745bdc9bc4e423afe/package.json#L10-L15>
  - Requires full git sha
  - You must provide the full sha. Commands like `https://github.com/owner/repo/blob/$(git rev-parse HEAD)/foo/bar` will not work, since your comment will be directly rendered in Markdown.
  - Repo name must match the repo you're code reviewing
  - # sign after the file name
  - Line range format is L[start]-L[end]
  - Provide at least 1 line of context before and after, centered on the line you are commenting about (eg. if you are commenting about lines 5-6, you should link to `L4-7`)

Evaluation Instructions

- **Security First**: Any High or Critical security issue automatically becomes blocker for merge
- **Quantify Everything**: Use numbers, not words like "some", "many", "few"
- **Skip Trivial Issues** in large PRs (>500 lines):
  - Focus on architectural and security issues
  - Ignore minor naming conventions
  - Prioritize bugs over style

#### if you found no issues

```markdown

# PR Review Report

No issues found. Checked for bugs and CLAUDE.md compliance.

```

## Remember

The goal is to catch bugs and security issues, improve code quality while maintaining development velocity, not to enforce perfection. Be thorough but pragmatic, focus on what matters for code safety and maintainability.
