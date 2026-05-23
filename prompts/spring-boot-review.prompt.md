---
name: "spring-boot-review"
description: "Review Spring Boot code against repository conventions for architecture, controllers, services, visibility, and HTTP patterns. Use when doing a strict code review or refactoring review."
argument-hint: "Files, package, or feature to review and any specific concern"
agent: "agent"
---

# Spring Boot Review

Review the provided Spring Boot code using these conventions:

- [Behavior Baseline](~/.copilot/instructions/copilot-behavior.instructions.md)
- [Spring Boot Skill](~/.copilot/skills/spring-boot/SKILL.md)

Use the Spring Boot skill as the canonical source for all Spring-specific rules and linked instruction files.

Perform a strict review.

## Output

Return:

1. A short findings list ordered by severity.
2. The exact rule or convention being violated for each finding.
3. If refactoring is needed, provide the corrected implementation for the first affected layer only, then stop and wait for confirmation.

Do not invent repository standards beyond the linked files. If the code is acceptable, say so clearly and list only the remaining risks or tradeoffs.
