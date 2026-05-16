# ia-instructions

Centralized VS Code Copilot configuration repository for software development and infrastructure as code projects.

Contains **Agents**, **Instructions**, **Prompts**, and **Skills** for:
- **Development**: Java/Spring Boot, SQL/PostgreSQL, Redis.
- **Infrastructure as Code**: Ansible, Terraform, Packer, Bash, Traefik.

All projects can share this single source of truth and stay synchronized with one `git submodule update` command.

## Contents

### Agents (`.github/agents/`)
Custom Copilot agents for multi-step workflows. Invoked via `/agent <name>` or discovered by description keywords.

### Instructions (`.github/instructions/`)
File-specific coding conventions applied automatically via `applyTo` patterns (e.g., `**/*.java`).

### Prompts (`.github/prompts/`)
Single-task Copilot prompts with parameterized inputs. Invoked via `/prompt <name>`.

### Skills (`.github/skills/`)
Bundles conventions, procedures, and known failure modes. Invoked via `/skill <name>` or discovered by description keywords.

| Skill | Status | Focus |
|---|---|---|
| `ansible` | ✅ Complete | Task naming, FQCN modules, variable hygiene, handlers, security conventions. |
| `bash` | 🚧 Draft | Script structure, error handling, shared libs. |
| `packer` | 🚧 Draft | HCL2 templates, external variables, credentials. |
| `spring-boot` | ✅ Complete | Maven, constructor injection, ResponseEntity, MyBatis, externalized secrets. |
| `sql-postgresql` | 🚧 Draft | Static SQL, explicit predicates, one DB per service. |
| `terraform` | 🚧 Draft | Provider pinning, typed variables, module contracts. |
| `traefik` | 🚧 Draft | Host-based routing, TLS, internal domains. |

## Installation

### Method 1: Direct submodule (projects without existing `.github/` tracked files)

```bash
git submodule add https://github.com/lsampaioweb/ia-instructions.git .github/
git submodule update --init --recursive
```

This places skills, agents, prompts, and instructions directly at `.github/`, where VS Code Copilot auto-discovers them.

**Use this for:** New projects or projects without existing `.github/` configuration.

### Method 2: Symlink approach (projects with existing `.github/` files)

If your project already tracks files in `.github/` (e.g., `copilot-instructions.md`), use this method to avoid Git conflicts:

```bash
git submodule add https://github.com/lsampaioweb/ia-instructions.git .ia-instructions
git submodule update --init --recursive

# Create symlinks for Copilot discovery.
mkdir -p .github/
ln -s ../.ia-instructions/agents .github/agents
ln -s ../.ia-instructions/hooks .github/hooks
ln -s ../.ia-instructions/instructions .github/instructions
ln -s ../.ia-instructions/prompts .github/prompts
ln -s ../.ia-instructions/skills .github/skills
```

Update `.gitignore`:
```
.github/agents
.github/hooks
.github/instructions
.github/prompts
.github/skills
```

**Use this for:** Projects with existing `.github/copilot-instructions.md` or other tracked `.github/` files.

## Usage

After installation, VS Code Copilot will auto-discover:

- **Agents** at `.github/agents/*.agent.md` — Invoke with `/agent <name>` or by keyword match
- **Prompts** at `.github/prompts/*.prompt.md` — Invoke with `/prompt <name>`
- **Instructions** at `.github/instructions/*.instructions.md` — Applied automatically to matching files
- **Skills** at `.github/skills/<name>/SKILL.md` — Invoke with `/skill <name>`

## Updating

Pull the latest conventions, agents, prompts, and skills:

```bash
git submodule update --remote --merge
```

This works for both installation methods (direct or symlink).

## Customization

You can extend ia-instructions without modifying this repo by adding project-specific customizations in your `.github/` folder:

```
.github/
├── copilot-instructions.md (project-specific agent behavior)
├── skills/                  (from submodule)
├── agents/                  (from submodule)
├── prompts/                 (from submodule + your additions)
└── my-custom-prompt.prompt.md (project-specific)
```

VS Code discovers all files at the canonical paths, so project files coexist with submodule files.

## License

See [LICENSE](LICENSE) for details.

## Created by
1. Luciano Sampaio.
