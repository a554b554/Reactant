---
name: edit
description: Generic text edit — modify surrounding content according to the prompt.
---

# Edit

Apply the edit described in the prompt to the surrounding text.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@edit: ...>` tag appears.
- **Prompt**: what to change (e.g., "make it more concise", "rewrite in formal tone").

## Workflow

1. Read the surrounding text. Read the full file if you need broader context.
2. Apply the edit as described in the prompt.
3. Write the result back to the file: replace the surrounding text (including the `<@edit: ...>` tag) with the edited text.

## Scope Modifiers

The surrounding text may contain scope modifier tags that control which regions you can edit.

- **Protect `<< >>`**: Text inside `<<` and `>>` must NOT be modified. Preserve it exactly as-is. Remove the `<<` `>>` delimiters in the output, but keep the content unchanged.
- **Field `(( ))`**: ONLY the text inside `((` and `))` may be modified. The surrounding text is context only — do not change it. Remove the `((` `))` delimiters in the output.

If neither modifier is present, the entire surrounding text is editable (default behavior).

## Rules

- Only change what the prompt asks for.
- Preserve meaning unless instructed otherwise.
- Respect scope modifiers: never edit protected regions, only edit field regions when present.
- The `<@edit: ...>` tag and all scope modifier delimiters (`<< >>`, `(( ))`) must be removed in the output.

