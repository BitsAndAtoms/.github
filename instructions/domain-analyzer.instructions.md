---
description: "Use when adding a new analysis method to the core domain module. Covers method signature contract, available helpers, and output shape."
applyTo: "**"
---
# Domain Analyzer — Method Contract Reference

## Method Signature Contract

All public analysis methods follow a strict contract:

```python
def analyze_<topic>(self, items: List[Dict], **kwargs) -> List[Dict]:
    """
    Returns a list of result dicts. Never returns None.
    Empty list = no results found.
    """
```

## Output Dict Shape

Every result dict must contain at minimum:

| Key | Type | Description |
|-----|------|-------------|
| `id` | `str` | Unique identifier of the source item |
| `type` | `str` | Category of the result |
| `details` | `dict` | Analysis-specific payload |

## Adding a New Analysis Method

1. Follow the method signature contract above.
2. Return an empty list `[]` on no results — never `None`.
3. Keep filtering logic inside the method — do not pre-filter in the caller.
4. Add a corresponding integration test in `integration_tests/`.

## What Not to Do

- Do not raise exceptions for empty input — return `[]`.
- Do not mutate the input `items` list.
- Do not call external APIs or I/O inside analysis methods.
