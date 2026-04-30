---
name: threat-model-skill
description: "Create or update the project threat model in specs/threat-model.md using STRIDE. Use after ADRs and FRDs are accepted, when new external interfaces are added, or when an FRD/ADR materially changes the trust boundaries or data flows."
argument-hint: "Leave blank to refresh the full threat model, or specify a feature/component to focus on..."
---

# Create or Update Threat Model

Produce or refresh `specs/threat-model.md` — the project's authoritative threat model. The threat model translates the Zero Trust principle from `copilot-instructions.md` into concrete, testable mitigations keyed to FRDs, ADRs, and tasks.

## When to Use

- After ADRs and the scaffolding FRD are accepted on a new project
- When a new external interface is introduced (new API, new third-party integration, new data store, new auth mechanism)
- When an FRD or ADR changes trust boundaries, identity, data flows, or stored data classes
- During the existing-codebase onboarding flow, after Phase 5 (AGENTS.md) of `/analyze-skill`
- On a recurring cadence defined by the project's compliance scope (PRD §8)

## Inputs

**ALWAYS start by reading these files:**

- `specs/prd.md` — Compliance scope (§8), privacy and data classification (§9), SLOs (§11). The threat model must cover every regulated data class.
- `specs/features/*.md` — All FRDs, especially user stories, acceptance criteria, and external interfaces.
- `specs/adr/*.md` — Technology choices, hosting/identity/data-store decisions.
- `AGENTS.md` — Security and observability standards already mandated.
- `specs/threat-model.md` — The current threat model (if it exists). Refresh in place; do not start from scratch.

## Procedure

### 1. Establish Scope

Define what the threat model covers in this iteration:

- **Full refresh** — Every component, interface, and data store described by current ADRs and FRDs.
- **Targeted update** — A specific feature or component plus everything it touches across trust boundaries.

State the scope at the top of the document so reviewers can verify coverage.

### 2. Identify Assets

For each asset, record what it is, who/what owns it, its data classification (per PRD §9), and the impact if it is compromised.

| Asset | Description | Classification | Impact if compromised |
|-------|-------------|----------------|----------------------|

Assets include but are not limited to: user credentials, sessions/tokens, PII, payment data, business records, audit logs, secrets, signing keys, deployment infrastructure.

### 3. Map Trust Boundaries and Data Flows

Produce a data-flow view (Mermaid `flowchart` is preferred for in-tree rendering). Mark every boundary that data crosses between trust zones:

- Browser ↔ frontend service
- Frontend ↔ backend API
- Backend ↔ data stores, caches, message brokers
- Backend ↔ third-party services
- CI/CD ↔ runtime environments
- Operator ↔ infrastructure

Each crossing is a candidate for STRIDE analysis.

### 4. Apply STRIDE per Boundary

For every trust boundary identified in Step 3, walk through the six STRIDE categories and record applicable threats. Skip categories that are not credible for that boundary, but record why they were skipped.

| Category | What to consider |
|----------|------------------|
| **S**poofing | Identity assertions: tokens, certificates, API keys, machine identities |
| **T**ampering | Integrity of requests, payloads, stored data, deployment artifacts |
| **R**epudiation | Audit logging, non-repudiation of critical actions |
| **I**nformation disclosure | Data exposure in transit, at rest, in logs, in error responses |
| **D**enial of service | Resource exhaustion, expensive operations, fan-out, retries |
| **E**levation of privilege | Authorization checks, role boundaries, admin paths |

### 5. Record Each Threat

For each threat, create an entry with this shape:

| Field | Required content |
|-------|------------------|
| ID | `THR-NNN` (sequential, never reused, never deleted) |
| Title | Short, action-oriented description |
| STRIDE | Category from Step 4 |
| Boundary | The boundary or component this affects |
| Asset(s) | One or more asset IDs from Step 2 |
| Description | What an attacker does and how |
| Impact | Confidentiality / integrity / availability impact and severity (critical / high / medium / low) |
| Likelihood | High / Medium / Low with rationale |
| Existing controls | What in the current design already mitigates this (with file references to FRDs/ADRs/code) |
| Residual risk | Severity after existing controls |
| Required mitigations | New controls needed; describes WHAT, not HOW (e.g., "rate-limit endpoint X", "scope token to resource Y") |
| Owning artifact | The FRD or ADR that must carry the mitigation as a requirement or decision |
| Status | `proposed`, `accepted`, `mitigated`, `accepted-risk` |

### 6. Cross-Reference to FRDs and ADRs

Threats are not implemented in the threat model itself — they must surface as requirements in FRDs or decisions in ADRs.

For each threat with `Required mitigations`:

- If the mitigation is a **functional/non-functional requirement**, state which FRD it belongs in and propose the requirement text. Hand off to the **po** agent to add it.
- If the mitigation is a **technology or pattern decision**, propose the ADR or ADR amendment. Author the ADR yourself via `/adr-skill`.
- If the mitigation is a **standard** (logging, validation pattern), propose the AGENTS.md update via `/standards-skill`.

The threat model lists the work; the owning artifact does the work. Both link to each other.

### 7. Validate Coverage

Before finalizing, check:

- Every regulated data class in PRD §9 appears in §2 Assets.
- Every external interface from FRDs has at least one boundary in §3.
- Every boundary in §3 has STRIDE analysis recorded in §4–§5.
- Every `Required mitigation` has an owning FRD or ADR.
- Every `accepted-risk` threat has explicit rationale and a sign-off note (who accepted it, when).

### 8. Maintenance

`specs/threat-model.md` is a living document.

- **Never delete threats.** Update `Status` as controls land. `mitigated` threats stay in the document with references to the implementing FRD/ADR/task.
- **Re-run on change triggers.** New ADR introducing a new data store or identity provider, new FRD with an external interface, or new compliance regime in PRD §8 — re-enter this skill with that scope.
- **Number stability.** `THR-NNN` IDs are stable. Superseded threats are marked, not renumbered.

## Document Structure

`specs/threat-model.md` must contain these sections in order:

1. Scope and revision history
2. Assets
3. Trust boundaries and data-flow diagram
4. Threats (table or one subsection per threat with the fields from Step 5)
5. Required mitigations cross-reference (THR → FRD/ADR mapping)
6. Accepted risks with rationale and approver

## Rules

- **Describe WHAT, never HOW** — Mitigations describe the required control, not the implementation.
- **Trace everything** — Each threat references assets, boundaries, and the owning FRD/ADR.
- **No speculative threats** — Every threat must be plausible against the actual design as documented in current FRDs/ADRs.
- **Critical threats cannot be `accepted-risk` silently** — They must be `mitigated` or carry an explicit, named approval.
- **Zero Trust is mandatory** — Authentication, authorization, input validation, and encryption controls cannot be removed by simplicity-first arguments.
- **No source-code edits** — This skill writes only `specs/threat-model.md` and (when needed) hands off ADR/FRD/AGENTS.md updates to the appropriate skill.

## Quality Checklist

- [ ] Scope is explicit (full refresh or targeted)
- [ ] Every regulated data class from PRD §9 appears as an asset
- [ ] Every external interface has at least one trust boundary
- [ ] STRIDE applied to every boundary, with skipped categories justified
- [ ] Every threat has all required fields including residual risk and owning artifact
- [ ] Every required mitigation maps to an FRD requirement, ADR decision, or AGENTS.md standard
- [ ] No critical threat is `accepted-risk` without explicit named approval
- [ ] Existing threat IDs preserved; updates respect history
- [ ] Diagram renders (Mermaid or referenced image exists)
