<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.60.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.60.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansion of untrusted data. In script.sh, the variable `${INPUT_SG_FLAGS}` (sourced from `inputs.sg_flags` via the env: block in action.yml) is expanded without double-quotes on the `ast-grep scan` command line (line 51). An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) will be parsed by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment acknowledges the word-splitting but does not mitigate the security risk.

Locations:

- `script.sh:51`

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansion of untrusted data. In script.sh, the variable `${INPUT_REVIEWDOG_FLAGS}` (sourced from `inputs.reviewdog_flags` via the env: block in action.yml) is expanded without double-quotes on the `reviewdog` command line (line 59). An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) will be parsed by the shell, enabling command injection.

Locations:

- `script.sh:59`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both script-injection findings in script.sh:
1. INPUT_SG_FLAGS (line 51): Replaced unquoted ${INPUT_SG_FLAGS} with a bash array `sg_flags` built using xargs-based tokenization (quote-aware splitting). Expanded as "${sg_flags[@]}" in the ast-grep command.
2. INPUT_REVIEWDOG_FLAGS (line 59): Replaced unquoted ${INPUT_REVIEWDOG_FLAGS} with a bash array `reviewdog_flags` built using xargs-based tokenization. The array is built before the pipeline so reviewdog still reads from stdin. Expanded as "${reviewdog_flags[@]}" in the reviewdog command.
Both arrays use the guarded `if [ -n ... ]` pattern to prevent xargs from emitting an empty token when the input is empty. Removed the now-unnecessary `# shellcheck disable=SC2086` comment.

