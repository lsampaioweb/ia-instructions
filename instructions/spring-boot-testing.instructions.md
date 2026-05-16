---
description: "Use when creating or reviewing Spring Boot tests. Covers slice testing with @WebMvcTest and @MybatisTest, unit tests with Mockito, test naming, and what each test scope should cover."
applyTo: "**/*Test.java"
---

# Spring Boot Testing Conventions

- Write tests at the narrowest scope that validates the behavior: unit tests for services, slice tests for controllers
- Use `@WebMvcTest` for controller tests; it loads only the web layer and does not start a full application context
- Use `@SpringBootTest` only for integration tests that require the full context; prefer slices otherwise
- Mock all dependencies of the class under test with `@MockBean` (slice tests) or `@Mock` / `@InjectMocks` (unit tests)
- Name test methods to describe behavior: `{method}_when{Condition}_should{Outcome}` (e.g. `findById_whenUserNotFound_shouldReturn404`)
- Assert on the response status, response body, and headers; do not assert on internal implementation details
- Keep each test focused on a single behavior; one assertion per test is a guideline, not a strict rule
- Do not share mutable state between tests; reset mocks in `@BeforeEach` if needed

## Controller Test Example (`@WebMvcTest`)

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

  @Autowired
  private MockMvc mockMvc;

  @MockBean
  private UserService userService;

  @Test
  void findById_whenUserExists_shouldReturn200() throws Exception {
    User user = new User(1L, "Alice", "alice@example.com");
    given(userService.findById(1L)).willReturn(Optional.of(user));

    mockMvc.perform(get("/api/v1/users/1"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.name").value("Alice"));
  }

  @Test
  void findById_whenUserNotFound_shouldReturn404() throws Exception {
    given(userService.findById(99L)).willReturn(Optional.empty());

    mockMvc.perform(get("/api/v1/users/99"))
        .andExpect(status().isNotFound());
  }

  @Test
  void delete_whenUserExists_shouldReturn204() throws Exception {
    given(userService.delete(1L)).willReturn(true);

    mockMvc.perform(delete("/api/v1/users/1"))
        .andExpect(status().isNoContent());
  }
}
```

## Service Unit Test Example (Mockito)

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

  @Mock
  private UserMapper userMapper;

  @InjectMocks
  private UserService userService;

  @Test
  void findById_whenUserExists_shouldReturnOptionalWithUser() {
    User user = new User(1L, "Alice", "alice@example.com");
    given(userMapper.findById(1L)).willReturn(user);

    Optional<User> result = userService.findById(1L);

    assertThat(result).isPresent().contains(user);
  }

  @Test
  void create_whenEmailAlreadyExists_shouldThrowUserAlreadyExistsException() {
    CreateUserRequest request = new CreateUserRequest("Alice", "alice@example.com");
    given(userMapper.existsByEmail("alice@example.com")).willReturn(true);

    assertThatThrownBy(() -> userService.create(request))
        .isInstanceOf(UserAlreadyExistsException.class);
  }
}
```
