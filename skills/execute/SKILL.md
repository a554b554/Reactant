---
name: execute
description: Router — scan a file for <@skill: prompt> tags and dispatch each to the matching skill subagent.
---

# Execute

Route all `<@skill-name: ...>` annotations in a file to their matching skill subagents.

## Usage

```
/execute <filename>
```

## Grammar

```
<@skill-name: prompt>
```

Tags are written inline, directly in the original text. `<@output: ...>` is a special data tag — never dispatched, only consumed by other skills.

### Modifiers

Prompts may contain **context references** (`` ``path`` ``) that point the skill agent to external resources (files, sections, etc.). The execute router does not interpret these — they are passed through to the subagent as part of the prompt.

## Skill registry

| Tag | Skill | Description |
|---|---|---|
| `<@edit: ...>` | `edit` | Generic edit per prompt. |
| `<@proofread>` | `proofread` | Fix grammar, spelling, punctuation. |
| `<@plan: ...>` | `plan` | Produce a revision plan. |
| `<@ph>` / `<@ph: ...>` | `ph` | Fill placeholder from context. |
| `<@resolve>` | `resolve` | Apply preceding plan/output chain. |
| `<@figure: ...>` | `figure` | Generate a figure. *(Placeholder — needs Gemini API.)* |

To add a new skill: create `skills/<name>/SKILL.md` and add a row to this table.

## Subagent contract

Every subagent receives:

1. **File path** — the full path of the file being processed.
2. **Surrounding text** — the paragraph or block where the tag appears (including any chained tags like `<@output: ...>`).
3. **Prompt** — the text inside the tag after the skill name.

Each subagent is responsible for applying its own changes directly to the file. The execute skill does not write back results — it only dispatches.

## Workflow

1. **Clear context** — disregard all prior conversation context. Start fresh with only this skill's instructions.
2. **Read** the target file.
2. **Scan** for all `<@...>` tags, top to bottom.
3. **For each tag**:
   - Skip `<@output: ...>` — it is data, not a dispatch target.
   - Look up the skill name in the registry above.
   - If unknown, skip and warn the user.
   - Spawn a subagent with the matching skill. Pass the file path, surrounding text, and prompt.
   - The subagent applies changes directly to the file.
   - Re-read the file before processing the next tag (the file may have changed).
4. **Report** a brief summary of how many tags were processed.

## Rules

- Process tags sequentially, top to bottom.
- Skip `<@output: ...>` tags.
- Re-read the file after each subagent completes.
- Unknown skill: skip and warn.
- Do not stop until all tags have been processed.
