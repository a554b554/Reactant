---
name: plan
description: Produce a revision plan for the surrounding text based on the prompt. Supports multi-turn conversation chains.
---

# Plan

Analyze the surrounding text and return a structured revision plan. Supports multi-turn conversation where the user can refine the plan iteratively before resolving.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@plan: ...>` tag appears, including any prior `<@plan>`/`<@output>` pairs in the chain.
- **Prompt**: the revision goal (e.g., "how to make this better", "restructure for clarity").

## Workflow

1. Read the surrounding text. Optionally read the full file for broader context.
2. Check for prior `<@plan>`/`<@output>` pairs in the tag chain. If they exist, treat them as conversation history.
3. Analyze the text against the revision goal, incorporating any conversation history.
4. Write the result back to the file: insert your plan as a `<@output: ...>` tag immediately after the **current** `<@plan>` tag. The format must be exactly: `<@output: your plan here>` do NOT use a closing `</@output>` tag, and do NOT place content outside the tag. Keep the original content and all `<@plan>`/`<@output>` tags intact.

## Multi-Turn Conversation

After receiving an `<@output>`, the user may:

1. **Edit the `<@output>` directly** — modify the plan text, then use `<@resolve>` to apply it.
2. **Add another `<@plan:>` tag** after the `<@output>` — this continues the conversation. The new `<@plan>` prompt is treated as a follow-up that can refine, extend, or redirect the previous plan.

Each `<@plan>`/`<@output>` pair forms one turn. When producing a new `<@output>`, read the **full chain** of prior turns as conversation history. All prior pairs stay intact — only append a new `<@output>` after the latest `<@plan>`. The output plan should always be included in the <@output> tag, e.g., <@output: your plan here>. The conversation chain is terminated by `<@resolve>`, which consumes the entire chain.

## Scope Modifiers

The surrounding text may contain scope modifier tags. When producing a plan, take these into account:

- **Protect `<< >>`**: Text inside `<<` and `>>` is locked. Do not suggest changes to protected regions in your plan.
- **Field `(( ))`**: Only text inside `((` and `))` is editable. Limit your suggestions to field regions only.

Preserve all scope modifier delimiters as-is in the output — they will be consumed later by `<@resolve>`.

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources (file paths, section titles, etc.) that you should look up.

- **As input**: Read the referenced resource and use it as additional context when producing the plan. E.g., `<@plan: how to align this with ``intro.md``>` — read `intro.md` and incorporate its content into your analysis.
- **As output**: If the prompt specifies an output destination via a context reference, write the plan to that file instead of inline. Then append `<@output: the plan is written in ``path``>` after the `<@plan>` tag so `<@resolve>` knows where to find it.
- Resolve references relative to the source file's directory unless an absolute path is given.
- Preserve `` `` delimiters in the output (they will be consumed later by `<@resolve>`).
