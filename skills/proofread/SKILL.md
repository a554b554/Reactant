---
name: proofread
description: Proofread surrounding text for grammar and spelling errors.
---

# Proofread

Fix grammar, spelling, and punctuation in the surrounding text.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@proofread>` tag appears.
- **Prompt** (optional): narrow the scope (e.g., "only fix punctuation"). Default: fix all grammar issues.

## Workflow

1. Read the surrounding text. Read the full file if you need broader context.
2. Fix grammar, spelling, and punctuation errors.
3. Write the result back to the file: replace the surrounding text (including the `<@proofread>` tag) with the corrected text.

## Scope Modifiers

The surrounding text may contain scope modifier tags that control which regions you can edit.

- **Protect `<< >>`**: Text inside `<<` and `>>` must NOT be modified, even if it contains errors. Remove the `<<` `>>` delimiters in the output, but keep the content unchanged.
- **Field `(( ))`**: ONLY the text inside `((` and `))` may be modified. The surrounding text is context only — do not change it. Remove the `((` `))` delimiters in the output.

If neither modifier is present, the entire surrounding text is editable (default behavior).

## Rules

- Do not change meaning, tone, or style.
- Only fix errors; do not rewrite.
- Respect scope modifiers: never edit protected regions, only edit field regions when present.
- The `<@proofread>` tag and all scope modifier delimiters (`<< >>`, `(( ))`) must be removed in the output.
