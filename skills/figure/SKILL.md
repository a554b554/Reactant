---
name: figure
description: Generate a figure using an image generation API based on the prompt or surrounding context. (Placeholder — requires Gemini API integration.)
---

# Figure

Generate a figure that matches the prompt or surrounding context.

**Status: Placeholder.** This skill requires Gemini API integration and is not yet functional.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@figure: ...>` tag appears.
- **Prompt** (optional): description of the figure to generate. Default: infer from context.

## Workflow

1. Read the surrounding text and prompt.
2. Call an image generation API (e.g., Gemini) to produce the figure.
3. Save the image and write the result back to the file: replace the `<@figure: ...>` tag with the image reference.

## TODO

- Integrate Gemini API for image generation.
- Determine output format (inline image link, file path, etc.).
