---
name: analyze-skill
description: "Reverse-engineer an existing codebase into project artifacts (PRD, FRDs, ADRs, AGENTS.md). Use when onboarding an existing project into the spec-to-code framework, analyzing unfamiliar codebases, or bootstrapping specs from code."
argument-hint: "Specify the analysis scope: 'full' for complete analysis, or a specific phase (discovery, prd, frds, adrs, standards, docs)..."
---

# Analyze Existing Codebase

Reverse-engineer an existing codebase into the standard project artifacts that the other agents need to operate.

## When to Use

- Dropping this agent setup into an existing project that lacks specs
- Onboarding a new team onto an unfamiliar codebase
- Bootstrapping a PRD, FRDs, and ADRs from working code
- Generating AGENTS.md from detected conventions

## Procedure

### Phase 1: Codebase Discovery

Systematically analyze the project to build a complete mental model before writing anything. **Do not skip any area.** If a directory exists, inspect it. If a config file exists, read it.

**Delegation**: Each discovery sub-phase (1.1–1.8) must be delegated to its own subagent for context isolation. Each subagent receives the task description below and must return a structured findings report. After all subagents complete, synthesize their findings into a unified understanding before proceeding to Phase 2.

**Subagent execution**: Use the **explore-write** agent (defined in `.github/agents/explore-write.agent.md`) for all discovery sub-phases. It has read, search, and edit tools — enough to explore broadly and write the analysis report files. Sub-phases 1.1 and 1.2 must run first (sequentially or in parallel). Once their results are available, sub-phases 1.3–1.8 may run in parallel since they benefit from but do not strictly depend on each other. Pass relevant findings from 1.1/1.2 into each subsequent subagent prompt for context.

For each subagent call, instruct it to:
- Perform only its assigned sub-phase
- **Write the full report** to `specs/.analysis/<phase>.md` (e.g., `specs/.analysis/1.1-project-structure.md`) using the **Findings Report Format** below
- **Return only a condensed summary** (one paragraph of key findings + a bullet list of issues found) — not the full report. This keeps the caller's context small.
- Cite specific file paths for every finding
- Flag issues separately from factual observations

**Findings Report Format** — Each subagent must write its report file with these sections:

1. **Summary** — One-paragraph overview of findings
2. **Findings** — Organized by category, each finding includes: description, evidence (file paths and line ranges), and notes
3. **Issues** — Separated from findings, each issue includes: description, location (file paths), impact (why it matters), and severity (critical / major / minor)

The full reports in `specs/.analysis/` serve as the detailed reference. The condensed summaries returned to the caller are sufficient for orchestration and synthesis. Downstream phases (2–5) should read specific analysis files when they need detail beyond what the synthesis provides.

#### 1.1 Project Structure

**Delegate as subagent** — Return: project type, entry points, directory-to-layer mapping, dead/orphaned directories.

- List the root directory and **every** subdirectory recursively (use search/listDirectory)
- Identify the project type (web app, API, CLI, library, monorepo, etc.)
- Locate **all** entry points (`main`, `index`, `app`, `server`, startup files, etc.)
- Map the directory layout to logical layers (frontend, backend, shared, infra, etc.)
- Identify dead or orphaned directories that don't connect to the main application
- Check for monorepo patterns (workspaces, multiple package files, shared libs)

#### 1.2 Technology Stack

**Delegate as subagent** — Return: languages, frameworks, libraries (with versions), build tools, CI/CD tools, infra-as-code, configuration files, version currency issues.

- Read **all** package/dependency files (`package.json`, `requirements.txt`, `*.csproj`, `go.mod`, `Cargo.toml`, `pom.xml`, `Gemfile`, `*.sln`, lock files, etc.)
- Identify languages, frameworks, libraries, and their **exact** versions
- Detect build tools, test frameworks, linters, and formatters
- Read **all** CI/CD configuration (`.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `Dockerfile`, `docker-compose.yml`, `.env.example`, etc.)
- Read infrastructure-as-code files if present (Terraform, Bicep, CloudFormation, Pulumi, etc.)
- Read **all** configuration files (`.env.example`, `appsettings.json`, `config/`, etc.) — note any secrets or sensitive values that should not be committed
- **Cross-check versions** — Use documentation tools (context7, deepwiki) to verify if dependency versions are current, supported, and free of known vulnerabilities. Flag outdated or EOL versions.

#### 1.3 Architecture Patterns

**Delegate as subagent** — Return: architectural style, component boundaries, data stores, API surface, auth mechanisms, external integrations, anti-pattern findings.

- Identify architectural style (MVC, layered, microservices, serverless, monolith, hexagonal, CQRS, etc.)
- Map **every** component boundary and inter-component communication (HTTP, message queues, shared DB, file system, etc.)
- Identify **all** data stores and their usage patterns (ORM, raw queries, caching layers, file storage)
- Detect the full API surface area (REST, GraphQL, gRPC, WebSocket, etc.) — read route definitions exhaustively
- Map authentication/authorization mechanisms — trace the full auth flow from entry to enforcement
- Identify **all** external service integrations (third-party APIs, cloud services, email providers, payment gateways, etc.)
- **Cross-check architecture** — Use documentation tools to verify the codebase follows framework-recommended patterns. Flag anti-patterns.

#### 1.4 Security Audit

**Delegate as subagent** — Return: findings per OWASP category, input validation gaps, auth/authz issues, data protection concerns, vulnerable dependencies.

- **Input validation** — Scan for user input handling: are inputs validated at system boundaries? Search for raw query construction, unescaped outputs, unsanitized parameters.
- **Authentication** — How are credentials handled? Are secrets hardcoded? Is token storage secure? Are passwords hashed with strong algorithms?
- **Authorization** — Is access control enforced at every endpoint? Are there unprotected routes? Is there role/permission checking?
- **Data protection** — Is sensitive data encrypted at rest and in transit? Are there PII exposure risks in logs or error messages?
- **Dependencies** — Are there known vulnerable packages? Use documentation tools to check CVE databases and security advisories.
- **OWASP Top 10** — Systematically check for: injection, broken auth, sensitive data exposure, XXE, broken access control, security misconfiguration, XSS, insecure deserialization, known vulnerable components, insufficient logging.
- **Cross-check with official security guidelines** — For each framework/library, verify the codebase follows the official security recommendations.

#### 1.5 Code Quality & Patterns

**Delegate as subagent** — Return: naming conventions per layer, error handling patterns, logging patterns, code duplication areas, separation of concerns assessment, convention deviations from official recommendations.

- Sample **at least 10 files per layer** to detect naming conventions (camelCase, snake_case, PascalCase)
- Identify error handling patterns (exceptions, result types, error codes) — check for consistency and completeness
- Note testing patterns (frameworks, file organization, naming, mocking strategy) — measure approximate coverage (count test files vs source files)
- Detect logging and instrumentation patterns — are they consistent? Do they leak sensitive data?
- Check for existing linting/formatting configuration (`.eslintrc`, `.prettierrc`, `ruff.toml`, etc.)
- Identify code duplication, dead code, and overly complex modules
- Check for proper separation of concerns — are boundaries clean or are layers leaking into each other?
- **Cross-check conventions** — Use documentation tools to verify the codebase follows idiomatic patterns for the detected language/framework.

#### 1.6 Testing Analysis

**Delegate as subagent** — Return: test file inventory, test classification, coverage assessment, test quality evaluation, untested critical paths.

- Identify **all** test files and test directories
- Classify tests: unit, integration, end-to-end, performance, contract
- Assess coverage: which modules have tests and which don't?
- Evaluate test quality: are tests meaningful or trivially passing? Do they test edge cases?
- Check for test infrastructure: fixtures, factories, mocks, test utilities
- Identify untested critical paths (auth flows, payment handling, data mutations)
- **Run the test suite** — If a test runner is configured, execute the tests using `execute/runInTerminal` and record: number of tests, pass/fail counts, failing test names, and overall execution time. If tests cannot run (missing dependencies, broken config), document why.

#### 1.7 Existing Documentation

**Delegate as subagent** — Return: inventory of existing docs, accuracy assessment per document, gaps between docs and code.

- Read existing `README.md`, `CONTRIBUTING.md`, `CHANGELOG.md`
- Check for existing docs in `docs/`, `wiki/`, or inline documentation
- Note any existing specs, ADRs, or design documents
- **Identify inaccuracies** — Compare existing docs against actual code behavior. Flag any documentation that is stale, misleading, or contradicts the code.

#### 1.8 Build & Dependency Verification

**Delegate as subagent** — Return: dependency install result, build result, build warnings, missing prerequisites, environment assumptions.

- **Install dependencies** — Attempt the standard install command (`npm install`, `pip install -r requirements.txt`, `dotnet restore`, `go mod download`, etc.) using `execute/runInTerminal`. Record success, warnings, or failures.
- **Build the project** — Attempt the standard build command if applicable. Record success, warnings, or failures.
- **Document prerequisites** — Note any required system dependencies, environment variables, or tools that must be pre-installed.
- If install or build fails, document the exact error output — this is a critical finding.

#### 1.9 Synthesize Discovery Findings

After all subagent summaries are returned, consolidate them into a unified view:
- Work primarily from the **condensed summaries** returned by each subagent
- When summaries conflict or lack detail, **read the full report** from `specs/.analysis/<phase>.md` to resolve
- Resolve any contradictions between subagent findings
- Build the complete mental model: project type, tech stack, components, data flows, security posture, quality level, test coverage
- Identify cross-cutting issues that span multiple sub-phases
- Write the synthesized overview to `specs/.analysis/1.9-synthesis.md` for downstream phases to reference
- This synthesized understanding feeds into Phases 2–5

### Phase Dependencies

Phases must execute in this order due to data dependencies:

- **Phase 1** (Discovery) must complete before any other phase
- **Phase 2** (PRD) must complete before Phase 3 (FRDs), since FRDs reference `REQ-N` items from the PRD
- **Phase 4** (ADRs) may run in parallel with Phases 2–3 — it depends only on Phase 1 findings
- **Phase 5** (AGENTS.md) should follow Phase 4, so conventions can cross-reference ADR decisions
- **Phase 6** (Issues Manifest Finalization) should follow Phases 2–5, since issues are accumulated throughout those phases
- **Phase 7** (Documentation Handoff) must run last — it consumes all prior artifacts

### Phase 2: PRD Generation

Reverse-engineer product requirements from what the code actually does. Read `specs/.analysis/1.9-synthesis.md` for the overall picture; drill into specific `specs/.analysis/1.*` files when you need detail (e.g., `1.3-architecture-patterns.md` for API surface, `1.4-security-audit.md` for auth flows).

1. **Identify user-facing capabilities** — What can users do with this system? Walk through **every** entry point, route, UI component, and CLI command. Don't just read route definitions — trace the handlers to understand actual behavior.
2. **Derive business goals** — Based on the capabilities, infer the business purpose and target audience.
3. **Extract functional requirements** — Each distinct capability becomes a `REQ-N` requirement.
4. **Note non-functional characteristics** — Observe actual performance patterns, security measures, scalability approach, reliability mechanisms.
5. **Write `specs/prd.md`** using the [PRD template](../prd-skill/assets/prd-template.md).
6. **Mark as reverse-engineered** — Add a note at the top: `> This PRD was reverse-engineered from the existing codebase by the analyst agent. Review and refine with the product owner.`
7. **Record issues directly to `specs/issues.md`** — Any systemic issues discovered during PRD analysis (missing functionality, security gaps, etc.) go straight into the issues manifest. Use category `functionality` or `security` as appropriate. Reference the PRD as the source artifact.

### Phase 3: FRD Generation

Decompose the PRD into feature specs mapped to actual code modules.

1. **Group requirements by feature area** — Map related `REQ-N` items to cohesive features.
2. **Map features to code** — Each FRD should correspond to identifiable code modules, routes, or components. **List the specific source files** that implement the feature.
3. **Write user stories** — Derive user stories from what the code enables.
4. **Define acceptance criteria** — Based on what the code actually does (including edge cases you observe).
5. **Create FRDs** in `specs/features/` using the [FRD template](../frd-skill/assets/frd-template.md).
6. **Naming**: `NNN-<feature-name>.md` — numbered kebab-case, check existing files for sequence.
7. **Mark as reverse-engineered** — Add a note at the top of each: `> This FRD was reverse-engineered from the existing codebase by the analyst agent. Review and refine with the product owner.`
8. **Record issues directly to `specs/issues.md`** — Feature-specific issues (bugs, missing edge case handling, missing validation, missing tests, UX gaps, accessibility gaps, etc.) go straight into the issues manifest. Reference the relevant FRD as the source artifact.

### Phase 4: ADR Generation

Document the technology decisions that are already baked into the code — and validate them against best practices. Read `specs/.analysis/1.2-technology-stack.md` and `specs/.analysis/1.3-architecture-patterns.md` for detailed evidence.

1. **One ADR per significant decision** — Language choice, framework, database, authentication approach, hosting model, state management, API style, testing framework, etc.
2. **Status: Accepted** — These decisions are already in effect.
3. **Context from code** — The "Context" section should describe what the code reveals (e.g., "The project uses Express.js with TypeScript, as seen in package.json and the src/ structure").
4. **Considered options** — List the chosen technology and 2-3 alternatives that were plausible. Note why the chosen option likely won (common reasons: team familiarity, ecosystem, project constraints).
5. **Cross-check with official documentation** — For each technology decision, use documentation tools (context7, deepwiki, mdn, microsoft.docs.mcp) to:
   - Verify the version in use is still supported and not EOL
   - Check if the usage patterns match recommended best practices
   - Identify any known security advisories for the version in use
   - Note if newer major versions offer significant improvements
6. **Create in `specs/adr/`** using the [MADR template](../adr-skill/assets/madr-template.md).
7. **Naming**: `NNN-short-title.md` — zero-padded, sequential, never reuse numbers.
8. **Record issues directly to `specs/issues.md`** — Technology-specific issues (version currency, deprecated API usage, pattern violations, missing recommended configuration, security advisory matches, license concerns) go straight into the issues manifest. Reference the relevant ADR as the source artifact. Include official doc reference in the rationale.

### Phase 5: AGENTS.md Generation

Extract coding standards from what the codebase actually practices — and validate against official recommendations. Read `specs/.analysis/1.5-code-quality-patterns.md` for detailed convention evidence.

1. **Document observed patterns** — Not aspirational standards, but what the code actually does.
2. **Include code examples** from the actual codebase.
3. **Organize by technology layer** — General, Backend, Frontend, Testing, etc.
4. **Cross-check conventions** — For each detected pattern, use documentation tools to verify it follows the official style guide and recommended practices for that language/framework. Reference the official source.
5. **Note inconsistencies** — If the codebase mixes patterns, document the dominant one as the standard.
6. **AGENTS.md must be issues-free** — AGENTS.md is loaded into every agent operation. It must contain only clean, authoritative guidelines — never issues, warnings, or deviation notes. Any findings about convention violations, inconsistent patterns, or deviations from best practices go directly into `specs/issues.md` with category `convention`. Include the observed pattern, the recommended pattern (with official doc link), and file path examples.
7. **Write `AGENTS.md`** at the project root using the [AGENTS.md template](../standards-skill/assets/agents-template.md).

### Phase 6: Issues Manifest Finalization

Throughout Phases 2–5, issues have been written directly to `specs/issues.md`. This phase validates and finalizes the manifest.

1. **Create the manifest early** — At the start of Phase 2 (before writing any issues), create `specs/issues.md` with the header, column definitions, and the note: `> This issues manifest was generated by the analyst agent. All issues require triage by the dev lead before they enter the implementation pipeline.`
2. **Issue format** — Every entry written during Phases 2–5 must include: a unique ID (`ISS-NNN`, sequentially assigned as issues are discovered), severity (critical / major / minor), category (security, quality, convention, testing, dependency, functionality), source artifact (file path of the PRD, FRD, or ADR the issue relates to), description, code location (file path and line range where the issue was observed), rationale (why it matters), and a `Triage` column initialized to `pending`.
3. **Validate completeness** — After Phase 5, review the manifest for: duplicate entries, missing fields, inconsistent severity ratings, and issues that lack code location evidence.
4. **Sort by severity** — Critical first, then major, then minor.
5. **Assign final IDs** — Renumber `ISS-NNN` entries sequentially after sorting so the manifest reads cleanly.

Valid triage values (set by the **lead** agent later, not by the analyst): `pending`, `promote` (will become a requirement in an FRD), `new-frd` (warrants a standalone remediation FRD), `accepted-debt` (acknowledged, not actioned now), `duplicate`.

This manifest is the **single source of truth** for analyst-discovered issues. Downstream agents (lead, po, dev) consume it — spec artifacts (PRD, FRDs, ADRs) contain only clean spec content, no issues.

### Phase 7: Documentation Handoff

Delegate to the **doc** agent to generate `docs/` content from the freshly created artifacts.

- Use `agent/runSubagent` to invoke the **doc** agent
- Instruct it to generate all documentation areas: architecture, operations, usage
- The doc agent will use the PRD, FRDs, ADRs, AGENTS.md, and source code as inputs

## Resumability

Each phase produces durable artifacts that persist to disk. If analysis is interrupted (context limit, timeout, or user request), it can be resumed by specifying the phase to continue from:

- **Phase 1** (discovery) → No prior artifacts needed. Re-run discovery from scratch.
- **Phase 2** (prd) → Requires Phase 1 findings (synthesized in the agent's context or re-derivable from the codebase).
- **Phase 3** (frds) → Requires `specs/prd.md` to exist.
- **Phase 4** (adrs) → Requires Phase 1 findings. Can run independently of Phases 2–3.
- **Phase 5** (standards) → Requires ADRs in `specs/adr/` to exist.
- **Phase 6** (issues) → Requires Phases 2–5 to be complete (issues accumulated throughout).
- **Phase 7** (docs) → Requires all prior artifacts in `specs/` and `AGENTS.md`.

When resuming, check which artifacts already exist and skip completed phases.

## Output Summary

After completing the analysis, provide a summary report:

- **Project type**: What kind of project this is
- **Tech stack**: Languages, frameworks, and key libraries detected (with version currency status)
- **Features identified**: Count and list of FRDs created
- **Architecture decisions**: Count and list of ADRs created
- **Issue summary**: Total count by severity (critical / major / minor) and by category (security, quality, convention, testing, dependency)
- **Top critical issues**: List the most urgent findings that need immediate attention
- **Observations**: Notable patterns, technical debt, or concerns found
- **Issues manifest**: Location of `specs/issues.md` and total issue count by severity
- **Next steps**: Recommend invoking **lead** agent to triage `specs/issues.md` before any refinement or planning begins

## Quality Checklist

- [ ] Every directory in the project was inspected — no folders were skipped
- [ ] Every requirement in the PRD maps to actual code functionality
- [ ] Every FRD maps to identifiable code modules with specific file paths listed
- [ ] Every ADR reflects a real technology choice visible in the codebase
- [ ] Every ADR has been cross-checked against official documentation for best practices and version currency
- [ ] AGENTS.md reflects actual coding patterns, with deviations from official recommendations flagged
- [ ] Security audit completed against OWASP Top 10
- [ ] Testing analysis completed with coverage gaps identified
- [ ] No invented features or requirements — only what the code does
- [ ] Every issue is recorded directly in `specs/issues.md` with description, file path, rationale, and severity
- [ ] All artifacts are marked as reverse-engineered for transparency
- [ ] Official documentation was consulted for every major technology in the stack
- [ ] Every issue entry includes a severity classification (critical / major / minor)
- [ ] AGENTS.md contains only clean guidelines — no issues, warnings, or deviation notes
- [ ] `specs/issues.md` manifest generated with all issues consolidated, normalized, and sorted by severity
- [ ] Every issue in the manifest has a unique ID, severity, category, source artifact, code location, and rationale
- [ ] Documentation handoff to doc agent was completed
- [ ] Build and dependency install were attempted and results documented
