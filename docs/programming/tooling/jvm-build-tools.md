# Maven and Gradle

Maven and Gradle resolve JVM dependencies, compile source, run tests, package applications, and coordinate plugins. A build file is executable delivery configuration: review it with the same care as application code.

## Maven

Maven projects use a `pom.xml`, conventional source directories, dependency scopes, plugins, and a lifecycle such as:

```text
validate -> compile -> test -> package -> verify -> install -> deploy
```

Use `mvn verify` when the build's verification plugins should run, not only `mvn test`. Keep compiler settings internally consistent; conflicting Java properties and compiler-plugin targets make the effective build difficult to predict.

## Gradle

Gradle projects define tasks and dependencies using Groovy or Kotlin DSL build files. Execute lifecycle tasks such as `check`, `build`, or Android variant tasks instead of coupling automation to internal task details.

Prefer the Gradle Wrapper in a repository so developers and CI use the intended version. If a project intentionally relies on a CI-installed Gradle version, document and pin that choice clearly.

## Generated Code and Annotation Processing

Build-time tools such as Lombok generate or transform Java members during compilation. They can reduce repetition but also make source behaviour depend on compiler plugins and IDE support. Keep generated behaviour simple, pin the processor, verify it in CI, and prefer explicit code when generation obscures ownership or invariants.

## Reproducibility and Security

- Pin plugins and important build-tool versions.
- Use trusted repositories and review new dependencies.
- Cache downloaded dependencies without treating a cache as the source of truth.
- Keep secrets out of build files and logs.
- Separate compile, test, packaging, signing, and publishing permissions.
- Build release artifacts once and promote the same verified artifact.

## Project Connections

The Java puzzle and API-test repositories use Maven. The Spring API uses the Maven Wrapper and Spring Boot plugin. Aurelius uses Gradle Kotlin DSL with the Android Gradle Plugin and CI-supplied release-signing values.

## Related Guides

- [Java](../languages/java/README.md)
- [Kotlin](../languages/kotlin.md)
- [Android](../platforms/android.md)
- [GitHub Actions](../../platform-engineering/ci-cd/github-actions.md)

Return to [Development Tooling](./README.md).
