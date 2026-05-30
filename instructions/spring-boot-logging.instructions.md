---
description: "Logging rules: @Slf4j, i18n keys for all log messages, log level selection, and what must never be logged."
applyTo: "**/*.java"
---

# Logging Rules

- Use `@Slf4j` (Lombok) on every class that logs; never declare `private static final Logger` manually
- Never hardcode message text as string literals in log statements; all log message templates must be defined in `messages.properties` and resolved by key before being passed to the logger
- Resolve log message keys via a project-level `LogMessages` utility that calls `messageSource.getMessage(key, args, Locale.ENGLISH)` — logs are developer-facing and always in English; inject this utility via constructor

Example of what NOT to do: `log.debug("User {} not found", id)`
Example of what to do: `log.debug(logMessages.get("user.not.found", id))`

## Log Levels

| Level   | When to use |
|---------|-------------|
| `ERROR` | Unrecoverable failure; always include the exception as the last argument |
| `WARN`  | Recoverable issue; unexpected but non-fatal state |
| `INFO`  | Service started, significant business event (order created, user registered) |
| `DEBUG` | Internal state during processing; disabled in production |
| `TRACE` | High-frequency or low-level detail; disabled in all non-local environments |

## What Not to Log

- Passwords, API keys, tokens, secrets, or any masked version of them
- Full request or response bodies that may contain PII
- Stack traces at `INFO` or `DEBUG` level; reserve those for `ERROR`
- Inside tight loops or high-throughput paths without a level guard: `if (log.isDebugEnabled())`
