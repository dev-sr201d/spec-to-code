---
name: implement-skill
description: "Implement a technical task from the plan, writing code and tests following project standards. Use when coding features, writing tests, scaffolding projects, or fixing defects from task specifications."
argument-hint: "Specify which task to implement, or leave blank to pick the next unfinished task..."
---

# Implement Technical Task

Implement a technical task from `specs/tasks/`, writing code and tests that follow project standards.

## When to Use

- A task file exists in `specs/tasks/` and is ready for implementation
- Code needs to be written following established ADRs and AGENTS.md

## Procedure

### 1. Gather Context

**ALWAYS start by reading these files — do not skip this step:**
- `AGENTS.md` — Development standards and guidelines to follow
- `specs/adr/*.md` — Architecture decisions for technology choices
- `specs/prd.md` — Product requirements for overall context
- `specs/features/*.md` — Relevant feature requirements
- `specs/tasks/*.md` — Task specifications for what to implement

### 2. Check Task Status

Before proceeding, verify the task is actionable:
- If the task is marked `Status: Deprecated`, **stop** — do not implement it. Inform the user and suggest picking the next task.
- If existing code already implements an older version of this task, identify what changed in the task spec and plan an incremental update rather than reimplementing from scratch.

### 3. Plan the Implementation

Before writing code, outline:
- Components or modules to be created/modified (note any existing code that needs updating)
- Data models and contracts
- API endpoints or interfaces
- Testing strategy

### 4. Research

Use available documentation tools (context7, deepwiki, mdn, microsoft.docs.mcp) to research:
- Best practices and patterns for the relevant technologies
- Code samples and examples
- Latest stable versions of libraries and frameworks

### 5. Implement the Code

Write the implementation following:
- Coding standards and patterns defined in `AGENTS.md`
- Architectural decisions from ADRs
- Type-safety requirements
- Modular, self-contained design principles

### 6. Write and Run Tests

- Write unit tests for all public methods and functions
- Cover edge cases and error conditions
- Follow the coverage threshold defined in the `General > Testing` section of `AGENTS.md`. If `AGENTS.md` does not specify a coverage threshold, **stop and hand off to the arch agent** to define one via `/standards-skill` before proceeding.
- Run tests via `execute/runInTerminal` (e.g., `dotnet test`, `npm test`) and iterate until all pass
- For integration, contract, or E2E tests beyond unit tests, use `/test-skill`

### 7. Verify Integration

For frontend tasks, run through the [frontend integration checklist](./references/frontend-integration.md).

### 8. Update Documentation

If the project has documentation:
- Update existing docs as defined in `AGENTS.md`
- Update API documentation if applicable

**CRITICAL**: If you discover that significant architectural decisions are needed during implementation:
- **STOP** — Do not make major architectural decisions during implementation
- **Hand back to the arch agent** to create proper ADRs first
- Provide context: what decision is needed and what the blocker is

## Quality

Before marking a task complete, run through the [quality checklist](./references/quality-checklist.md).

Once the checklist passes, hand off to the **lead** agent for code review via `/code-review-skill`. The task is not complete until the lead agent approves it.
