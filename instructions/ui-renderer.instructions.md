---
description: "Use when adding a new renderer method, modifying existing output tables or panels, or rendering a new data type."
applyTo: "**"
---
# UI Renderer — Conventions Reference

## Output Conventions

- All output goes through a single console/output object. Never use `print()` directly.
- Use a consistent table library and style across all renderers.

## Table Conventions

| Property | Convention |
|----------|------------|
| Box style (primary tables) | Define and document your chosen style here |
| Box style (compact sub-tables) | Define here |
| Header style | Define here (e.g. bold, coloured) |

## Column Header Format

Use consistent separators for compound column names:
- `"Source / Target"` — space-slash-space for hierarchical names
- `"Start / End"` — space-slash-space for ranges

## Date and Time Formatting

Never format dates inline. Define and use shared helpers:

```python
format_display_date(value)     # → "Apr 27 (Mon)"
format_display_datetime(value) # → "Apr 27 (Mon) 14:30"
```

Document your helpers here once defined.

## Adding a New Renderer

1. Use the table/panel conventions above — do not introduce a new box style.
2. Use the shared date formatting helpers — never inline `strftime`.
3. Route all output through the central console object.
4. Add a renderer smoke test in `integration_tests/`.
