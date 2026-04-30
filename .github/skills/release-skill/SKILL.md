---
name: release-skill
description: "Cut a release: decide semver bump, update changelog, manage deprecations, tag, and emit release notes. Use when one or more reviewed tasks are ready to ship as a versioned release."
argument-hint: "Optionally specify the target version (e.g., 1.4.0) or release type (patch/minor/major). Leave blank to derive from the changelog."
---

# Cut a Release

Produce a versioned, traceable release artifact from a set of reviewed, merged tasks. This skill closes the loop between "code reviewed" and "shipped" — without it there is no auditable boundary between in-flight work and what is running in production.

## When to Use

- One or more tasks have passed `/code-review-skill` and are merged on the release branch
- A user-visible change, security fix, or contract change needs to be published
- A scheduled release cadence (per `specs/prd.md` §12 Operational Expectations) has come due

## Inputs

**ALWAYS start by reading these files:**

- `AGENTS.md` — Release commands, version locations, deprecation policy, supported branches
- `specs/prd.md` — §12 Operational Expectations (release cadence), §11 SLOs (error-budget policy)
- `specs/adr/*.md` — Any ADR that prescribes versioning scheme, distribution channel, or signing requirements
- `specs/threat-model.md` — Threat status; unmitigated critical/high threats block release
- `CHANGELOG.md` — Existing release history (if the project maintains one)
- The changeset since the last release (use `search/changes` and `git log <last-tag>..HEAD`)
- Task files for every change in the changeset (`specs/tasks/FNNN/NNN-*.md`) — acceptance criteria become release-note bullets

## Procedure

### 1. Verify Preconditions

Hard gates. If any check fails, **stop** and report.

- **Working tree clean.** `git status --porcelain` returns empty.
- **On the release branch.** Per `AGENTS.md`. If the project does not define one, default to `main`.
- **Up to date.** `git fetch` then verify the local branch matches its remote.
- **Every change has an approved code review.** For each task in the changeset, the task's `/code-review-skill` verdict must be `approved`. If any change lacks a review, hand off to **lead** before continuing.
- **No critical or high-severity threats are unmitigated.** Read `specs/threat-model.md`. Any threat with `Status: proposed` and severity `critical` or `high` blocks the release. Either mitigate, formally accept the risk via the threat model's accepted-risks section with a named approver, or postpone.
- **Full validation passes.** Run the project's full test, lint, typecheck, build, secret-scan, and dependency-audit commands as documented in `AGENTS.md`. A release must not ship a regression.

### 2. Determine Version Bump

Use **Semantic Versioning** (`MAJOR.MINOR.PATCH`) unless `AGENTS.md` or an ADR prescribes a different scheme (e.g., CalVer for a date-based product).

| Bump | Trigger |
|------|---------|
| **MAJOR** | Any backward-incompatible change to a public contract: removed/renamed APIs, breaking schema changes, removed CLI flags, removed config keys, raised minimum runtime/dependency versions in a way that breaks consumers. |
| **MINOR** | Backward-compatible additions: new endpoints, new optional fields, new feature flags off-by-default, new deprecation announcements. |
| **PATCH** | Backward-compatible fixes only: bug fixes, performance improvements, internal refactors, doc-only changes, dependency patch/minor updates with no API impact. |
| **Security** | Apply the smallest bump that ships the fix — usually PATCH, MINOR if the fix requires a new field or option, MAJOR only when the fix is itself a breaking change. Always flag in release notes. |

For each task in the changeset, classify the change and pick the **highest** bump triggered. Record the rationale.

### 3. Manage Deprecations

Read every change for deprecation activity:

- **New deprecations** — A symbol, endpoint, flag, or behavior is being marked deprecated. Verify:
  - The deprecation notice is in code (e.g., `@deprecated` tag, runtime warning) per `AGENTS.md`.
  - A migration path is documented (link or inline in release notes).
  - A removal target version is stated.
  - The deprecation policy window in `AGENTS.md` is honored (e.g., "deprecated symbols remain for at least one MINOR release before removal").
- **Scheduled removals** — A symbol previously marked deprecated is being removed. This is a **MAJOR** trigger. Verify the deprecation has been published for the required minimum number of releases.
- **Silent removals are forbidden.** Any removed public API without a prior deprecation cycle blocks the release. Hand back to the dev agent to either restore-and-deprecate or escalate to the arch agent for an ADR amendment.

### 4. Update Version Locations

Update every file that carries a version string. `AGENTS.md` must list these — if it doesn't, hand off to **arch** via `/standards-skill` to enumerate them. Common locations:

- Package manifest (`package.json`, `*.csproj`, `pyproject.toml`, `Cargo.toml`, `go.mod` module path for v2+, etc.)
- Lock file (re-generated, never hand-edited)
- Application metadata (e.g., assembly info, build constants)
- Container image tags / Helm chart versions (if owned by this repo)
- Documentation that pins a version (e.g., install instructions in `docs/usage/`)

### 5. Update CHANGELOG.md

Use [Keep a Changelog](https://keepachangelog.com/) format unless `AGENTS.md` overrides it.

For the new version section, include:

- **Version and date** — `## [X.Y.Z] — YYYY-MM-DD`
- **Added** — New features (one bullet per task, link to the task file)
- **Changed** — Behavior changes that are backward compatible
- **Deprecated** — New deprecations with migration notes and removal target version
- **Removed** — Symbols/features removed (only valid in a MAJOR release, must reference prior deprecation)
- **Fixed** — Bug fixes
- **Security** — Security fixes with CVE IDs where applicable; do not include exploit details

Every bullet must trace to a task ID (`FNNN/NNN`) or an issue ID (`ISS-NNN`). If a change has no traceable artifact, stop — that change should not be in the release.

If `CHANGELOG.md` does not exist, create it with the standard header before adding the version section.

### 6. Generate Release Notes

Release notes are a user-facing, audience-appropriate summary, distinct from the raw `CHANGELOG.md`:

- **Summary paragraph** — What this release does for the user.
- **Highlights** — Up to five most-important changes.
- **Breaking changes** — Required for any MAJOR release; include migration steps.
- **Security advisories** — Required if the release contains security fixes.
- **Upgrade notes** — Migration scripts, config changes, infrastructure changes operators must perform.
- **Known issues** — Reference open `ISS-NNN` entries that are deferred but relevant.

Write release notes to `docs/operations/release-notes/X.Y.Z.md`. If `docs/operations/release-notes/` does not exist, create it. If the project hosts release notes elsewhere (per `AGENTS.md`), follow that location instead.

### 7. Tag and Sign

- **Tag format** — `vX.Y.Z` unless `AGENTS.md` prescribes another (e.g., `package-name@X.Y.Z` for monorepos).
- **Annotated tag** — Use `git tag -a vX.Y.Z -m "Release X.Y.Z"`. Annotated, never lightweight, so the tag carries author/date metadata.
- **Sign the tag** if the project requires signed tags (per `AGENTS.md` or a security ADR). Use `git tag -s` when signing keys are configured for the developer.
- **Do not push automatically.** Tag pushing is destructive across teams; surface the prepared tag, the diff vs. the previous release, the changelog entry, and the release notes path to the user, and ask for explicit confirmation before `git push --follow-tags`.

### 8. Hand Off to Lead

Hand off to the **lead** agent for release review. The review scope is:

- The version-bump rationale matches the actual contract changes
- `CHANGELOG.md`, release notes, and version locations agree on the same version
- Every release-note bullet traces to a reviewed task
- Deprecation policy was honored
- Preconditions in Step 1 were genuinely met (tests, threat model, secret scan)

If the lead's verdict is `changes-requested`, fix the findings and re-submit. The release is not "shipped" until the lead approves and the user authorizes the tag push.

### 9. Post-Release

After the user pushes the tag:

- Verify the release pipeline (CI release job) succeeded. If it fails, treat the release as not-shipped and either roll forward (next patch) or roll back the tag per `AGENTS.md`'s rollback policy.
- Update `docs/operations/maintenance-log.md` with a release entry: version, date, changeset summary, any operator actions required.
- If the release closed any `ISS-NNN` issues, update their status in `specs/issues.md` with `Resolution: shipped in vX.Y.Z`.

## Rules

- **No silent breaks.** Every breaking change requires MAJOR, a deprecation cycle (when applicable per policy), and a release-note migration path.
- **No untraceable changes.** Every changelog bullet maps to a task or issue.
- **No release with unmitigated critical/high threats.** Threat-model status is a precondition, not a footnote.
- **No automatic tag push.** The user authorizes the publish step.
- **No edits to past changelog entries.** Released versions are immutable history; corrections live in the next release.
- **No ADR or AGENTS.md edits from this skill.** Versioning scheme, branch model, and signing requirements are arch concerns — hand off if changes are needed.

## Quality Checklist

- [ ] Working tree clean and on the release branch
- [ ] Every change in the changeset has an approved code review
- [ ] Threat model has no unmitigated critical/high threats
- [ ] Full validation (tests, lint, typecheck, build, secret scan, dep audit) passes
- [ ] Version bump matches the highest-impact change in the changeset
- [ ] Deprecation policy honored; no silent removals
- [ ] All version-bearing files updated to the new version
- [ ] `CHANGELOG.md` updated; every bullet traces to a task or issue
- [ ] Release notes written under `docs/operations/release-notes/X.Y.Z.md`
- [ ] Annotated (and signed, where required) tag prepared but not pushed
- [ ] Lead reviewed and approved the release
- [ ] User authorization captured before pushing the tag
