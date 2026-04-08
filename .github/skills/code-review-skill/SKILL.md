---
name: code-review-skill
description: "Review implemented code against task acceptance criteria, AGENTS.md standards, and test coverage thresholds. Use after a task is implemented and before marking it complete."
argument-hint: "Specify the task to review, or leave blank to review the most recently implemented task..."
---

# Review Implementation

Review implemented code against the task specification, engineering standards, and test coverage requirements. This is the quality gate between implementation and task completion.

## When to Use

- After the dev agent completes a task implementation (code + unit tests pass)
- Before marking a task as complete
- When code changes are made to an existing feature and need validation

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `AGENTS.md` — Engineering standards and conventions to verify against
- `specs/adr/*.md` — Architecture decisions (to verify technology and pattern compliance)
- The specific task file in `specs/tasks/` — Acceptance criteria define pass/fail
- The relevant FRD in `specs/features/` — Functional requirements the code must satisfy

### 2. Identify Changed Files

Use `search/changes` to identify which source files were created or modified for the task. This scopes the review to relevant code only.

### 3. Verify Acceptance Criteria

Walk through every acceptance criterion in the task specification:

1. **Locate the implementing code** — For each criterion, identify the specific files, functions, or components that satisfy it.
2. **Trace the behavior** — Read the code path to confirm it does what the criterion describes. Do not assume from names alone.
3. **Check edge cases** — If the criterion specifies error handling, boundary conditions, or specific states, verify those paths exist.
4. **Record result** — Mark each criterion as satisfied or unsatisfied with evidence (file path + what was found or missing).

If any acceptance criterion is not met, stop and report the gap. Do not approve incomplete work.

### 4. Verify AGENTS.md Compliance

Check the implementation against the standards defined in `AGENTS.md`:

- **Naming conventions** — Variables, functions, files, classes follow the documented patterns
- **Error handling** — Errors are handled per the documented strategy (not swallowed, not leaking internals)
- **Security patterns** — Input validation at boundaries, auth checks, no hardcoded secrets
- **API design** — Endpoints follow documented conventions (error format, pagination, versioning)
- **Project structure** — Files are placed in the correct directories per the documented layout
- **Code patterns** — Implementation uses the prescribed patterns (e.g., repository pattern, dependency injection) from ADRs

Skip sections of `AGENTS.md` that don't apply to the task (e.g., frontend standards for a backend-only task).

### 5. Verify Test Coverage

- **Confirm tests pass** — The dev agent must have run all tests before handing off for review. Check the task's test output or ask the dev agent for evidence. If test results are not available, hand back to the dev agent to run tests first.
- **Check coverage** — If a coverage report is available, verify coverage meets the threshold defined in the `General > Testing` section of `AGENTS.md`. If `AGENTS.md` does not specify a coverage threshold, **stop and hand off to the arch agent** to define one via `/standards-skill` before continuing the review.
- **Assess test quality** — Tests should verify behavior, not implementation details. Flag tests that:
  - Only test the happy path without error/edge cases
  - Mock the system under test rather than its dependencies
  - Assert on implementation details (private methods, internal state) rather than observable behavior
  - Are trivially passing (e.g., `expect(true).toBe(true)`)

### 6. Check for Common Issues

Scan the changed files for:

- **Security** — SQL/NoSQL injection, XSS, hardcoded credentials, missing auth checks, unvalidated redirects
- **Resource management** — Unclosed connections, missing dispose/cleanup, unbounded collections
- **Concurrency** — Race conditions, shared mutable state without synchronization
- **Error leakage** — Stack traces or internal details exposed in error responses
- **Dead code** — Unused imports, unreachable branches, commented-out code

### 7. Report Findings

Provide a structured review:

- **Task**: Which task was reviewed (file path)
- **Verdict**: `approved` or `changes-requested`
- **Acceptance criteria**: Checklist with pass/fail per criterion and evidence
- **Standards compliance**: Any deviations from `AGENTS.md` with file path and line reference
- **Test assessment**: Coverage level, quality issues, missing test cases
- **Issues found**: Each issue with severity (blocker / warning / nit), file path, description, and suggested fix
- **Next action**: If approved, the task can be marked complete. If changes requested, list the specific changes needed before re-review.

### 8. Re-Review Loop

If the verdict is `changes-requested`, hand off to the **dev** agent with the specific findings. When the dev agent reports fixes are complete, re-run this entire procedure from Step 2 on the updated code. The loop continues until the verdict is `approved`. Each re-review is a full review — do not skip steps based on previous findings, as fixes may introduce new issues.

## Rules

- **Review only, never fix** — Do not edit source code. Report findings for the dev agent to address.
- **Evidence-based** — Every finding must reference a specific file and describe what was observed vs. what was expected.
- **Acceptance criteria are pass/fail** — A task either meets all criteria or it doesn't. No partial approvals.
- **Blockers must be fixed** — If any finding is severity `blocker`, the verdict must be `changes-requested`.
- **Warnings are advisory** — Warnings should be fixed but don't block approval on their own.
- **Nits are optional** — Style preferences that don't violate `AGENTS.md`. Note them but never block on them.
- **No scope creep** — Review against the task spec as written. Don't request features or improvements not in the acceptance criteria.

## Quality Checklist

- [ ] Every acceptance criterion has been verified with evidence
- [ ] Implementation checked against `AGENTS.md` standards
- [ ] All tests confirmed passing (dev-provided evidence)
- [ ] Test coverage meets threshold
- [ ] Security scan completed on changed files
- [ ] No source code was modified by this review
- [ ] Findings report includes verdict, evidence, and clear next action
