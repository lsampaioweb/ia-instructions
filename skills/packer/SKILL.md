---
name: packer
description: 'Build and maintain Packer templates with conventions. Use for reproducible VM images, deterministic provisioning, variable hygiene, and build pipeline consistency.'
argument-hint: 'Template path, image purpose, and target platform'
---

# Packer Templates

## When To Use
- Creating and refactoring Packer templates.
- Standardizing image build workflows for homelab environments.
- Improving reproducibility and build reliability.

## Style Conventions
- Apply these conventions in order: Compatibility, Security, Determinism, Maintainability.
- Prefer HCL2 templates.
- Compatibility: Separate base image creation and customization into distinct templates or clearly separated sections, without overlapping purposes.
- Keep variables explicit and environment overrides external.
- Reuse shared provisioning scripts where possible.
- Keep provisioning steps deterministic and rerunnable.

## Procedure
1. Read existing builder/provisioner patterns before editing.
2. Apply only the changes necessary for the intended functionality, avoiding unrelated modifications to sources, builds, and variables.
3. Preserve compatibility with downstream Terraform/Ansible workflows.
4. Keep credentials externalized via variables/environment.
5. Validate output naming and artifact expectations.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **JSON templates instead of HCL2**: Generating `.json` Packer templates when the project standard is `.pkr.hcl`.
- **Hardcoded credentials in sources**: Embedding username/password/token directly in the `source` block instead of referencing `var.` variables.
- **Order-dependent provisioners without guards**: Adding shell provisioners that assume a previous step succeeded without an idempotency check.
- **Mixed platform logic in one template**: Placing Ubuntu and a different distro build side by side in one file instead of separate templates.
- **Missing `packer_cache_dir` / artifact naming**: Omitting output artifact naming conventions, causing collisions in multi-template repos.

## Guardrails
- Do not embed secrets in templates.
- Do not add fragile order-dependent script chains without checks.
- Do not mix unrelated platform logic in one template.
