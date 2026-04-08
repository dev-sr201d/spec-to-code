# Guiding Principles

These principles apply to all work across the entire project — architecture decisions, code, documentation, and reviews.

## SOLID

All code and design must follow the SOLID principles:

- **Single Responsibility**: Every module, class, or function has one reason to change.
- **Open/Closed**: Open for extension, closed for modification.
- **Liskov Substitution**: Subtypes must be substitutable for their base types without altering correctness.
- **Interface Segregation**: Prefer small, focused interfaces over large, general-purpose ones.
- **Dependency Inversion**: Depend on abstractions, not concrete implementations.

## Zero Trust

Assume no implicit trust at any boundary:

- **Validate all inputs** at every system boundary — API endpoints, message handlers, user input, external service responses.
- **Authenticate and authorize** every request. Never rely on network location or caller identity by assumption.
- **Least privilege** — grant only the minimum permissions required for each operation.
- **Encrypt in transit and at rest** — no plaintext secrets, tokens, or sensitive data.
- **Fail securely** — errors must not leak internal details or leave the system in an exploitable state.
