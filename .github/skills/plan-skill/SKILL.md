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
- `specs/threat-model.md` — Threats and required mitigations that the tasks must deliver
- `AGENTS.md` — Development standards and guidelines to follow

**Context management for large projects:** When the project has many ADRs or FRDs, read selectively to stay within context limits:
- If planning a **specific feature**, read that feature's FRD in full, but only skim other FRDs for cross-feature dependencies.
- Read only the **ADRs referenced by the target FRD** (check ADR numbers cited in requirements), not all ADRs. Read remaining ADRs only if the FRD references infrastructure or patterns you haven't seen yet.
- Read **every threat in `specs/threat-model.md`** whose `Owning artifact` is the target FRD or any ADR referenced by that FRD — these are the mitigations the planned tasks must implement.
- Read `AGENTS.md` in full — it's always relevant for task acceptance criteria and testing thresholds.
- Read `specs/prd.md` for overall vision — focus on the requirements table and scope boundaries rather than every section.

### 2. Verify Scaffolding FRD

Before planning any feature, confirm that `specs/features/000-project-scaffolding.md` exists. This FRD is authored by the **arch agent** via `/scaffold-skill` and defines all cross-cutting project infrastructure.

- If the scaffolding FRD **exists**, treat it like any other feature when creating tasks — scaffolding tasks use the `F000` prefix and must be completed before feature work begins. If the feature being planned requires infrastructure not covered by the existing scaffolding FRD (e.g., a database, message queue, or new deployment target), **hand off to the arch agent** to update the scaffolding FRD before proceeding.
- If the scaffolding FRD **does not exist**, **stop** and hand off to the **arch agent** to create it via `/scaffold-skill` before proceeding with planning.

### 3. Reconcile Existing Tasks

Check `specs/tasks/FNNN/` (the feature's task folder) for tasks related to the feature being planned:
- **Update** tasks whose backing FRD requirements have changed (revised scope, new acceptance criteria)
- **Add** new tasks for newly introduced requirements
- **Deprecate** tasks whose backing requirements were deprecated in the FRD — add a `Status: Deprecated` marker, never delete
- **Preserve numbering** — never reuse or renumber existing task IDs (`FNNN/NNN`)

If no existing tasks are found, skip this step.

### 4. Break Down Features

Create a comprehensive list of technical tasks ensuring:
- All layers of the architecture are covered for each feature (based on ADR decisions)
- Dependencies between tasks are clearly identified
- Tasks are ordered by their implementation sequence

**Task granularity:** Each task should be independently implementable and reviewable in a single session. If a task touches more than 3 architecture layers or would require more than ~300 lines of new code, split it. If a task would produce fewer than ~20 lines, merge it with a related task.

### 5. Document Each Task

Create a file per task under `specs/tasks/FNNN/` using the [task template](./assets/task-template.md). Create the per-feature folder if it does not yet exist.

**Layout & naming**: `specs/tasks/FNNN/NNN-<task-name>.md` — where `FNNN` is the feature number from the FRD (e.g., `F001` for `001-user-authentication.md`) and `NNN` is a per-feature sequential task number with a kebab-case description (e.g., `specs/tasks/F001/001-backend-scaffolding.md`, `specs/tasks/F001/002-login-endpoint.md`). Scaffolding tasks shared across features live under `specs/tasks/F000/` (e.g., `specs/tasks/F000/001-project-init.md`). Reference tasks elsewhere by the path-style ID `FNNN/NNN` (e.g., `F001/002`).

Include in each task file:
- **Task title and description**: Clear explanation of what needs to be built
- **Dependencies**: Tasks that must be completed first
- **Threats Mitigated**: Every `THR-NNN` from `specs/threat-model.md` whose required mitigation this task delivers — or `None` with a one-line rationale when the task implements no mitigation. Each cited threat must map to at least one acceptance criterion and one mitigation test.
- **Technical requirements**: Specific details, APIs, data structures — no implementation code
- **Acceptance criteria**: Measurable, testable criteria for completion
- **Testing requirements**: Unit and integration tests required, following coverage standards in AGENTS.md. Mitigation tests are mandatory for every cited `THR-NNN`.

**Rules:**
- **NEVER** include implementation code in task files — describe WHAT, not HOW
- Be detailed enough that any developer can pick up the task
- Eliminate ambiguity in requirements
- **Every required mitigation in scope must be assigned to a task.** After drafting the task set, walk every threat in `specs/threat-model.md` whose owning artifact is the target FRD (or an ADR it references) and confirm at least one task carries that `THR-NNN` in its `Threats Mitigated`. Unassigned mitigations are blockers — either add a task, extend an existing task, or hand off to **arch** if the mitigation belongs in scaffolding (`F000`).

### 6. Report Threat Coverage to Arch

After all task files for the feature are written or reconciled, produce a `THR-NNN → FNNN/NNN` coverage table and hand it to the **arch** agent so the threat model's task back-references can be updated via `/threat-model-skill`. The dev agent does not edit `specs/threat-model.md` directly.

Include in the handoff:
- Every in-scope threat ID and the task(s) that carry it
- Any threat with no implementing task, with rationale (deferred, accepted-risk pending, scaffolding-owned)
- The full task list for the feature, so arch can also confirm scaffolding coverage

## Quality Checklist

- [ ] All context files read (PRD, FRDs, ADRs, threat model, AGENTS.md)
- [ ] Scaffolding FRD (`000-project-scaffolding.md`) exists — handed off to arch if missing
- [ ] Existing tasks reconciled (updated, added, or deprecated)
- [ ] All architecture layers covered per feature
- [ ] Task dependencies clearly mapped
- [ ] Each task has acceptance criteria and testing requirements
- [ ] Every required mitigation in scope is assigned to at least one task via `Threats Mitigated`
- [ ] Tasks with no mitigation declare `Threats Mitigated: None` with rationale (no silent omissions)
- [ ] `THR-NNN → FNNN/NNN` coverage table handed to arch for threat-model back-reference update
- [ ] Tasks are implementation-agnostic (no code included)
- [ ] Task files created under `specs/tasks/FNNN/` with proper naming
