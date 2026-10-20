<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.66.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.66.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In script.sh, the shell variables ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} — which hold values sourced from workflow-controllable inputs `inputs.sg_flags` and `inputs.reviewdog_flags` respectively — are expanded **unquoted** inside the `run:` shell command. Unquoted expansion allows an attacker to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) through these inputs. The offending lines are:
  `  ${INPUT_SG_FLAGS} |`  (passed as unquoted flags to `ast-grep scan`)
  `    ${INPUT_REVIEWDOG_FLAGS} |`  (passed as unquoted flags to `reviewdog`)
These should be quoted as `"${INPUT_SG_FLAGS}"` and `"${INPUT_REVIEWDOG_FLAGS}"`, or use the guarded form `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` if they are optional positional arguments that must be omitted when empty.

Locations:

- `script.sh:53`
- `script.sh:62`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted expansions of ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} in script.sh. Both variables are now tokenized into bash arrays using `read -r -d '' -a` with guards for empty values and `|| true` to handle `read -d ''`'s EOF exit code under `set -e`. Arrays are expanded with `"${sg_flags[@]}"` and `"${reviewdog_flags[@]}"` to keep each token as a separate argument while preventing shell metacharacter injection. The `# shellcheck disable=SC2086` comment was also removed as it's no longer needed.

