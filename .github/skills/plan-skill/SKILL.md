---
name: plan-skill
description: "Break down feature requirements into ordered, independent technical tasks for implementation. Use when creating task files from FRDs, planning implementation order, or identifying scaffolding needs."
argument-hint: "Optionally specify which features to plan, or leave blank to plan all..."
---

# Plan Technical Tasks

Break down feature requirements into ordered, independent technical tasks that any developer can pick up and implement.

## When to Use

- New features need implementation planning
- FRDs have been reviewed and approved
- ADRs define the technology choices

## Procedure

### 1. Read and Understand Context

**ALWAYS start by reading these files — do not skip this step:**
- `specs/prd.md` — Product requirements for overall vision
- `specs/features/*.md` — Feature requirements for specific work
- `specs/adr/*.md` — Architecture decisions for technology choices and constraints
- `AGENTS.md` — Development standards and guidelines to follow

### 2. Identify Scaffolding Tasks

Based on the technologies chosen in the ADRs, create scaffolding tasks for project setup before any feature tasks. Scaffolding tasks use the `F000` prefix and must be completed before feature work begins.

Review ADRs and identify scaffolding needs across these categories:

| Category | What to Check | Example Tasks |
|----------|--------------|---------------|
| Project initialization | Language, framework, package manager ADRs | `F000-T001-project-init.md` — project skeleton, dependency install, base config |
| CI/CD pipeline | Hosting, deployment, testing ADRs | `F000-T00N-ci-cd-pipeline.md` — build workflow, test workflow, deployment stages, environment configs |
| Containerization | Hosting, infrastructure ADRs | `F000-T00N-containerization.md` — Dockerfile, compose files, registry config |
| Test infrastructure | Testing framework ADRs | `F000-T00N-test-infrastructure.md` — test runner config, test database setup, fixture/factory scaffolding, E2E test harness |
| Code quality tooling | Language, framework ADRs | `F000-T00N-code-quality.md` — linter, formatter, pre-commit hooks |

Skip categories that don't apply. A library project doesn't need containerization. A CLI tool may not need CI/CD deployment stages. Only create scaffolding tasks for what the ADRs mandate.

### 3. Reconcile Existing Tasks

Check `specs/tasks/` for tasks related to the feature being planned:
- **Update** tasks whose backing FRD requirements have changed (revised scope, new acceptance criteria)
- **Add** new tasks for newly introduced requirements
- **Deprecate** tasks whose backing requirements were deprecated in the FRD — add a `Status: Deprecated` marker, never delete
- **Preserve numbering** — never reuse or renumber existing task IDs (`FNNN-TNNN`)

If no existing tasks are found, skip this step.

### 4. Break Down Features

Create a comprehensive list of technical tasks ensuring:
- All layers of the architecture are covered for each feature (based on ADR decisions)
- Dependencies between tasks are clearly identified
- Tasks are ordered by their implementation sequence

### 5. Document Each Task

Create a file per task in `specs/tasks/` using the [task template](./assets/task-template.md).

**Naming**: `FNNN-TNNN-<task-name>.md` — where `FNNN` is the feature number from the FRD (e.g., `F001` for `001-user-authentication.md`) and `TNNN` is a per-feature sequential task number (e.g., `F001-T001-backend-scaffolding.md`, `F001-T002-login-endpoint.md`). Scaffolding tasks shared across features use `F000` (e.g., `F000-T001-project-init.md`).

Include in each task file:
- **Task title and description**: Clear explanation of what needs to be built
- **Dependencies**: Tasks that must be completed first
- **Technical requirements**: Specific details, APIs, data structures — no implementation code
- **Acceptance criteria**: Measurable, testable criteria for completion
- **Testing requirements**: Unit and integration tests required, following coverage standards in AGENTS.md

**Rules:**
- **NEVER** include implementation code in task files — describe WHAT, not HOW
- Be detailed enough that any developer can pick up the task
- Eliminate ambiguity in requirements

## Quality Checklist

- [ ] All context files read (PRD, FRDs, ADRs, AGENTS.md)
- [ ] Scaffolding tasks created and ordered first
- [ ] Existing tasks reconciled (updated, added, or deprecated)
- [ ] All architecture layers covered per feature
- [ ] Task dependencies clearly mapped
- [ ] Each task has acceptance criteria and testing requirements
- [ ] Tasks are implementation-agnostic (no code included)
- [ ] Task files created in `specs/tasks/` with proper naming
