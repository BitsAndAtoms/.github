---
description: "Use when adding or modifying the central configuration object for this project. Covers required field shapes, optional fields, and validation rules."
applyTo: "**"
---
# Configuration — Conventions Reference

## Configuration Object Shape

Document the shape of your central config object here. Example:

```python
CONFIG = {
    "mode": str,           # required — controls application behaviour
    "feature_flags": dict, # optional — defaults to {}
    "roles": list,         # required — list of active roles
}
```

## Required Fields

List every key that must be present and its expected type/values:

| Key | Type | Allowed Values | Notes |
|-----|------|----------------|-------|
| `mode` | `str` | `"default"`, `"verbose"` | Add your modes here |

## Optional Fields

List keys that are optional and their defaults:

| Key | Default | Notes |
|-----|---------|-------|
| `debug` | `False` | Enable extra logging |

## Adding a New Key

1. Define the key and its type in the config object.
2. Document it in the tables above.
3. Add a validation check if the value is constrained.
4. Update any tests that assert on config shape.

## Policy Rules

- Never read config keys directly from environment variables inside business logic — resolve them at the config layer only.
- Config must be fully constructed before any module imports it.
- Do not mutate config at runtime except through a designated setter.
