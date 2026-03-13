---
name: plan
description: Produce a revision plan for the surrounding text based on the prompt.
---

# Plan

Analyze the surrounding text and return a structured revision plan.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@plan: ...>` tag appears.
- **Prompt**: the revision goal (e.g., "how to make this better", "restructure for clarity").

## Workflow

1. Read the surrounding text. Optionally read the full file for broader context.
2. Analyze the text against the revision goal.
3. Write the result back to the file: insert your plan as a `<@output: ...>` tag immediately after the `<@plan>` tag. The format must be exactly: `<@output: your plan here>` do NOT use a closing `</@output>` tag, and do NOT place content outside the tag. Keep the original content and the `<@plan>` tag intact.

## Scope Modifiers

The surrounding text may contain scope modifier tags. When producing a plan, take these into account:

- **Protect `<< >>`**: Text inside `<<` and `>>` is locked. Do not suggest changes to protected regions in your plan.
- **Field `(( ))`**: Only text inside `((` and `))` is editable. Limit your suggestions to field regions only.

Preserve all scope modifier delimiters as-is in the output — they will be consumed later by `<@resolve>`.

## Rules

- List specific, actionable suggestions.
- Keep the plan concise.
- Respect scope modifiers: do not plan edits to protected regions or outside field regions.
- The `<@plan>` tag and original content stay; only append `<@output>`.
