<!-- markdownlint-disable -->

# Hardening Report: actions-js--push/v1.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **actions-js--push/v1.4** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): In start.sh, user-controlled input variables are expanded unquoted and executed directly as shell commands. The patterns `if ${INPUT_EMPTY}; then`, `if ${INPUT_AMEND}; then`, `if ${INPUT_FORCE}; then`, and `if ${INPUT_TAGS}; then` execute the value of each variable as a shell command — an attacker who controls these inputs (e.g., via `inputs.amend: 'malicious_command'`) can achieve arbitrary command execution. Additionally, `${_AMEND}`, `$_EMPTY`, `$_FORCE_OPTION`, and `$_TAGS` are passed unquoted to `git commit` and `git push`, allowing shell metacharacter injection from those same inputs. All of these variables are derived from workflow-controllable `inputs.*` values (`inputs.empty`, `inputs.amend`, `inputs.force`, `inputs.tags`).

Locations:

- `start.sh:25`
- `start.sh:29`
- `start.sh:33`
- `start.sh:37`
- `start.sh:51`
- `start.sh:56`
- `start.sh:61`
- `start.sh:65`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed start.sh by: (1) Replacing all `if ${INPUT_X}; then` patterns (which executed variable values as shell commands) with safe string comparisons `if [ "${INPUT_X}" = "true" ]; then`. (2) Eliminated unquoted intermediate variables `$_AMEND`, `$_EMPTY`, `$_FORCE_OPTION`, `$_TAGS` passed to git commands by restructuring the logic into explicit conditional branches that use literal flag strings (--amend --no-edit, --allow-empty, --force, --tags) directly in git invocations. All original functionality is preserved.

