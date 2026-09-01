# ROP --- Developer Experience

## 1. Scope of this document

This document describes a **representative backend developer
contribution** to the project.

It must not be interpreted as claiming ownership of the whole platform
by one developer.

The expected level is approximately **Middle → Senior progression**:
implementation of production features, ownership of selected technical
problems, participation in design/review, but not sole ownership of
architecture, migration, infrastructure or all business capabilities.

## 2. Primary ownership area

The developer's main area is `debt-case-service` and adjacent
Kafka/workflow integration.

### Individual contributions

-   Designed domain model:
    -   `DebtCase`;
    -   `RestructuringProposal`;
    -   `LegalHold`;
    -   `CollectionEvent`;
    -   `RestructuringWorkflowLock`.
-   Implemented Inbox consumer for Saga command topics:
    -   `applyNewTerms`;
    -   `closeOldCase`;
    -   `compensateApplyNewTerms`.
-   Implemented Transactional Outbox.
-   Added `aggregateVersion` to `DebtCase` events.
-   Implemented ordering/buffering support in migration integration.
-   Implemented Resilience4j protection in legal-hold integration.
-   Implemented reconciliation batch use case.
-   Implemented selected compensation Service Tasks.
-   Added pagination/filtering and date-boundary fixes.
-   Wrote operational runbook for failed Inbox records and replay.
-   Participated in Kafka contract review.

## 3. Inbox implementation

### Problem

The monolith did not need distributed command idempotency because
restructuring logic executed inside a single Oracle transaction.

After decomposition:

``` text
Kafka delivery = at-least-once
```

A workflow retry could produce the same command more than once.

### Solution

``` text
commandId PK
```

Processing:

``` text
BEGIN

INSERT INTO inbox(command_id)
ON CONFLICT DO NOTHING

if inserted:
    execute business mutation
    insert audit/outbox records

COMMIT
```

The inbox insert and business effect are part of the same transaction.

Therefore:

``` text
same commandId
    |
    +--> first delivery  -> business effect
    |
    +--> duplicate       -> no second effect
```

This guarantees exactly-once **business effect for the protected
command**, not exactly-once Kafka delivery.

Messages are processed one at a time. For the expected throughput of
tens of messages/minute, batching was rejected because partial failure
handling and offset coordination would add complexity without meaningful
performance benefit.

## 4. Outbox implementation

The developer implemented:

``` text
DebtCase mutation
      +
outbox INSERT
```

in the same PostgreSQL transaction.

Relay:

``` sql
SELECT *
FROM outbox
WHERE sent = false
FOR UPDATE SKIP LOCKED;
```

Then:

``` text
publish -> Kafka ack -> mark sent
```

The implementation intentionally accepts possible duplicate publication
around the ack/DB-update boundary.

Therefore downstream consumers must be idempotent.

CDC/Debezium was considered but rejected for the described traffic level
because the operational complexity of Kafka Connect/CDC was higher than
the benefit of eliminating polling.

## 5. Workflow lock

### Incident

QA identified a double-start scenario:

``` text
UI double click
   |
   +--> process A
   |
   +--> process B
```

Both processes used the same `caseId`.

### Options considered

  -----------------------------------------------------------------------
  Option                  Decision                Reason
  ----------------------- ----------------------- -----------------------
  Frontend lock           Reject                  Does not protect API

  Optimistic locking on   Reject                  Does not protect
  DebtCase                                        process creation

  Redis/ZooKeeper lock    Reject                  Extra infrastructure
                                                  dependency

  PostgreSQL unique lock  Select                  Existing DB + direct
                                                  invariant
  -----------------------------------------------------------------------

Selected model:

``` text
RestructuringWorkflowLock
caseId PK
activeProcessInstanceId
lockedAt
```

The lock is acquired atomically before starting the process.

Orphan locks are removed only when:

``` text
lock expired
AND
Camunda process instance is not active
```

and the recovery is audited.

## 6. Kafka contract work

The developer participated in designing and implementing JSON Schema
contracts.

Typical workflow:

``` text
Business requirement
       |
       v
Event semantic definition
       |
       v
Payload + envelope design
       |
       v
Review with producer/consumer owners
       |
       v
JSON Schema
       |
       v
Contract tests
       |
       v
PR
```

For non-trivial changes, the developer coordinated with:

-   analyst;
-   tech lead;
-   owners of downstream consumers;
-   QA.

A later production incident demonstrated why this process was necessary.

## 7. Production incident

An event field changed from:

``` json
"overdueAmount": 123.45
```

to:

``` json
"overdueAmount": {
  "amount": 123.45,
  "currency": "RUB"
}
```

without consumer-owner approval.

A strict consumer failed deserialization and stopped progressing through
the topic. Consumer lag accumulated for approximately six hours and
affected roughly 140 cases.

### Recovery

1.  Incident escalated to team lead.
2.  Event format reverted.
3.  Undelivered outbox records were replayed.
4.  Notification processing recovered.
5.  Affected cases were manually reconciled/notified.
6.  Blameless post-mortem performed.

### New rule

Breaking Kafka changes require:

-   `contract-change` label;
-   consumer-owner review;
-   contract tests;
-   new event type/version for incompatible changes;
-   mandatory `schemaVersion`.

## 8. Reconciliation

### Initial state

Approximately 400 cases/week were manually compared.

### Implementation

``` text
MigrationBatch
    |
    v
extract/load
    |
    v
snapshot comparison
    |
    v
ReconciliationRecord
    |
    +--> MAPPING_ERROR
    |
    +--> DATA_DRIFT
    |
    +--> unexplained -> manual review
```

The mechanism reduced manual verification to approximately 15--20
cases/week and supported migration of 40,000+ cases.

The 40,000+ figure is a project-level migration result; it should not be
presented as the developer personally migrating all cases.

## 9. Legal-hold integration

The developer implemented consumer-side resilience:

``` text
Kafka
  |
  v
bulkhead
  |
  v
circuit breaker
  |
  v
external source
```

Response model:

``` text
HOLD_CONFIRMED
NO_HOLD_CONFIRMED
UNKNOWN
```

The key design decision was to preserve uncertainty instead of
converting upstream timeout/error into `NO_HOLD`.

## 10. SLA escalation

The developer implemented the technical part of the escalation flow:

``` text
Proposal SENT
      |
      v
boundary timer
      |
      v
Proposal EXPIRED
      |
      v
DebtCase ESCALATED
```

The SLA starts from the agreed notification-processing milestone rather
than from proposal creation.

This replaces a nightly batch flag with an explicit, auditable workflow
transition.

## 11. Routine production work

Examples:

-   fixed `overdueDays` race around month boundaries and timezone/DST;
-   added pagination and filtering to `GET /debt-cases`;
-   improved webhook validation;
-   added Flyway migrations;
-   handled failed Inbox records;
-   documented admin replay;
-   reviewed Kafka contract changes;
-   participated in PR reviews.

## 12. Development process

``` plantuml
@startuml
start
:Jira ticket;
:3 Amigos refinement;
if (Debt/Legal/Compliance impact?) then (yes)
  :Compliance / Legal review;
endif
:Planning Poker;
:Feature branch;
if (Saga/contract change?) then (yes)
  :Design note / contract review;
endif
:Implementation;
:Unit tests;
:Component tests;
:Integration tests;
:Code review;
:QA;
:Stage;
:Canary 5% -> 25% -> 100%;
:Production monitoring;
stop
@enduml
```

## 13. What the developer should not claim

The developer should not present these as sole personal achievements
unless they actually owned them:

-   entire microservice migration;
-   all Camunda architecture;
-   all Kafka infrastructure;
-   production Kubernetes/DevOps platform;
-   all compliance decisions;
-   all 40,000+ migrated cases;
-   entire system architecture;
-   all resilience/observability work.

Correct framing:

> Owned selected backend components and cross-service integration points
> while contributing to the broader decomposition and migration effort.
