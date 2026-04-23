---
name: latexify
description: Turn textual annotations of mathematical equations and notations into LaTeX code.
---

# Latexify

Turn textual annotations of mathematical equations and notations into LaTeX code.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@latexify>` tag appears.
- **Prompt** (optional): narrow the scope (e.g., "only convert to LaTeX"). Default: convert all equations and notations to LaTeX.

## Workflow

1. Read the surrounding text. Read the full file if you need broader context.
2. Convert the textual annotations of mathematical equations and notations into LaTeX code.
3. Write the result back to the file: replace the surrounding text (including the `<@latexify>` tag) with the LaTeX code. Only touch the text concerning mathematical equations and notations.

## Scope Modifiers

The surrounding text may contain scope modifier tags that control which regions you can edit.

- **Protect `<< >>`**: Text inside `<<` and `>>` must NOT be modified, even if it contains errors. Remove the `<<` `>>` delimiters in the output, but keep the content unchanged.
- **Field `(( ))`**: ONLY the text inside `((` and `))` may be modified. The surrounding text is context only — do not change it. Remove the `((` `))` delimiters in the output.

If neither modifier is present, the entire surrounding text is editable (default behavior).

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources (file paths, section titles, etc.) that you should look up.

- **As input**: Read the referenced resource for additional context (e.g., to understand domain-specific terminology that should not be flagged). E.g., `<@latexify: use terminology from ``glossary.md``>`.
- Resolve references relative to the source file's directory unless an absolute path is given.
- Remove the `` `` delimiters from the output.

## Rules

- Do not change meaning, tone, or style.
- Only convert to LaTeX; do not rewrite.
- Respect scope modifiers: never edit protected regions, only edit field regions when present.
- Resolve all context references (`` `` ``) in the prompt before converting to LaTeX.
- The `<@latexify>` tag and all modifier delimiters (`<< >>`, `(( ))`, `` `` ``) must be removed in the output.
