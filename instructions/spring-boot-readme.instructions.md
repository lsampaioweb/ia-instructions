---
description: "README structure rules: recommended sections and no-filler-prose policy for Spring Boot project documentation."
applyTo: "**/README.md"
---

# Spring Boot README Guidelines

These sections are recommended for Spring Boot project READMEs. Include the ones that are relevant; omit sections that do not apply to the project.

Recommended sections (in suggested order):
1. **Overview** — what the service does and its role in the system
2. **Prerequisites** — required tools, Java version, and environment setup
3. **Running locally** — steps to start the application
4. **Environment variables** — table of required and optional variables with descriptions
5. **API summary** — endpoints, HTTP method, brief description, and required auth

Rules:
- No filler prose, marketing language, or section padding
- Document every required environment variable when the Environment variables section is present
- The API summary is a quick reference; it is not a replacement for full API documentation
