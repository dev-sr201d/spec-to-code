---
applyTo: "specs/tasks/**"
description: "Conventions for task specification files. Use when creating or editing implementation task files."
---

# Task File Conventions

- **One task per file** — Each file in `specs/tasks/` describes a single, independently implementable unit of work.
- **Naming**: `FNNN-TNNN-<task-name>.md` — `FNNN` matches the FRD number, `TNNN` is sequential within the feature, kebab-case description. Scaffolding tasks use `F000` (e.g., `F000-T001-project-init.md`).
- **Required sections** — Every task file must include: Description, Dependencies, Technical Requirements, Acceptance Criteria, and Testing Requirements.
- **No implementation code** — Describe what to build and what to test, never how. No code snippets, class names, or method signatures.
- **Explicit dependencies** — List task IDs (`FNNN-TNNN`) that must be completed first, with a brief reason why.
- **Testable acceptance criteria** — Each criterion must be a verifiable statement, not vague ("works correctly"). Use the `- [ ]` checkbox format.
