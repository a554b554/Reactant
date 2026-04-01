---
name: plot
description: Generate and execute a visualization script for a given data file based on user instructions and context.
---

# Plot

Generate a data visualization by producing and executing a Python script (Matplotlib/Seaborn) based on the prompt, surrounding context, and a referenced data file.

## Input

- **File path**: the file being processed.
- **Surrounding text**: the paragraph or block where the `<@plot: ...>` tag appears.
- **Prompt**: description of the desired visualization, including chart type, styling, and any specific formatting instructions.

## Workflow

1. Read the surrounding text and prompt.
2. Identify the **data file** from the prompt's context references (`` `` ``). Read the data file to understand its structure (columns, types, number of rows).
3. Identify the **output path** from the prompt's context references. If none is specified, save the plot in the same directory as the source file with a name derived from the prompt (e.g., `plot_completion_time.png`).
4. Generate a self-contained Python script that:
   - Reads the data file (using `pandas` for CSV/TSV/Excel, or appropriate libraries for other formats).
   - Produces the requested visualization using `matplotlib` and/or `seaborn`.
   - Applies any styling or formatting instructions from the prompt.
   - Saves the figure to the output path with `dpi=300` and `bbox_inches='tight'`.
5. Execute the script in a single Bash command:
   ```bash
   python -c "
   <generated script here>
   "
   ```
6. Verify the output file was created.
7. Replace the `<@plot: ...>` tag with a format-appropriate image reference:
   - Markdown (`.md`): `![description](output_path.png)`
   - LaTeX (`.tex`): `\includegraphics{output_path.png}`
   - other format, try to determine by yourself.

## Context References

The prompt may contain **context references** wrapped in double backticks (`` `` ``). These point to external resources.

- **As data source**: The reference points to the data file to visualize. E.g., `<@plot: bar chart using ``data/results.csv``>` -- read `data/results.csv` as the input data.
- **As output path**: The reference specifies where to save the generated plot. E.g., `<@plot: line chart, save to ``figures/trend.png``>` -- save the plot to `figures/trend.png`.
- Resolve references relative to the source file's directory unless an absolute path is given.
- Remove the `` `` delimiters from the output.
- If the input tag does not contain any context reference, ask user the elaborate.

## Rules

- Always read the data file first to understand its structure before generating the script. Do not assume column names or data types.
- Generate a complete, self-contained Python script. Do not rely on variables or state from previous executions.
- Run the script in a single Bash invocation. Do not split into separate calls.
- Use `python` (not `python3`) for execution.
- Default to clean, publication-ready styling: white background, legible font sizes, clear axis labels.
- If the prompt does not specify a chart type, infer the most appropriate one from the data structure and surrounding context.
- If the data file cannot be found or read, warn the user and skip.
- If execution fails, report the error and include the generated script so the user can debug.
- Sanitize the output filename: lowercase, underscores, no spaces or special characters (unless an explicit output path is given via context reference).
- The `<@plot: ...>` tag and all `` `` delimiters must be removed in the output.
