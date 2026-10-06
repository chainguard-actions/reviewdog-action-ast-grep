<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.13** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansions of workflow-controllable data in script.sh. `${INPUT_SG_FLAGS}` (line 52) and `${INPUT_REVIEWDOG_FLAGS}` (line 61) are sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the `env:` block in action.yml). Because they are expanded without double-quotes, an attacker can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to achieve command injection. The `# shellcheck disable=SC2086` comment acknowledges the word-splitting but does not mitigate the injection risk. Offending lines: `  ${INPUT_SG_FLAGS} |` and `    ${INPUT_REVIEWDOG_FLAGS} |`.

Locations:

- `script.sh:52`
- `script.sh:61`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of INPUT_SG_FLAGS (line 52) and INPUT_REVIEWDOG_FLAGS (line 61) in script.sh. Both variables are now tokenized into bash arrays using the xargs + NUL-delimited read loop pattern with process substitution (< <(printf '%s' "$VAR" | xargs printf '%s\0')). Each array is guarded with 'if [ -n "$VAR" ]' to prevent xargs from emitting an empty token when the input is empty. The arrays are then expanded with "${sg_flags[@]}" and "${reviewdog_flags[@]}" respectively, keeping each token as a separate argument while preventing shell metacharacter injection. The shellcheck disable comment was also removed since it's no longer needed.

