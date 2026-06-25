<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep/v1.57.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of user-controlled inputs appear in script.sh. (1) `${INPUT_SG_FLAGS}` on line 52 is passed unquoted as arguments to `ast-grep scan`, allowing shell metacharacter injection from the `inputs.sg_flags` value. The `# shellcheck disable=SC2086` comment confirms intentional word-splitting but does not mitigate injection risk. (2) `${INPUT_REVIEWDOG_FLAGS}` on line 62 is passed unquoted as arguments to `reviewdog`, allowing the same attack via `inputs.reviewdog_flags`. Both variables are sourced from caller-controlled `inputs.*` values set in action.yml's `env:` block and must be double-quoted (or handled with an array) to prevent command injection.

Locations:

- `script.sh:52`
- `script.sh:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection vulnerabilities in script.sh: (1) Replaced unquoted ${INPUT_SG_FLAGS} on line 52 with a bash array (read -ra sg_flags <<< "${INPUT_SG_FLAGS}") expanded as "${sg_flags[@]}". (2) Replaced unquoted ${INPUT_REVIEWDOG_FLAGS} on line 62 with a bash array (read -ra reviewdog_flags <<< "${INPUT_REVIEWDOG_FLAGS}") expanded as "${reviewdog_flags[@]}". Both fixes allow multiple flags to be passed while preventing shell metacharacter injection. The now-unnecessary # shellcheck disable=SC2086 comment was also removed.

