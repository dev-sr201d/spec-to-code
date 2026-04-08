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

Based on the technologies chosen in the ADRs, create scaffolding tasks for project setup before any feature tasks. Scaffolding tasks must be completed before feature work begins.

### 3. Reconcile Existing Tasks

Check `specs/tasks/` for tasks related to the feature being planned:
- **Update** tasks whose backing FRD requirements have changed (revised scope, new acceptance criteria)
- **Add** new tasks for newly introduced requirements
- **Deprecate** tasks whose backing requirements were deprecated in the FRD — add a `Status: Deprecated` marker, never delete
- **Preserve numbering** — never reuse or renumber existing task IDs

If no existing tasks are found, skip this step.

### 4. Break Down Features

Create a comprehensive list of technical tasks ensuring:
- All layers of the architecture are covered for each feature (based on ADR decisions)
- Dependencies between tasks are clearly identified
- Tasks are ordered by their implementation sequence

### 5. Document Each Task

Create a file per task in `specs/tasks/` using the [task template](./assets/task-template.md).

**Naming**: `NNN-<feature-name>.md` — zero-padded, sequential (e.g., `001-backend-scaffolding.md`).

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
