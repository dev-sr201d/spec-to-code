---
description: "Implement a specific task from the plan. Use when task files exist in specs/tasks/ and you want to pick one to implement."
agent: dev
---

Implement task **${input:task}** from `specs/tasks/`.

If significant architectural decisions are needed that aren't covered by existing ADRs, **stop and hand off to the arch agent** before proceeding.
