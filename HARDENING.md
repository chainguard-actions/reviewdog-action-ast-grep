<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansion of untrusted input data in script.sh. The variables `${INPUT_SG_FLAGS}` (line 51) and `${INPUT_REVIEWDOG_FLAGS}` (line 60) are expanded without double-quotes in shell commands. These env vars hold values sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` (set via the `env:` block in action.yml). An attacker-controlled input containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) can break out of the intended command and execute arbitrary shell commands. The `# shellcheck disable=SC2086` comment suppresses the linter warning but does not fix the underlying injection risk.

Locations:

- `script.sh:51`
- `script.sh:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansion of INPUT_SG_FLAGS (line 51) and INPUT_REVIEWDOG_FLAGS (line 60) in script.sh. Both are list-style inputs (additional flags), so they are now tokenized using xargs into bash arrays (sg_flags and reviewdog_flags respectively) with proper NUL-delimited splitting that handles quoted arguments correctly. The arrays are then expanded safely as "${sg_flags[@]}" and "${reviewdog_flags[@]}". Each tokenization is guarded with an if [ -n "$VAR" ] check to prevent xargs from emitting an empty token on empty input. The # shellcheck disable=SC2086 comment was also removed.

