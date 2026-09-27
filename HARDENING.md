<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.57.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs in script.sh allow shell metacharacter injection.

1. Line 47: `  ${INPUT_SG_FLAGS} |` — INPUT_SG_FLAGS is set from `inputs.sg_flags` (via env: in action.yml) and expanded unquoted in the `ast-grep scan` command. An attacker-controlled caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.).

2. Line 57: `    ${INPUT_REVIEWDOG_FLAGS} |` — INPUT_REVIEWDOG_FLAGS is set from `inputs.reviewdog_flags` (via env: in action.yml) and expanded unquoted in the `reviewdog` command. Same injection risk.

The `# shellcheck disable=SC2086` comment on line 46 confirms the author suppressed the linter warning but did not fix the underlying issue. These should use the guarded form `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` or be passed via an array to avoid word-splitting on attacker-controlled metacharacters.

Locations:

- `script.sh:47`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted variable expansions in script.sh (lines 47 and 57). Both INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now tokenized into bash arrays using the xargs-based NUL-delimited pattern (with required 'if [ -n "$VAR" ]' guards) before the pipeline runs, then expanded with "${array[@]}" to keep each token as a separate properly-quoted argument. The # shellcheck disable=SC2086 comment was removed as it's no longer needed. The fix correctly handles the stdin constraint for reviewdog by building the array before the pipeline starts rather than piping into xargs during the pipeline.

