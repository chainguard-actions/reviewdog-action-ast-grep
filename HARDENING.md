<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep/v1.61.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-ast-grep/v1.61.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: Two unquoted shell variable expansions of workflow-controllable env vars appear in script.sh. `${INPUT_SG_FLAGS}` (line 49) and `${INPUT_REVIEWDOG_FLAGS}` (line 57) are both set from `inputs.sg_flags` and `inputs.reviewdog_flags` respectively (via the `env:` block in action.yml). Although the shellcheck SC2086 warning is suppressed to allow intentional word-splitting, these bare unquoted expansions allow an attacker-controlled input to inject shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) into the command. The safe form for optional positional arguments is `${INPUT_SG_FLAGS:+"$INPUT_SG_FLAGS"}` or proper quoting.

Locations:

- `script.sh:49`
- `script.sh:57`

### missing-permissions (severity: medium)

None of the five workflow files under .github/workflows/ define a top-level `permissions:` key, and no job within any of these files defines a job-level `permissions:` key. Without explicit permissions, workflows run with the default (potentially broad) token permissions. Affected files: depup.yml, labels.yml, release.yml, reviewdog.yml, test.yml.

Locations:

- `.github/workflows/depup.yml:1`
- `.github/workflows/labels.yml:1`
- `.github/workflows/release.yml:1`
- `.github/workflows/reviewdog.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed script-injection in script.sh by replacing bare unquoted expansions of INPUT_SG_FLAGS (line 49) and INPUT_REVIEWDOG_FLAGS (line 57) with safe xargs-based tokenization into bash arrays. Each variable is guarded with an emptiness check, tokenized via 'printf %s | xargs printf %s\0' with a NUL-delimited read loop, and expanded as '"${array[@]}"'. Added top-level permissions blocks to all 5 workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write, pull-requests:write), reviewdog.yml (contents:read, checks:write, pull-requests:write), test.yml (contents:read, checks:write, pull-requests:write).

