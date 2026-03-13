# Holistic Test 4 — Technical Documentation

## Overview

The EAML executor processs files by scanning for inline annotation tags and dispatching each to the apropriate skill subagent. The executor itself does not modifiy content — it acts purley as a router.
<@proofread>

## Architecture

The system follows a simple pipeline: (1) parse the file for `<@...>` tags, (2) <@ph: describe the second step>, and (3) re-read the file after each skill completes to account for changes.

## Execution Semantics

When multiple annotations appear in a single file, the executor processes them sequentially from top to bottom. This design choice was made because earlier annotations may modify the document in ways that affect later annotation positions. A parallel execution model was considered but rejected due to the complexity of handling overlapping edits and the risk of race conditions in file writes.
<@edit: split into two paragraphs — one about sequential execution, one about why parallel was rejected>

## Error Handling

The executor currently handles errors in a basic way. <<If a skill name is not found in the registry, the tag is skipped and a warning is emitted.>> If a skill subagent fails during execution, the error is logged but the executor continues processing remaining tags.
<@edit: make this more precise about what information the warning and error log contain>
