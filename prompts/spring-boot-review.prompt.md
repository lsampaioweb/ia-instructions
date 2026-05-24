---
name: "spring-boot-review"
description: "Review Spring Boot code against repository conventions for architecture, controllers, services, visibility, and HTTP patterns. Use when doing a strict code review or refactoring review."
argument-hint: "Files, package, or feature to review and any specific concern"
agent: "agent"
---

# Spring Boot Review

Review the provided Spring Boot code using these conventions:

Follow all conventions from:

<!-- Path traverses 6 levels up from ~/.config/Code/User/profiles/HASH/prompts/ to reach ~/ -->
- [Spring Boot Skill](./../../../../../../.copilot/skills/spring-boot/SKILL.md)

## Output

Return:

1. A short findings list ordered by severity.
2. The exact rule or convention being violated for each finding.
3. If refactoring is needed, provide the corrected implementation for the first affected layer only, then stop and wait for confirmation.

Do not invent repository standards beyond the linked files. If the code is acceptable, say so clearly and list only the remaining risks or tradeoffs.
