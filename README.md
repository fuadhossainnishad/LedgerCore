# LedgerCore

LedgerCore is a Java financial transaction and double-entry ledger platform. The current increment establishes a minimal Spring Boot application shell; no financial behavior, HTTP API, persistence, or authentication is implemented.

## Direction

The foundation uses Java 21, Maven, and Spring Boot 4.1.1. Spring Boot's official requirements list Java 17–26 and Maven 3.6.3 or newer; the local environment used for verification has Java 21 and Maven 3.8.7. See the [Spring Boot system requirements](https://docs.spring.io/spring-boot/system-requirements.html). Only the core starter is included; web, database, security, and migration dependencies await a use case that needs them. The proposed architecture remains a modular monolith. See [Architecture](docs/architecture.md), [Domain rules](docs/domain-rules.md), [Roadmap](docs/roadmap.md), and [ADR 0001](docs/adr/0001-java-modular-monolith.md).

## Build and run

Requirements: JDK 21 and Maven 3.6.3 or newer.

```sh
mvn verify
mvn spring-boot:run
```

The current non-web application starts its Spring context and then exits because no long-running server or worker is configured. It does not expose an HTTP endpoint or connect to external infrastructure.

Surefire loads Mockito as an explicit test JVM agent because Java 21+ restricts dynamic agent attachment.

## Current status

No financial application functionality is implemented yet. Repository state before this increment: branch `feat/project-foundation`, no commits, no tracked project files, and an `origin` remote. The pre-existing untracked `.codex/config.toml` is local configuration and is preserved.
