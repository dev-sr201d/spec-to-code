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
- The specific task file under `specs/tasks/FNNN/` — Acceptance criteria define pass/fail
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
- **Verify mitigation tests** — For every `THR-NNN` listed in the task's `Threats Mitigated` section, locate the corresponding mitigation test and confirm it actively exercises the control (e.g., asserts a 401/403 on unauthenticated/unauthorized access, asserts rejection of malformed input, asserts rate-limit enforcement). A cited threat with no enforcing test is a `blocker`. If the task declares `Threats Mitigated: None`, confirm by reading the diff that no new trust boundary, endpoint, data store, or external integration was introduced — if one was, the task is mis-declared and the verdict is `changes-requested`.
- **Assess test quality** — Tests should verify behavior, not implementation details. Flag tests that:
  - Only test the happy path without error/edge cases
  - Mock the system under test rather than its dependencies
  - Assert on implementation details (private methods, internal state) rather than observable behavior
  - Are trivially passing (e.g., `expect(true).toBe(true)`)

### 6. Check for Production-Grade Concerns

Scan the changed files against the dev's [quality-checklist.md](../implement-skill/references/quality-checklist.md) and, for frontend code, [frontend-integration.md](../implement-skill/references/frontend-integration.md). Those files are the canonical lists; the categories below are the review lens.

#### 6.1 Security & Supply Chain

- **Injection** — SQL/NoSQL/command/LDAP injection, XSS, SSRF, unvalidated redirects, template injection
- **Secrets** — No hardcoded credentials, tokens, or internal hostnames; secrets read from a secret store
- **AuthN/AuthZ** — Every new endpoint/handler enforces authentication and authorization; no public-by-default routes; object-level authorization checks present
- **Input validation** — Validation at every boundary (API, message handler, CLI, file I/O); deserialization is safe
- **Output encoding** — Parameterized queries; encoded/escaped output; safe template rendering
- **Crypto** — No deprecated algorithms (MD5/SHA1 for auth, ECB mode); no hand-rolled crypto; strong password hashing
- **Dependencies** — New dependencies are maintained, license-compatible, and free of known CVEs (verify via package-audit / SCA output)

#### 6.2 Observability

- **Structured logs** at boundaries with correlation/request IDs
- **No PII, secrets, tokens, or full payloads in logs** — verify via grep on changed log statements
- **Errors logged with diagnostic context** (operation, identifiers, sanitized inputs)
- **Metrics and traces** emitted for new code paths per `AGENTS.md` instrumentation standards
- **User-facing errors** do not leak stack traces, SQL, file paths, or internal class names

#### 6.3 Reliability & Performance

- **Timeouts and bounded retries** on every external call; no infinite loops or unbounded fan-out
- **Resource cleanup** on every path including error paths (connections, file handles, subscriptions, tasks)
- **No obvious N+1 queries**, blocking I/O on async paths, synchronous network calls inside loops, or unbounded in-memory collections
- **Concurrency** — Shared mutable state is synchronized or eliminated; no TOCTOU races
- **Idempotency** — Retryable operations (writes, message handlers, webhooks) are safe to replay

#### 6.4 Data & Migrations

- **Migration safety** — Forward-only and backward-compatible with the prior app version (expand-contract); no destructive operations without explicit FRD authorization
- **Migration testing** — Tested on a non-empty dataset; idempotent
- **Data classification** — PII / sensitive fields handled per `AGENTS.md` (encryption, redaction, retention)
- **Schema/contract changes** — OpenAPI / GraphQL / proto / event-schema artifacts updated alongside code

#### 6.5 Risk Controls & Compatibility

- **Backward compatibility** — Public API contracts preserved unless the FRD authorizes a breaking change; deprecations follow the project's deprecation policy
- **Feature flags / kill switches** — Risky changes gated where `AGENTS.md` or the FRD requires it
- **Rollback path** — Obvious and recorded in the task (revert, flag off, migration-down)

#### 6.6 Hygiene

- **Dead code** — Unused imports, unreachable branches, commented-out code
- **Tooling drift** — No new compiler/linter warnings introduced
- **CHANGELOG / release notes** updated if the project maintains them

### 7. Report Findings

Provide a structured review:

- **Task**: Which task was reviewed (file path)
- **Verdict**: `approved` or `changes-requested`
- **Acceptance criteria**: Checklist with pass/fail per criterion and evidence
- **Threat mitigations**: For each `THR-NNN` cited in the task, the test that enforces the control with file path and assertion summary
- **Standards compliance**: Any deviations from `AGENTS.md` with file path and line reference
- **Test assessment**: Coverage level, quality issues, missing test cases
- **Issues found**: Each issue with severity (blocker / warning / nit), file path, description, and suggested fix
- **Next action**: If approved, the task can be marked complete. If changes requested, list the specific changes needed before re-review.

### 8. Re-Review Loop

If the verdict is `changes-requested`, hand off to the **dev** agent with the specific findings. When the dev agent reports fixes are complete, re-run this entire procedure from Step 2 on the updated code. Each re-review is a full review — do not skip steps based on previous findings, as fixes may introduce new issues.

**Iteration limit.** A task may go through at most **three** review cycles (initial review + two re-reviews). If a fourth cycle would be required, **stop the loop** and escalate to the user with:

- The task ID and parent FRD
- A delta summary across cycles: which findings were fixed, which recurred, which are new
- The persistent root cause if identifiable (e.g., "acceptance criterion is ambiguous", "dev and lead disagree on whether requirement is met", "underlying ADR does not cover this case")
- A recommended next action: refine the FRD via **po**, amend an ADR via **arch**, split the task via **dev**, or accept the current state with a captured deviation

Do not silently approve to break the loop, and do not allow indefinite ping-pong. Escalation is a normal outcome, not a failure.

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
- [ ] Every cited `THR-NNN` has an enforcing mitigation test located in the diff
- [ ] Tasks declaring `Threats Mitigated: None` have been confirmed against the diff (no new trust boundaries introduced)
- [ ] Implementation checked against `AGENTS.md` standards
- [ ] All tests confirmed passing (dev-provided evidence)
- [ ] Test coverage meets threshold
- [ ] Production-grade concerns reviewed (security, observability, reliability, data, risk controls)
- [ ] No source code was modified by this review
- [ ] Iteration limit respected — escalated to user if a fourth review cycle would be required
- [ ] Findings report includes verdict, evidence, and clear next action
