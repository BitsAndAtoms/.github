---
description: "Use when writing new integration tests, extending test files, or adding coverage for a new feature. Covers file organisation, fixture rules, helper functions, and output patterns."
applyTo: "integration_tests/**"
---
# Integration Tests — Conventions Reference

## File Organisation

- One test file per feature area: `test_<feature>.py`
- Place shared fixtures in `conftest.py` — not in individual test files
- Place shared helper functions in `helpers.py`

## Fixture Rules

- Fixtures that build sample data belong in `conftest.py` with appropriate scope.
- Use `scope="function"` (default) unless the fixture is provably read-only and expensive.
- Never write to real external services in integration tests — use stubs or recorded fixtures.

## Test Function Naming

```
test_<feature>_<scenario>_<expected_outcome>
```

Examples:
- `test_conflict_detection_overlapping_events_returns_conflict`
- `test_sleep_analysis_no_events_returns_empty`

## Helper Functions

- Place any assertion helpers or data builders in `helpers.py`.
- Import them explicitly — do not use star imports.

## Output Capture

- Do not assert on printed output unless the test is specifically for the UI layer.
- For UI tests, capture output by injecting a mock console object — do not redirect `stdout`.

## What Not to Do

- Do not test implementation details — test behaviour and outputs only.
- Do not share mutable state between tests.
- Do not create test-specific fixtures inside test functions — put them in `conftest.py`.
