---
name: adr-skill
description: "Create or update Architecture Decision Records (ADRs). Use when making architecture decisions, evaluating technology options, or documenting technical choices. Follows MADR format with research-backed option analysis."
argument-hint: "Describe the decision area or let the arch agent identify decisions from PRD/FRDs..."
---

# Create Architecture Decision Records

Create ADRs that document key technical and architectural decisions for the project.

## When to Use

- A new technology choice or architectural pattern needs to be decided
- An existing ADR needs updating due to changed requirements
- The PRD or FRDs introduce new technical concerns that need decisions

## Procedure

### 1. Read Context

Review the following files to understand existing decisions and requirements:
- `specs/prd.md` — Product requirements
- `specs/features/*.md` — Feature requirements
- `AGENTS.md` — Current engineering standards (if exists)
- `specs/adr/*.md` — Previous architecture decisions

### 2. Identify Decisions

Determine which architectural decisions need documentation based on the requirements. Check existing ADRs to avoid duplicates.

### 3. Research Options

For each decision, use available documentation tools (context7, deepwiki, mdn, microsoft.docs.mcp) to evaluate at least 3 alternatives with pros and cons.

### 4. Create ADRs

Create one ADR per decision in `specs/adr/` using the [MADR template](./assets/madr-template.md).

**Naming**: `NNN-short-title.md` — zero-padded, sequential, never reuse numbers.

### 5. Review Alignment

Ensure new ADRs align with:
- Requirements from the PRD and FRDs
- Existing accepted ADRs (no contradictions)
- Project principles in `copilot-instructions.md`

## Quality Checklist

- [ ] At least 3 considered options per decision
- [ ] Clear rationale linking back to decision drivers
- [ ] Honest trade-offs — include negatives, not just positives
- [ ] No implementation code — leave that to the dev agent
- [ ] No duplicate ADRs — check existing ones first
- [ ] Superseded ADRs marked as such, never deleted
