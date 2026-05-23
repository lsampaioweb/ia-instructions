---
description: "Use when creating or reviewing Spring Boot i18n configuration, MessageSource usage, locale resolution, or message properties files. Covers message file structure, locale resolution, MessageSourceHolder, and controller usage."
applyTo: "**/*.java"
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

## MessageSourceHolder (Static Accessor)

Use `MessageSourceHolder` to resolve messages in contexts where constructor injection is not available, such as inside domain exceptions or infrastructure utilities. Do **not** use it as a replacement for injected `MessageSource` in controllers, services, or advice — those should use constructor injection.

```java
@Component
public class MessageSourceHolder {

  private static final AtomicReference<MessageSource> ref = new AtomicReference<>();

  public MessageSourceHolder(MessageSource messageSource) {
    ref.set(messageSource);
  }

  public static String getMessage(String key, Object... args) {
    return ref.get().getMessage(key, args, LocaleContextHolder.getLocale());
  }
}
```

Do **not** call `MessageSourceHolder` from inside domain exceptions. Exceptions should store the message key and arguments; message resolution must happen at the HTTP boundary (in `@RestControllerAdvice`). See the exception-handling instructions for the correct pattern.
