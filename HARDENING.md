<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.63.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.63.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): Unquoted shell variable expansions of user-controlled inputs in script.sh. On line 51, `${INPUT_SG_FLAGS}` is expanded without double-quotes, and on line 58, `${INPUT_REVIEWDOG_FLAGS}` is expanded without double-quotes. Both variables are sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the env: block in action.yml). An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, whitespace, glob chars) will be interpreted by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment acknowledges the unquoted expansion but does not mitigate the security risk.

Locations:

- `script.sh:51`
- `script.sh:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} in script.sh. Both are 'flags/args'-style list inputs, so they were tokenized into bash arrays using the xargs-based approach (with null delimiters and guarded by `if [ -n ... ]` checks). The arrays are expanded with `"${sg_flags[@]}"` and `"${reviewdog_flags[@]}"` respectively, preventing shell metacharacter injection while preserving correct argument boundaries. The `# shellcheck disable=SC2086` comment was also removed since it's no longer needed.

