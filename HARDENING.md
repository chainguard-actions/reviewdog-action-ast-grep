<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.62.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.62.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): unquoted shell expansion of workflow-controlled data. In script.sh, which the composite action's run step calls, the inputs sg_flags and reviewdog_flags are passed in through env as INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS. They are expanded unquoted as command arguments: `  ${INPUT_SG_FLAGS} |` (ast-grep scan) and `    ${INPUT_REVIEWDOG_FLAGS} |` (reviewdog). The '# shellcheck disable=SC2086' exemption only covers NAME=($VAR) array assignments. These are direct unquoted expansions in command arguments, so they don't qualify.

Locations:

- `script.sh:57`
- `script.sh:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

script.sh: INPUT_SG_FLAGS and INPUT_REVIEWDOG_FLAGS are now tokenized with unquoted array assignments (sg_flags=(${INPUT_SG_FLAGS}), reviewdog_flags=(...)) under '# shellcheck disable=SC2086'. The arrays are expanded as ${arr[@]+"${arr[@]}"}, which handles empty arrays safely. The whitespace split and glob behaviour are the same as upstream. I did not use xargs because reviewdog reads its input from stdin through the pipe.

