<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.64.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.64.15** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the env vars ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} — which hold values sourced from inputs.sg_flags and inputs.reviewdog_flags in action.yml — are expanded **unquoted** inside shell commands. Unquoted expansion allows an attacker-controlled value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to be interpreted by the shell, enabling command injection. The `# shellcheck disable=SC2086` comment confirms the quoting was intentionally suppressed. Offending lines:
- `  ${INPUT_SG_FLAGS} |` (passed unquoted to ast-grep scan)
- `    ${INPUT_REVIEWDOG_FLAGS} |` (passed unquoted to reviewdog)

Locations:

- `script.sh:47`
- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansion of INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS in script.sh. Both variables hold user-controlled 'additional flags' list inputs. Replaced unquoted ${VAR} expansions with xargs-based bash array tokenization: each variable is tokenized into an array using 'printf %s | xargs printf %s\0' with a NUL-delimited read loop (guarded by an emptiness check), then expanded as "${array[@]}". This prevents shell metacharacters in attacker-controlled values from being interpreted while correctly handling quoted arguments. The reviewdog_flags array is built via process substitution (not piped into reviewdog) so reviewdog's stdin from the pipeline remains intact. Removed the '# shellcheck disable=SC2086' comment that was suppressing the quoting warning.

