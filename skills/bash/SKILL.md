---
name: bash
description: 'Write and refactor Bash automation. Use for robust scripts, shared shell libraries, strict error handling, and Linux/macOS compatibility workflows.'
argument-hint: 'Script path, target OS, and expected execution flow'
---

# Bash Automation

## When To Use
- Creating or refactoring project automation scripts.
- Improving script safety, readability, and maintainability.
- Standardizing helper libraries and logging behavior.

## Style Conventions
- Apply these conventions in order: Safety, Portability, Correctness, Maintainability, Observability.
- Shell safety defaults: use `#!/bin/bash`, `set -e` and `set -o pipefail` (use `set -euo pipefail` only after declaring all sourced variables with defaults).
- Naming and structure: keep constants in UPPER_SNAKE_CASE, functions in snake_case, and prefer small reusable helpers in `lib/`.
- Command construction: quote variable expansions unless unquoted usage is intentional, and prefer arrays over string-built command arguments.
- Control flow and checks: use `[[ ... ]]` for conditionals (Bash-only, but safer than `[ ]` for variable expansion), `case` for option parsing, and validate external tools with `command -v` before execution.
- Logging: keep messages explicit and actionable, with concise user output and detailed failure context.

## Source Safety: Entry Scripts vs Sourced Libraries

**Entry scripts** (executable from command line):
- Use `SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd)"` to find script location safely (works with symlinks and relative paths).
- Source helpers with absolute paths: `. "${SCRIPT_DIR}/libs/log.sh"` (not relative to CWD).
- Load project variables after sourcing common infrastructure: `. "${project_dir}/$VARIABLES_FILE"`.
- Add annotation above each source statement: `# shellcheck source=./relative/path/to/file.sh`.

**Sourced libraries** (`lib/*.sh`):
- Contain only **function definitions**, no top-level executable code.
- Export functions and constants that entry scripts depend on.
- Use `# shellcheck disable=SC1090,SC2154` only for dynamic sourcing or intentional exports (e.g., IMAGE_FULLNAME from project variables).
- Do **not** invoke themselves—let entry scripts decide when functions run.

**Wrapper scripts** (call sibling scripts):
- Use absolute paths via SCRIPT_DIR to invoke siblings: `"${SCRIPT_DIR}/build.sh"` instead of `./build.sh`.
- This ensures scripts work regardless of CWD (critical for automation pipelines and CI/CD).

## `[[ ]]` vs `[ ]`: When To Use Each

- Use `[[ ]]` for all conditionals in Bash scripts (safer, no word-splitting, regex support with `=~`).
- Use `[ ]` only if you must support POSIX sh (not common in modern projects).
- Since this project uses `#!/bin/bash`, adopt `[[ ]]` consistently.

**Example difference:**
```bash
# POSIX style (fragile with unquoted variables):
if [ -f $file ]; then ...

# Bash style (safe, recommended):
if [[ -f $file ]]; then ...
```

## `set -u` Edge Case: Undefined Variables in Sourced Code

When using `set -u`, reading an undefined variable causes immediate exit. This breaks patterns where variables are conditionally set:

```bash
# BREAKS with set -u:
if [ -z "$MAYBE_UNDEFINED" ]; then

# SAFE with set -u (use default expansion):
if [ -z "${MAYBE_UNDEFINED:-}" ]; then

# Or declare before using:
declare MAYBE_UNDEFINED=""
```

**Your code handles this correctly by defaulting sourced variables:**
```bash
IMAGE_FULLNAME="${IMAGE_FULLNAME:-}"  # Safe pattern
```

Only add `set -u` after ensuring all potentially-undefined variables have defaults or are declared.

## ShellCheck Validation: The `-x` Flag

Run `shellcheck -x` (recursively follow source directives) to validate scripts and their sourced libraries:

```bash
find scripts java -type f -name '*.sh' -print0 | xargs -0 shellcheck -x
```

This checks:
- The main script
- All files referenced with `# shellcheck source=...`
- Dynamically-sourced files (within limits)

If ShellCheck complains about sourced variables, use **narrow disable directives**:
```bash
# shellcheck disable=SC2154  # ONLY for this variable
echo "$INHERITED_FROM_SOURCE"
```

Avoid blanket `# shellcheck disable=SC2154` across the entire file unless all inherited variables are expected.

## Procedure
1. Inspect existing scripts and helper libraries before editing.
2. Add a `usage()` function and validate required arguments at startup.
3. Fail early on invalid input/state, including missing files and directories.
4. Validate dependencies (`git`, `docker`, `podman`, `jq`, etc.) before execution.
5. Keep side effects explicit and grouped in predictable steps.
6. Add cleanup/error traps when temporary files, locks, or background processes are used.
7. Ensure Linux/macOS compatibility by explicitly handling common GNU vs BSD differences (for example in `sed`, `awk`, and `find`) and avoiding assumptions that break either platform.
8. For reusable logic, move helpers to `lib/` and keep scripts thin.
9. When changing behavior, add or update smoke checks (or examples) in docs.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Missing `set -euo pipefail` or premature `set -u`**: Generate new scripts with `set -e` and `set -o pipefail`. Only add `set -u` after ensuring all sourced variables have defaults or are explicitly declared—otherwise undefined variables cause hard exits.
- **Fragile source paths**: Using relative sourcing like `. $(dirname "$BASH_SOURCE")/libs/log.sh` instead of `SCRIPT_DIR="..."` pattern. Fails with spaces, symlinks, and non-CWD execution.
- **Sourced files executing top-level code**: Libraries should only define functions. Any imperative code in a sourced file runs immediately, making entry scripts unpredictable. Move execution logic to functions.
- **CWD-dependent wrapper scripts**: Calling sibling scripts with `./build.sh` instead of `"${SCRIPT_DIR}/build.sh"` breaks in CI/CD and automation where CWD varies.
- **Using `[ ]` instead of `[[ ]]`**: In Bash-only projects, `[ ]` requires extra quoting to avoid word-splitting. Use `[[ ]]` consistently for safety and clarity.
- **Missing dependency checks**: Assuming required binaries exist and failing later in the flow.
- **Unquoted variable expansions**: Writing `$VAR` instead of `"$VAR"` inside conditionals or command arguments, causing word-splitting bugs.
- **`#!/bin/sh` instead of `#!/bin/bash`**: Using the wrong shebang when Bash-specific syntax is present.
- **String-built commands**: Constructing complex command strings and running them with `eval` instead of arrays.
- **Duplicating lib helpers inline**: Reimplementing logging or path resolution inline instead of sourcing from `lib/`.
- **lowercase constants**: Naming environment-level constants in `snake_case` instead of `UPPER_SNAKE_CASE`.
- **Swallowed errors**: Wrapping commands in `if` or `||` in a way that silently suppresses failures instead of handling them explicitly.
- **Fragile temp file handling**: Creating temp files without cleanup traps (`trap 'rm -f ...' EXIT`).
- **Unsafe glob assumptions**: Iterating on globs that may remain unexpanded when no file matches.

## Guardrails
- Do not write unsafe unquoted command paths or user-input interpolation.
- Do not mask failures with broad catch-all flows.
- Do not duplicate helper logic that already exists in shared libraries.
- Do not use `eval` unless absolutely necessary and reviewed for input safety.
- Do not make destructive operations the default path without explicit confirmation.
