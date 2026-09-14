<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.63.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.63.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the variables `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` are expanded **unquoted** inside shell commands. These variables are populated from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (set via the `env:` block in action.yml), making them workflow-controllable and therefore untrusted. Unquoted expansion allows an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, backticks, glob characters, etc.) to achieve arbitrary command execution. The `# shellcheck disable=SC2086` comment in the script acknowledges the unquoted expansion but does not mitigate the security risk. Offending lines:
- `  ${INPUT_SG_FLAGS} |` (line 47 in script.sh)
- `    ${INPUT_REVIEWDOG_FLAGS} |` (line 57 in script.sh)
Fix: use quoted forms `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or use an array to safely pass multiple flags.

Locations:

- `script.sh:47`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansion of ${INPUT_SG_FLAGS} (line 47) and ${INPUT_REVIEWDOG_FLAGS} (line 57) in script.sh. Both variables are now tokenized into bash arrays using the xargs-based pattern (with `if [ -n ... ]` guards to prevent empty-token issues), then expanded as "${sg_flags[@]}" and "${reviewdog_flags[@]}". This prevents shell metacharacter injection while correctly handling multiple flags and quoted arguments. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

