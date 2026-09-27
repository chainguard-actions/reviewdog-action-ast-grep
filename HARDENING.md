<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.59.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.59.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b) violation: Two unquoted shell variable expansions of workflow-controllable data appear in script.sh. (1) `${INPUT_SG_FLAGS}` on line 52 is passed unquoted as arguments to `ast-grep scan`. (2) `${INPUT_REVIEWDOG_FLAGS}` on line 61 is passed unquoted as arguments to `reviewdog`. Both variables are set from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (via the `env:` block in action.yml), meaning a caller can inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) to execute arbitrary commands. The `# shellcheck disable=SC2086` comment on line 51 acknowledges the unquoted expansion but does not mitigate the injection risk. These should be quoted or handled via an array to prevent command injection.

Locations:

- `script.sh:52`
- `script.sh:61`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed both unquoted variable expansions in script.sh (lines 52 and 61). Replaced unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} with bash arrays built using the xargs/read-loop tokenization idiom (quote-aware, injection-safe). Each array is guarded with `if [ -n ... ]` to prevent empty-argument injection. Arrays are expanded as "${sg_flags[@]}" and "${reviewdog_flags[@]}". The # shellcheck disable=SC2086 comment was removed since unquoted expansions are no longer used. The reviewdog array is built before the pipeline (not piped into xargs at command time) to preserve reviewdog's stdin.

