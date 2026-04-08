---
name: doc-skill
description: "Create or update project documentation in docs/. Use when generating architecture docs, operations guides, or usage manuals from specs, ADRs, and source code."
argument-hint: "Specify which documentation area to create or update (architecture, operations, usage), or 'all' for full generation..."
---

# Create and Update Project Documentation

Generate and maintain comprehensive project documentation derived from specs, ADRs, AGENTS.md, and source code.

## When to Use

- A new project needs initial documentation scaffolding
- Specs or ADRs have changed and documentation needs updating
- A specific documentation area needs creation or revision
- Documentation review reveals gaps or inaccuracies

## Procedure

### 1. Read Context

Review the following sources to gather accurate information:
- `specs/prd.md` — Product requirements and business goals
- `specs/features/*.md` — Feature requirements and acceptance criteria
- `specs/adr/*.md` — Architecture decisions, technology choices, and rationale
- `AGENTS.md` — Engineering standards and development conventions
- Source code — Actual implementation details (scan `src/` or equivalent)

### 2. Determine Scope

Identify which documentation areas need work:

| Area | Path | Sources | Template |
|------|------|---------|----------|
| Documentation Index | `docs/README.md` | All docs below | `assets/docs-index-template.md` |
| Architecture Overview | `docs/architecture/overview.md` | PRD, ADRs, AGENTS.md | `assets/architecture-overview-template.md` |
| Components | `docs/architecture/components.md` | FRDs, ADRs, source code | `assets/components-template.md` |
| Technology Stack | `docs/architecture/technology.md` | ADRs, AGENTS.md | `assets/technology-template.md` |
| Data Model | `docs/architecture/data-model.md` | FRDs, ADRs, source code | `assets/data-model-template.md` |
| Security | `docs/architecture/security.md` | ADRs, FRDs (auth features) | `assets/security-template.md` |
| Dependencies | `docs/architecture/dependencies.md` | package files, AGENTS.md | `assets/dependencies-template.md` |
| Deployment | `docs/architecture/deployment.md` | ADRs, CI/CD configs | `assets/deployment-template.md` |
| Integration | `docs/architecture/integration.md` | FRDs, ADRs, source code | `assets/integration-template.md` |
| Operations Overview | `docs/operations/overview.md` | ADRs, AGENTS.md | `assets/operations-overview-template.md` |
| Configuration Reference | `docs/operations/configuration.md` | source code, ADRs, AGENTS.md | `assets/configuration-template.md` |
| Logging | `docs/operations/logging.md` | ADRs, AGENTS.md, source code | `assets/logging-template.md` |
| Instrumentation | `docs/operations/instrumentation.md` | ADRs, AGENTS.md | `assets/instrumentation-template.md` |
| Runbooks | `docs/operations/runbooks.md` | ADRs, source code, ops experience | `assets/runbooks-template.md` |
| Disaster Recovery | `docs/operations/disaster-recovery.md` | ADRs, infra config | `assets/disaster-recovery-template.md` |
| Troubleshooting | `docs/operations/troubleshooting.md` | Known issues, source code | `assets/troubleshooting-template.md` |
| User Manual | `docs/usage/user-manual.md` | PRD, FRDs | `assets/user-manual-template.md` |
| API Reference | `docs/usage/api-reference.md` | source code, FRDs, ADRs | `assets/api-reference-template.md` |
| Integrator Manual | `docs/usage/integrator-manual.md` | FRDs, ADRs, API specs | `assets/integrator-manual-template.md` |

### 3. Delegate Documentation Work

Documentation must be delegated to subagents for context isolation and quality. Do not try to write all documents in a single pass.

**Documentation index** — generate last, after all other docs exist:
- `docs/README.md` — Table of contents linking to all generated docs. Update links to include only docs that were actually created.

**Architecture docs — delegate individually** (one subagent call per document):
- `docs/architecture/overview.md`
- `docs/architecture/components.md`
- `docs/architecture/technology.md`
- `docs/architecture/data-model.md`
- `docs/architecture/security.md`
- `docs/architecture/dependencies.md`
- `docs/architecture/deployment.md`
- `docs/architecture/integration.md`

**Operations docs — delegate as one batch** (one subagent call):
- `docs/operations/overview.md`, `configuration.md`, `logging.md`, `instrumentation.md`, `runbooks.md`, `disaster-recovery.md`, `troubleshooting.md`

**Usage docs — delegate as one batch** (one subagent call):
- `docs/usage/user-manual.md`, `api-reference.md`, `integrator-manual.md`

For each subagent call, provide:
1. The specific document(s) to write
2. The relevant sources from the scope table above
3. The template(s) to use from `assets/`
4. A reminder: describe what IS, never recommend or suggest

### 4. Write Each Document

Within each delegated unit:

1. **Check if the file exists** — If yes, update in place. If no, create from the template listed in the scope table (paths are relative to `.github/skills/doc-skill/`).
2. **Gather source material** — Read the relevant sources listed in the scope table.
3. **Identify what changed (updates only)** — Use `search/changes` to detect which specs, ADRs, or source files changed since the last documentation update. Focus edits on sections affected by those changes rather than rewriting the entire document.
4. **Write content** — Follow the template structure and the doc-files conventions.
5. **Cross-reference** — Link to related docs within the `docs/` tree. Reference ADR numbers and requirement IDs where relevant.

### 5. Validate

After writing:
- **Verify claims** — Spot-check key statements against source material using `search/textSearch` to confirm specs/ADRs support each claim. No invented details.
- **Check internal links** — Use `search/fileSearch` to confirm every linked file path in the docs actually exists in the workspace.
- **No secrets** — Search doc content for patterns like API keys, tokens, passwords, or internal URLs. Use placeholders where examples need them.
- **Consistent structure** — Confirm heading hierarchy matches the template for each document type.

### 6. Handle Edge Cases

- **Missing source specs** — If `specs/prd.md` or `specs/features/*.md` do not exist, skip the corresponding documentation areas and note which docs were skipped and why. Do not generate docs from assumptions.
- **Source code drift** — If source code contradicts specs or ADRs, document what the code actually does and add a note referencing the conflicting spec/ADR. Flag the discrepancy for the lead agent to resolve.
- **Contradictory ADRs** — If two ADRs conflict (e.g., one supersedes another without marking it), document per the most recent ADR and reference both. Flag for the arch agent to reconcile.

## Quality Checklist

- [ ] Every statement is traceable to a spec, ADR, or source code
- [ ] No recommendations, suggestions, opinions, or improvement ideas anywhere in the docs
- [ ] Documentation describes only what the system does, never what it should do
- [ ] No duplicate content — reference other docs instead of copying
- [ ] Audience-appropriate language for each section
- [ ] Consistent heading hierarchy and formatting
- [ ] Internal cross-links are correct and target files exist
- [ ] No security-sensitive information exposed
- [ ] Edge cases (missing sources, drift, conflicts) are flagged, not papered over
