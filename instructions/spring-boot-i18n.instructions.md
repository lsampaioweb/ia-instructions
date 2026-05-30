---
description: "i18n rules: message file layout, English+pt_BR required, locale resolution via Accept-Language header only."
applyTo: "**/i18n/**, **/messages*.properties, **/*LocaleConfig.java"
---

# i18n Rules

## File Layout
- Store all message files under `src/main/resources/i18n/`
- `messages.properties` (English) and `messages_pt_BR.properties` (Portuguese) are required in every project; additional locales are optional

## Configuration
Configure the basename in `application.yml`:

```yaml
spring:
  messages:
    basename: "i18n/messages"
```

## Usage
- Inject `MessageSource` via constructor; never use field injection
- Resolve the current locale with `LocaleContextHolder.getLocale()` via a private helper method
- Resolve locale from the `Accept-Language` request header; this is the only supported locale detection mechanism
- Browsers send `Accept-Language` automatically based on user OS/browser settings; mobile apps set it programmatically; for manual testing use curl with `-H "Accept-Language: pt-BR"`

## Locale Resolution
- Register `LocaleResolver` as a `@Bean` inside a dedicated `@Configuration` class configured to read the `Accept-Language` header
- Do not support `?lang=` query parameter; the header mechanism is sufficient for all environments including testing
