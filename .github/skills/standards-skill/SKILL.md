---
name: standards-skill
description: "Generate or update AGENTS.md with engineering standards based on architecture decisions. Use when ADRs define new technologies, coding standards need documentation, or development guidelines need updating."
argument-hint: "Optionally specify which areas to focus on (e.g., backend, frontend, testing)..."
---

# Generate Engineering Standards

Generate or update the `AGENTS.md` file at the project root with engineering standards and coding guardrails.

## When to Use

- ADRs have been created or updated with technology choices
- A new technology is being added to the stack
- Development guidelines need to be documented or revised

## Procedure

### 1. Read Project Context

Review the following to understand chosen technologies and architecture:
- `specs/prd.md` — Product requirements
- `specs/features/*.md` — Feature requirements
- `specs/adr/*.md` — Architecture decisions (primary input)

### 2. Research Best Practices

For each technology identified in the ADRs, use available documentation tools (context7, deepwiki, mdn, microsoft.docs.mcp) to research:
- Current coding standards and conventions
- Recommended patterns and anti-patterns
- Testing best practices
- Security guidelines

### 3. Author AGENTS.md

Write a comprehensive `AGENTS.md` at the project root using the [template structure](./assets/agents-template.md).

Organize into clear sections:
- **General**: Cross-cutting principles (naming, error handling, testing, documentation)
- **Backend**: Standards specific to the backend technology stack
- **Frontend**: Standards specific to the frontend technology stack
- Add or omit sections based on the actual tech stack — do not include sections for technologies not in use.

### 4. Include Practical Examples

For each guideline, show a brief code example demonstrating the expected pattern.

### 5. Review Existing AGENTS.md

If `AGENTS.md` already exists, update it rather than replacing it wholesale — preserve any manually added guidelines.

Standards should be actionable guardrails for developers, not aspirational prose.
