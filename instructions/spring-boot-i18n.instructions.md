---
description: "Use when creating or reviewing Spring Boot i18n configuration, MessageSource usage, locale resolution, or message properties files. Covers message file structure, locale resolution, MessageSourceHolder, and controller usage."
applyTo: "**/{*MessageSource*,*LocaleResolver*,*i18n*}.java"
---

# Spring Boot i18n Conventions

- Store all message files under `resources/i18n/`
- Configure the basename in `application.yml`:

```yaml
spring:
  messages:
    basename: "i18n/messages"
```

- Provide at least `messages.properties` (default/English) and `messages_pt_BR.properties`
- Use `MessageSource` with constructor injection to resolve messages in controllers and services
- Resolve the current locale with `LocaleContextHolder.getLocale()` via a private helper method
- Resolve locale from the `Accept-Language` request header; this is the preferred mechanism
- The `?lang=` URL query parameter is supported as a convenience for testing only; do not rely on it in production as it can cause cache-poisoning in reverse proxies
- Register the custom `LocaleResolver` as a `@Bean` inside a `@Configuration` class

## Controller Usage Example

```java
@RestController
public class GreetingController {

  private final MessageSource messageSource;

  public GreetingController(MessageSource messageSource) {
    this.messageSource = messageSource;
  }

  @GetMapping("/greet")
  public ResponseEntity<String> greet(@RequestParam(defaultValue = "User") String name) {
    String message = messageSource.getMessage("greeting.message", new Object[] { name }, getLocale());
    return ResponseEntity.ok(message);
  }

  private Locale getLocale() {
    return LocaleContextHolder.getLocale();
  }
}
```

## Message File Example

```properties
# messages.properties
greeting.message=Hello, {0}!
user.notfound=User with id "{0}" was not found.
user.exists=User with name "{0}" and email "{1}" already exists.
method.called=The method "{0}" was called.
```
