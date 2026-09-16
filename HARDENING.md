<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.63.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.63.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of user-controlled inputs exist in script.sh. (1) Line 44: `${INPUT_SG_FLAGS}` is passed unquoted to `ast-grep scan`, allowing an attacker to supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) via the `sg_flags` input and inject arbitrary shell commands. (2) Line 55: `${INPUT_REVIEWDOG_FLAGS}` is passed unquoted to `reviewdog`, allowing the same attack via the `reviewdog_flags` input. Both variables are sourced from `inputs.*` (mapped through the `env:` block in action.yml) and are intentionally unquoted (a `# shellcheck disable=SC2086` comment is present), but this does not prevent command injection. They must be double-quoted or handled via an array to prevent shell metacharacter injection.

Locations:

- `script.sh:44`
- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection vulnerabilities in script.sh at lines 44 and 55. Both INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS were previously passed unquoted to ast-grep and reviewdog respectively (with a '# shellcheck disable=SC2086' comment). They are now tokenized into bash arrays using the xargs-based pattern (quote-aware tokenization with NUL delimiters), guarded by 'if [ -n "${VAR}" ]' to prevent empty-token emission, and expanded safely with "${array[@]}". The shellcheck disable comment was removed as it is no longer needed.

