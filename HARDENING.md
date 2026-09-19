<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two shell variables sourced from user-controlled inputs are expanded **unquoted** inside `run:` commands in `script.sh`, allowing shell metacharacter injection.

1. Line 51: `${INPUT_SG_FLAGS}` (from `inputs.sg_flags`) is passed unquoted as a positional argument to `ast-grep scan`. An attacker can inject arbitrary shell commands via this input.
   Offending line: `  ${INPUT_SG_FLAGS} |`

2. Line 60: `${INPUT_REVIEWDOG_FLAGS}` (from `inputs.reviewdog_flags`) is passed unquoted as arguments to `reviewdog`. An attacker can inject arbitrary shell commands via this input.
   Offending line: `    ${INPUT_REVIEWDOG_FLAGS} |`

Both variables are set in the `env:` block of `action.yml` from `${{ inputs.sg_flags }}` and `${{ inputs.reviewdog_flags }}` respectively. Routing through `env:` does not sanitize the values; the shell expansions must be double-quoted (or use the guarded form `${VAR:+"$VAR"}` for optional inputs) to prevent word-splitting and glob expansion of attacker-controlled content. The `# shellcheck disable=SC2086` comment on line 50 explicitly suppresses the unquoted-variable warning, confirming the issue.

Locations:

- `script.sh:51`
- `script.sh:60`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script.sh lines 51 and 60 where INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS were expanded unquoted. Both are 'args'-style inputs (lists of flags), so they were tokenized into bash arrays using the xargs/printf '%s\0' pattern with guarded if-blocks to handle empty values correctly. The arrays are then expanded as "${sg_flags[@]}" and "${reviewdog_flags[@]}" which preserves argument boundaries while preventing word-splitting and glob injection. The # shellcheck disable=SC2086 comment was removed as it's no longer needed.

