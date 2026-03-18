---
name: figure
description: Generate a figure using Google Gemini Nano Banana 2 API based on the prompt or surrounding context.
---

# Figure

Generate a figure from the prompt and surrounding context.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@figure ...>` tag appears.
- **Prompt** (optional): description of the figure. If absent, infer from surrounding text.

## Workflow

1. Read the surrounding text and prompt.
2. Synthesize a detailed image-generation prompt from user intention and context.
3. Generate the image using the API call below.
4. Save the image in the same directory as the source file. Name it based on the prompt (e.g., `figure_system_architecture.png`).
5. Replace the `<@figure>` tag with a format-appropriate image reference:
   - Markdown (`.md`): `![description](filename.png)`
   - LaTeX (`.tex`): `\includegraphics{filename.png}`

## API call

Requires `GEMINI_API_KEY` environment variable.

Run the entire generate-and-save flow in a **single** Bash command to avoid redundant API calls. Use single-quoted JSON with the prompt inserted via variable substitution to avoid escaping issues.

```bash
source ~/.bashrc && RESPONSE=$(curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "parts": [{"text": "Generate an image: YOUR_PROMPT_HERE"}]
    }],
    "generationConfig": {
      "responseModalities": ["TEXT", "IMAGE"]
    }
  }') && echo "$RESPONSE" | python -c "
import json, sys, base64
r = json.load(sys.stdin)
if 'error' in r:
    print('API error: ' + r['error'].get('message', 'unknown')); sys.exit(1)
if 'candidates' not in r:
    print('No candidates in response'); sys.exit(1)
for part in r['candidates'][0]['content']['parts']:
    if 'inlineData' in part:
        data = base64.b64decode(part['inlineData']['data'])
        with open('OUTPUT_PATH.png', 'wb') as f:
            f.write(data)
        print(f'Saved {len(data)} bytes')
        break
"
```

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources.

- **As input**: Read the referenced resource to inform the figure generation. E.g., `<@figure: a diagram based on the architecture in ``system.md``>` — read `system.md` for context.
- **As output**: If the prompt specifies an output path via a context reference, save the image there instead of auto-naming. E.g., `<@figure: pipeline diagram, save to ``figures/pipeline.png``>` — save to `figures/pipeline.png`.
- Resolve references relative to the source file's directory unless an absolute path is given.
- Remove the `` `` delimiters from the output.

## Rules

- Run the API call and image save in a single Bash invocation. Do not split into separate calls.
- Keep the prompt in single-quoted JSON to avoid shell escaping issues. If the prompt contains single quotes, escape them.
- If `GEMINI_API_KEY` is not set, warn the user and skip.
- Use `python` (not `python3`) for base64 decoding.
- Sanitize the filename: lowercase, underscores, no spaces or special characters (unless an explicit output path is given via context reference).
- Resolve all context references (`` `` ``) in the prompt before generating.
