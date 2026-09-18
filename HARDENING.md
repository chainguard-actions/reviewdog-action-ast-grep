<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.63.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.63.7** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variables ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} are expanded **unquoted** inside shell commands. These variables are populated from inputs.sg_flags and inputs.reviewdog_flags (workflow-controllable inputs) via the env: block in action.yml. Unquoted expansion allows an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, whitespace, glob chars) through these inputs. The `# shellcheck disable=SC2086` comment on line 54 explicitly suppresses the shellcheck warning about this unquoted expansion.

Offending lines:
- `  ${INPUT_SG_FLAGS} |` (passed unquoted to `ast-grep scan`)
- `    ${INPUT_REVIEWDOG_FLAGS} |` (passed unquoted to `reviewdog`)

Locations:

- `script.sh:55`
- `script.sh:64`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} in script.sh. Replaced the unquoted variable expansions (which allowed shell metacharacter injection via attacker-controlled inputs) with safe xargs-based tokenization into bash arrays. Each variable is guarded with `if [ -n ... ]` to prevent empty-token issues, then tokenized using `printf '%s' "$VAR" | xargs printf '%s\0'` with a NUL-delimited read loop into a bash array. The arrays are expanded as `"${sg_flags[@]}"` and `"${reviewdog_flags[@]}"` to keep each token as a separate properly-quoted argument. The `# shellcheck disable=SC2086` comment suppressing the shellcheck warning was also removed.

