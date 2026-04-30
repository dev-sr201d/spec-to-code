---
description: "Implement a specific task from the plan. Use when task files exist under specs/tasks/FNNN/ and you want to pick one to implement."
agent: dev
---

Implement task **${input:task}** from `specs/tasks/` using `/implement-skill`. The task argument may be the path-style ID `FNNN/NNN` (e.g., `F001/003`) or the full path/filename (e.g., `F001/003-login-endpoint`).
