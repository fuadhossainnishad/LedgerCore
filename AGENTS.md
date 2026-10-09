# LedgerCore Engineering Standards

These instructions govern work in this repository. The repository and its tested behavior are the source of truth. Read the relevant code, migrations, documentation, and tests before changing them. Preserve existing user work; do not reset, rewrite history, commit, push, or make destructive changes unless explicitly authorized.

## Workflow and task boundaries

For each task, establish the current state and scope; identify acceptance criteria, domain invariants, and failure/security risks; choose the smallest complete increment; update tests and documentation with behavior; run appropriate checks and inspect their actual results; then review the diff for correctness, security, consistency, and unrelated changes. State assumptions where ambiguity is minor. Ask before decisions that materially affect financial correctness or commit to irreversible behavior. Do not expand a task into later roadmap phases.

Add or update tests that are relevant to the behavior changed. Run the appropriate available formatting, compilation, test, migration, and static checks for the increment, and inspect their actual results. For documentation-only changes, validate links/content and whitespace without running unrelated application suites. Do not claim checks passed unless run. Distinguish passing, failing, and unrun checks. Do not conceal failures by weakening assertions or suppressing errors.

## Java and design

Target Java 21 and Maven. Select and document a Spring Boot release compatible with the chosen Java and dependency set when implementation begins; verify current compatibility then. Use clear, idiomatic Java. Use immutability and language features where they improve clarity or correctness. Keep every type, member, dependency, annotation, and abstraction justified by a real need. Prefer composition and small cohesive units. Avoid speculative frameworks, needless interfaces, inheritance, utility classes, AOP, static mutable state, magic numbers, broad exception swallowing, and cleverness that obscures behavior. Keep methods and services focused; do not put workflows in controllers or expose persistence entities as API models.

## Architecture and domain

Use a modular monolith unless measured or concrete operational requirements justify otherwise. Organize around actual business capabilities. Keep domain rules independent of web and persistence details where practical. Maintain dependency direction: API/adapters call application use cases; application coordinates domain behavior and ports; domain does not depend on Spring, persistence, or transport; infrastructure implements ports and adapts external systems. Avoid extra Maven modules without a demonstrated boundary benefit.

Document business meaning, invariants, lifecycle, and failure behavior before implementing consequential domain workflows. Keep business transaction, transfer, ledger posting, idempotency key, and event identities distinct when their semantics differ. Treat the unresolved choices in `docs/domain-rules.md` as decisions to make with evidence and explicit rationale, not silently assumed requirements.

## Financial integrity

Represent money with exact decimal arithmetic and explicit currency/scale rules; never use `float` or `double`. Define rounding explicitly at the domain boundary. Every posted ledger transaction must balance debits and credits. Posted entries are immutable; correct them with controlled reversals or adjustments. Specify the authoritative balance source and reconciliation process before relying on balances. A transfer must be atomic, and duplicate requests must not create duplicate effects. Protect concurrent operations against overdrafts and invariant violations. Use database constraints to reinforce invariants, and describe exactly what the schema enforces; do not claim a database proves cross-row balancing unless it does.

## Spring, persistence, and transactions

Use Spring Boot, Spring Data JPA/Hibernate, Spring Security, PostgreSQL, and Flyway only where the increment needs them. Production schema evolution uses reviewed, versioned Flyway migrations; never rely on automatic Hibernate schema generation in production. Use explicit column types, nullability, foreign keys, unique/check constraints, and indexes justified by access patterns. Use `BigDecimal` and an appropriate fixed precision/scale for monetary columns once currency rules are decided. Use timezone-aware instants for event/audit time and document timestamp semantics.

Define transaction boundaries around use cases and understand proxy interception, self-invocation, propagation, rollback rules, persistence-context behavior, flush versus commit, and actual isolation. Choose optimistic locking, pessimistic locking, conditional updates, or another mechanism based on the invariant and access pattern. Analyze lock ordering, deadlocks, transaction duration, and bounded retries. Verify database semantics with PostgreSQL integration tests, not mocks. Avoid N+1 queries, unbounded result sets, and unnecessary round trips; inspect query plans when performance warrants it.

## Idempotency and concurrency

Before implementing an idempotent API, specify key scope, uniqueness, retention, request fingerprint/equivalence, response replay, and behavior after rollback or in-progress contention. Enforce concurrent uniqueness in durable storage. Never treat a cache as the authority for financial deduplication. Define a concurrency strategy for each balance-affecting operation, including lock ordering and retryable failures. Test simultaneous conflicting requests and duplicate keys against the real database when those capabilities exist.

## Security, validation, and errors

Use explicit validated request/response DTOs, consistent error bodies, and correct HTTP semantics. Validate syntax at the boundary and enforce business constraints in the domain/application layer. Authenticate and authorize every protected operation at an appropriate boundary; enforce object-level ownership so changing an ID cannot expose or alter another customer's data. Model roles and permissions from actual requirements rather than building speculative RBAC machinery. Never log credentials, tokens, secrets, or unnecessary customer/financial data. When authentication is added, document token/session lifecycle, expiry, refresh/revocation, and relevant threats. Map expected failures explicitly; do not leak internals or return success for partial failure.

## Database changes and auditability

Treat migrations as production code: forward-only, reviewed, safe for existing data, and consistent with entities and docs. Preserve referential and financial integrity with database constraints wherever feasible. Audit history must be attributable, useful, and protected from unauthorized alteration. Plan correction and reconciliation paths; do not silently mutate or delete posted financial records. For schema or data migrations, explain rollout, compatibility, and recovery implications.

## API and documentation

Document implemented contracts, validation, authorization, idempotency, and error semantics. Prefer generated API descriptions from actual contracts. Keep README, architecture/domain docs, diagrams, DBML, and API specs aligned with implementation. Mark plans and unresolved choices as such. Do not describe hypothetical features as present. Add documentation proportional to the decision and useful to the next engineer.

## Events and external infrastructure

Establish correct synchronous database transaction processing first. Add Kafka only for a concrete event-driven requirement; where business state and event publication must be coupled, use a transactional outbox written in the same database transaction. Design for duplicate delivery, retries, ordering, poison messages, observability, and recovery. Do not claim atomic PostgreSQL/Kafka commit without an implemented guarantee. Redis and other infrastructure must not be authoritative for financial truth and require a documented need.

## Testing and evidence

Use JUnit 5. Use Mockito for isolated collaborators where useful, not to claim database behavior. Build coverage around domain invariants, application success/failure workflows, real persistence/migrations/constraints/transactions, API validation/security, architecture boundaries, and concurrency as each capability is introduced. Verify rollback, insufficient funds, balanced postings, idempotent replay/conflict, authorization boundaries, and concurrent transfers when relevant. Use Testcontainers PostgreSQL when PostgreSQL semantics matter. Tests must assert both effects and absence of partial/duplicate effects.

## Performance and operations

Begin with a correct, understandable baseline. For nontrivial algorithms and queries, record relevant time/space complexity, expected data volume, indexes, and alternatives where useful. For performance changes identify the bottleneck or expected cost and measure when feasible. Consider query count/plans, connection pool, transaction duration, lock contention, allocation, network calls, serialization, and data growth. Do not claim optimization, throughput, linearizability, or production readiness without evidence.

Use structured logs and useful metrics/traces without exposing sensitive data. Handle transient failures with bounded, observable recovery; avoid unbounded retry loops. Define operational recovery and reconciliation for financial workflows before production use. Fail clearly when integrity cannot be established.

## Git hygiene and definition of done

Keep changes scoped and review the complete diff, including untracked files. Preserve local and user changes. Do not commit or push unless requested. A task is done when its stated acceptance criteria and relevant invariants are addressed, tests/checks are reported honestly, docs match actual behavior, security and failure cases are considered, and no unrelated or speculative changes remain. Report files changed, decisions, evidence, limitations, and a sensible next step.

Avoid overengineering: no speculative features, abstractions, modules, infrastructure, indexes, or frameworks without a demonstrated requirement. Correctness and maintainability take priority over file count and novelty.
