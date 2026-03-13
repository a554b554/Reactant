# EAML — Extensible Agentic Markup Language

**Inline annotations that drive agentic writing workflows.**

EAML lets you write `<@skill: prompt>` tags directly in your text. An AI agent scans the file, dispatches each tag to the matching skill, and applies the result in place — no special file format, no copy-paste loops.

```
              your document
              ┌──────────────────────────────────────────────┐
              │ ...regular text...                           │
              │                                              │
              │ This paragraf needs fixing. <@proofread>     │
              │                                              │
              │ ...more text...                              │
              └──────────────────────────────────────────────┘
                                  │
                        /execute file.md
                                  │
                                  ▼
              ┌──────────────────────────────────────────────┐
              │ ...regular text...                           │
              │                                              │
              │ This paragraph needs fixing.                 │
              │                                              │
              │ ...more text...                              │
              └──────────────────────────────────────────────┘
```

---

## Quick start

> **Prerequisites:** An AI coding agent that supports [Agent Skills](https://agentskills.io/home) (e.g., Claude Code, Codex, Cursor).

**1. Install the skills**

Copy the `skills/` folder into your agent's skills directory.

**2. Annotate your text**

Write `<@skill: prompt>` tags directly in any file where you want AI help. No special file extension or YAML header needed.

**3. Run it**

```
/execute myfile.md
```

The agent processes every `<@...>` tag top-to-bottom and writes results back into the same file.

---

## Grammar

```
<@skill-name: prompt>
```

- `skill-name` — which skill to run.
- `prompt` — additional instructions for the skill (optional for some skills like `proofread`).

Tags are written inline, directly in the original text. After processing, the tag is removed and replaced with the skill's output (or appended, depending on the skill).

There is one special tag: `<@output: ...>` holds a skill's result for later use (e.g., a plan to be resolved). It is not dispatched — it serves as data for downstream skills.

---

## Built-in skills

| Skill | Example | What it does |
|---|---|---|
| `edit` | `<@edit: make this more concise>` | Generic edit — modifies text according to the prompt. |
| `proofread` | `<@proofread>` | Fixes grammar, spelling, and punctuation. |
| `plan` | `<@plan: how to improve this>` | Appends a revision plan as `<@output: ...>`. |
| `ph` | `<@ph>` | Fills a placeholder by inferring from context. |
| `resolve` | `<@resolve>` | Applies a preceding plan to the content, removes all tags. |
| `figure` | `<@figure: a diagram of the architecture>` | Generates a figure. *(Placeholder — requires Gemini API.)* |

---

## Scope modifiers

Two inline delimiters give fine-grained control over which text a skill can touch.

### Protect `<< >>`

Text inside `<< >>` is locked — skills must not modify it, even if it contains errors.

```
Cooking supports independence and health,
<<such as sharp knives or hot pans.>>
<@edit: make this shorter>
```

The edit shortens the surrounding text but leaves "such as sharp knives or hot pans." untouched.

### Field `(( ))`

Text inside `(( ))` is the *only* part the skill should modify — everything else is read-only context.

```
The system received ((positve feedbak from all partcipants.))
<@proofread>
```

Only errors inside the field are corrected; the surrounding sentence stays as-is.

Both modifiers work with all content-editing skills (`edit`, `proofread`, `ph`, `plan`, `resolve`). After processing, the delimiter characters are removed.

---

## Plan → Resolve workflow

A two-pass workflow for structured revisions:

**Pass 1 — Plan.** Add a plan tag and run `/execute`:
```
Your content here <@plan: how to make this easier to understand>
```

The plan skill appends its result:
```
Your content here <@plan: how to make this easier to understand><@output: simplify X and restructure Y>
```

**Pass 2 — Resolve.** Review the output (edit it if you like), add `<@resolve>`, and run `/execute` again:
```
Your content here <@plan: how to make this easier to understand><@output: simplify X and restructure Y><@resolve>
```

The resolve skill applies the plan and removes all tags:
```
Your revised content here
```

---

## How `/execute` works

1. **Read** the target file.
2. **Scan** for all `<@...>` tags, top to bottom.
3. **For each tag**:
   - Skip `<@output: ...>` — it is data, not a dispatch target.
   - **Dispatch** to the matching skill subagent with the file path, surrounding text, and prompt.
   - The subagent applies its changes directly to the file.
   - Re-read the file before processing the next tag.
4. **Report** a summary of what was processed.

Each skill decides how to write its output (replace the tag, append after it, etc.). See individual skill docs for details.

---

## Writing your own skill

1. Create `skills/<skillname>/SKILL.md` with YAML frontmatter (`name`, `description`).
2. Define the skill's input (context + prompt) and workflow.
3. Add a row to the skill registry in `skills/execute/SKILL.md`.
4. Keep it short and agentic — the skill is executed by a subagent, not by code.

---

## Examples

The `examples/` directory contains test files for each skill and feature:

| File | Coverage |
|---|---|
| `00_baseline.md` | No annotations — verifies no unintended changes. |
| `01_edit.md` | Basic, tonal, and targeted edits. |
| `02_proofread.md` | Spelling/grammar fixes, scoped proofreading. |
| `03_plan.md` | Plan generation with structural and improvement goals. |
| `04_ph.md` | Placeholder filling from context, with and without hints. |
| `05_resolve.md` | Plan → resolve chains, including user-edited plans. |
| `06_scope_protect.md` | Protect modifier `<< >>` with edit and proofread. |
| `07_scope_field.md` | Field modifier `(( ))` with edit and proofread. |
| `08–12_holistic_*.md` | Multi-skill documents: academic, memo, creative, technical, mixed. |

---

## Repo layout

```
EAML/
├── README.md
├── examples/
└── skills/
    ├── execute/
    ├── edit/
    ├── proofread/
    ├── plan/
    ├── ph/
    ├── resolve/
    └── figure/
```
