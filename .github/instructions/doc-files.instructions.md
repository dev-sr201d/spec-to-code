---
applyTo: "docs/**/*.md"
description: "Conventions for documentation files. Use when editing or reviewing files under docs/."
---

# Documentation File Conventions

- **Living documents** — Docs are updated in place as the project evolves. Outdated sections are revised, not deleted.
- **Source-derived** — Every claim must trace back to specs (`specs/prd.md`, `specs/features/*.md`), ADRs (`specs/adr/*.md`), `AGENTS.md`, or source code. Never invent details.
- **No secrets** — Never include credentials, API keys, internal URLs, or other sensitive data. Use placeholders (e.g., `<API_KEY>`) where examples require them.
- **Audience-appropriate** — Architecture docs target developers. Operations docs target DevOps/SRE. Usage docs target end-users and integrators.
- **Consistent headings** — Follow the established template structure for each document type. Do not add ad-hoc top-level sections.
- **Cross-references** — Link to related docs within the `docs/` tree. Reference ADR numbers (`NNN`) and requirement IDs (`REQ-N`, `FR-N`) where relevant.
- **No duplication** — Reference other docs instead of copying content. Add context and explanation beyond what the referenced document provides.
