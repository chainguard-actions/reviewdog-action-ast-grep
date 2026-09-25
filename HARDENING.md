<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b) violation: In script.sh, the env vars `${INPUT_SG_FLAGS}` (line 48) and `${INPUT_REVIEWDOG_FLAGS}` (line 56) are expanded **unquoted** inside shell commands. Both variables hold workflow-controllable data sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the `env:` block in action.yml). An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be parsed by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment on line 47 confirms the author is aware of the unquoted expansion. The safe alternative for optional flag inputs is the guarded form: `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` and `${INPUT_REVIEWDOG_FLAGS:+"$INPUT_REVIEWDOG_FLAGS"}`.

Offending lines:
- Line 48: `  ${INPUT_SG_FLAGS} |`
- Line 56: `    ${INPUT_REVIEWDOG_FLAGS} |`

Locations:

- `script.sh:48`
- `script.sh:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} in script.sh. Both are 'args/options'-style inputs that can contain multiple flags, so they were tokenized into bash arrays using the xargs quote-aware pattern with guards (if [ -n "$VAR" ]). The arrays are built before the pipeline so that reviewdog (which reads from stdin) is not affected by stdin-stealing. Arrays are expanded with "${sg_flags[@]}" and "${reviewdog_flags[@]}" to preserve argument boundaries. The # shellcheck disable=SC2086 comment was removed as it's no longer needed.

