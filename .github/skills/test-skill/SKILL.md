---
name: test-skill
description: "Define and implement integration, E2E, and contract tests for implemented features. Use when a task needs tests beyond unit tests, when setting up test infrastructure, or when validating cross-component behavior."
argument-hint: "Specify the task or feature to test, or 'setup' for test infrastructure..."
---

# Integration & E2E Testing

Define the test strategy and implement integration, end-to-end, and contract tests for implemented features.

## When to Use

- After unit tests are written and a task involves cross-component or cross-layer behavior
- When API endpoints need contract tests
- When frontend features need component or E2E tests
- When test infrastructure (fixtures, factories, test database, test server) needs setup
- When validating a feature's full workflow from entry point to persistence

**Workflow position**: This skill runs as a continuation of step 6 in `/implement-skill`, within the same task implementation. The sequence is: implement code → write unit tests → write integration/E2E tests (this skill) → hand off to `/code-review-skill`.

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `AGENTS.md` — Testing standards, coverage thresholds, and conventions
- `specs/adr/*.md` — Architecture decisions (test framework choices, infrastructure)
- `specs/tasks/**/*.md` — The task(s) being tested (acceptance criteria drive test cases)
- `specs/features/*.md` — The feature requirements (functional requirements map to integration scenarios)

### 2. Determine Test Strategy

Based on the task type, select the appropriate test levels:

| Task Type | Test Levels | Examples |
|-----------|-------------|---------|
| Backend API endpoint | Contract tests, integration tests | Request/response validation, auth enforcement, error responses |
| Database/data layer | Integration tests | Repository methods against test database, migration verification |
| Frontend component | Component tests, E2E tests | Rendered output, user interaction flows, navigation |
| Cross-service integration | Integration tests, contract tests | Service-to-service communication, message handling |
| Full feature (multi-layer) | Integration tests, E2E tests | User workflow from UI to persistence and back |

Skip test levels that don't apply. A pure data-layer task doesn't need E2E tests. A pure UI component doesn't need database integration tests.

### 3. Design Test Cases

For each applicable test level:

1. **Derive from acceptance criteria** — Every acceptance criterion in the task spec maps to at least one test case.
2. **Add boundary cases** — Invalid inputs, unauthorized access, missing data, concurrent access.
3. **Add error paths** — Network failures, timeout handling, malformed responses.
4. **Add security cases** — Auth bypass attempts, injection attacks, privilege escalation (per Zero Trust principles).

### 4. Set Up Test Infrastructure

If test infrastructure doesn't exist yet, create it before writing tests:

- **Test file organization** — Check `AGENTS.md` for test directory conventions. If none are defined, establish a convention before writing tests (e.g., `tests/integration/`, `tests/e2e/`, `tests/contract/`). Keep integration and E2E tests separate from unit tests.
- **Test database** — In-memory or containerized, seeded with known data. Never test against production or shared databases.
- **Test server** — Application instance configured for testing (test ports, test config, mocked external services).
- **Fixtures and factories** — Reusable test data builders. Prefer factories over static fixtures for flexibility.
- **Mocks and stubs** — For external service dependencies only. Never mock the system under test.
- **Environment configuration** — Test-specific environment variables, config files, or test profiles.

Follow patterns and frameworks specified in `AGENTS.md` and relevant ADRs.

### 5. Implement Tests

Write tests following the conventions in `AGENTS.md`:

- **Arrange-Act-Assert** — Clear test structure with setup, execution, and verification phases.
- **Descriptive names** — Test names should describe the scenario and expected outcome.
- **Independent tests** — No test should depend on another test's execution or side effects.
- **Clean up** — Tests must clean up their own state (database records, temp files, server instances).
- **No sleep/polling** — Use proper async waiting mechanisms, not arbitrary delays.

### 6. Run and Validate

- Run the full test suite via `execute/runInTerminal`.
- Verify all new tests pass.
- Verify existing tests still pass (no regressions).
- Check coverage meets the threshold defined in `AGENTS.md`.
- If tests fail, fix the tests or the implementation — diagnose root cause before changing either.

## Quality Checklist

- [ ] Every acceptance criterion has at least one integration or E2E test
- [ ] Error paths and boundary cases are covered
- [ ] Security-relevant scenarios are tested (auth, input validation)
- [ ] Test infrastructure is reusable (factories, helpers, config)
- [ ] Tests are independent and can run in any order
- [ ] No external service dependencies without mocks/stubs
- [ ] All tests pass, including pre-existing tests
- [ ] Coverage meets `AGENTS.md` thresholds
