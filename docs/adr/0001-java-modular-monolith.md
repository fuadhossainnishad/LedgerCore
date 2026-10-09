# ADR 0001: Java modular monolith as the initial application shape

- **Status:** Accepted for the initial foundation
- **Date:** 2026-10-09

## Context

The repository has no commits, source, build, database schema, or implemented behavior. The requested product is a financial transaction and double-entry ledger platform. Financial consistency and a clear dependency model matter more than early service decomposition. Runtime, scale, deployment, and team topology requirements are not yet established.

## Decision

Use Java 21 and Maven for the initial foundation. Start with a modular monolith and explicit API, application, domain, and infrastructure dependency boundaries. The build uses Spring Boot 4.1.1; its [official system requirements](https://docs.spring.io/spring-boot/system-requirements.html) list Java 17–26 and Maven 3.6.3 or newer. The current shell includes only Spring Boot's core and test starters. Use PostgreSQL and Flyway for durable state and versioned schema changes when a persistence slice is approved. Introduce Spring Security with protected behavior. Do not include Kafka or Redis in the initial foundation absent a concrete requirement.

## Alternatives considered

- **Microservices:** premature without independent deployment, scaling, or ownership needs; distributed transactions would increase financial consistency risk.
- **Many Maven modules:** no demonstrated stable module boundaries exist yet; packages can establish boundaries first.
- **Kafka/Redis at bootstrap:** no event-delivery or cache requirement has been established; each adds operational and consistency obligations.

## Consequences

The initial deployment can keep a financial use case within one database transaction and one operational unit. In-process boundaries still need enforcement through package design and tests. A modular monolith does not settle ledger semantics, concurrency control, balance authority, or authorization; those require explicit decisions before the first financial workflow. The Boot release is pinned in the Maven parent and should be upgraded through reviewed dependency changes. Revisit the architecture decision only when concrete operational evidence supports a different shape.
