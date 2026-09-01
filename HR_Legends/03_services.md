# ROP --- Services

## 1. Service map

  ------------------------------------------------------------------------------------------
  Service                                 Responsibility             Data ownership
  --------------------------------------- -------------------------- -----------------------
  `debt-case-service`                     DebtCase domain, commands, DebtCase DB
                                          events                     

  `restructuring-workflow-service`        Saga orchestration         Workflow metadata

  `legal-holds-integration-service`       External legal-hold        Integration state
                                          integration                

  `legacy-collection-migration-service`   Temporary                  Migration DB
                                          migration/reconciliation   

  `collection-notification-service`       Notifications              Notification domain

  Camunda                                 Workflow execution         Process state
  ------------------------------------------------------------------------------------------

## 2. debt-case-service

### Responsibilities

-   CRUD/read API для `DebtCase`;
-   state transitions;
-   restructuring-related commands;
-   Inbox processing;
-   Outbox;
-   workflow lock;
-   domain events;
-   audit initiation.

### Domain model

``` plantuml
@startuml
class DebtCase {
  id
  status
  overdueDays
  overdueAmount
  aggregateVersion
}

class RestructuringProposal {
  id
  caseId
  status
  sentAt
  expiresAt
}

class LegalHold {
  id
  caseId
  status
}

class CollectionEvent {
  id
  caseId
  type
  occurredAt
}

class RestructuringWorkflowLock {
  caseId
  activeProcessInstanceId
  lockedAt
}

DebtCase "1" --> "*" RestructuringProposal
DebtCase "1" --> "*" LegalHold
DebtCase "1" --> "*" CollectionEvent
DebtCase "1" --> "0..1" RestructuringWorkflowLock
@enduml
```

### REST

``` text
PATCH /debt-cases/{id}/status
GET   /debt-cases
POST  /restructuring/{caseId}/start
```

REST path records the initiating `userId`.

Kafka path records `commandId` and `processInstanceId`.

## 3. restructuring-workflow-service

### Responsibility

-   starts restructuring workflow;
-   owns BPMN definitions;
-   correlates domain events;
-   sends saga commands;
-   handles timeouts;
-   invokes compensation;
-   produces workflow audit information.

### Example commands

``` text
cmd.applyNewTerms
cmd.closeOldCase
cmd.compensateApplyNewTerms
```

### Example events

``` text
evt.newTermsApplied
evt.termsProposed
evt.restructuringCompleted
```

## 4. legal-holds-integration-service

### Responsibility

-   consumes legal-hold requests/events;
-   calls unstable external source;
-   normalizes result;
-   publishes domain-compatible response.

### Response semantics

``` text
HOLD_CONFIRMED
NO_HOLD_CONFIRMED
UNKNOWN
```

`UNKNOWN` is not equivalent to `NO_HOLD_CONFIRMED`.

### Resilience

``` text
consumer
   |
bulkhead
   |
circuit breaker
   |
external source
```

Bulkhead prevents external calls from consuming the same resources as
unrelated operations.

Circuit breaker stops sending requests after configured
failure/slow-call thresholds.

## 5. legacy-collection-migration-service

Temporary service.

### Responsibilities

-   extract/load migration batches;
-   map legacy statuses;
-   normalize monetary values;
-   run parallel-run reconciliation;
-   classify mismatches;
-   provide cutover gate.

### Main entities

``` text
MigrationBatch
ReconciliationRecord
```

### Mismatch classes

``` text
MAPPING_ERROR
DATA_DRIFT
```

The exact taxonomy can be extended without changing the target domain.

### Lifecycle

``` text
extract/load
    |
parallel run
    |
daily diff
    |
triage
    |
N consecutive clean days
    |
cutover
    |
disable legacy handler
```

После полного cutover сервис и его migration tables выводятся из
эксплуатации.

## 6. collection-notification-service

Consumes notification-related events and invokes the notification
platform.

ROP does not own the notification platform.

The relevant contract distinguishes:

``` text
rop.notifications.sent
rop.notifications.failed
```

These represent notification processing state rather than guaranteed
physical delivery to a device.

## 7. Shared operational concerns

Все сервисы должны иметь:

-   structured logging;
-   correlation/trace IDs;
-   metrics;
-   health/readiness probes;
-   Kafka consumer lag monitoring;
-   explicit retry policy;
-   graceful shutdown;
-   Flyway migrations;
-   documented runbook for operational failure modes.
