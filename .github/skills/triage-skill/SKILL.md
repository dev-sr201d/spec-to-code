---
name: triage-skill
description: "Triage the analyst's issues manifest into actionable dispositions. Use after analyst onboarding to classify issues before any spec refinement or implementation planning begins."
argument-hint: "Point to specs/issues.md, or leave blank to auto-detect..."
---

# Triage Issues Manifest

Review and classify every issue in `specs/issues.md` so that downstream agents (po, dev) only act on triaged, intentional decisions.

## When to Use

- After the analyst agent completes codebase onboarding and `specs/issues.md` exists
- When new issues are appended to `specs/issues.md` after incremental analysis
- Before the po agent refines specs or the dev agent plans tasks — triage is a **blocking prerequisite**

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `specs/issues.md` — The issues manifest to triage
- `specs/prd.md` — Product requirements (to understand existing scope)
- `specs/features/*.md` — Feature requirements (to check for overlap with existing requirements)
- `specs/adr/*.md` — Architecture decisions (to understand technology context for tech-related issues)
- `AGENTS.md` — Development standards (if exists)

### 2. Review Each Issue

For every issue with `Triage` set to `pending`:

1. **Understand the issue** — Read the description, code location, severity, and rationale.
2. **Check for duplicates** — Does an existing FRD requirement or another issue already cover this?
3. **Assess impact** — Is this a real user-facing risk, a security concern, a quality gap, or a minor style deviation?
4. **Classify** — Assign one of the triage dispositions below.

### 3. Assign Triage Dispositions

Update the `Triage` column for each issue to one of:

| Disposition | Meaning | When to Use |
|------------|---------|-------------|
| `promote` | Becomes a requirement in an existing FRD | The issue maps clearly to a feature that already has an FRD. Note which FRD and what the requirement should say. |
| `new-frd` | Warrants a standalone remediation FRD | The issue (or a group of related issues) doesn't fit any existing FRD. Propose an FRD title. |
| `needs-investigation` | Evidence insufficient to decide | The analyst's findings are ambiguous or incomplete. Note what additional information is needed and who should provide it (user, analyst re-check, arch review). |
| `accepted-debt` | Acknowledged, not actioned now | Low risk, out of scope for current phase, or cost exceeds benefit. Provide rationale. |
| `duplicate` | Already covered | An existing requirement or another triaged issue addresses this. Note the duplicate reference. |

### 4. Apply Decisions to the Manifest

For each triaged issue, update **two fields** in `specs/issues.md`:
- **`Triage`** — Set to the chosen disposition.
- **`Resolution`** — A brief note explaining the decision. Examples:
  - `"Promote to FRD 003 as new acceptance criterion for input validation"`
  - `"New FRD: Security Remediation (with ISS-004, ISS-012, ISS-017)"`
  - `"Accepted debt: cosmetic inconsistency, no user impact"`
  - `"Duplicate of ISS-003"`

### 5. Report Triage Summary

After all issues are classified, provide a summary:

- **Counts per disposition** — How many promote, new-frd, needs-investigation, accepted-debt, duplicate
- **Critical issues** — List every critical-severity issue with its resolution
- **Proposed new FRDs** — For each `new-frd` group: proposed title, list of `ISS-NNN` IDs, and a one-line scope description
- **Promoted issues** — For each `promote`: target FRD and proposed requirement text
- **Needs investigation** — For each `needs-investigation`: what information is missing and who should provide it
- **Next action** — Hand off to **po** to apply `promote` and `new-frd` decisions to actual spec files. If `needs-investigation` issues exist, note they must be resolved before the affected areas can proceed.

## Rules

- **Critical issues cannot be `accepted-debt`** — Every `critical` issue must be either `promote` or `new-frd`. If you believe a critical issue should be deferred, escalate to the user rather than accepting it as debt.
- **Simplicity-first** — Do not promote minor issues into requirements unless they represent real user-facing risk. Note where complexity can be added later if needed.
- **Group related issues** — When multiple issues share a theme (e.g., several security findings, several missing-test findings), group them under a single `new-frd` rather than creating one FRD per issue.
- **Describe WHAT, never HOW** — Triage notes should describe what needs to happen ("input validation required for all API endpoints"), not how to implement it ("add Zod schemas to Express middleware").
- **Do not edit spec files** — Triage only updates `specs/issues.md`. Do not edit any other file — not PRD, FRDs, ADRs, AGENTS.md, or source code. The po agent applies the decisions to PRD/FRD files.
- **Preserve analyst evidence** — Do not alter the issue's description, code location, or rationale. Only update `Triage` and `Resolution`.

## Quality Checklist

- [ ] Every `pending` issue in `specs/issues.md` has been classified
- [ ] No `critical` issue is marked `accepted-debt`
- [ ] Every `needs-investigation` entry specifies what information is missing and who should provide it
- [ ] Every `promote` entry specifies a target FRD and proposed requirement
- [ ] Every `new-frd` entry has a proposed title and grouped `ISS-NNN` IDs
- [ ] Every `accepted-debt` entry has a rationale
- [ ] Every `duplicate` entry references what it duplicates
- [ ] Only `specs/issues.md` was edited — no other files were modified
- [ ] Triage summary provided with counts, critical resolutions, and next action
