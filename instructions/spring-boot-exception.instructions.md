---
description: "Exception handling rules: single @RestControllerAdvice, domain exception hierarchy, ErrorResponse DTO, and stacktrace policy."
applyTo: "**/*Exception.java, **/*ControllerAdvice.java, **/*ExceptionHandler.java, **/*ErrorResponse.java"
---

# Exception Handling Rules

## @RestControllerAdvice
- One single `@RestControllerAdvice` class handles all exceptions for the entire application
- Always include a catch-all `@ExceptionHandler(Exception.class)` handler mapped to HTTP 500
- Handle `MethodArgumentNotValidException` separately; return a list of `{field, message}` records — one per validation failure — not a single `ErrorResponse`
- Never expose stack traces by default; set `server.error.include-stacktrace: "never"` in `application.yml` and `server.error.include-stacktrace: "always"` in `application-development.yml`

## Domain Exceptions
- All domain exceptions extend a shared abstract base class that extends `RuntimeException`
- The base class stores three fields: `String messageKey`, `Object[] args`, `HttpStatus status`
- Exception constructors must never contain hardcoded message text; always pass an i18n key as `messageKey`
- Domain exceptions pass key, args, and status to the base constructor; message text resolution happens in `@RestControllerAdvice` using `MessageSource` and the request locale from `LocaleContextHolder.getLocale()`
- Exceptions are plain data holders; do not call `MessageSource` or any Spring infrastructure from inside an exception constructor

## ErrorResponse
- Every exception handler (except the `MethodArgumentNotValidException` handler) returns the same `ErrorResponse` DTO
- `ErrorResponse` fields: `timestamp` (LocalDateTime), `status` (int), `error` (HTTP reason phrase), `message` (resolved i18n string), `path` (request URI), `trace` (stack trace string, null when not exposed)
- The `message` field is always locale-aware; the same exception may return different text depending on the `Accept-Language` header
