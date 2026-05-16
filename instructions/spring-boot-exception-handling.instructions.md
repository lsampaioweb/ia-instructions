---
description: "Use when creating or reviewing Spring Boot exception handling, custom exceptions, ErrorResponse structure, or @RestControllerAdvice. Covers centralized error handling, domain exceptions, stacktrace exposure, and validation errors."
applyTo: "**/{*Exception,*Advice,*ErrorResponse}.java"
---

# Spring Boot Exception Handling Conventions

- Centralize all exception handling in a single `@RestControllerAdvice` class
- Embed the HTTP status inside each domain exception; do not hardcode status codes in the advice
- Always include a catch-all `@ExceptionHandler(Exception.class)` mapped to HTTP 500
- Return a consistent `ErrorResponse` DTO from every exception handler
- Control stacktrace exposure via `server.error.include-stacktrace` in `application.yml`; never expose it by default, only in dev profiles
- Handle `MethodArgumentNotValidException` separately and return field-level validation errors inside `ErrorResponse`

## Custom Exceptions

- All domain exceptions extend a shared abstract base class that extends `RuntimeException`
- Each exception stores a message key (String), arguments (Object[]), and an `HttpStatus` as private fields
- Domain-specific exceptions pass the key, arguments, and status; message resolution happens in `@RestControllerAdvice`
- Exceptions are pure data holders with no Spring/MessageSource dependencies

## ErrorResponse Structure

```java
@Data
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ErrorResponse {
  private LocalDateTime timestamp;
  private int status;
  private String error;
  private String message;
  private String path;
  private String trace;
  private List<FieldViolation> fieldErrors;

  public record FieldViolation(String field, String message) {}
}
```

## Exception Hierarchy Example

```java
// Shared base class
public abstract class CustomException extends RuntimeException {
  private final String messageKey;
  private final Object[] args;
  private final HttpStatus httpStatus;

  protected CustomException(String messageKey, Object[] args, HttpStatus httpStatus) {
    super(messageKey); // Store key as message for logging
    this.messageKey = messageKey;
    this.args = args != null ? args : new Object[0];
    this.httpStatus = httpStatus;
  }

  public String getMessageKey() { return messageKey; }
  public Object[] getArgs() { return args; }
  public HttpStatus getHttpStatus() { return httpStatus; }
}

// Domain-specific exceptions — each carries its own HTTP status
public class UserNotFoundException extends CustomException {
  public UserNotFoundException(Long id) {
    super("user.notfound", new Object[] { id }, HttpStatus.NOT_FOUND);
  }
}

public class UserAlreadyExistsException extends CustomException {
  public UserAlreadyExistsException(String name, String email) {
    super("user.exists", new Object[] { name, email }, HttpStatus.CONFLICT);
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
  public ResponseEntity<ErrorResponse> handleCustomException(CustomException ex, HttpServletRequest request) {
    String localizedMessage = messageSource.getMessage(
        ex.getMessageKey(),
        ex.getArgs(),
        LocaleContextHolder.getLocale()
    );
    ErrorResponse body = newErrorResponse(localizedMessage, request, ex.getHttpStatus(), ex);
    return ResponseEntity.status(ex.getHttpStatus()).body(body);
  }

  @ExceptionHandler(Exception.class)
  @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
  public ErrorResponse handleGeneric(Exception ex, HttpServletRequest request) {
    return newErrorResponse("An unexpected error occurred", request, HttpStatus.INTERNAL_SERVER_ERROR, ex);
  }

  @ExceptionHandler(MethodArgumentNotValidException.class)
  @ResponseStatus(HttpStatus.BAD_REQUEST)
  public ErrorResponse handleValidation(MethodArgumentNotValidException ex, HttpServletRequest request) {
    ErrorResponse body = newErrorResponse("Validation failed", request, HttpStatus.BAD_REQUEST, ex);
    body.setFieldErrors(ex.getFieldErrors().stream()
        .map(fe -> new ErrorResponse.FieldViolation(fe.getField(), fe.getDefaultMessage()))
        .toList());
    return body;
  }

  private ErrorResponse newErrorResponse(String message, HttpServletRequest request, HttpStatus status, Exception ex) {
    ErrorResponse response = new ErrorResponse();
    response.setTimestamp(LocalDateTime.now());
    response.setStatus(status.value());
    response.setError(status.getReasonPhrase());
    response.setMessage(message);
    response.setPath(request.getRequestURI());
    if (Arrays.asList(environment.getActiveProfiles()).contains("dev")) {
      response.setTrace(Arrays.toString(ex.getStackTrace()));
    }
    return response;
  }
}
```
