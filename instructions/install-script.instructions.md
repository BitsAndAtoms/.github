---
description: "Use when modifying install, update, or uninstall scripts. Covers idempotency rules, teardown order, and desktop/service entry conventions."
applyTo: "scripts/**"
---
# Install Script — Conventions Reference

## General Rules

- Scripts must be **idempotent** — running them twice must produce the same result as running once.
- Always check if a resource exists before creating it.
- Print a status line for every major step: `echo "[install] Installing dependencies..."`

## Install Order

1. Check prerequisites (OS version, required tools)
2. Create directories
3. Install dependencies
4. Write config/secrets
5. Register desktop entry or service (if applicable)
6. Print success message with next steps

## Uninstall / Teardown Order

Always tear down in reverse dependency order:

1. Remove desktop entry or service
2. Remove config/secrets
3. Remove dependencies / virtualenv
4. Remove directories

## Error Handling

- Exit immediately on error: `set -e` at the top of every script.
- Print a clear message before exiting: `echo "[error] <reason>" && exit 1`
- Never silently continue past a failed step.

## What Not to Do

- Do not use `sudo` unless absolutely required — prefer user-local installs.
- Do not hardcode absolute paths — use `$HOME` or paths relative to the script location.
- Do not delete files without first confirming they exist.
