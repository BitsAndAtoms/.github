---
description: "Use when writing or editing prompts passed to the LLM client. Covers required guards, output format instructions, and token hygiene."
applyTo: "**"
---
# LLM Prompts — Construction Reference

## Required Boilerplate

Every system prompt must include:

1. **Role declaration** — what the model is acting as
2. **Scope guard** — limit to only the data provided, no hallucination
3. **Output format** — explicit instruction on response structure

Example skeleton:

```
You are a <role> assistant. Only use the information provided below.
Do not invent or assume data not present in the input.

Respond in the following format:
<define your format here>
```

## Input Filtering

Before building the prompt:
- Filter to only the relevant time window or scope
- Collapse repeated/recurring items to one representative entry
- Strip fields the model does not need (e.g. raw IDs, internal metadata)

## Output Format

Specify the exact output format the model must return. If JSON:
- Define the schema explicitly in the prompt
- Validate the response against the schema before using it

## Token Hygiene

- Keep system prompts under 500 tokens where possible
- Do not repeat static instructions across multiple turns
- Summarise long input lists rather than passing them verbatim
