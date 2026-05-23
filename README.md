# ia-instructions

Centralized VS Code Copilot configuration repository for software development and infrastructure as code projects.

Contains **Agents**, **Instructions**, **Prompts**, and **Skills** for:
- **Development**: Java/Spring Boot, SQL/PostgreSQL, Redis.
- **Infrastructure as Code**: Ansible, Terraform, Packer, Bash, Traefik.

Clone this repo once on your machine and symlink it to VS Code Copilot's user-level directories — all your projects share this single source of truth with no submodule needed in each project.

## Contents

### Agents (`agents/` → `~/.copilot/agents/`)
Custom Copilot agents for multi-step workflows. Usually auto-selected by intent; can also be invoked via `/agent <name>`.

### Instructions (`instructions/` → `~/.copilot/instructions/`)
File-specific coding conventions applied automatically via `applyTo` patterns (e.g., `**/*.java`).

### Skills (`skills/` → `~/.copilot/skills/`)
Domain orchestrators that route implementation and review flow to canonical instruction files. Usually discovered by intent; can be invoked via `/skill <name>`.

### Prompts (`prompts/` → `~/.config/Code/User/profiles/<profile-id>/prompts/`)
Single-task Copilot prompts with parameterized inputs. Optional convenience entry points; can be invoked via `/prompt <name>`.

## Installation

### Method 1: User-level setup (one-time, no submodule required)

Clone this repo once on your machine, then create symlinks to the VS Code Copilot user-level directories. After this, **all your workspaces** automatically pick up agents, instructions, hooks, prompts, and skills — no submodule needed per project.

```bash
# Clone the repo to a permanent location on your machine.
git clone https://github.com/lsampaioweb/ia-instructions.git ~/ia-instructions

REPO="$HOME/ia-instructions"

# Create symlinks to the VS Code Copilot user-level directories.
ln -s "$REPO/agents"       ~/.copilot/agents
ln -s "$REPO/hooks"        ~/.copilot/hooks
ln -s "$REPO/instructions" ~/.copilot/instructions
ln -s "$REPO/skills"       ~/.copilot/skills

# For prompts, the path is profile-specific. Find your profile ID:
# ls ~/.config/Code/User/profiles/
PROFILE_ID="$(ls ~/.config/Code/User/profiles/ | grep -v '^builtin$')"
ln -s "$REPO/prompts" ~/.config/Code/User/profiles/$PROFILE_ID/prompts
```

**Verify the symlinks:**

```bash
ls -la ~/.copilot/agents ~/.copilot/hooks ~/.copilot/instructions ~/.copilot/skills
ls -la ~/.config/Code/User/profiles/$(ls ~/.config/Code/User/profiles/ | grep -v '^builtin$')/prompts
```

**Keep it updated:**

```bash
cd ~/ia-instructions && git pull
```

**Where VS Code Copilot reads user-level files:**

| Primitive | User-level path | Source |
|-----------|----------------|--------|
| Agents (`*.agent.md`) | `~/.copilot/agents/` | [docs](https://code.visualstudio.com/docs/copilot/customization/custom-agents) |
| Hooks (`*.json`) | `~/.copilot/hooks/` | [docs](https://code.visualstudio.com/docs/copilot/customization/hooks) |
| Instructions (`*.instructions.md`) | `~/.copilot/instructions/` | [docs](https://code.visualstudio.com/docs/copilot/customization/custom-instructions) |
| Prompts (`*.prompt.md`) | `~/.config/Code/User/profiles/<profile-id>/prompts/` | [docs](https://code.visualstudio.com/docs/copilot/customization/prompt-files) |
| Skills (`SKILL.md`) | `~/.copilot/skills/` | [docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills) |

## Usage

After installation, VS Code Copilot will auto-discover:

- **Agents** at `~/.copilot/agents/*.agent.md` — Invoke with `/agent <name>` or by keyword match
- **Hooks** at `~/.copilot/hooks/*.json` — Run automatically at agent lifecycle events
- **Instructions** at `~/.copilot/instructions/*.instructions.md` — Applied automatically to matching files
- **Prompts** at `~/.config/Code/User/profiles/<profile-id>/prompts/*.prompt.md` — Invoke with `/prompt <name>`
- **Skills** at `~/.copilot/skills/<name>/SKILL.md` — Invoke with `/skill <name>` or by keyword match

### Recommended interaction model

Use natural language as the default workflow:

- "Review my code in this controller"
- "Add a new endpoint for user search"
- "Refactor this service method"

In these cases, Copilot should infer intent, apply matching instruction files automatically, and use skills for orchestration.

Use slash commands (`/agent`, `/prompt`, `/skill`) only when you explicitly want to force a specific entry point.

## Customization

User-level files (from this repo) apply to all workspaces. You can layer project-specific customizations on top by adding files directly to your workspace's `.github/` folder — VS Code merges both automatically:

```
.github/
├── copilot-instructions.md          (always-on project-specific instructions)
├── instructions/
│   └── my-project.instructions.md  (project-specific file instructions)
└── prompts/
    └── my-custom-prompt.prompt.md  (project-specific prompts)
```

## License

See [LICENSE](LICENSE) for details.

## Created by
1. Luciano Sampaio.
