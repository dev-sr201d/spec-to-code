---
name: frd-skill
description: "Break down the PRD into individual Feature Requirements Documents for independent implementation. Use when decomposing product requirements into focused feature specs."
argument-hint: "Optionally specify which features to focus on..."
---

# Create Feature Requirements Documents

Read the PRD from `specs/prd.md` and decompose it into individual Feature Requirements Documents.

## When to Use

- A PRD exists and needs to be broken into implementable feature specs
- New requirements have been added to the PRD that need their own FRDs
- Existing FRDs need revision after PRD changes

## Procedure

### 1. Read the PRD

Read `specs/prd.md` and identify distinct features that can be independently specified and implemented.

### 2. Propose Features

Present a summary of each proposed feature and ask for confirmation before creating the file. This avoids unnecessary work on features the user doesn't want yet.

### 3. Create FRD Files

For each confirmed feature, create a file in `specs/features/` using the [FRD template](./assets/frd-template.md).

**Naming**: `NNN-<feature-name>.md` — numbered kebab-case (e.g., `001-user-authentication.md`). Check existing files to determine the next sequence number.

### 4. Ensure Traceability

Every FRD must reference the specific PRD requirements it addresses (e.g., REQ-1, REQ-3). Every functional requirement should trace back to a user story.

### 5. Verify Completeness

Ensure all PRD requirements are covered by at least one FRD. Flag any orphaned requirements.

FRDs are living documents — update and revise them when the PRD changes or the user provides feedback.
