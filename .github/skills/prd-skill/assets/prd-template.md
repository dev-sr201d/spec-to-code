# Product Requirements Document (PRD)

## 1. Purpose

Briefly describe the problem this product or feature solves and who it is for.

## 2. Scope

- **In Scope:** What will be delivered.
- **Out of Scope:** What will not be addressed.

## 3. Goals & Success Criteria

- What are the business or user goals?
- How will success be measured? (Include measurable KPIs.)

## 4. High-Level Requirements

- [REQ-1] High-level requirement description
- [REQ-2] Another high-level requirement

## 5. User Stories

As a [user type], I want to [do something], so that [benefit].
**Acceptance Criteria:**
- Given [context], when [action], then [outcome].

## 6. Stakeholders

| Role | Name / Team | Responsibility |
|------|-------------|----------------|
| Decision-maker | | Final approval on scope and priorities |
| Consulted | | Domain expertise, technical feasibility |
| Informed | | Progress updates, release readiness |

## 7. Non-Functional Requirements

State concrete, testable targets. Mark `N/A` with a one-line rationale where a category does not apply — never leave a row blank.

| Category | Target | Notes |
|---------|--------|-------|
| Performance (latency, throughput) | e.g., p95 API latency < 300 ms at 100 RPS | |
| Capacity / scale | e.g., 10k active users, 1M records | |
| Availability target | e.g., 99.9% monthly | See §11 SLOs |
| Browser / device / platform support | | |
| Localization / supported locales | | See §10 i18n |

## 8. Compliance & Regulatory

| Regime | In scope? | Notes |
|--------|-----------|-------|
| GDPR / UK GDPR | Yes / No | |
| CCPA / CPRA | Yes / No | |
| HIPAA | Yes / No | |
| PCI-DSS | Yes / No | |
| SOC 2 | Yes / No | |
| Other (e.g., FedRAMP, ISO 27001, sector-specific) | | |

For every regime marked "Yes", list the specific obligations the product must satisfy (data residency, audit logging, consent capture, breach notification windows, etc.).

## 9. Privacy & Data Classification

- **Data classes handled** — Public / Internal / Confidential / Restricted (PII, PHI, payment, secrets).
- **PII / sensitive fields collected** — List each field, purpose, and lawful basis.
- **Retention policy** — How long is each data class kept? When is it deleted/anonymized?
- **Data residency** — Where may data be stored and processed?
- **Subject rights** — Access, rectification, deletion, export, consent withdrawal — which apply and how are they served?
- **Third-party processors** — Any data shared with third parties; purpose and safeguards.

## 10. Accessibility & Internationalization

- **Accessibility target** — WCAG conformance level (e.g., WCAG 2.2 AA). Applies to all user-facing surfaces unless explicitly excluded.
- **Assistive technology support** — Screen readers, keyboard-only, voice control expectations.
- **Locales / languages** — Initial set and how the list is expanded.
- **RTL / bidirectional text** — Required or not.
- **Locale-aware formatting** — Dates, numbers, currencies, addresses.

## 11. Service Level Objectives (SLOs)

Define SLOs for each user-facing capability. The arch and dev agents use these to size infrastructure and shape error handling.

| SLI | Definition | SLO target | Measurement window |
|-----|------------|-----------|--------------------|
| Availability | Successful requests / total requests | e.g., 99.9% | 30-day rolling |
| Latency | p95 of successful request duration | e.g., < 500 ms | 30-day rolling |
| Correctness | Successful business outcomes / attempts | | |

- **Error budget policy** — What happens when an SLO is breached (freeze releases, prioritize reliability work, etc.)?
- **RTO / RPO** — Maximum tolerable downtime and data loss for disaster scenarios.

## 12. Operational Expectations

- **Supported environments** — Production, staging, preview, local.
- **Release cadence** — Continuous, weekly, monthly, etc.
- **Backup and disaster recovery** — Frequency, retention, restore drill cadence.
- **Support model** — On-call expectations, severity definitions, response targets.

## 13. Telemetry & Analytics

- **Product analytics** — Events to capture, who consumes them.
- **Operational telemetry** — Logs, metrics, traces required to operate the system.
- **Privacy controls** — Anonymization / consent gating for analytics events.

## 14. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [Risk 1] | High / Medium / Low | High / Medium / Low | [Mitigation strategy] |

## 15. Assumptions & Constraints

- [Assumption 1]
- [Constraint 1]

## 16. Milestones

| Milestone | Description | Target |
|-----------|-------------|--------|
| [Milestone 1] | What it includes | [Timeframe or dependency] |
