---
description: "Use when creating or reviewing Podman container files and compose manifests. Covers rootless defaults, image hardening, runtime configuration, and health checks."
applyTo: "**/{Containerfile,Dockerfile,podman-compose.yml,podman-compose.yaml,compose.yml,compose.yaml,*podman*.sh}"
---

# Podman Container Conventions

- Prefer `Containerfile` naming for Podman-based projects
- Run containers rootless when possible and avoid privileged mode
- Use minimal base images and pin explicit image tags
- Create and use a non-root user inside the image for application runtime
- Keep build context small and avoid copying secrets into image layers
- Inject configuration via environment variables or mounted files, not baked secrets
- Expose only required ports and bind to specific interfaces when possible
- Define a health check for long-running services
- Use read-only filesystem and dropped capabilities when compatible with the app

## Compose and Runtime Checks

- Prefer Podman-native commands and `podman-compose` workflows
- Keep volume mounts explicit and minimal
- Avoid host network mode unless required and justified
- Do not commit real credentials in compose files or helper scripts

## Spring Boot Project Placement

For Spring Boot projects, place all container-related files at the **project root** (same level as `pom.xml`):

- `Dockerfile` — at project root; copies `./target/*.jar` into the image
- `docker-compose.yml` — at project root; mounts `./logs/container` and `./ssl/` as volumes
- `.dockerignore` — at project root
- `.env` — at project root
- `logs/container/.keep` — placeholder for the log volume mount directory
- `ssl/.keep` — placeholder for the TLS certificate directory

Never place any of these inside `src/`.
