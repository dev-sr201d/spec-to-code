---
description: "Create or refresh the project threat model in specs/threat-model.md. Use after ADRs/FRDs change trust boundaries, identity, data flows, or compliance scope."
agent: arch
---

Create or refresh the project threat model using `/threat-model-skill`.

If a scope is provided as **${input:scope}**, focus the threat model on that feature, component, or boundary. Otherwise refresh the full model from the current PRD, FRDs, and ADRs.
