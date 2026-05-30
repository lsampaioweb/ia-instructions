---
description: "Security rules: Spring Security 6 Lambda DSL, deny-by-default, @PreAuthorize, CSRF/CORS policy, secrets, and error message hygiene."
applyTo: "**/*SecurityConfig.java, **/security/**/*.java"
---

# Security Rules

## Configuration
- Use Spring Security 6+ Lambda DSL for `HttpSecurity`; never use chained `.and()` style
- Deny by default; explicitly permit only required public endpoints
- The public endpoint list must be minimal and documented in the security config class

## Authorization
- Use `@PreAuthorize` for method-level authorization on business-sensitive operations
- Never disable CSRF globally unless the service is strictly stateless and uses token-based authentication

## CORS
Configure CORS with explicit allowed origins, methods, and headers. Never use wildcard origins in production.

## Secrets
- Inject signing keys, credentials, and secrets from environment variables or an external secret store
- Never hardcode any secret in code or YAML files

## Logging and Errors
- Do not log credentials, tokens, raw authorization headers, or full security exceptions with sensitive payloads
- Return generic messages for authentication and authorization failures; never expose internal details

## Cryptography
Never implement custom cryptography. Use approved Spring Security password encoders and providers.
