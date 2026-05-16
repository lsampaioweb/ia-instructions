---
name: ansible
description: 'Create, review, and refactor Ansible automation following project conventions. Use for homelab playbooks/roles, idempotency fixes, task naming, role structure, variable consistency, include/import patterns, handlers, facts, and ansible-lint alignment.'
argument-hint: 'Role/playbook path, target behavior, and environment details'
---

# Ansible Automation

## When To Use
- Building or refactoring playbooks, task files, roles, and handlers.
- Reviewing idempotency, variable scope, naming, and facts usage.
- Fixing module usage, include/import patterns, and playbook structure.
- Standardizing automation in any homelab project.
- Applying only the sections relevant to the current task to keep execution focused.

## Task Naming
- Task names use **gerund form** (for example: "Creating...", "Installing...", "Updating...", "Removing...") and do not use imperative verbs ("Create...", "Install...").
- Task names are always **quoted**.
- Task names are **descriptive and action-oriented** — include the resource or subject being acted on.

```yaml
# Correct
- name: "Installing required packages"
- name: "Creating the configuration file"
- name: "Restarting the nginx service"

# Wrong
- name: Install required packages
- name: "Create config"
- name: "restart nginx"
```

## Module Usage
- Always use **FQCN** modules: `ansible.builtin.copy`, `community.general.dconf`, etc.
- Prefer static includes (`import_role`, `import_tasks`) over dynamic (`include_role`, `include_tasks`) unless dynamic loading is genuinely required.
- When using `tasks_from` (for example with `include_role` or `import_role`), always omit the `.yml` extension: `tasks_from: "debian/packages/apt"`.
- When calling tasks from another role, always use `include_role` with `tasks_from`. Never use `include_tasks` with relative cross-role paths.

```yaml
# Correct
- ansible.builtin.include_role:
    name: "common"
    tasks_from: "debian/packages/apt"

# Wrong
- ansible.builtin.include_tasks: "{{ role_path }}/../other_role/tasks/some_task.yml"
```

## Facts
- Use `ansible_facts['key']` bracket notation.
- Use `ansible_facts['distribution_release']` — **not** `ansible_lsb.codename` or `ansible_distribution_release` (deprecated since 2.24).
- Always run `ansible.builtin.service_facts` before referencing `ansible_facts.services`.

## Variables
- Variables use **snake_case** with scope prefixes.
- Role-specific variables live in `roles/<role>/vars/main.yml`.
- Shared variables live in `group_vars/all.yml`.
- Namespace variables to their role (e.g. `system_settings.*`).
- OS-specific vars use `first_found` lookup: try `{{ ansible_distribution_release }}.yml`, fall back to `packages.yml`.
- Reset output variables at the start of each role to prevent contamination from previous runs:

```yaml
- name: "Resetting role output variables"
  ansible.builtin.set_fact:
    role_output: {}
    role_specific_path: ""
```

## Task Style
```yaml
- name: "Descriptive gerund-form name in quotes"
  when:
    - condition_one
    - condition_two
  ansible.builtin.[module]:
    param: value
  register: "result_variable"
  notify: "Handler Name"
```

## Playbook Structure
- Every playbook uses `hosts: "{{ inventory_hosts | default('target') }}"` — never hardcode a host group name.
- `gather_facts: false` is only used when facts are gathered manually in `pre_tasks`.

## Error Handling
- Prefer `failed_when` over `ignore_errors`.
- Use handlers for service restarts and config reloads — never restart inline.

## Sensitive Data
- Use `no_log` on tasks that handle sensitive values (for example passwords, tokens, or private keys), and allow logs only when debugging is explicitly enabled:

```yaml
no_log: "{{ not debug | default(true) }}"
```

## Role Structure
Each role follows this layout:
```
roles/<role>/
├── files/               # Static files
├── meta/main.yml        # Role metadata and dependencies
├── handlers/main.yml    # Service restart handlers
├── tasks/main.yml       # Entry point — uses import_tasks for sub-files
├── vars/main.yml        # Role variables
└── README.md            # Purpose, usage, tasks performed, author
```

## Debug Tasks
Every debug task must use `tags: ["never", "debug"]` so it never runs unless explicitly requested. Never generate a debug task without this tag.

```yaml
- name: "Printing the output variable"
  ansible.builtin.debug:
    msg: "{{ output }}"
  tags: ["never", "debug"]
```

## Environment Setup
- Install Ansible and all tools via **`pipx`** on Linux or **`brew`** on macOS.
- **Never use `pip install`** directly for Ansible or its dependencies.

## Security Conventions
- Sensitive values required at runtime (passwords, tokens) come from `secret-tool lookup` — never hardcoded.
- Sensitive config values that must be committed to version control (e.g. identities, keys) are **Base64-encoded** before committing; decode only at runtime where needed, and treat Base64 as obfuscation (not encryption).
- Use `no_log` on any task that handles secrets, and allow logs only when debugging is explicitly enabled:

```yaml
no_log: "{{ not debug | default(true) }}"
```

## Developing New Roles — Incremental Testing
When building a new role, follow this sequence to catch failures early:
1. Start with variable setup only — verify vars load correctly.
2. Comment out complex operations and test each section independently.
3. Uncomment and test one section at a time.
4. Verify end-to-end behavior only after each section works in isolation.

## Procedure
1. **Read** nearby role/playbook patterns before proposing any change.
2. **Fit** changes into existing roles and variable structures before creating new ones.
3. Implement the **minimal** idempotent change first.
4. Add or update **handlers** when config changes require service reload/restart.
5. Validate playbook structure (`hosts`, `gather_facts`, `become`, `vars` flow).
6. Suggest `ansible-lint` and `--syntax-check` commands when appropriate.
7. When running playbooks or terminal commands, **never truncate output** — do not append `| tail`, `| head`, `| grep`, or any other filter. Always show full output so failures are visible.

## Code Review
When asked to review code (e.g. "review before I commit"), **systematically check every section of this skill** in order. Report findings grouped by section, flagging violations clearly.

Checklist:
- [ ] **Task Naming** — gerund form, quoted, descriptive.
- [ ] **Module Usage** — FQCN on every module; static import/include unless dynamic is justified; `tasks_from` without `.yml`; no relative cross-role `include_tasks` paths.
- [ ] **Facts** — bracket notation; no deprecated aliases; `service_facts` gathered before `ansible_facts.services` is read.
- [ ] **Variables** — snake_case with scope prefixes; role vars in `vars/main.yml`; shared in `group_vars/all.yml`; output vars reset at role start.
- [ ] **Task Style** — `when` as list; `register` and `notify` values quoted.
- [ ] **Playbook Structure** — `hosts` is variable-driven; `gather_facts` policy is intentional.
- [ ] **Error Handling** — `failed_when` used instead of `ignore_errors`; restarts via handlers only.
- [ ] **Debug Tasks** — every debug task has `tags: ["never", "debug"]`.
- [ ] **Sensitive Data** — `no_log` applied; no hardcoded secrets; Base64 for committed sensitive values.
- [ ] **Role Structure** — expected directories and files are present.
- [ ] **Guardrails** — no `pip` usage; no `shell`/`command` when a native module exists; no hardcoded host groups; no new features added without explicit confirmation.

## Question Assumptions
During any review or implementation, proactively challenge:
- New variables that duplicate existing ones.
- New tasks or roles that could fit within an existing role.
- Hardcoded values that should be configurable variables.
- Complex conditionals that could be simplified.
- Missing or inadequate error handling.

Always raise these concerns **before** making changes. Never silently "fix" an assumption.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Short module names under edit pressure**: When modifying existing tasks, reverting to `copy:` instead of `ansible.builtin.copy:`. Always keep FQCN.
- **`pip install` for Python tools**: Defaulting to `pip install` when asked to install a CLI tool. Always use `pipx` or `brew`.
- **Unnecessary `gather_facts: true`**: Adding or preserving `gather_facts: true` on plays where no `ansible_facts` key is used. Default is already `true`; only set it explicitly when you need to force `false`.
- **Truncated output**: Summarising playbook output with `...` or `[output truncated]` instead of showing it in full. Never truncate.
- **Imperative task names under copy-paste**: Copying existing imperative names ("Install packages") instead of correcting to gerund form ("Installing packages").
- **`ansible_distribution_release` instead of `ansible_facts['distribution_release']`**: The bare magic variable is deprecated — always use bracket-notation facts.
- **`include_tasks` with cross-role paths**: Using relative `../other_role/tasks/file.yml` paths instead of `include_role` + `tasks_from`.
- **`no_log: true` on every secrets task**: Applying `no_log` globally or always, instead of only on tasks that actually print secret values.

## Guardrails
- Do not use `shell`/`command` when a native module exists.
- Do not hardcode host groups or environment-specific values when a variable already exists.
- **Never add features, variants, or naming suffixes without explicit user confirmation.** Ask first, implement after approval.
- Do not assume file state — always read files before editing or advising.
- Do not add new roles or variables if the change fits within an existing one.
- Never use `pip install` — use `pipx` or `brew` only.
- Keep all content in English.
