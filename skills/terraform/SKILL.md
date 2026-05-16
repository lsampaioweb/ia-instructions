---
name: terraform
description: 'Create and maintain Terraform IaC with conventions. Use for module design, typed variables, outputs, provider/version pinning, and safe homelab/cloud changes.'
argument-hint: 'Module/root path, target infra, and expected plan impact'
---

# Terraform IaC

## When To Use
- Building and refactoring Terraform modules and root compositions.
- Standardizing variables, locals, outputs, and provider blocks.
- Reviewing plan safety and infrastructure drift risk.

## Style Conventions
- Apply these conventions in order: Security, Versioning, Modularity, Contracts, Formatting.
- Security: Keep environment-specific values outside module internals.
- Versioning: Pin Terraform and provider version constraints.
- Modularity: Use typed variables with clear descriptions.
- Modularity: Keep modules composable and focused.
- Contracts: Use outputs as stable contracts between modules.
- Formatting: Keep naming/tagging consistent with existing homelab conventions.

## Procedure
1. Inspect existing layout and naming before editing.
2. Implement minimal change with clear variable/input boundaries.
3. Preserve reusable module contracts.
4. Provide a detailed plan-impact summary listing resources to be created, updated, and destroyed, plus the associated risk for each change.
5. Validate formatting and basic static checks when requested.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Unpinned provider versions**: Generating `source = "hashicorp/aws"` without a `version` constraint, causing non-deterministic `terraform init`.
- **Untyped variables**: Declaring `variable "x" {}` without a `type` constraint, losing validation and documentation.
- **Secrets in defaults**: Setting `default = "admin123"` or similar on sensitive variables instead of leaving them unset and requiring injection.
- **Module internals leaking**: Hardcoding environment-specific values (IP ranges, region names) inside a module that should receive them as variables.
- **Missing outputs**: Forgetting to expose key attributes as outputs when the module will be consumed by another root/module.
- **`terraform fmt` drift**: Producing code that doesn't match canonical HCL formatting, failing CI checks.

## Guardrails
- Do not hardcode secrets or sensitive tokens in code/defaults.
- Do not duplicate existing module capabilities.
- Do not add broad lifecycle ignores without explicit justification.
