# Discovery Metadata File — `specs/.analysis/.meta.json`

This file tracks analysis run state across invocations, enabling checkpoint/resume.

## Schema

```json
{
  "consent": "static-only | authorized",
  "started": "ISO-8601 timestamp of first invocation",
  "resumed": "ISO-8601 timestamp of latest resume (null on first run)",
  "rootHash": "SHA-256 hex of sorted top-level file/directory names",
  "phases": {
    "1.1": { "status": "completed | failed | degraded | pending", "timestamp": "ISO-8601 | null" },
    "1.2": { "status": "...", "timestamp": "..." },
    "1.3": { "status": "...", "timestamp": "..." },
    "1.4": { "status": "...", "timestamp": "..." },
    "1.5": { "status": "...", "timestamp": "..." },
    "1.6": { "status": "...", "timestamp": "..." },
    "1.7": { "status": "...", "timestamp": "..." },
    "1.8": { "status": "...", "timestamp": "..." }
  }
}
```

## Field Definitions

| Field | Description |
|-------|-------------|
| `consent` | Build verification consent mode selected by the user. Persisted so resume does not re-ask. |
| `started` | ISO-8601 timestamp of when the first invocation began. |
| `resumed` | ISO-8601 timestamp updated on each resume invocation. `null` on fresh runs. |
| `rootHash` | SHA-256 hex digest of the sorted, newline-joined list of top-level file and directory names in the project root (excluding `.git`). Used for stale-report detection on resume. |
| `phases` | Status map for sub-phases 1.1–1.8. Phase 1.9 (synthesis) is excluded because it always regenerates. |

## Phase Status Values

| Status | Meaning | Resume Behavior |
|--------|---------|-----------------|
| `completed` | Report file passed validation. | Skip — do not re-run. Read existing report's Summary for downstream context. |
| `failed` | Report file is a failure stub from a prior run. | Retry — pass the failure reason from the stub as additional context to the subagent. |
| `degraded` | Failed after retry in a prior run. | Retry — the structural issue may have been resolved since the last attempt. |
| `pending` | Not yet attempted. | Run normally. |

## Lifecycle Rules

1. **Creation** — Write `.meta.json` immediately after the consent gate on a fresh run. Initialize all phases as `pending`.
2. **Status transitions** — Update `.meta.json` after every phase status transition (not in bulk at the end). This ensures progress is persisted even if a later phase causes the orchestrator to fail.
3. **Resume timestamp** — Update the `resumed` field at the start of each resume invocation.
4. **Validation on resume** — Re-validate each `completed` report file (exists, non-zero, has Summary/Findings/Issues sections, no `<!-- FAILURE STUB -->` marker). Downgrade to `failed` if validation fails.
5. **Stale-report check** — Recompute `rootHash` on resume and compare. If different, warn user before proceeding.

## Failure Stub Marker

Report files that are failure stubs must begin with an HTML comment marker for machine detection:

```markdown
<!-- FAILURE STUB -->
# Sub-phase X.Y — [Phase Name]

**Status:** Failed/Degraded
**Attempted:** [description of what the subagent tried]
**Validation failure:** [specific reason]
**Note:** This phase is incomplete. It will be retried on next invocation.
```

## rootHash Computation

To compute `rootHash`:

1. List all files and directories in the project root (depth 1 only).
2. Exclude `.git`.
3. Sort names lexicographically.
4. Join with newline characters.
5. Compute SHA-256 hex digest of the resulting string.

This catches major structural changes (added/removed top-level modules) without being triggered by routine file edits within existing directories.
