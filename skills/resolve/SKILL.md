---
name: resolve
description: Apply a preceding plan/output chain to the content, then remove all tags.
---

# Resolve

Finalize a revision by applying the plan from preceding tags to the original content.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the text before the tag chain, plus the full chain of tags (e.g., `<@plan: ...><@output: ...><@resolve>` or a multi-turn chain like `<@plan: ...><@output: ...><@plan: ...><@output: ...><@resolve>`).
- **Prompt**: typically empty for resolve.

The tag chain may contain a single `<@plan>`/`<@output>` pair, or a multi-turn conversation consisting of multiple `<@plan>`/`<@output>` pairs. Each pair represents one turn of iterative refinement.

## Workflow

1. Read the original content (the text before the tag chain).
2. Read the **full conversation history** — all `<@plan>`/`<@output>` pairs in sequence. The final `<@output>` represents the most refined plan, but earlier turns provide context for intent and constraints.
3. Apply the plan to the original content. When multiple turns exist, the last `<@output>` takes precedence, but use earlier turns to resolve ambiguity about what the user intended.
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
