# ia-instructions

VS Code Copilot Skills for software development — Java/Spring Boot, SQL/PostgreSQL, Redis, and .NET and also for infrastructure as code — Ansible, Terraform, Packer, Bash, and Traefik conventions.

## Skills included

| Skill | Status | Description |
|---|---|---|
| `spring-boot` | ✅ Complete | Maven, constructor injection, ResponseEntity, MyBatis, externalized secrets |
| `sql-postgresql` | 🚧 Draft | Static SQL in mapper files, explicit predicates, one DB per service |
| `ansible` | ✅ Complete | Task naming, FQCN modules, variable hygiene, handlers, security conventions, code review checklist |
| `bash` | 🚧 Draft | Script structure, error handling, shared libs |
| `packer` | 🚧 Draft | HCL2 templates, external variables, credentials |
| `terraform` | 🚧 Draft | Provider pinning, typed variables, module contracts |
| `traefik` | 🚧 Draft | Host-based routing, TLS, internal domains |

## Usage

```bash
git submodule add https://github.com/lsampaioweb/ia-instructions.git .github/
git submodule update --init --recursive
```

Skills will be available at `.github/skills/<name>/SKILL.md`, which is where VS Code Copilot discovers them automatically.

### Advanced: combining both IAC and Dev repos

If a project needs both IAC and Dev skills (rare), add both as submodules to staging paths and create symlinks:

```bash
git submodule add https://github.com/lsampaioweb/ia-instructions.git .ia-instructions
git submodule update --init --recursive

# Create flat symlink structure for Copilot discovery
mkdir -p .github/
for folder in .ia-instructions/*/; do
  ln -s "../../${folder}" ".github/$(basename "$folder")"
done
```

Add to `.gitignore`:
```
.github/*
```

## Updating

```bash
git submodule update --remote --merge
```
