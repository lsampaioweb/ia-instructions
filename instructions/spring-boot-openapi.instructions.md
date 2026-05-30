---
description: "OpenAPI/Swagger rules: springdoc-openapi setup, OpenAPI bean, endpoint annotation, and profile-based UI toggle."
applyTo: "**/*OpenApiConfig*.java, **/*SwaggerConfig*.java, **/*Controller.java"
---

# OpenAPI Rules

## Dependency
Use `springdoc-openapi-starter-webmvc-ui` for API documentation. Do not use springfox.

## OpenAPI Bean
Define a single `OpenAPI` bean in a dedicated `@Configuration` class with project title, version, description, and contact information.

## Endpoint Annotations
- Annotate each controller method with `@Operation(summary = "...")` — one short sentence
- Annotate each possible response with `@ApiResponse(responseCode = "...", description = "...")`
- Annotate request body and path variable parameters with `@Parameter` only when the name alone is not self-explanatory
- Do not duplicate information already expressed by the method signature, HTTP method, or return type

## Profile Visibility
- Disable Swagger UI in the production profile:

```yaml
springdoc:
  swagger-ui:
    enabled: false
```

- Enable it in the development profile; it does not need to be accessible in production or test environments
