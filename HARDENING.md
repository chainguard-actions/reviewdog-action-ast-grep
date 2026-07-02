<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep/v1.57.4** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the variable ${INPUT_SG_FLAGS} (sourced from inputs.sg_flags via the env: block in action.yml) is expanded unquoted on line 48 inside the `ast-grep scan` command. An attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) will be parsed by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment on line 47 confirms the author suppressed the linter warning for this unquoted expansion. Offending line: `  ${INPUT_SG_FLAGS} |`

Locations:

- `script.sh:48`
- `action.yml:74`

### script-injection (severity: high)

Rule (b) violation: In script.sh, the variable ${INPUT_REVIEWDOG_FLAGS} (sourced from inputs.reviewdog_flags via the env: block in action.yml) is expanded unquoted on line 56 inside the `reviewdog` command. An attacker-controlled value containing shell metacharacters will be parsed by the shell, enabling command injection. Offending line: `    ${INPUT_REVIEWDOG_FLAGS} |`

Locations:

- `script.sh:56`
- `action.yml:71`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection vulnerabilities in script.sh:
1. ${INPUT_SG_FLAGS} (line 48): Was expanded unquoted in the `ast-grep scan` command. Replaced with a bash array (SG_FLAGS_ARRAY) populated via `read -ra` only when non-empty, then expanded as "${SG_FLAGS_ARRAY[@]}" to safely quote each element.
2. ${INPUT_REVIEWDOG_FLAGS} (line 56): Was expanded unquoted in the `reviewdog` command. Replaced with a bash array (REVIEWDOG_FLAGS_ARRAY) populated via `read -ra` only when non-empty, then expanded as "${REVIEWDOG_FLAGS_ARRAY[@]}" to safely quote each element.
Also removed the `# shellcheck disable=SC2086` comment that was suppressing the linter warning for the unquoted expansion. The empty-check guards prevent passing an empty string argument when the optional flags inputs are unset (default '').

