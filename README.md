# Reactant: A Document-Native and Extensible Annotation Language for AI-Assisted Writing

A document-native, editor-agnostic annotation language that lets you embed AI instructions directly in your writing and execute them in place.

## Quick Example

```
test.md
Augmented reality (AR) is a technology that overlays digital information,
such as images, sounds, and text, onto the physical world in real time.
It has been widely explored in domains including education, healthcare,
manufacturing, and entertainment, demonstrating significant potential
to enhance user experience and task performance across a variety of contexts.
<@edit: make this shorter>
```

After running ``/execute test.md`` in your AI Agent, the annotation is consumed and the paragraph is edited in place:

```
test.md
Augmented reality (AR) overlays digital content onto the real world in
real time and has shown strong potential to improve user experience and
task performance across fields such as education, healthcare,
manufacturing, and entertainment.
```

## Quick Start

0. **Claude Code users** can install Reactant as a plugin. In your Claude Code terminal:
   ```
   /plugin marketplace add a554b554/Reactant
   /plugin install reactant@reactant
   ```
   Skip to step 2 after installation.

1. Copy the `skills/` folder into your AI agent's configuration directory:
   - Claude Code: `.claude/skills/`
   - Codex: `.agents/skills/`
   - Or the equivalent directory for your agent.

2. Open any text file and add Reactant annotations inline.

3. In your AI agent, run:
   ```
   /execute filename.md
   ```
   The agent will scan the file for all `<@...>` tags and process them in order.

Check out the `examples/` folder for ready-to-use sample files you can run with `/execute`.

## Core Syntax

An annotation follows this form:

```
<@skill-name: prompt>
```

- **Skill name**: a keyword that identifies the operation (e.g., `edit`, `proofread`, `cite`).
- **Prompt** (optional): natural-language instructions that elaborate the operation.

Annotations are placed after the content you want to modify. After execution, each tag is consumed and its output replaces the content in place.

### Scope Modifiers

**Protect `<< >>`** -- locks text so the agent cannot modify it:

```
Our system achieves <<92.4%>> accuracy on the benchmark.
<@edit: make more concise>
```

The protected figure stays exactly as written; surrounding text is edited freely.

**Field `(( ))`** -- restricts edits to only the enclosed text:

```
We found that ((participants from a local university who
were recruited via email and compensated with course
credit)) preferred our system.
<@edit: shorten>
```

Only the field content changes (e.g., to "university participants"); the framing sentence remains intact.

**Context Reference `` `` ``** -- points the agent to external resources:

```
<@edit: revise to align with ``intro.md``>
```

References can serve as input (read a file for context) or as output destinations (save artifacts to a path). Multiple references are supported in a single annotation.

### Plan and Resolve

For tasks that benefit from review before execution, use the plan-resolve workflow:

```
This section introduces our system and its benefits...
<@plan: how to make this more compelling>
```

The agent appends a structured plan as an `<@output>` tag without modifying the original text:

```
This section introduces our system and its benefits...
<@plan: how to make this more compelling>
<@output 1. Open with a concrete problem.
  2. Add a citation. 3. Forward reference.>
```

You can refine the plan with follow-up `<@plan>` tags, edit the `<@output>` directly, or append `<@resolve>` to apply the plan and remove all intermediate tags:

```
This section introduces our system and its benefits...
<@plan: how to make this more compelling>
<@output 1. Open with a concrete problem.
  2. Add a citation. 3. Forward reference.>
<@resolve>
```

After resolution, only the revised clean text remains.

### Figure and Plot

Reactant extends beyond text to multimodal content generation.

**Figure** -- generates an image via an image-generation API:

```
<@figure: a side-by-side comparison diagram of the three
  interaction types, save to ``figures/types.png``>
```

**Plot** -- generates and executes a Python visualization script grounded in real data:

```
<@plot: create a grouped bar chart of task completion
  time across three conditions, use ``data/results.csv``,
  save to ``figures/completion_time.png``>
```

The agent reads the data file, generates executable code (Matplotlib/Seaborn), runs it, and inserts the resulting figure reference.

## Core Skills

| Skill | Tag | Description |
|---|---|---|
| Edit | `<@edit>` | Modify surrounding content according to a natural-language instruction. |
| Proofread | `<@proofread>` | Fix grammar, spelling, and punctuation without changing meaning. |
| Placeholder | `<@ph>` | Infer and fill in missing content from surrounding context. |
| Cite | `<@cite>` | Find and insert citations based on surrounding context. |
| Plan | `<@plan>` | Produce a revision plan without modifying the original content; supports multi-turn conversation. |
| Resolve | `<@resolve>` | Apply a preceding plan/output chain to the content and remove all intermediate tags. |
| Figure | `<@figure>` | Generate a figure via an image-generation API based on the prompt or surrounding context. |
| Plot | `<@plot>` | Generate and execute a visualization script for a given data file based on user instructions. |

## Adding New Skills

1. Create a folder under `skills/` with your skill name (e.g., `skills/translate/`).
2. Add a `SKILL.md` file that defines the skill's trigger tag, editing scope, workflow, scope modifier handling, and output mode.
3. Register the skill in `skills/execute/SKILL.md` by adding a row to the skill registry table.

The folder may also include optional `scripts/`, `references/`, and `assets/` subdirectories for executable code, supplementary documentation, and static resources.
