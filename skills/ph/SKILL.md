---
name: ph
description: Fill a placeholder — infer the best content from surrounding context.
---

# Placeholder

Generate content that fits naturally at the tag position.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@ph>` tag appears.
- **Prompt** (optional): hint about what to fill (e.g., "a transition sentence"). Default: infer from context.

## Workflow

1. Read the surrounding text before and after the tag. Read the full file if you need broader context.
2. Generate content that fits naturally at that position in tone, style, and meaning.
3. Write the result back to the file: replace the `<@ph>` tag with the generated text.

## Scope Modifiers

The surrounding text may contain scope modifier tags. Since `<@ph>` replaces only the tag itself (not surrounding content), scope modifiers primarily provide context:

- **Protect `<< >>`**: Protected text is context only — do not modify it. Remove the `<<` `>>` delimiters in the output.
- **Field `(( ))`**: If the `<@ph>` tag appears inside a field, generate content within that field only. Remove the `((` `))` delimiters in the output.

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources (file paths, section titles, etc.) that you should look up.

- Read the referenced resource and use it to inform the generated content. E.g., `<@ph: a summary sentence based on ``results.md``>` — read `results.md` to generate an appropriate placeholder.
- Resolve references relative to the source file's directory unless an absolute path is given.
- Remove the `` `` delimiters from the output.

## Rules

- Output should read as if it was always part of the text.
- Keep it concise and contextually appropriate.
- Respect scope modifiers if present.
- Resolve all context references (`` `` ``) in the prompt before generating content.
- The `<@ph>` tag and all modifier delimiters (`<< >>`, `(( ))`, `` `` ``) must be removed in the output.
