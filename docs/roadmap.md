# Roadmap

This roadmap records staged work, not a commitment to implement every stage. Each stage should be a separately scoped increment with requirements, verification, and documentation appropriate to its behavior.

## Confirmed starting point

At bootstrap, the repository had no commits, tracked source, build configuration, database schema, or implemented product behavior. It is on `feat/project-foundation` and has an `origin` remote. An untracked local `.codex/config.toml` exists and is preserved. No claim about a running or deployable service is warranted.

## Major risks to resolve as implementation begins

- **Financial semantics:** currency, rounding, overdraft/available-funds rules, transfer states, and correction policy are not yet specified.
- **Consistency under failure and concurrency:** atomic postings, duplicate request handling, lock strategy, and retry behavior have no implementation or database evidence yet.
- **Balance integrity:** no authoritative balance source or reconciliation procedure has been selected.
- **Access control and audit:** customer ownership boundaries, operator permissions, and audit retention/protection requirements are undecided.
- **Operational compatibility:** although the foundation build now exists, there is no schema, deployment target, or runtime profile to validate persistence, migration, and recovery choices against.

## Stages

1. **Foundation — complete** — minimal Java 21 Maven/Spring Boot 4.1.1 application shell and context-start verification. No business behavior, API, persistence, or authentication was added.
2. **First vertical slice** — after product decisions are resolved, implement one narrowly scoped financial workflow end to end, including domain rules, API contract, persistence migration, authorization, idempotency/concurrency behavior, and real PostgreSQL tests.
3. **Ledger and reconciliation maturity** — expand ledger lifecycle, correction/reversal, audit, balance derivation, and reconciliation based on agreed accounting semantics and operational needs.
4. **Operational hardening** — add measured observability, recovery procedures, performance work, and deployment concerns justified by a concrete runtime environment.

Do not begin later stages merely because they are listed. Kafka, Redis, multi-module decomposition, and broad permission frameworks are not baseline requirements.

## Decisions needed before financial implementation

Resolve the questions in [Domain rules](domain-rules.md), especially account model, currency/rounding, balance source, transfer lifecycle, insufficient-funds/overdraft policy, idempotency contract, authorization model, and correction/reconciliation semantics. Record consequential choices as ADRs.
