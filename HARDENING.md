<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.63.8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.63.8** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two env vars sourced from user-controlled inputs are expanded **unquoted** inside shell commands in script.sh, allowing shell metacharacter injection.

1. `${INPUT_SG_FLAGS}` (from `inputs.sg_flags`) is passed unquoted as a positional argument to `ast-grep scan` (line ~49): `${INPUT_SG_FLAGS} |`. An attacker-controlled value containing `;`, `|`, `&`, `$(...)`, or glob characters will be interpreted by the shell.

2. `${INPUT_REVIEWDOG_FLAGS}` (from `inputs.reviewdog_flags`) is passed unquoted to `reviewdog` (line ~57): `${INPUT_REVIEWDOG_FLAGS} |`. Same risk applies.

Both variables are set in the `env:` block of action.yml from `${{ inputs.sg_flags }}` and `${{ inputs.reviewdog_flags }}` respectively. The `env:` routing does not sanitize the values; the shell still parses metacharacters from unquoted expansions. These should be double-quoted: `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or handled via an array if word-splitting of flags is intentional.

Locations:

- `script.sh:49`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection vulnerabilities in hardened/action/script.sh at lines 49 and 57. Both INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS were previously expanded unquoted (with a # shellcheck disable=SC2086 comment), allowing shell metacharacter injection. The fix tokenizes each flag list variable using xargs into a bash array (with the guarded xargs/printf '%s\0'/read-loop pattern), then expands the arrays safely with "${sg_flags[@]}" and "${reviewdog_flags[@]}". This preserves the intended word-splitting behavior for flag lists while preventing injection of shell metacharacters like ;, |, &, $(...), and globs.

