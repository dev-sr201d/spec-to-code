---
name: prd-skill
description: "Create or update the Product Requirements Document from a project idea or stakeholder input. Use when starting a new project, gathering requirements, or refining product scope."
argument-hint: "Describe the project idea or feature..."
---

# Create or Update PRD

Create or update the Product Requirements Document in `specs/prd.md` based on the user's input.

## When to Use

- Starting a new project from an idea or stakeholder input
- Refining or expanding existing product requirements
- Incorporating feedback from reviews or user research

## Procedure

### 1. Gather Input

Ask clarifying questions if the input is vague or incomplete before writing. Seek to understand:
- The problem being solved and who it is for
- Business goals and success metrics
- Scope boundaries (what's in and out)
- Known constraints

### 2. Create or Update the PRD

Write the PRD in `specs/prd.md` using the [PRD template](./assets/prd-template.md).

- If the file already exists, update it in place — preserve existing content and add or revise sections.
- Use prefixed requirement IDs (`REQ-N`) so FRDs and tasks can trace back to them.

### 3. Verify Completeness

Ensure every section of the template is addressed. Flag any sections where information is missing and ask the user for clarification.

This is a living document — update and revise it any time the user provides new information or feedback.
