<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.6** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variable ${INPUT_SG_FLAGS} is expanded unquoted on the ast-grep command line (line ~48: `${INPUT_SG_FLAGS} |`). This variable is sourced from inputs.sg_flags (set via `INPUT_SG_FLAGS: ${{ inputs.sg_flags }}` in action.yml), which is attacker-controlled. An unquoted expansion allows shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) embedded in the input to be interpreted by the shell, enabling command injection.

Locations:

- `script.sh:48`
- `action.yml:72`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variable ${INPUT_REVIEWDOG_FLAGS} is expanded unquoted on the reviewdog command line (line ~57: `${INPUT_REVIEWDOG_FLAGS} |`). This variable is sourced from inputs.reviewdog_flags (set via `INPUT_REVIEWDOG_FLAGS: ${{ inputs.reviewdog_flags }}` in action.yml), which is attacker-controlled. An unquoted expansion allows shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) embedded in the input to be interpreted by the shell, enabling command injection.

Locations:

- `script.sh:57`
- `action.yml:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection vulnerabilities in script.sh:
1. ${INPUT_SG_FLAGS} (line ~48): Replaced unquoted expansion with xargs-based tokenization into bash array `sg_flags`, expanded as "${sg_flags[@]}".
2. ${INPUT_REVIEWDOG_FLAGS} (line ~57): Replaced unquoted expansion with xargs-based tokenization into bash array `reviewdog_flags`, expanded as "${reviewdog_flags[@]}".
Both fixes use the required `if [ -n "$VAR" ]` guard, `printf '%s'` to avoid flag interpretation, NUL-delimited xargs output with `read -r -d ''` loop, and safe array expansion. The script already uses #!/bin/bash so bash arrays are available.

