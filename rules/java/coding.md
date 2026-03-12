---
paths:
  - "**/*.java"
  - "**/pom.xml"
  - "**/build.gradle"
  - "**/build.gradle.kts"
---
# Java Development Rules

> Extends CLAUDE.md with Java/Spring specific standards.

## Logging

- **slf4j**: Use `{}` placeholders, NEVER string concatenation
  ```java
  // ✅ Good
  log.info("User {} logged in from {}", userId, ipAddress);
  log.error("Failed to process order {}", orderId, exception);

  // ❌ Bad
  log.info("User " + userId + " logged in from " + ipAddress);
  ```
- **Log levels**: ERROR(system failure) > WARN(recoverable issue) > INFO(business event) > DEBUG(troubleshooting)
- **Sensitive data**: NEVER log passwords, tokens, PII, credit card numbers
- **Exception logging**: Always pass exception as last argument to `log.error()`/`log.warn()`


## Exception Handling

- **Specific exceptions**: Catch specific types, never bare `catch (Exception e)` unless at top-level handler
- **Meaningful messages**: Include context (what operation, what input, what went wrong)
- **Don't swallow**: Never empty catch blocks; at minimum log the error
- **Custom exceptions**: Extend `RuntimeException` for business errors, include error codes
- **Checked vs Unchecked**: Prefer unchecked (Runtime) for business logic; checked only for recoverable I/O

```java
// ✅ Good
try {
    orderService.placeOrder(order);
} catch (InsufficientStockException e) {
    log.warn("库存不足, orderId={}: {}", order.getId(), e.getMessage());
    throw new BusinessException(ErrorCode.STOCK_INSUFFICIENT, e);
}

// ❌ Bad
try {
    orderService.placeOrder(order);
} catch (Exception e) {
    e.printStackTrace();
}
```


## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Class | `UpperCamelCase` | `UserService`, `OrderController` |
| Method/Variable | `lowerCamelCase` | `findUserById`, `orderCount` |
| Constant | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` |
| Package | `lowercase` | `com.example.service` |
| Boolean | `is`/`has`/`can` prefix | `isActive`, `hasPermission` |
| Collection | plural | `users`, `orderItems` |


## Spring Patterns

- **Constructor injection**: ALWAYS prefer over `@Autowired` field injection
  ```java
  // ✅ Good — use @RequiredArgsConstructor or explicit constructor
  @RequiredArgsConstructor
  public class UserService {
      private final UserRepository userRepository;
      private final PasswordEncoder passwordEncoder;
  }
  ```
- **@Transactional**: Only on public methods; use `readOnly = true` for queries
- **Validation**: Use `@Valid`/`@Validated` at controller layer, JSR-303 annotations on DTOs
- **DTO/Entity separation**: Never expose JPA entities directly in API responses
- **Configuration**: Use `@ConfigurationProperties` over `@Value` for grouped config


## Concurrency

- **Thread safety**: Use `synchronized`, `volatile`, `AtomicXxx`, or `ConcurrentHashMap`
- **Thread pools**: Use `ExecutorService`, never `new Thread()` directly
  ```java
  // ✅ Good
  ExecutorService executor = Executors.newFixedThreadPool(10);
  executor.submit(() -> processTask());
  executor.shutdown();

  // ❌ Bad
  new Thread(() -> processTask()).start();
  ```
- **Locks**: Prefer `ReentrantLock` over `synchronized` for complex scenarios
- **Immutability**: Use `final` fields, `Collections.unmodifiableXxx()`, or immutable collections


## Stream API

- **Prefer streams**: For filtering, mapping, reducing collections
- **Avoid side effects**: Don't modify external state in stream operations
- **Parallel streams**: Only for CPU-intensive tasks with large datasets (>10k elements)
  ```java
  // ✅ Good
  List<String> names = users.stream()
      .filter(User::isActive)
      .map(User::getName)
      .collect(Collectors.toList());

  // ❌ Bad - side effect
  users.stream().forEach(u -> externalList.add(u.getName()));
  ```


## JPA/Hibernate

- **N+1 queries**: Use `@EntityGraph` or `JOIN FETCH` to avoid
  ```java
  // ✅ Good
  @Query("SELECT u FROM User u JOIN FETCH u.orders WHERE u.id = :id")
  User findByIdWithOrders(@Param("id") Long id);

  // ❌ Bad - triggers N+1
  User user = userRepository.findById(id);
  user.getOrders().size(); // Lazy load triggers N queries
  ```
- **Batch operations**: Use `@BatchSize` or native batch inserts for bulk operations
- **Lazy loading**: Be aware of `LazyInitializationException` outside transaction scope
- **Projections**: Use DTO projections for read-only queries instead of full entities


## Common Anti-Patterns

- 🔴 `Optional.get()` without check → Use `orElseThrow()`/`orElse()`/`map()`
- 🔴 Mutable shared state → Use immutable objects or thread-safe collections
- 🔴 String concatenation in loops → Use `StringBuilder`
- 🔴 `new Date()` / `Calendar` → Use `java.time` API (`LocalDateTime`, `Instant`)
- 🔴 Raw types (`List` instead of `List<User>`) → Always parameterize generics
- 🔴 God class (>500 lines) → Split by responsibility
- 🔴 `@Autowired` on field → Constructor injection
- 🔴 Business logic in Controller → Move to Service layer
