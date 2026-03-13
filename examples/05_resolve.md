# Resolve Skill Test

## Test 1: Plan only — generate a plan (no resolve yet)

The annotation grammar is intentionally minimal, but this simplicity comes at a cost. Users must learn the tag syntax, understand scope modifiers, and reason about execution order when multiple annotations are present.
<@plan: suggest how to restructure this into a strength-then-limitation framing>

## Test 2: Basic plan → resolve chain

The interface presents a simple text editor with a sidebar showing available tools. Users can drag and drop tools onto the canvas.
<@plan: make more specific and technical>
<@output 1. Replace "simple text editor" with the actual editor name or a descriptive phrase. 2. Replace "tools" with specific tool categories. 3. Replace "drag and drop" with the actual interaction mechanism.>
<@resolve>

## Test 3: Resolve with user-edited plan

Our system is fast and works well.
<@plan: expand>
<@output Rewrite to: "Our system achieves sub-200ms latency for single-annotation execution and scales linearly with annotation count, maintaining interactive response times for documents with up to 50 concurrent annotations.">
<@resolve>
