<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-ast-grep--/v1.58.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **reviewdog--action-ast-grep--/v1.58.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### permissions (severity: medium)

missing-permissions: No top-level 'permissions:' key and no job-level 'permissions:' key found in .github/workflows/depup.yml. Any job in this workflow runs with the default (broad) token permissions.

Locations:

- `.github/workflows/depup.yml:1`

### permissions (severity: medium)

missing-permissions: No top-level 'permissions:' key and no job-level 'permissions:' key found in .github/workflows/labels.yml. Any job in this workflow runs with the default (broad) token permissions.

Locations:

- `.github/workflows/labels.yml:1`

### permissions (severity: medium)

missing-permissions: No top-level 'permissions:' key and no job-level 'permissions:' key found in .github/workflows/release.yml. Any job in this workflow runs with the default (broad) token permissions.

Locations:

- `.github/workflows/release.yml:1`

### permissions (severity: medium)

missing-permissions: No top-level 'permissions:' key and no job-level 'permissions:' key found in .github/workflows/reviewdog.yml. Any job in this workflow runs with the default (broad) token permissions.

Locations:

- `.github/workflows/reviewdog.yml:1`

### permissions (severity: medium)

missing-permissions: No top-level 'permissions:' key and no job-level 'permissions:' key found in .github/workflows/test.yml. Any job in this workflow runs with the default (broad) token permissions.

Locations:

- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansion of untrusted data. In script.sh, the composite-action input ${INPUT_SG_FLAGS} (line 48) is expanded without double-quotes. This variable is set from inputs.sg_flags via the env: block in action.yml and is workflow-controllable. An attacker can inject shell metacharacters (;, |, &, $(...), etc.) through this input. The shellcheck disable comment (SC2086) confirms the quoting is intentionally omitted for word-splitting, but this does not prevent injection.

Locations:

- `script.sh:48`

### script-injection (severity: high)

Rule (b) violation: Unquoted shell variable expansion of untrusted data. In script.sh, the composite-action input ${INPUT_REVIEWDOG_FLAGS} (line 55) is expanded without double-quotes. This variable is set from inputs.reviewdog_flags via the env: block in action.yml and is workflow-controllable. An attacker can inject shell metacharacters (;, |, &, $(...), etc.) through this input.

Locations:

- `script.sh:55`

## Iteration Notes

### Iteration 1

**Fixes applied:** permissions, script-injection

**Notes:**

Fixed 5 missing-permissions findings by adding top-level 'permissions:' blocks to all workflow files: depup.yml (contents:write, pull-requests:write), labels.yml (issues:write), release.yml (contents:write), reviewdog.yml (checks:write, contents:read, pull-requests:write), test.yml (checks:write, contents:read, pull-requests:write). Fixed 2 script-injection findings in script.sh by replacing unquoted ${INPUT_SG_FLAGS} and ${INPUT_REVIEWDOG_FLAGS} expansions with 'read -ra' array splitting and quoted array expansion ("${sg_flags[@]}" and "${reviewdog_flags[@]}"), which prevents shell metacharacter injection while preserving word-splitting behavior for multiple flags.

