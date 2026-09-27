<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.56.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.56.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two env vars holding user-controlled inputs are expanded unquoted inside `script.sh`, allowing shell metacharacter injection.

1. `${INPUT_SG_FLAGS}` (from `inputs.sg_flags`) is passed unquoted as a positional argument to `ast-grep scan`: `  ${INPUT_SG_FLAGS} |` — an attacker can inject shell metacharacters (`;`, `|`, `$(...)`, etc.).

2. `${INPUT_REVIEWDOG_FLAGS}` (from `inputs.reviewdog_flags`) is passed unquoted to `reviewdog`: `    ${INPUT_REVIEWDOG_FLAGS} |` — same risk.

Both variables are set via the `env:` block in `action.yml` from `inputs.*` values and must be double-quoted: `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"` (or use the guarded form `${VAR:+"$VAR"}` for optional flags).

Locations:

- `script.sh:44`
- `script.sh:52`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted variable expansions in script.sh:
1. `${INPUT_SG_FLAGS}` (line 44): Replaced with a bash array `sg_flags` populated via xargs tokenization, expanded as `"${sg_flags[@]}"`.
2. `${INPUT_REVIEWDOG_FLAGS}` (line 52): Replaced with a bash array `reviewdog_flags` populated via xargs tokenization, expanded as `"${reviewdog_flags[@]}"`.

Both use the guarded `if [ -n "${VAR}" ]` pattern to avoid empty-token issues. The reviewdog flags are pre-tokenized into an array (not piped through xargs at call time) because reviewdog reads its input from stdin in the pipeline. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

