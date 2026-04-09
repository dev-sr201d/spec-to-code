---
name: maintain-skill
description: "Scan, classify, and apply dependency updates — patches and minors automatically, majors via arch referral. Use for routine maintenance, vulnerability remediation, or dependency health checks."
argument-hint: "Leave blank for a full scan, or specify a package name or scope (e.g., 'express', 'frontend', 'security-only')..."
---

# Maintain Dependencies

Scan project dependencies for outdated, deprecated, or vulnerable packages, classify updates by risk, apply safe updates, and refer major changes to the arch agent.

## When to Use

- Routine dependency maintenance (periodic or ad-hoc)
- Security vulnerability remediation
- Preparing for a major version upgrade
- After the analyst flags outdated dependencies during onboarding

## Procedure

### 1. Read Context

**ALWAYS start by reading these files:**
- `AGENTS.md` — Testing standards and conventions (needed for running tests)
- `specs/adr/*.md` — Architecture decisions (to understand which dependencies are ADR-governed)
- Package/dependency files (`package.json`, `requirements.txt`, `*.csproj`, `go.mod`, `Cargo.toml`, `pom.xml`, `Gemfile`, etc.)
- Lock files (`package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `poetry.lock`, `go.sum`, etc.)

### 2. Scan Dependencies

Run the package manager's built-in audit and outdated commands via `execute/runInTerminal`:

- **npm/pnpm/yarn**: `npm outdated`, `npm audit`
- **pip**: `pip list --outdated`, `pip-audit` (if available)
- **.NET**: `dotnet list package --outdated`, `dotnet list package --vulnerable`
- **Go**: `go list -m -u all`
- **Cargo**: `cargo outdated`, `cargo audit`

If the package manager lacks a built-in audit command, use documentation tools (context7, deepwiki) to check for known vulnerabilities in the detected versions.

Record every finding: package name, current version, latest version, update type, and any known vulnerabilities.

### 3. Classify Updates

Categorize each outdated dependency:

| Classification | Criteria | Action |
|---------------|----------|--------|
| **Patch** | Patch version bump (e.g., 1.2.3 → 1.2.5) | Apply automatically |
| **Minor** | Minor version bump (e.g., 1.2.3 → 1.4.0) | Apply automatically |
| **Major** | Major version bump (e.g., 1.x → 2.x) | Refer to arch agent |
| **Vulnerability** | Has a known CVE or security advisory | Apply fix regardless of version bump size |
| **Deprecated** | Package is unmaintained or officially deprecated | Refer to arch agent (replacement decision needed) |

If no outdated or vulnerable dependencies are found, skip to Step 7 and log a clean scan result.

### 4. Apply Patch and Minor Updates

1. **Create a pre-update snapshot** — Note the current versions being changed (these go into the maintenance log).
2. **Apply updates** — Use the package manager's update commands (e.g., `npm update`, `dotnet outdated --upgrade`).
3. **Run the full test suite** via `execute/runInTerminal`.
4. **If tests pass** — Continue to the next step.
5. **If tests fail** — Diagnose the failure:
   - If the failure is caused by a specific package update, revert that package to its previous version and reclassify it as a major update (refer to arch).
   - Re-run tests to confirm the revert resolves the failure.
   - Record the failure and revert in the maintenance log.

### 5. Handle Vulnerability Fixes

Vulnerability fixes take priority over the patch/minor/major classification:

1. **If the fix is a patch or minor bump** — Apply it in Step 4 with other safe updates.
2. **If the fix requires a major bump** — Apply it directly (do not defer to arch). Security trumps stability per Zero Trust principles.
3. **If no fix is available** — Record in the maintenance log with severity and any available mitigations. Flag for arch review if a replacement package is needed.
4. **Run the full test suite** after each vulnerability fix. If tests fail, do not revert the security fix — diagnose and fix the test or the consuming code instead.

### 6. Refer Major Updates to Arch

For each major version bump or deprecated package:

1. **Prepare a summary** — Current version, target version, breaking changes (check release notes via documentation tools), and which parts of the codebase are affected.
2. **Hand off to the arch agent** — The arch agent evaluates whether to proceed, creates or updates the relevant ADR, and updates `AGENTS.md` if coding patterns change.
3. **Wait for the arch decision** — Do not apply major updates without arch approval.
4. **Once approved** — Apply the update, run the full test suite, and fix any breaking changes following the updated `AGENTS.md` guidance.
5. **If rejected or deferred** — Record the decision in the maintenance log with the arch's rationale.

### 7. Write Maintenance Log Entry

Append an entry to `docs/operations/maintenance-log.md` using the [maintenance log template](./assets/maintenance-log-template.md).

If the file does not exist, create it with the template header before appending.

Each entry must include:
- **Date** — When the maintenance was performed
- **Scope** — Full scan, specific packages, or security-only
- **Updates applied** — Table of package, old version, new version, classification
- **Vulnerabilities addressed** — CVE IDs and resolutions
- **Test results** — Pass/fail, any regressions encountered and how they were resolved
- **Arch referrals** — Major updates or deprecated packages sent to arch, with their decisions
- **Deferred items** — Updates not applied and why

### 8. Hand Off to Lead for Review

After all updates are applied and tests pass, hand off to the **lead** agent for code review:
- The review scope is the dependency file changes and any code changes made to fix breaking updates.
- The lead verifies tests pass and no regressions were introduced.

## Rules

- **Never skip the test suite** — Every update batch must be validated by a full test run.
- **Never revert a security fix** — If a vulnerability patch breaks tests, fix the consuming code instead.
- **Never apply major updates without arch approval** — Major bumps may change patterns, APIs, or conventions.
- **Never modify specs or ADRs** — Maintenance operates outside the feature/task system. ADR updates are the arch agent's responsibility.
- **Always log** — Every maintenance run produces a log entry, even if no updates were applied (record the scan results).
- **Respect lock files** — Always commit updated lock files alongside dependency file changes.

## Quality Checklist

- [ ] All dependency files and lock files read before scanning
- [ ] Package manager audit and outdated commands executed
- [ ] Every outdated dependency classified (patch / minor / major / vulnerability / deprecated)
- [ ] Patch and minor updates applied and tested
- [ ] Vulnerability fixes applied regardless of version bump size
- [ ] Major updates and deprecated packages referred to arch agent
- [ ] Full test suite passes after all updates
- [ ] Maintenance log entry appended to `docs/operations/maintenance-log.md`
- [ ] Handed off to lead for review
