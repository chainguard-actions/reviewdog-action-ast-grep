<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.10

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.10** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansions of workflow-controllable inputs in script.sh. `${INPUT_SG_FLAGS}` (line 48) and `${INPUT_REVIEWDOG_FLAGS}` (line 57) are expanded without double-quotes in shell commands. These variables are populated from `${{ inputs.sg_flags }}` and `${{ inputs.reviewdog_flags }}` respectively via the action.yml env: block. An attacker-controlled caller can supply values containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) that the shell will interpret, enabling command injection. The `# shellcheck disable=SC2086` comment confirms the author is aware of word-splitting but does not mitigate the injection risk. Fix: use `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"` (or the guarded form `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` for optional flag arguments).

Locations:

- `script.sh:48`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted ${INPUT_SG_FLAGS} (line 48) and ${INPUT_REVIEWDOG_FLAGS} (line 57) in script.sh. Both are 'additional flags' list-style inputs that need quote-aware tokenization. Replaced both with bash array construction using the xargs/NUL-delimiter pattern: each variable is guarded with 'if [ -n ... ]', tokenized via 'printf '%s' "$VAR" | xargs printf '%s\0'' piped into a 'while IFS= read -r -d '' t; do arr+=("$t"); done' loop, then expanded as '"${arr[@]}"' in the command. This preserves argument boundaries, handles quoted sub-arguments correctly, and prevents shell metacharacter injection. The '# shellcheck disable=SC2086' comment was removed as it's no longer needed.

