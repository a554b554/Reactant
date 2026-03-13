---
name: resolve
description: Apply a preceding plan/output chain to the content, then remove all tags.
---

# Resolve

Finalize a revision by applying the plan from preceding tags to the original content.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the text before the tag chain, plus the full chain of tags (e.g., `<@plan: ...><@output: ...><@resolve>`).
- **Prompt**: typically empty for resolve.

The `<@output: ...>` tag within the surrounding text contains the revision plan (possibly edited by the user).

## Workflow

1. Read the original content (the text before the tag chain).
2. Read the revision plan from the `<@output: ...>` tag.
3. Apply the plan to the original content.
4. Write the result back to the file: replace the entire block (original content + all tags) with the revised text.

## Scope Modifiers

The original content may contain scope modifier tags that constrain which regions the plan can be applied to.

- **Protect `<< >>`**: Text inside `<<` and `>>` must NOT be modified, even if the plan suggests changes to it. Remove the `<<` `>>` delimiters in the output, but keep the content unchanged.
- **Field `(( ))`**: ONLY the text inside `((` and `))` may be modified by the plan. The surrounding text is context only — do not change it. Remove the `((` `))` delimiters in the output.

If neither modifier is present, the entire original content is editable (default behavior).

## Rules

- Follow the plan faithfully.
- Respect scope modifiers: never edit protected regions, only edit field regions when present.
- All tags (`<@plan: ...>`, `<@output: ...>`, `<@resolve>`) and all scope modifier delimiters (`<< >>`, `(( ))`) must be removed; only clean text remains.
