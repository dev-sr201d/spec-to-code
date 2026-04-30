---
applyTo: "specs/tasks/**"
description: "Conventions for task specification files. Use when creating or editing implementation task files."
---

# Task File Conventions

- **One task per file** — Each file under `specs/tasks/` describes a single, independently implementable unit of work.
- **Layout**: Tasks live in per-feature subfolders — `specs/tasks/FNNN/NNN-<task-name>.md`. `FNNN` matches the FRD number (the folder name), `NNN` is sequential within the feature, kebab-case description. Scaffolding tasks use the `F000` folder (e.g., `specs/tasks/F000/001-project-init.md`).
- **Task ID** — A task is referenced by its path-style identifier `FNNN/NNN` (e.g., `F001/003`). Use this form in dependency lists, handoffs, and prompts.
- **Required sections** — Every task file must include: Description, Dependencies, Technical Requirements, Acceptance Criteria, and Testing Requirements.
- **No implementation code** — Describe what to build and what to test, never how. No code snippets, class names, or method signatures.
- **Explicit dependencies** — List task IDs (`FNNN/NNN`) that must be completed first, with a brief reason why.
- **Testable acceptance criteria** — Each criterion must be a verifiable statement, not vague ("works correctly"). Use the `- [ ]` checkbox format.
