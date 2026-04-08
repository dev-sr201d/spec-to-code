---
applyTo: "AGENTS.md"
description: "Conventions for the AGENTS.md engineering standards file. Use when editing or reviewing AGENTS.md."
---

# AGENTS.md Conventions

- **ADR-derived** — Every technology-specific section must trace back to an accepted ADR in `specs/adr/`. Do not add standards for technologies that lack an ADR.
- **Architect-owned** — Only the arch agent (via `/standards-skill`) authors or updates this file. Manual edits that contradict ADR decisions must be flagged and reconciled.
- **Required fields** — The `General > Testing` section must define a code coverage threshold. Downstream agents (dev, lead) depend on this value and will block if it is missing.
- **Practical examples** — Every guideline should include a brief code example demonstrating the expected pattern.
- **Living document** — Updated in place when ADRs are created or revised. Superseded standards are removed or replaced, never left alongside their replacements.
- **No aspirational content** — Standards describe what the project WILL follow, not wish-list items. Every entry must be actionable by the dev agent.
