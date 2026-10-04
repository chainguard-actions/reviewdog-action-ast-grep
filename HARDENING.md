<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.11

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.11** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the env vars `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` — which hold values sourced from `inputs.sg_flags` and `inputs.reviewdog_flags` (set via the `env:` block in action.yml) — are expanded **unquoted** inside shell commands. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that will be parsed by the shell, enabling command injection.

Offending lines:
- `${INPUT_SG_FLAGS}` used unquoted in the `ast-grep scan` pipeline (the `# shellcheck disable=SC2086` comment acknowledges but does not fix this)
- `${INPUT_REVIEWDOG_FLAGS}` used unquoted in the `reviewdog` invocation

Fix: Use `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or use an array to pass flags safely.

Locations:

- `script.sh:46`
- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansion of INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS in script.sh. Both are argument-list inputs (flags), so replaced unquoted ${VAR} expansions with bash arrays built using the xargs/NUL-delimited read loop pattern (guarded by [ -n "$VAR" ] checks). Arrays are expanded as "${array[@]}" in the ast-grep and reviewdog commands respectively. Removed the now-unnecessary # shellcheck disable=SC2086 comment.

