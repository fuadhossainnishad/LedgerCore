# Proposed architecture

## Status and scope

This is a proposed direction for the application. The initial foundation now contains a minimal Spring Boot application shell, but no business runtime behavior. Use a modular monolith unless concrete workload or organizational evidence supports another deployment shape.

## Technology direction

Target Java 21 and Maven. The build currently uses Spring Boot 4.1.1, whose official system requirements support Java 17–26 and Maven 3.6.3 or newer. The only dependency is the core Boot starter plus its test starter. PostgreSQL is the proposed durable store; use Spring Data JPA/Hibernate where it fits the access patterns and Flyway for schema evolution. Introduce Spring Security with the first protected use case. Do not add Kafka, Redis, or other services without a documented requirement.

## Dependency direction

Keep business rules independent of frameworks where practical:

```text
HTTP/API adapters -> application use cases -> domain model
infrastructure    -> application/domain ports
```

The diagram is conceptual: application/domain define needed boundaries; infrastructure depends on those boundaries and implements application ports. Domain code must not depend on Spring MVC, JPA, or transport types. API adapters translate and validate requests, invoke use cases, and map explicit responses/errors. Application use cases establish transaction boundaries and coordinate domain operations. Persistence and external-system details stay in infrastructure. Choose packages around actual business capabilities once requirements establish them; do not create speculative modules.

## Transaction and consistency direction

The database is the durable authority for financial state. A balance-affecting workflow must commit atomically with its ledger effects and idempotency record, subject to the chosen schema and transaction design. Posted ledger entries are immutable. Use explicit database constraints and a justified concurrency control strategy; constraints alone must not be represented as enforcing cross-row debit/credit balance unless they actually do. Define balance derivation and reconciliation before implementing a balance API. If later events need reliable publication, evaluate a transactional outbox committed with the business state.

## Validation evidence expected at implementation

Use domain and application tests for rules and workflows; real PostgreSQL integration tests for migrations, constraints, and transaction semantics; API/security tests for contracts and authorization; and concurrency tests for competing operations and idempotency. Document actual build/run commands and supported operational assumptions when those exist.
