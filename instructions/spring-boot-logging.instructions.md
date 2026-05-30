---
description: "Logging rules: @Slf4j, i18n keys for all log messages, log level selection, and what must never be logged."
applyTo: "**/*.java"
---

# Logging Rules

- Use `@Slf4j` (Lombok) on every class that logs; never declare `private static final Logger` manually
- Never hardcode message text as string literals in log statements; all log message templates must be defined in `messages.properties` and resolved by key before being passed to the logger
- Resolve log message keys via a project-level `LogMessages` utility; inject it via constructor
- Implement `LogMessages` with two overloads so callers never specify a locale — the no-locale overload delegates to the locale-aware one with `Locale.ENGLISH`; logs are always developer-facing and always in English:

```java
public String get(String key, Object... args) {
  return get(Locale.ENGLISH, key, args);
}

public String get(Locale locale, String key, Object... args) {
  return messageSource.getMessage(key, args, locale);
}
```

Example of what NOT to do: `log.debug("User {} not found", id)`
Example of what to do: `log.debug(logMessages.get(LOG_USER_NOT_FOUND, id))`

See `spring-boot-architecture.instructions.md` for the constant naming rule.

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

## Logback Configuration
Place `logback-spring.xml` under `src/main/resources/log/`. Configure profile-based appenders:

- `development` profile: console appender + async file appender at `DEBUG` level
- `production` and default profiles: async file appender only at `INFO` level

File rotation settings:
- `maxFileSize`: 100MB
- `totalSizeCap`: 1GB
- `maxHistory`: 1 (days to retain)
