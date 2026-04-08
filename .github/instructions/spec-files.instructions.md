---
applyTo: "specs/**/*.md"
description: "Conventions for specification files (PRD, FRDs, ADRs). Use when editing or reviewing files under specs/."
---

# Specification File Conventions

- **Living documents** — Specs are updated in place, never deleted. Superseded ADRs are marked with `Status: Superseded`, not removed.
- **Requirement IDs** — Use prefixed IDs (`REQ-N` in PRDs, `FR-N` in FRDs) so tasks and tests can trace back to requirements.
- **WHAT not HOW** — PRDs and FRDs describe business and user requirements. Never include code, technology names, class names, or implementation details. Technology choices belong in ADRs.
- **Consistent headings** — Follow the established heading structure for each document type. Do not add ad-hoc sections.
- **Cross-references** — When a FRD addresses a PRD requirement, reference the REQ ID. When an ADR is motivated by an FRD, link to the feature file.
