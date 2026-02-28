---
name: execute-light
description: Lightweight inline execution — scan for angle-bracket tags, treat contents as prompts, process in place, and remove the tags.
---

# Execute-Light Skill

A lightweight alternative to the full `execute` skill. Scans a document for angle-bracket annotated tags (`<prompt>`), treats the text inside as a prompt, processes the surrounding content according to that prompt, and replaces the tag with the result.

## Usage

```
/execute-light <filename>
```

## Workflow

1. **Read** the target file.
2. **Scan** for all `<prompt>` tags in the document. Any text enclosed in `<` and `>` is treated as a prompt — e.g., `<rewrite this paragraph in formal tone>`.
3. **Process each tag in order** (top to bottom):
   - Extract the prompt from inside the `<` and `>` delimiters.
   - Use the prompt to determine what modification to make at that location in the document. The prompt describes how to transform, generate, or edit the surrounding text.
   - Apply the modification — remove the entire `<prompt>` tag (including the angle brackets) and apply the generated content in its place.
4. **Report** a brief summary of how many tags were processed.

## Rules

- Process tags sequentially from top to bottom so that earlier results can inform later ones.
- The prompt inside `< >` may reference surrounding context in the document — use nearby text as context when processing.
- After processing, the `<` and `>` delimiters and the prompt text must be fully removed; only the output remains.
- If a tag contains an empty prompt (`<>`), skip it and remove the empty tag.
