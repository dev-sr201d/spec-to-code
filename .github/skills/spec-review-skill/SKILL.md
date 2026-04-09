---
name: spec-review-skill
description: "Review PRDs and FRDs for technical feasibility, completeness, and missing requirements. Use when specs are created or updated and need technical validation before architecture or planning begins."
argument-hint: "Specify which specs to review (PRD, a specific FRD, or all), or leave blank to review everything..."
---

# Review Specifications

Review PRDs and FRDs for technical feasibility, completeness, and missing requirements needed for successful implementation.

## When to Use

- After the PO creates or updates the PRD
- After the PO creates or updates FRDs
- Before the arch agent creates ADRs (specs must be reviewed first)
- Before the dev agent plans tasks (specs must be complete)
- When requirements change and specs need re-validation

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `specs/prd.md` — Product requirements
- `specs/features/*.md` — Feature requirements
- `AGENTS.md` — Development standards (if exists)
- `specs/adr/*.md` — Architecture decisions (if any exist)

### 2. Review for Technical Feasibility

For each requirement in the PRD and each functional requirement in the FRDs:

1. **Assess implementability** — Can this requirement be realistically built? Flag vague, contradictory, or infeasible requirements.
2. **Check for conflicts** — Does this requirement conflict with other requirements, existing ADRs, or the principles in `copilot-instructions.md`?
3. **Verify testability** — Can acceptance criteria be objectively verified? Flag criteria that are subjective ("works well") or unmeasurable.

### 3. Identify Missing Requirements

Look for gaps that would block implementation:

- **Missing acceptance criteria** — User stories or functional requirements without Given/When/Then criteria
- **Undefined error states** — What happens when things fail? Missing error handling requirements.
- **Missing data flows** — Input/output not specified, data sources not identified
- **Missing non-functional requirements** — Performance, scalability, accessibility expectations not stated
- **Missing dependencies** — Features that depend on other features or external systems without noting it
- **Missing edge cases** — Boundary conditions, empty states, concurrent access scenarios

### 4. Validate Completeness

- **PRD coverage** — Every REQ-N in the PRD should be addressed by at least one FRD
- **FRD traceability** — Every FRD should reference the PRD requirements it addresses. The scaffolding FRD (`000-project-scaffolding.md`) is an exception — it traces to ADRs instead of PRD requirements, using an ADR Traceability table.
- **Cross-feature consistency** — FRDs should not contradict each other
- **Architecture layer coverage** — User-facing features should have requirements that span all relevant layers (UI, API, data, auth as applicable)

### 5. Apply Simplicity First

**Only flag missing requirements that are necessary for what the user explicitly asked for.**
- Do not request databases, caching, message queues, complex observability, or advanced resilience patterns unless the PRD explicitly calls for them
- Default to the simplest workable solution — in-memory, file-based, or single-process
- Note where complexity can be added later if explicitly requested, but do not make it a requirement now

**Exception — Zero Trust requirements are always mandatory.** Authentication, authorization, input validation, and encryption requirements from `copilot-instructions.md` must be flagged as missing if absent, regardless of whether the user explicitly asked for them. Simplicity-first applies to implementation complexity (in-memory vs. distributed cache, simple auth vs. OAuth federation), not to whether security boundaries exist at all.

### 6. Report Findings

Provide a structured review:

- **Findings** — List each recommended addition, change, or concern with:
  - File path (which PRD/FRD)
  - Requirement ID (REQ-N or FR-N) if applicable
  - Description of the gap or issue
  - Suggested requirement text (describing WHAT, not HOW)
- **Completeness assessment** — Overall readiness for architecture and planning. Rate as: ready / ready with minor gaps / not ready (blocking gaps)
- **Next action** — State whether the PO should update specs, the arch agent can proceed with ADRs, or the dev agent can proceed with planning

## Rules

- **Describe WHAT, never HOW** — "The system must support user authentication" not "Use MSAL library for OAuth flows." Leave all technology choices to the **arch** agent.
- **NEVER** include code, class names, database schemas, library choices, or architecture details
- **NEVER** edit PRD or FRD files directly — produce recommendations for the PO to apply
- **Question assumptions** — If the PRD doesn't mention persistence, real-time, or auth, don't add them
- **Progressive enhancement** — Note where complexity can be added later if explicitly requested
- **No gold-plating** — Do not request requirements beyond what the user asked for

## Quality Checklist

- [ ] Every requirement reviewed for feasibility and testability
- [ ] Missing requirements identified with suggested text
- [ ] PRD-to-FRD traceability verified
- [ ] Cross-feature consistency checked
- [ ] Simplicity-first principle applied — no unnecessary complexity added
- [ ] All findings describe WHAT, never HOW
- [ ] No spec files were edited — findings are recommendations only
- [ ] Completeness assessment and next action provided
