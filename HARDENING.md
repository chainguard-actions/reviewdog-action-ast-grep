<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b): script.sh is the supporting script run by the action's composite step. It expands two workflow-controlled env vars, both set from inputs.* in action.yml, without quotes and outside an array assignment: line 58 `${INPUT_SG_FLAGS} |` (inputs.sg_flags) and line 68 `${INPUT_REVIEWDOG_FLAGS} |` (inputs.reviewdog_flags). Both are passed directly as command arguments to ast-grep and reviewdog. Word-splitting does not run ';', '|' or $(...) from the value, but the caller can inject arbitrary extra flags and the values are glob-expanded. The fix that keeps the whitespace-separated list behaviour is to split each into an array first, e.g. `# shellcheck disable=SC2206` then `SG_FLAGS=(${INPUT_SG_FLAGS})`, and pass it as "${SG_FLAGS[@]}". Do the same for INPUT_REVIEWDOG_FLAGS.

Locations:

- `script.sh:58`
- `script.sh:68`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

I fixed the script-injection finding in script.sh. Both flag inputs are now split into arrays before they reach the commands: SG_FLAGS=(${INPUT_SG_FLAGS}) and REVIEWDOG_FLAGS=(${INPUT_REVIEWDOG_FLAGS}). The arrays are passed to ast-grep and reviewdog as ${ARR[@]+"${ARR[@]}"}. The split is unquoted on purpose, so it breaks on whitespace and expands globs exactly as upstream did, and an empty input adds no argument (the ${ARR[@]+...} guard also keeps it safe under `set -u` on bash 3.2). The commands' stdin still comes from the pipeline, and there is no xargs or quote-aware splitting. I did not run the script. action.yml did not need changes.

