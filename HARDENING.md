<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.57.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep/v1.57.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable inputs in script.sh allow shell metacharacter injection. (1) Line 47: `${INPUT_SG_FLAGS}` is expanded unquoted as a positional argument to `ast-grep scan`. This variable is set from `inputs.sg_flags` (via `INPUT_SG_FLAGS: ${{ inputs.sg_flags }}` in action.yml). An attacker-controlled value containing `;`, `|`, `&`, `$(...)`, or backticks can break out of the command and execute arbitrary shell code. The `# shellcheck disable=SC2086` comment confirms intentional word-splitting but does not mitigate injection. (2) Line 55: `${INPUT_REVIEWDOG_FLAGS}` is similarly expanded unquoted as arguments to `reviewdog`, sourced from `inputs.reviewdog_flags`. Both should be quoted or passed via an array to prevent shell injection.

Locations:

- `script.sh:47`
- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two unquoted shell variable expansions in script.sh (lines 47 and 55). Replaced unquoted `${INPUT_SG_FLAGS}` and `${INPUT_REVIEWDOG_FLAGS}` expansions with bash arrays populated via `read -ra`. Each variable is now split into an array using `read -ra array <<< "$VAR"` (only when non-empty), then expanded as `"${array[@]}"` to pass each flag as a separate, properly-quoted argument. This prevents shell metacharacter injection while preserving the intended word-splitting behavior for multi-flag inputs. The `# shellcheck disable=SC2086` comment was removed as it is no longer needed.

