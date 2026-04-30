# Task: [Task Title]

## Description

Clear explanation of what needs to be built.

## Dependencies

- [FNNN/NNN] — Why this dependency exists

## Threats Mitigated

<!--
List every threat from `specs/threat-model.md` whose `Required mitigations` this task implements (in whole or in part).
Use `None` only when the task implements no mitigation (e.g., pure refactor, doc-only task, internal helper with no boundary impact).
Never omit this section.
-->

- [THR-NNN] — Which mitigation this task delivers and how it will be verified

## Technical Requirements

- Specific details, APIs, data structures
- No implementation code

## Acceptance Criteria

- [ ] Measurable, testable criterion
- [ ] Another criterion (each cited THR-NNN must map to at least one acceptance criterion)

## Testing Requirements

- Unit tests: What to test
- Integration tests: What to verify
- Mitigation tests: For each cited `THR-NNN`, the test that proves the mitigation holds (e.g., authorization denial, input rejection, rate-limit enforcement)
