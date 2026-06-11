<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.56.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep/v1.56.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansions of untrusted (workflow-controllable) input data in script.sh. `${INPUT_SG_FLAGS}` (line 52) and `${INPUT_REVIEWDOG_FLAGS}` (line 61) are both set from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (via the `env:` block in action.yml), but are expanded **without double-quotes** in the shell command. This allows an attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to be interpreted by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment confirms the intentional suppression of the quoting warning. These should be quoted or handled safely.

Locations:

- `script.sh:52`
- `script.sh:61`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in script.sh at lines 52 and 61. Replaced unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions (which had shellcheck SC2086 suppressed) with safe bash array handling: `read -ra SG_FLAGS <<< "${INPUT_SG_FLAGS}"` and `read -ra REVIEWDOG_FLAGS <<< "${INPUT_REVIEWDOG_FLAGS}"`, then expanded as `"${SG_FLAGS[@]}"` and `"${REVIEWDOG_FLAGS[@]}"`. This splits inputs on whitespace into arrays without allowing shell metacharacter injection, while still supporting multiple flags.

