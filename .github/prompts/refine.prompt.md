---
description: "Refine an existing Feature Requirements Document based on feedback or changed requirements. Use when an FRD needs adjustment after review, testing, or stakeholder input."
agent: po
---

Refine the FRD for feature **${input:feature}** in `specs/features/`.

Preserve existing requirement IDs and traceability. If new requirements are added, use the next available FR-N ID. If requirements are removed, mark them as deprecated rather than deleting.

If the refinement changes scope, adds capabilities, or contradicts existing product requirements, cascade the changes up to `specs/prd.md` — update or add REQ-N entries as needed and confirm what was changed.

After completing the refinement, check if tasks exist under `specs/tasks/FNNN/` for this feature. If so, remind the user to run `/plan` to reconcile them with the updated requirements.
