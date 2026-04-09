---
name: scaffold-skill
description: "Create the scaffolding Feature Requirements Document (FRD 000) from ADR decisions. Use after ADRs and AGENTS.md are finalized to define project infrastructure before feature work begins."
argument-hint: "Leave blank to generate the scaffolding FRD from current ADRs, or specify categories to focus on..."
---

# Create Scaffolding Feature Requirements

Synthesize accepted ADRs into a scaffolding FRD (`specs/features/000-project-scaffolding.md`) that defines all cross-cutting project infrastructure required before feature implementation begins.

## When to Use

- ADRs have been accepted and AGENTS.md has been generated
- The project needs infrastructure setup before feature work
- Scaffolding requirements need revision after ADR changes

## Procedure

### 1. Read Context

**ALWAYS start by reading these files — do not skip this step:**
- `specs/adr/*.md` — Architecture decisions (primary input)
- `AGENTS.md` — Engineering standards (must exist with coverage threshold)
- `specs/prd.md` — Product requirements for overall context
- `specs/features/*.md` — Existing FRDs to understand what features will need

### 2. Identify Scaffolding Categories

Review ADRs and determine which scaffolding categories apply:

| Category | What to Check | Applies When |
|----------|--------------|--------------|
| Project initialization | Language, framework, package manager ADRs | Always |
| CI/CD pipeline | Hosting, deployment, testing ADRs | Project has deployment targets |
| Containerization | Hosting, infrastructure ADRs | ADRs specify container-based deployment |
| Test infrastructure | Testing framework ADRs | Always (test runner, fixtures, harness) |
| Code quality tooling | Language, framework ADRs | Always (linter, formatter, hooks) |

**Skip categories that don't apply.** A library project doesn't need containerization. A CLI tool may not need CI/CD deployment stages. Only include categories mandated by the ADRs.

### 3. Write the Scaffolding FRD

Create `specs/features/000-project-scaffolding.md` using the [scaffolding FRD template](./assets/scaffold-frd-template.md).

For each applicable category, write concrete functional requirements that:
- **Trace to specific ADRs** — every requirement references the ADR that drives it
- **Describe WHAT, not HOW** — state what infrastructure must exist, not implementation steps
- **Are testable** — each requirement has verifiable acceptance criteria
- **Consider feature needs** — review existing FRDs to ensure scaffolding supports upcoming feature work (e.g., if a feature needs a database, test infrastructure should include DB setup)

> **Note on technology references:** Unlike standard FRDs, the scaffolding FRD derives its requirements from ADR technology choices. Technology names (e.g., "Node.js test runner", "Docker container") are expected here because the requirements describe infrastructure that implements ADR decisions, not business requirements.

### 4. ADR Traceability

Fill in the ADR Traceability table mapping every functional requirement to the ADR(s) that motivated it. This is the scaffolding equivalent of PRD requirement tracing in standard FRDs.

### 5. Reconcile with Existing FRD

If `specs/features/000-project-scaffolding.md` already exists:
- **Update** requirements whose backing ADRs have changed
- **Add** requirements for newly accepted ADRs
- **Mark deprecated** any requirements whose backing ADRs were superseded
- **Preserve requirement IDs** — never reuse `FR-N` numbers

### 6. Present for Confirmation

Present the scaffolding requirements summary and ask for confirmation before writing the file, consistent with the FRD skill workflow.

## Quality Checklist

- [ ] All accepted ADRs reviewed for scaffolding implications
- [ ] Only applicable categories included — no speculative infrastructure
- [ ] Every requirement traces to at least one ADR
- [ ] Requirements are implementation-agnostic (no code, no tool-specific config details)
- [ ] Feature FRDs reviewed to ensure scaffolding supports their needs
- [ ] `AGENTS.md` coverage threshold referenced in test infrastructure requirements
- [ ] File created at `specs/features/000-project-scaffolding.md`
