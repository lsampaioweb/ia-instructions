---
description: "REST controller rules: no business logic, @Valid inputs, DTO responses, API versioning, and HTTP status conventions."
applyTo: "**/*Controller.java"
---

# Controller Rules

- Annotate with `@RestController`
- Declare the full versioned base path at class level: `@RequestMapping("/api/v1/resource-name")` using lowercase plural resource names
- Method annotations use only the path suffix relative to the class mapping: `@GetMapping("/")`, `@GetMapping("/{id}")`, `@PostMapping("/")` — never repeat the base path in method annotations
- No business logic; delegate all processing to the service layer
- Do not call mapper classes or integration clients directly
- Accept input via `@RequestBody` or `@PathVariable`; always annotate request body objects with `@Valid`
- Return DTOs only; never return domain objects directly
- Use specific mapping annotations: `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
- HTTP status discipline: 200 for successful reads, 201 for creation via `ResponseEntity`, 204 for no-content, 400 for validation errors, 404 for not found, 500 for unexpected errors
- Keep handler methods `public`; keep helper methods `private`

## API Versioning
- Use URL path versioning: `/api/v1/...`
- The version segment belongs on the class-level `@RequestMapping`, never on individual method annotations
- When introducing a new incompatible version, create a new controller class (e.g. `UserV2Controller`) with `@RequestMapping("/api/v2/users")`; do not modify the existing controller
