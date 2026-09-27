<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable (untrusted) inputs in script.sh. (1) `${INPUT_SG_FLAGS}` on the `ast-grep scan` command line is unquoted — this env var holds `inputs.sg_flags` and allows an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, glob chars, whitespace word-splitting) into the command. (2) `${INPUT_REVIEWDOG_FLAGS}` passed to `reviewdog` is similarly unquoted and holds `inputs.reviewdog_flags`. Both should be quoted as `"${INPUT_SG_FLAGS}"` / `"${INPUT_REVIEWDOG_FLAGS}"` (or use the guarded form `${VAR:+"$VAR"}` if they are optional multi-word flag lists that must be omitted when empty). The `# shellcheck disable=SC2086` comment suppresses the shellcheck warning but does not eliminate the security risk.

Locations:

- `script.sh:49`
- `script.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted flag list expansions in script.sh:
1. INPUT_SG_FLAGS (line 49): Tokenized into bash array 'sg_flags' using xargs with NUL-delimited output and a read loop, guarded by 'if [ -n ... ]'. Expanded as "${sg_flags[@]}" in the ast-grep scan command.
2. INPUT_REVIEWDOG_FLAGS (line 57): Similarly tokenized into 'reviewdog_flags' array before the pipeline (to avoid stdin conflict since reviewdog reads piped input). Expanded as "${reviewdog_flags[@]}" in the reviewdog command.
Removed the '# shellcheck disable=SC2086' comment that was suppressing the warning. The script uses #!/bin/bash so bash arrays and process substitution are appropriate.

