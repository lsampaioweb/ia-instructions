---
name: traefik
description: 'Design and review Traefik reverse-proxy and load-balancer configuration using conventions. Use for host-based routing, TLS, internal service exposure, and multi-instance balancing.'
argument-hint: 'Domain/subdomain plan, services, and network topology'
---

# Traefik Reverse Proxy

## When To Use
- Creating or reviewing Traefik routing and TLS configuration.
- Exposing APIs and dashboards through internal homelab domains.
- Validating load-balancing behavior for multi-instance services.
- Hardening reverse-proxy boundaries.

## Style Conventions
- Apply these conventions in order: Exposure Boundaries, Routing, Service Reachability, Transport Security.
- Use Traefik as the main reverse proxy/load balancer.
- Prefer host-based routing on internal domains.
- Exposure Boundaries: Ensure all public host exposure is managed exclusively through Traefik entry points.
- Keep internal services reachable by service/container name on private network.
- Avoid exposing internal app ports directly to host/internet.
- Use TLS for routed endpoints.

## Procedure
1. Confirm desired service entry points and which ones should be externally reachable.
2. Define router rules by host and map to the correct service port.
3. Keep internal-only services without Traefik exposure labels.
4. Validate that only intended endpoints are reachable through Traefik.
5. Check compatibility with multi-instance service naming and balancing.

## Known Failure Modes
These are patterns that tend to reappear even when the rules are known. Check for each one before producing output:

- **Direct port exposure alongside Traefik labels**: Adding both `ports:` and Traefik router labels for the same service, bypassing the proxy for direct access.
- **`Host` rule with wrong domain format**: Writing `Host(example.com)` instead of `` Host(`example.com`) `` (backtick syntax required).
- **HTTP router without redirect to HTTPS**: Creating a plain HTTP router without a redirect middleware when TLS is available.
- **Internal service given public entrypoint**: Assigning `websecure` entrypoint to a service that should only be reachable internally.
- **Missing TLS configuration on routed endpoint**: Defining a router with a domain but omitting `tls: {}` or the certificate resolver.

## Guardrails
- Do not bypass Traefik with ad-hoc host port mappings for app services.
- Do not mix external/public and internal/private routing carelessly.
- Do not introduce routing that breaks service-to-service internal DNS patterns.
