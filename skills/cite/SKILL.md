---
name: cite
description: Find and insert citations to support a claim in the surrounding text.
---

# Cite

Find relevant references to support a statement or claim, and insert a citation.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@cite: ...>` tag appears.
- **Prompt**: where/how to find references. May include a source hint, an explicit reference, or a search instruction.

## Workflow

1. Read the surrounding text to understand the claim or statement that needs a citation.
2. Determine the citation source from the prompt: user may specify a ``.bib`` or ``.md`` file, an ``arxiv`` search, a web search, or another file containing references. 
3. Determine the output format: look at the existing citations and file format to determine the output, e.g., in LaTeX it should be \cite{key}, and in Markdown it should be [number] or [@key]. Check out the existing citation style in the document and follow it.

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources.

- **As source** (most common): The reference points to a citation source — a `.bib` file, `arxiv`, a URL, or another file containing references. E.g., `<@cite: ``refs.bib``>` — search `refs.bib` for a matching entry.
- Resolve file references relative to the source file's directory unless an absolute path is given.
- Remove the `` `` delimiters from the output.


## Rules

- Only insert citations — do not modify the surrounding text.
- The `<@cite: ...>` tag and all `` `` delimiters must be removed in the output.
- When searching online or on arXiv, prefer peer-reviewed or well-cited sources.
- If no credible source can be found, replace the tag with a placeholder comment (e.g., `[citation needed]`) and warn the user.
- In latex doc, when cite a new paper found online, add it to the main `.bib` file. When adding to a `.bib` file, do not duplicate existing entries.
- Match the citation style already used in the document.
