# ROP --- Data Flows

## 1. Start restructuring

``` plantuml
@startuml
actor Operator
participant "debt-case-service" as DCS
participant "Camunda" as C
participant "restructuring-workflow-service" as RWS
database "PostgreSQL" as DB

Operator -> DCS : POST /restructuring/{caseId}/start
DCS -> DB : INSERT workflow_lock
alt lock acquired
  DCS --> RWS : start accepted
  RWS -> C : start process(caseId)
else lock exists
  DCS --> Operator : 409 workflow already active
end
@enduml
```

## 2. Saga command

``` plantuml
@startuml
participant Camunda
queue Kafka
participant "debt-case-service" as DCS
database PostgreSQL as DB

Camunda -> Kafka : cmd.applyNewTerms(commandId)
Kafka -> DCS : command

DCS -> DB : INSERT inbox(commandId)
DCS -> DB : UPDATE DebtCase
DCS -> DB : INSERT outbox(event)
DB --> DCS : COMMIT

DCS --> Kafka : evt.newTermsApplied
Kafka --> Camunda : event
@enduml
```

The database transaction covers inbox deduplication and the business
mutation. A duplicate `commandId` produces no second business effect.

## 3. Outbox relay

``` plantuml
@startuml
participant "debt-case-service" as DCS
database PostgreSQL as DB
queue Kafka

DCS -> DB : SELECT outbox\nFOR UPDATE SKIP LOCKED
DB --> DCS : pending event
DCS -> Kafka : publish(event)
Kafka --> DCS : ack
DCS -> DB : mark sent
```

Failure after Kafka acknowledgement but before `mark sent` can cause
republishing. Consumers must therefore be idempotent.

## 4. Compensation

``` plantuml
@startuml
start
:ApplyNewTerms;

if (Failure?) then (yes)
  :CompensateApplyNewTerms;
  :Write audit event;
  :Retry until success;
else (no)
  :Continue workflow;
endif

stop
@enduml
```

Compensation is local to the owning service whenever possible and does
not introduce an unnecessary synchronous external dependency.

## 5. Proposal expiration and escalation

``` plantuml
@startuml
participant Camunda
participant "debt-case-service" as DCS
queue Kafka

Camunda -> DCS : cmd.create/send proposal
DCS -> Kafka : evt.termsProposed
Kafka -> Camunda : evt.termsProposed

Camunda -> Camunda : boundary timer / SLA

Camunda -> DCS : cmd.expireProposal
DCS -> Kafka : evt.proposalExpired
Camunda -> DCS : cmd.escalateCase
DCS -> Kafka : evt.case.status-changed
@enduml
```

The SLA starts from the agreed notification processing milestone
(`rop.notifications.sent`), not from the mere creation of the proposal.

## 6. Legal hold integration

``` plantuml
@startuml
participant Kafka
participant "legal-holds-integration-service" as LHIS
participant "External Legal Source" as EXT

Kafka -> LHIS : legal-hold request
LHIS -> LHIS : bulkhead
LHIS -> EXT : request

alt confirmed
  EXT --> LHIS : HOLD
  LHIS --> Kafka : HOLD_CONFIRMED
else no hold
  EXT --> LHIS : NO HOLD
  LHIS --> Kafka : NO_HOLD_CONFIRMED
else timeout/error
  EXT --> LHIS : error/timeout
  LHIS --> Kafka : UNKNOWN
end
@enduml
```

`UNKNOWN` preserves the uncertainty of the external source.

## 7. Migration and reconciliation

``` plantuml
@startuml
participant "Legacy Oracle" as L
participant "legacy-collection-migration-service" as M
database "Target DB" as T
participant Analyst

L -> M : extract batch
M -> T : load batch

loop parallel run
  L -> M : snapshot
  T -> M : snapshot
  M -> M : compare + classify
end

alt known mismatch
  M -> M : classify MAPPING_ERROR/DATA_DRIFT
else unexplained mismatch
  M -> Analyst : manual review
end

M -> Analyst : cutover gate
@enduml
```

## 8. Aggregate ordering during migration

``` plantuml
@startuml
participant Kafka
participant "legacy-collection-migration-service" as M
database "Migration state" as DB

Kafka -> M : event v43
M -> DB : buffer v43

Kafka -> M : event v42
M -> DB : apply v42
M -> DB : apply buffered v43
@enduml
```

Ordering is based on `aggregateVersion` for the same aggregate.

## 9. Failure handling

  Failure                             Handling
  ----------------------------------- -----------------------------------------------
  Duplicate command                   Inbox deduplication
  DB rollback                         Kafka offset not advanced
  Outbox relay crash                  Event remains pending / may be republished
  Consumer business error             Retry or domain-specific failure path
  Malformed event                     DLQ
  External legal source unavailable   Circuit breaker + `UNKNOWN`
  Camunda crash                       Orphan lock recovery
  Migration mismatch                  Reconciliation classification + manual triage
