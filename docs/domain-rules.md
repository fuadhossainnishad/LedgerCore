# Domain rules and open decisions

## Status

No domain model or financial behavior has been implemented. The rules below are invariants for future design; listed policies remain unresolved until product/accounting requirements establish them.

## Invariants to preserve

- A posted ledger transaction has equal total debits and credits in its accounting currency.
- Monetary amounts use exact decimal arithmetic and explicit currency and rounding rules.
- Posted entries are immutable. Corrections use controlled reversal or adjustment postings with an audit trail.
- A transfer is all-or-nothing: debit, credit, ledger posting, and durable deduplication effects must not partially commit.
- Repeating an equivalent request under the same idempotency identity must not create another financial effect; reuse with a materially different request must have a defined outcome.
- Concurrent operations must preserve account and ledger invariants, including the agreed available-funds policy.
- Balance source, derivation, and reconciliation must be explicit; a cached/materialized balance needs a defined consistency and repair strategy.

## Identity vocabulary

Keep identities distinct when needed: a **business transaction** identifies a user/business intent; a **transfer** identifies movement between accounts; a **ledger posting** identifies the accounting record; an **idempotency key** identifies a retryable API intent within a defined scope; an **event** identifies a published fact/delivery unit. Their cardinalities and lifecycle are unresolved and must be specified before schema/API design.

## Decisions required before implementation

1. Which account types, ownership model, and permitted account states exist?
2. Are transfers single-currency only? What precision, scale, rounding mode, and treatment of fees or FX apply?
3. Is the account balance derived from ledger entries, maintained as a transactional projection, or both? What reconciliation detects and repairs drift?
4. What does “available funds” mean? Are overdrafts, holds/reservations, pending transfers, limits, or backdated entries in scope?
5. What transfer states and transitions are required, and when is a transfer considered posted/final?
6. What is the idempotency scope, key retention, request equivalence rule, replay response, and behavior for concurrent/in-progress requests?
7. What customer, operator, and service identities/roles may initiate, inspect, reverse, or reconcile transactions?
8. Which audit facts must be immutable and attributable, and how are reversals, adjustments, and reconciliation discrepancies approved?

Resolve these with product/accounting stakeholders before committing to financial schema or externally visible behavior. Record consequential decisions in ADRs.
