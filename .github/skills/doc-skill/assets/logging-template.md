# Logging

> **Source**: Derived from `specs/adr/*.md`, `AGENTS.md`, source code
> **Audience**: Developers, DevOps, SRE

## Overview

<!-- Logging strategy, framework, and standards. -->

## Log Levels

| Level | Usage |
|-------|-------|
| ERROR | Unrecoverable failures requiring attention |
| WARN | Unexpected but recoverable conditions |
| INFO | Significant business or lifecycle events |
| DEBUG | Diagnostic detail for development |

## Log Format

<!-- Structured logging format: fields, encoding (JSON, etc.). -->

## Log Storage & Retention

<!-- Where logs are stored, retention policies, and rotation. -->

## Sensitive Data

<!-- What must never appear in logs: PII, credentials, tokens. Redaction strategy. -->

## Correlation

<!-- How requests are traced across services: correlation IDs, trace context. -->
