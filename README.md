# EAML: Extensible Agentic Markup Language

**Inline annotations for agentic document editing.**

EAML lets you write tags like `<@proofread>`, `<@edit: ...>`, or `<@plan: ...>` directly inside a document. An agent scans the file, routes each tag to the matching skill, and writes the result back in place.

## Example

Before:

```md
As of last week, we have complted the migration of our internal documantation pipeline from the legacy system to the new platfrom. The migratoin was completed ahed of schedual.
<@proofread>
```

Run (in your AI Agent like Claude Code):

```text
/execute memo.md
```

After:

```md
As of last week, we have completed the migration of our internal documentation pipeline from the legacy system to the new platform. The migration was completed ahead of schedule.
```

The same pattern works for targeted rewrites, placeholder filling, planning, plan resolution, and figure generation.

## Quick Start

> Prerequisite: an AI agent that supports skills, such as Codex, Claude Code, or Cursor with an equivalent skill workflow.

1. Copy the [`skills/`](./skills) directory into your agent's skills location. (e.g., `.claude/` in Claude Code)
2. Add EAML tags directly inside a document.
3. Run:

```text
/execute your-file.md
```

The executor scans the file top-to-bottom, dispatches each tag to the matching skill, and re-reads the file after every change.

## Core Syntax

### Skill Tags

```text
<@skill-name: prompt>
```

- `skill-name` selects the skill to run.
- `prompt` is optional for some skills, such as `proofread`, `ph`, and `figure`.

Examples:

```md
<@proofread>
<@edit: make this shorter>
<@plan: restructure this into two paragraphs>
<@ph: a transition sentence leading into our contribution>
<@figure: a system architecture diagram>
```

### Special Data Tag

```text
<@output: ...>
```

`<@output: ...>` is not dispatched as a skill. It stores data for downstream steps, most notably the `plan -> resolve` workflow.

### Scope Modifiers

EAML includes two inline scope controls for content-editing skills.

`<<protect>>`

- Protected text must remain unchanged.
- The delimiters are removed during output.

`((field))`

- Only the text inside the field may be changed.
- Everything outside the field is read-only context.
- The delimiters are removed during output.

Example:

```md
The system was designed to help users complete complex tasks. It received ((positve feedbak from all partcipants.))
<@proofread>
```

After execution:

```md
The system was designed to help users complete complex tasks. It received positive feedback from all participants.
```

### Context References

Prompts may contain context references wrapped in double backticks:

```text
``path/or/resource``
```

Skills use these references to read additional context or, for some skills, write output to a destination file.

Examples:

```md
<@edit: revise to match the terminology in ``glossary.md``>
<@plan: align this section with ``intro.md``>
<@figure: create a pipeline diagram and save it to ``figures/pipeline.png``>
```

## Built-In Skills

| Skill | Tag form | Purpose |
|---|---|---|
| `execute` | `/execute file.md` | Router that scans a file and dispatches all EAML tags. |
| `edit` | `<@edit: ...>` | Rewrites surrounding text according to the prompt. |
| `proofread` | `<@proofread>` / `<@proofread: ...>` | Fixes grammar, spelling, and punctuation without changing meaning. |
| `plan` | `<@plan: ...>` | Appends a revision plan as an `<@output: ...>` tag. |
| `ph` | `<@ph>` / `<@ph: ...>` | Fills a placeholder from local context. |
| `resolve` | `<@resolve>` | Applies a preceding `<@output: ...>` plan and removes the full tag chain. |
| `figure` | `<@figure>` / `<@figure: ...>` | Generates an image and replaces the tag with an image reference. |

## Execution Model

The executor skill, defined in [`skills/execute/SKILL.md`](./skills/execute/SKILL.md), works as follows:

1. Read the target file.
2. Scan for `<@...>` tags from top to bottom.
3. Skip `<@output: ...>` tags because they are data, not dispatch targets.
4. For each other tag, look up the matching skill in the registry.
5. Invoke that skill with the file path, surrounding text, and prompt.
6. Re-read the file before moving to the next tag.
7. Report a brief summary.

Sequential execution is intentional. Earlier edits may shift later text spans, so parallel writes would be unsafe.

## Plan and Resolve

This repo supports a two-pass revision workflow.

Pass 1:

```md
The annotation grammar is intentionally minimal, but this simplicity comes at a cost.
<@plan: suggest how to restructure this into a strength-then-limitation framing>
```

After running `/execute`, the plan skill appends:

```md
The annotation grammar is intentionally minimal, but this simplicity comes at a cost.
<@plan: suggest how to restructure this into a strength-then-limitation framing>
<@output: ...revision plan here...>
```

Pass 2:

```md
The annotation grammar is intentionally minimal, but this simplicity comes at a cost.
<@plan: suggest how to restructure this into a strength-then-limitation framing>
<@output: ...revision plan here...>
<@resolve>
```

`resolve` applies the plan to the original content, respects scope modifiers, and removes the entire tag chain.

## Figure Generation

The `figure` skill uses the Gemini image API and expects `GEMINI_API_KEY` to be set. It writes the generated image next to the source document unless the prompt supplies an explicit output path via a context reference.

For Markdown files, `<@figure>` is replaced with:

```md
![description](generated_figure.png)
```

For LaTeX files, it is replaced with:

```tex
\includegraphics{generated_figure.png}
```

## Examples

The [`examples/`](./examples) directory is organized as a compact test suite for the language.


## Adding a New Skill

1. Create `skills/<name>/SKILL.md`.
2. Add YAML frontmatter with at least `name` and `description`.
3. Define the skill's expected inputs, workflow, and rules.
4. Register the new tag in [`skills/execute/SKILL.md`](./skills/execute/SKILL.md).

That is enough for the executor to route `<@your-skill: ...>` tags to the new skill.
