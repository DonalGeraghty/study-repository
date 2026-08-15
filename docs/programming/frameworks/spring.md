# Spring and Spring Boot

Spring is a Java application framework centred on dependency injection and composable infrastructure. Spring Boot builds on Spring with dependency starters, auto-configuration, executable applications, production features, and opinionated defaults.

## Application Context and Beans

The Spring container creates and connects managed objects called beans. Prefer constructor injection so dependencies are explicit and instances can be valid after construction.

```java
@Service
public class ResultService {
    private final ResultRepository repository;

    public ResultService(ResultRepository repository) {
        this.repository = repository;
    }
}
```

Component scanning discovers annotated types, while `@Bean` methods register objects explicitly. Keep the application class in a sensible root package and avoid scanning unrelated packages.

Spring beans are singleton-scoped by default. Singleton does not mean thread-safe: avoid shared mutable request state inside singleton services.

## Bootstrapping and Auto-Configuration

`@SpringBootApplication` combines configuration, component scanning, and auto-configuration. Auto-configuration reacts to classpath contents, properties, and existing beans; it normally backs away when an application supplies its own configuration.

```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Treat auto-configuration as inspectable behaviour, not magic. Use condition reports, startup logs, and configuration metadata when the context differs from expectations.

## Configuration

Spring Boot can read configuration from files, environment variables, system properties, and command-line arguments with defined precedence. Bind related settings to validated configuration objects:

```java
@ConfigurationProperties("clients.catalogue")
@Validated
public record CatalogueProperties(
        @NotBlank URI baseUrl,
        @Positive Duration timeout) {}
```

Use profiles sparingly for cohesive environment differences. Do not store credentials in ordinary configuration files; inject them through an appropriate secret system and prevent them from appearing in logs or actuator output.

## Web APIs

Spring MVC uses a servlet-based execution model; WebFlux supports reactive, non-blocking pipelines. Do not select WebFlux merely because an application calls remote services—its value depends on an end-to-end workload and library model that benefits from non-blocking I/O.

```java
@RestController
@RequestMapping("/results")
class ResultController {
    @GetMapping("/{id}")
    ResultResponse get(@PathVariable long id) {
        return service.find(id);
    }
}
```

Keep transport DTOs separate from persistence entities where their contracts evolve differently. Validate request data, map errors consistently, enforce authorisation at the correct boundary, and avoid leaking implementation exceptions to clients.

## Persistence and Transactions

Spring Data can reduce repository boilerplate, but database semantics still matter. Understand generated queries, fetch strategies, transaction boundaries, locks, indexes, and the number of round trips.

`@Transactional` is commonly applied through proxies. Self-invocation and calls outside the managed proxy can therefore bypass advice. Keep transactions short, avoid remote calls inside them, and verify rollback rules for checked and unchecked failures.

## Observability and Operations

Spring Boot Actuator exposes operational endpoints for health, metrics, information, and diagnostics. Expose only what is needed, secure management access, and distinguish liveness from readiness.

Use structured logs, metrics, and traces with stable domain context. Never place secrets or uncontrolled high-cardinality values into telemetry.

## Testing Strategy

Choose the narrowest test that proves the behaviour:

| Scope | Typical approach |
| --- | --- |
| Pure domain logic | Plain JUnit test, no Spring context |
| Web mapping and validation | Focused web slice |
| Persistence mapping and query | Focused data slice with real engine where important |
| Wiring and configuration | Context test |
| Full boundary behaviour | `@SpringBootTest`, optionally with a real server |

Context caching makes repeated compatible configurations cheaper. Excessive mock replacement and unique properties fragment the cache and slow the suite. Use Testcontainers or another production-like dependency when in-memory substitutes conceal important database or messaging behaviour.

```bash
./mvnw verify
# or
./gradlew check
```

## Common Failure Modes

- circular dependencies caused by unclear responsibilities;
- field injection hiding required collaborators;
- business logic trapped in controllers or repository callbacks;
- lazy-loading failures outside transaction boundaries;
- configuration that differs silently across profiles;
- loading the full context for every test;
- retrying non-idempotent operations without a safety model.

## Readiness Checklist

You should be able to:

- explain beans, scopes, dependency injection, proxies, and auto-configuration;
- bind and validate external configuration safely;
- design stable HTTP boundaries and error responses;
- place transaction boundaries deliberately and diagnose query behaviour;
- select a focused test scope rather than always loading the application;
- expose health and diagnostics without leaking sensitive data;
- diagnose startup and runtime problems from conditions, logs, metrics, and traces.

## Official References

- [Spring Framework reference](https://docs.spring.io/spring-framework/reference/)
- [Spring Boot reference](https://docs.spring.io/spring-boot/reference/)
- [Spring Data](https://spring.io/projects/spring-data)
- [Testing Spring Boot applications](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html)

Return to [Frameworks and Libraries](./README.md).
