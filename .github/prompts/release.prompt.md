---
description: "Cut a versioned release from reviewed, merged tasks. Use when work is ready to ship and needs version bump, changelog, release notes, and tag."
agent: dev
---

Cut a release using `/release-skill`. Optionally specify a target version or release type via **${input:version}**; leave blank to derive the version from the changeset.

The release must satisfy all preconditions (clean tree, every change reviewed, no unmitigated critical/high threats, full validation passes) before any version bump or tag is prepared. The tag is **not pushed** without explicit user authorization.

After preparing the release, hand off to the **lead** agent for release review. Once approved, surface the prepared tag and changelog to the user for the publish step.
