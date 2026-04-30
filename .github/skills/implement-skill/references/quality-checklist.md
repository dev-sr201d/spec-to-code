# Quality Checklist

Before marking a task complete, verify:

## Build & Tests

- [ ] All tests pass (unit, integration, regression)
- [ ] Code coverage meets the threshold defined in `AGENTS.md`
- [ ] Type safety enforced (no type errors)
- [ ] Linters and formatters pass
- [ ] No new compiler/tooling warnings introduced

## Standards & Documentation

- [ ] Code follows team standards in `AGENTS.md`
- [ ] Public APIs and exported symbols documented
- [ ] OpenAPI / GraphQL schema / contract artifacts updated when interfaces change
- [ ] CHANGELOG entry added if the project maintains one
- [ ] Deprecations announced via the project's deprecation policy (no silent removals)

## Security & Supply Chain

- [ ] No secrets, tokens, credentials, or internal hostnames committed (verified by secret-scan output or grep)
- [ ] Inputs validated at every system boundary (API, message handler, CLI, file I/O)
- [ ] AuthN/AuthZ enforced on every new endpoint/handler — no public-by-default routes
- [ ] Output encoding / parameterized queries used — no string-concatenated SQL/HTML/shell
- [ ] New dependencies vetted: maintained, license compatible, no known CVEs
- [ ] SAST / dependency-audit run locally or in CI with no new high/critical findings

## Observability

- [ ] Structured logs emitted at boundaries with correlation/request IDs
- [ ] No PII, secrets, tokens, or full request bodies in logs
- [ ] Errors logged with enough context to diagnose without reproducing
- [ ] Metrics / traces emitted for new code paths per `AGENTS.md` instrumentation standards
- [ ] User-facing errors do not leak stack traces or internal details

## Reliability & Performance

- [ ] External calls have timeouts and bounded retries (no infinite loops, no unbounded fan-out)
- [ ] Resources (connections, file handles, subscriptions) are released on every path including errors
- [ ] No obvious N+1 queries, blocking I/O on async paths, or unbounded in-memory collections
- [ ] Concurrency: shared mutable state is synchronized or eliminated
- [ ] Idempotency considered for retryable operations (writes, message handlers)

## Data & Migrations

- [ ] Database migrations are forward-only and backward-compatible with the previous app version (expand-contract)
- [ ] Migrations are idempotent and tested on a non-empty dataset
- [ ] Data classification respected — PII fields encrypted/redacted per `AGENTS.md`
- [ ] Retention / deletion semantics implemented when required by the FRD

## Risk Controls

- [ ] Risky changes gated behind a feature flag or kill switch where the FRD/AGENTS.md requires it
- [ ] Backward compatibility preserved for public APIs unless an FRD explicitly authorizes a breaking change
- [ ] Rollback path is obvious (revert PR, flag off, migration down) and documented in the task notes
