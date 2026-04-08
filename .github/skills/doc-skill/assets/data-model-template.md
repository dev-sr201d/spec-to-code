# Data Model

> **Source**: Derived from `specs/features/*.md`, `specs/adr/*.md`, source code
> **Audience**: Developers, database administrators

## Overview

<!-- Summary of the data layer: database technology, ORM/query layer, and overall data strategy. Reference relevant ADRs. -->

## Entity Relationship Diagram

<!-- Describe or embed a diagram showing the core entities and their relationships. -->

## Entities

<!--
Repeat the section below for each entity/table in the data model.
Remove this comment block when writing the actual document.
-->

### [Entity Name]

**Description**: <!-- What this entity represents. -->

**Table/Collection**: <!-- Actual table or collection name in the database. -->

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| | | | |

**Indexes**:
| Name | Fields | Type | Purpose |
|------|--------|------|---------|
| | | | |

**Relationships**:
- <!-- e.g., belongs to [OtherEntity], has many [OtherEntity] -->

## Data Lifecycle

<!-- How data flows through the system: creation, updates, archival, deletion. Retention policies if defined. -->

## Migrations

<!-- How schema changes are managed: migration framework, naming conventions, rollback strategy. -->

## Seed & Test Data

<!-- How development/test data is provisioned. -->
