---
description: "Use when creating or reviewing Spring Boot security configuration, authentication, authorization, CORS, and endpoint protection. Covers safe defaults, secrets, and public endpoint exposure."
applyTo: "**/{*Security*,*Auth*,*Authorization*,*Jwt*,*Filter*}.java"
---

# Spring Boot Security Conventions

- Keep security rules centralized in dedicated security configuration classes
- Use Spring Security 6+ Lambda DSL for `HttpSecurity` configuration; avoid chained `.and()`-style configurers
- Deny by default; explicitly allow only required public endpoints
- Use method-level authorization (`@PreAuthorize`) for business-sensitive operations
- Never disable CSRF globally unless the service is strictly stateless and token-based
- Configure CORS with explicit origins, methods, and headers; never use wildcard origins in production
- Keep secrets and signing keys out of source code; read from environment variables or external secret stores
- Do not log credentials, tokens, raw authorization headers, or full security exceptions with sensitive payloads
- Return generic authentication/authorization error messages; do not leak internals
- Keep password handling delegated to approved encoders/providers; never implement custom crypto

## Baseline Checks

- Authentication required for non-public endpoints
- Authorization checked on admin or cross-tenant operations
- Public endpoint list is minimal and documented in config
- No hardcoded keys, passwords, or tokens in code or YAML

## SecurityFilterChain Example

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

  @Bean
  public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
        .csrf(csrf -> csrf.disable())
        .cors(cors -> cors.configurationSource(corsConfigurationSource()))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/v1/auth/**", "/actuator/health").permitAll()
            .anyRequest().authenticated()
        )
        .sessionManagement(session -> session
            .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
        );
    return http.build();
  }

  @Bean
  public CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowedOrigins(List.of("${ALLOWED_ORIGIN}"));
    config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
    config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", config);
    return source;
  }
}
```

## @PreAuthorize Examples

```java
// Role-based access control
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

// Combined role and ownership check
@PreAuthorize("hasRole('USER') and #userId == authentication.principal.id")
public UserResponse findById(Long userId) { ... }
```
