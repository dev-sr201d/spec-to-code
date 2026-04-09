# Guiding Principles

These principles apply to all work across the entire project — architecture decisions, code, documentation, and reviews.

## SOLID

All code and design must follow the SOLID principles:

- **Single Responsibility**: Every module, class, or function has one reason to change.
- **Open/Closed**: Open for extension, closed for modification.
- **Liskov Substitution**: Subtypes must be substitutable for their base types without altering correctness.
- **Interface Segregation**: Prefer small, focused interfaces over large, general-purpose ones.
- **Dependency Inversion**: Depend on abstractions, not concrete implementations.

## Documentation Tool Priority

When researching technologies, use the most appropriate documentation source:

| Tool | Use For |
|------|---------|
| **context7** | Library and framework API docs, configuration syntax, version-specific usage patterns |
| **mdn** | Web standards — HTML, CSS, JavaScript language features, browser APIs, compatibility data |
| **microsoft.docs.mcp** | .NET, Azure, TypeScript, and other Microsoft ecosystem technologies |
| **deepwiki** | GitHub repository-specific questions — how a particular open-source project works, its internals, conventions |

When a topic spans multiple sources (e.g., a .NET library that wraps a web standard), start with the most specific source and fall back to general ones.

## Zero Trust

Assume no implicit trust at any boundary:

- **Validate all inputs** at every system boundary — API endpoints, message handlers, user input, external service responses.
- **Authenticate and authorize** every request. Never rely on network location or caller identity by assumption.
- **Least privilege** — grant only the minimum permissions required for each operation.
- **Encrypt in transit and at rest** — no plaintext secrets, tokens, or sensitive data.
- **Fail securely** — errors must not leak internal details or leave the system in an exploitable state.
