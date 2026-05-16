---
description: "Use when creating or reviewing Spring Boot exception handling, custom exceptions, ErrorResponse structure, or @RestControllerAdvice. Covers centralized error handling, domain exceptions, stacktrace exposure, and validation errors."
applyTo: "**/*Exception.java,**/*Advice.java,**/*ErrorResponse.java"
---

# Spring Boot Exception Handling Conventions

- Centralize all exception handling in a single `@RestControllerAdvice` class
- Map each domain exception to a specific HTTP status using `@ExceptionHandler` and `@ResponseStatus`
- Always include a catch-all `@ExceptionHandler(Exception.class)` mapped to HTTP 500
- Return a consistent `ErrorResponse` DTO from every exception handler
- Control stacktrace exposure via `server.error.include-stacktrace` in `application.yml`; never expose it by default, only in dev profiles
- Handle `MethodArgumentNotValidException` separately and return a list of field-level validation errors

## Custom Exceptions

- All domain exceptions extend a shared abstract base class that extends `RuntimeException`
- Each exception stores a message key (String) and arguments (Object[]) as private fields
- Domain-specific exceptions pass only the key and arguments; message resolution happens in `@RestControllerAdvice`
- Exceptions are pure data holders with no Spring/MessageSource dependencies

## ErrorResponse Structure

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class ErrorResponse {
  private LocalDateTime timestamp;
  private int status;
  private String error;
  private String message;
  private String path;
  private String trace;
}
```

## Exception Hierarchy Example

```java
// Shared base class
public abstract class CustomException extends RuntimeException {
  private final String messageKey;
  private final Object[] args;

  protected CustomException(String messageKey, Object[] args) {
    super(messageKey); // Store key as message for logging
    this.messageKey = messageKey;
    this.args = args;
  }

  public String getMessageKey() { return messageKey; }
  public Object[] getArgs() { return args; }
}

// Domain-specific exceptions
public class UserNotFoundException extends CustomException {
  public UserNotFoundException(Long id) {
    super("user.notfound", new Object[] { id });
  }
}
```

## Advice Example

```java
@RestControllerAdvice
public class ExceptionHandlingAdvice {

  private final MessageSource messageSource;
  private final Environment environment;

  public ExceptionHandlingAdvice(MessageSource messageSource, Environment environment) {
    this.messageSource = messageSource;
    this.environment = environment;
  }

  @ExceptionHandler(CustomException.class)
  @ResponseStatus(HttpStatus.NOT_FOUND) // or BAD_REQUEST, etc. depending on exception type
  public ErrorResponse handleCustomException(CustomException ex, HttpServletRequest request) {
    // Resolve message key + args to localized text
    String localizedMessage = messageSource.getMessage(
        ex.getMessageKey(),
        ex.getArgs(),
        LocaleContextHolder.getLocale()
    );
    return newErrorResponse(localizedMessage, request, HttpStatus.NOT_FOUND, ex);
  }

  @ExceptionHandler(Exception.class)
  @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
  public ErrorResponse handleGeneric(Exception ex, HttpServletRequest request) {
    return newErrorResponse("An unexpected error occurred", request, HttpStatus.INTERNAL_SERVER_ERROR, ex);
  }

  @ExceptionHandler(MethodArgumentNotValidException.class)
  public ResponseEntity<Object> handleValidation(MethodArgumentNotValidException ex) {
    return ResponseEntity.badRequest()
        .body(ex.getFieldErrors().stream().map(FieldsWithError::new).toList());
  }

  private record FieldsWithError(String field, String message) {
    public FieldsWithError(FieldError error) {
      this(error.getField(), error.getDefaultMessage());
    }
  }
}
```
