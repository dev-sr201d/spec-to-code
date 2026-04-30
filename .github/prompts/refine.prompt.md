---
description: "Refine an existing Feature Requirements Document based on feedback or changed requirements. Use when an FRD needs adjustment after review, testing, or stakeholder input."
agent: po
---

Refine the FRD for feature **${input:feature}** in `specs/features/`.

Preserve existing requirement IDs and traceability. If new requirements are added, use the next available FR-N ID. If requirements are removed, mark them as deprecated rather than deleting.

If the refinement changes scope, adds capabilities, or contradicts existing product requirements, cascade the changes up to `specs/prd.md` — update or add REQ-N entries as needed and confirm what was changed.

After completing the refinement, run the following cascade checks:

1. **ADR re-validation** — Determine whether the refined FRD invalidates, contradicts, or requires new architecture decisions. Triggers include: new external interfaces, new data classes, new identity/auth requirements, new performance/availability targets, new compliance scope, new technologies. If any apply, hand off to the **arch** agent to re-evaluate the affected ADRs via `/reconsider` and to refresh `specs/threat-model.md` via `/threat-model-skill`.
2. **Threat-model refresh** — If trust boundaries, data flows, stored data classes, or compliance scope changed, the threat model must be refreshed even if no ADR changes. Hand off to **arch** with `/threat-model-skill` scoped to this feature.
3. **Task reconciliation** — Check whether tasks exist under `specs/tasks/FNNN/` for this feature. If so, remind the user to run `/plan` so the **dev** agent can reconcile them with the updated requirements.

Report which cascades were triggered, which were skipped (with rationale), and what the user should run next.
