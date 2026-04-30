---
name: plan-review-skill
description: "Review task specifications for quality, correctness, and adherence to the parent FRD. Use when task files are created or updated and need validation before implementation begins."
argument-hint: "Specify which feature's tasks to review (e.g., F001), a specific task file, or leave blank to review all..."
---

# Review Task Specifications

Review task files under `specs/tasks/FNNN/` for quality, feasibility, completeness, and faithful adherence to the parent FRD. This is the quality gate between planning and implementation.

## When to Use

- After the dev agent creates or updates task files via `/plan-skill`
- Before the dev agent begins implementing tasks
- When an FRD changes and existing tasks need re-validation

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `AGENTS.md` — Engineering standards (testing thresholds, naming, conventions)
- `specs/prd.md` — Product requirements for overall vision
- `specs/features/*.md` — The FRDs that the tasks trace to
- `specs/adr/*.md` — Architecture decisions for technology constraints
- `specs/threat-model.md` — Threats and required mitigations whose coverage the tasks must deliver
- `specs/tasks/**/*.md` — The task files under review (organized in per-feature `FNNN/` subfolders)

**Context management for large projects:** When reviewing tasks for a specific feature, read selectively:
- Read the **target feature's FRD** in full.
- Read only the **ADRs referenced by that FRD**.
- Read **every threat** whose `Owning artifact` is the target FRD or any ADR it references.
- Skim other FRDs only for cross-feature dependency verification.
- Read `AGENTS.md` in full — it defines testing and acceptance criteria standards.

### 2. Verify FRD Coverage

For each functional requirement (FR-N) in the parent FRD:

1. **Trace to tasks** — Identify which task(s) address this requirement. Every FR must be covered by at least one task.
2. **Flag gaps** — Requirements with no corresponding task are blockers.
3. **Flag over-reach** — Tasks that introduce scope beyond what the FRD requires. Tasks must implement the FRD, not extend it.

### 2a. Verify Threat Coverage

Walk every threat in `specs/threat-model.md` whose `Owning artifact` is the target FRD or an ADR referenced by that FRD:

1. **Trace to tasks** — Each in-scope threat must appear in the `Threats Mitigated` section of at least one task. Threats whose mitigation is delegated to scaffolding must cite an `F000/NNN` task.
2. **Verify acceptance and test coverage** — For every cited `THR-NNN`, the implementing task must include at least one acceptance criterion and one mitigation test that verify the control. A bare citation without a corresponding criterion or test is a `blocker`.
3. **Flag inconsistencies** — Tasks that cite a `THR-NNN` not present in `specs/threat-model.md` are `blocker` findings. Tasks that declare `Threats Mitigated: None` for work that obviously crosses a trust boundary (new endpoint, new data store, new external integration) are `blocker` findings.
4. **Flag stale back-references** — If a threat's `Implementing tasks` list disagrees with the actual task citations, hand off to **arch** to refresh the threat model.

### 2b. Verify FRD §8 Release Criteria

The parent FRD's §8 Release Criteria block must be filled before tasks ship:

- **Feature flag** — Either a flag name or `N/A` with a one-line rationale. Blank is a `blocker`.
- **Rollout strategy** — Either a strategy or `N/A` with rationale. Blank is a `blocker`.
- **Rollback plan** — Required for every user-facing feature; `N/A` is only acceptable for internal-only or pure-refactor features and must include a rationale. Blank is a `blocker`.

If the FRD §8 fields are missing or blank, the verdict is `changes-requested` and the next action is to hand off to **po** to populate them — not to the dev agent. Plan-review does not edit FRDs.

### 3. Verify Task Quality

For each task file, check:

- **Description** — Is it clear and unambiguous? Could a developer unfamiliar with the project understand what to build?
- **Dependencies** — Are all prerequisite tasks listed? Are there missing dependencies (e.g., a data service task that doesn't depend on the data access foundation)? Are there unnecessary dependencies that would serialize work?
- **Technical requirements** — Are they specific enough to implement without guessing? Do they align with ADR decisions? Do they avoid prescribing implementation code?
- **Acceptance criteria** — Is every criterion objectively testable? Are there vague criteria ("works correctly", "handles errors properly")? Does each criterion map to a verifiable behavior?
- **Testing requirements** — Do they align with `AGENTS.md` coverage thresholds? Are both unit and integration test expectations stated where appropriate?

### 4. Verify Task Structure

Check the task set as a whole:

- **Dependency graph** — Are there circular dependencies? Is the implementation order logical?
- **Granularity** — Tasks that touch more than 3 architecture layers or would require ~300+ lines should be split. Tasks under ~20 lines should be merged.
- **Architecture coverage** — For user-facing features, are all relevant layers covered (UI, API, service, data, auth)?
- **Layout & naming** — Tasks live at `specs/tasks/FNNN/NNN-<task-name>.md` with kebab-case descriptions. Reference IDs use the `FNNN/NNN` form.
- **No implementation code** — Task files describe WHAT, not HOW. Flag any code snippets, class names, or method signatures.

### 5. Cross-Check Against Standards

Verify tasks account for standards defined in `AGENTS.md`:

- **Security** — Tasks involving API endpoints include authorization and input validation in their acceptance criteria.
- **Error handling** — Tasks specify expected error behavior (Problem Details for APIs, user-facing messages for UI).
- **Accessibility** — UI tasks include accessibility criteria consistent with `AGENTS.md` targets.
- **Internationalization** — UI tasks account for i18n requirements if defined in `AGENTS.md`.

### 6. Report Findings

Provide a structured review:

- **Feature**: Which feature's tasks were reviewed (FRD file path)
- **Verdict**: `approved` or `changes-requested`
- **FRD coverage**: Table mapping each FR-N to its task(s), noting gaps or over-reach
- **Threat coverage**: Table mapping each in-scope `THR-NNN` to its task(s) and mitigation tests, noting unassigned threats and stale back-references
- **FRD §8 release criteria**: Status of feature flag, rollout strategy, and rollback plan (filled / `N/A` with rationale / blank)
- **Task findings**: For each task with issues:
  - Task file path
  - Issue description (what's wrong or missing)
  - Severity: `blocker` (must fix before implementation), `warning` (should fix), `nit` (optional improvement)
  - Suggested fix (describing WHAT to change, not providing task text)
- **Structural findings**: Dependency graph issues, granularity concerns, missing layers
- **Next action**: If approved, the dev agent can proceed with implementation. If changes requested, list the specific changes needed before re-review.

### 7. Re-Review Loop

If the verdict is `changes-requested`, hand off to the **dev** agent with the specific findings. When the dev agent reports fixes are complete, re-run this entire procedure from Step 2 on the updated tasks. Each re-review is a full review — do not skip steps based on previous findings.

**Iteration limit.** A task plan may go through at most **three** review cycles (initial review + two re-reviews). If a fourth cycle would be required, **stop the loop** and escalate to the user with:

- The feature ID and FRD path
- A delta summary across cycles: which findings were fixed, which recurred, which are new
- The persistent root cause if identifiable (e.g., FRD is ambiguous, scaffolding is missing, ADR coverage is incomplete)
- A recommended next action: refine the FRD via **po**, extend the scaffolding FRD via **arch**, or split the feature into smaller FRDs

Do not silently approve to break the loop. Escalation is a normal outcome.

## Rules

- **Review only, never write tasks** — Do not create or edit task files. Report findings for the dev agent to address.
- **Evidence-based** — Every finding must reference a specific task file and describe what was observed vs. what was expected.
- **FRD is the source of truth** — Tasks must faithfully implement the FRD. If the FRD seems wrong, escalate to the PO via the "Continue with Product Owner" handoff, not a reason to approve a divergent task.
- **Blockers must be fixed** — If any finding is severity `blocker`, the verdict must be `changes-requested`.
- **No scope creep** — Do not request tasks or criteria beyond what the FRD requires.
- **No implementation opinions** — Do not suggest specific implementations, class designs, or code patterns. Tasks describe WHAT, not HOW.

## Quality Checklist

- [ ] Every FRD requirement traced to at least one task
- [ ] Every in-scope threat (`THR-NNN`) cited by at least one task with matching acceptance criteria and mitigation tests
- [ ] No task cites a non-existent `THR-NNN`; no boundary-crossing task silently declares `Threats Mitigated: None`
- [ ] Parent FRD §8 release criteria filled (flag, rollout, rollback) or `N/A` with rationale
- [ ] No tasks exceed the FRD scope
- [ ] Every acceptance criterion is objectively testable
- [ ] Dependency graph has no cycles and follows logical order
- [ ] Task granularity is appropriate (not too large, not too small)
- [ ] No implementation code in task files
- [ ] Security, error handling, and accessibility criteria present where required
- [ ] No task files were modified by this review
- [ ] Iteration limit respected — escalated to user if a fourth review cycle would be required
- [ ] Findings report includes verdict, evidence, and clear next action
