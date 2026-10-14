<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.65.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.65.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansions of untrusted inputs in script.sh. `${INPUT_SG_FLAGS}` (line 51) and `${INPUT_REVIEWDOG_FLAGS}` (line 60) are expanded without double-quotes. These env vars hold values from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively, which are workflow-controllable. An attacker can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through these inputs to achieve command injection. The `# shellcheck disable=SC2086` comment confirms the intentional unquoting. Offending lines: `  ${INPUT_SG_FLAGS} |` and `    ${INPUT_REVIEWDOG_FLAGS} |`.

Locations:

- `script.sh:51`
- `script.sh:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of INPUT_SG_FLAGS (line 51) and INPUT_REVIEWDOG_FLAGS (line 60) in script.sh. Both variables hold user-controllable flag lists that were expanded without quotes, allowing shell metacharacter injection. Replaced with bash array tokenization using the xargs pattern: each variable is pre-tokenized into an array (sg_flags and reviewdog_flags) using 'while IFS= read -r -d '' t; do arr+=("$t"); done < <(printf '%s' "$VAR" | xargs printf '%s\0')' with the required if-guard, then expanded as "${array[@]}" in the commands. The reviewdog_flags array is built before the pipeline (not inline) since reviewdog reads from stdin. Removed the '# shellcheck disable=SC2086' comment that was acknowledging the intentional unquoting.

