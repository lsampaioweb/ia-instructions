---
description: "HTTP client rules: RestClient setup, configuration, and usage patterns for calling external APIs."
applyTo: "**/*Client.java, **/*ApiClient.java, **/*HttpClient.java"
---

# HTTP Client Rules

## RestClient
Use `RestClient` (Spring 6.1+) as the HTTP client for all outbound API calls. Do not use `RestTemplate` or third-party HTTP clients unless the project requires a specific capability that `RestClient` cannot provide.

## Configuration
- Declare external API base URLs in `application.yml` under a dedicated key group (e.g. `external.api.*`) and bind them with `@ConfigurationProperties` or `@Value`
- Inject `RestClient.Builder` via constructor; do not instantiate `RestClient` directly
- Initialize the `RestClient` instance in a `@PostConstruct` method using `restClientBuilder.baseUrl(url).build()`

```java
@Service
class UserApiClient {
  private final RestClient.Builder restClientBuilder;
  private RestClient restClient;

  @Value("${external.api.users}")
  private String usersUrl;

  UserApiClient(RestClient.Builder restClientBuilder) {
    this.restClientBuilder = restClientBuilder;
  }

  @PostConstruct
  private void init() {
    this.restClient = restClientBuilder.baseUrl(usersUrl).build();
  }
}
```

## Usage
- Return `Optional.ofNullable(result)` for single-resource responses that may be absent
- Use `.retrieve().body(Type.class)` for typed responses
- Use `.retrieve().toBodilessEntity()` for responses with no body (e.g. DELETE)
- Set `Content-Type` explicitly on requests with a body: `.contentType(MediaType.APPLICATION_JSON)`

## Error Handling
Catch `RestClientException` and its subtypes when you need to translate HTTP errors into domain exceptions. Let unexpected exceptions propagate to `@RestControllerAdvice`.
