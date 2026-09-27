<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.60.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.60.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variables `${INPUT_SG_FLAGS}` (line 52) and `${INPUT_REVIEWDOG_FLAGS}` (line 61) are expanded **unquoted** inside shell commands. These variables are populated from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (via the `env:` block in action.yml), making them workflow-controllable/attacker-supplied values. Unquoted expansion allows the shell to parse metacharacters (`;`, `|`, `&`, `$(...)`, whitespace, glob chars) out of the value, enabling command injection. The `# shellcheck disable=SC2086` comment acknowledges the unquoted expansion but does not mitigate the security risk. Offending lines:
- Line 52: `  ${INPUT_SG_FLAGS} |`
- Line 61: `    ${INPUT_REVIEWDOG_FLAGS} |`

Locations:

- `script.sh:52`
- `script.sh:61`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of INPUT_SG_FLAGS (line 52) and INPUT_REVIEWDOG_FLAGS (line 61) in script.sh. Both are 'args'-style inputs (lists of additional flags) that were expanded unquoted, allowing shell metacharacter injection. Replaced with bash array tokenization using the xargs/while-read-NUL pattern: each variable is tokenized into an array (sg_flags and reviewdog_flags) with proper quoting guards, then expanded as "${array[@]}" in the commands. The arrays are built before the pipeline starts, avoiding any stdin conflict with reviewdog. Removed the now-unnecessary '# shellcheck disable=SC2086' comment.

