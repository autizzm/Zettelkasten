# ROP --- Architecture

## 1. High-level architecture

``` plantuml
@startuml
skinparam componentStyle rectangle

actor Operator
actor Client

rectangle "ROP Platform" {
  [debt-case-service] as DCS
  [restructuring-workflow-service] as RWS
  [legal-holds-integration-service] as LHIS
  [legacy-collection-migration-service] as LCM
  [collection-notification-service] as CNS
  database "PostgreSQL" as PG
  queue "Kafka" as KAFKA
  [Camunda] as CAMUNDA
  [Audit Store] as AUDIT
}

rectangle "External Systems" {
  [Legacy Oracle Monolith] as LEGACY
  [Legal Holds Source] as LEGAL
}

Operator --> DCS : REST
Client --> DCS : REST

DCS --> PG : transaction
DCS --> KAFKA : outbox relay
DCS --> AUDIT : audit events

RWS --> KAFKA : commands/events
RWS --> CAMUNDA : workflow execution
CAMUNDA --> KAFKA : saga commands
KAFKA --> DCS : commands
KAFKA --> CNS : domain events
KAFKA --> LHIS : legal-hold events

LHIS --> LEGAL : resilient API
LCM --> LEGACY : migration/reconciliation
LCM --> KAFKA : migration events
@enduml
```

## 2. Architectural principles

### 2.1 Ownership

`debt-case-service` является владельцем состояния `DebtCase`.

Другие сервисы не изменяют его таблицы напрямую. Изменение выполняется:

-   через REST operator/client path;
-   через Kafka command path.

### 2.2 Command vs event

Kafka topics разделены по семантике:

-   `cmd.*` --- запрос на выполнение операции;
-   `evt.*` --- факт произошедшего доменного изменения.

Пример:

``` text
Camunda
  |
  | cmd.applyNewTerms
  v
debt-case-service
  |
  | evt.newTermsApplied
  v
Camunda
```

### 2.3 At-least-once delivery

Kafka и workflow engine рассматриваются как источники повторной
доставки.

Гарантия строится на уровне бизнес-операции:

``` text
message
   |
   v
commandId uniqueness
   |
   +---- duplicate ----> no business effect
   |
   +---- new ----------> business transaction
```

Важно: это не означает exactly-once delivery Kafka. Гарантируется
отсутствие повторного бизнес-эффекта для одного `commandId`.

### 2.4 Transactional Outbox

``` text
BEGIN
  UPDATE debt_case
  INSERT INTO outbox
COMMIT

outbox relay
  |
  +--> Kafka
```

Outbox гарантирует атомарность **DB mutation + event intent**.

Relay может опубликовать одно событие повторно, если Kafka ack получен
до фиксации `sent=true`. Поэтому downstream consumers также должны быть
идемпотентными.

## 3. Saga orchestration

Camunda является оркестратором длительного процесса.

``` plantuml
@startuml
start

:Start restructuring;
:Validate DebtCase;
:Create proposal;
:Apply new terms;

if (Apply successful?) then (yes)
  :Send notification;
  :Wait for response;
else (no)
  :Compensate;
  stop
endif

if (Client accepted?) then (yes)
  :Complete restructuring;
else (no)
  :Proposal expires;
  :Escalate DebtCase;
endif

stop
@enduml
```

Saga не использует одну распределённую транзакцию. Каждый шаг имеет
собственную транзакцию и явный failure/compensation path.

## 4. Workflow concurrency invariant

Camunda correlation использует `caseId`. Поэтому одновременно
существующие workflow для одного `caseId` недопустимы.

Инвариант обеспечивается PostgreSQL:

``` sql
INSERT INTO restructuring_workflow_lock
    (case_id, active_process_instance_id, locked_at)
VALUES
    (:caseId, :processInstanceId, now())
ON CONFLICT (case_id) DO NOTHING;
```

Если вставка не произошла --- workflow уже активен.

Redis/ZooKeeper для этого инварианта не используются.

### Orphan lock recovery

Фоновый job проверяет locks, превышающие безопасный TTL:

1.  lock старше threshold;
2.  Camunda не содержит активного process instance;
3.  lock помечается orphaned;
4.  lock снимается;
5.  создаётся audit event.

## 5. Kafka contract governance

Kafka-события имеют envelope:

``` json
{
  "eventType": "rop.debt.case.status-changed",
  "schemaVersion": 2,
  "eventId": "uuid",
  "aggregateId": "case-id",
  "aggregateVersion": 42,
  "occurredAt": "2026-01-01T12:00:00Z",
  "payload": {}
}
```

Правила:

-   добавление optional field --- backward-compatible;
-   изменение типа существующего поля --- breaking change;
-   переименование существующего поля --- breaking change;
-   breaking change требует нового event version/type;
-   PR должен иметь `contract-change`;
-   владельцы consumer'ов обязаны review;
-   contract tests запускаются в CI.

## 6. Aggregate ordering

`DebtCase.aggregateVersion` увеличивается в одной транзакции с
изменением агрегата.

Пример:

``` text
case.status-changed v41
case.payment-received v42
case.status-changed v43
```

`legacy-collection-migration-service` буферизует события и применяет их
в порядке `aggregateVersion`.

Если пришло `43`, пока отсутствует `42`:

``` text
43 -> buffer
42 -> apply
43 -> apply
```

Это решает проблему ordering между несколькими Kafka
deliveries/partitions, но не заменяет корректное проектирование
partition key.

## 7. Dead-letter policy

DLQ используется для сообщений, которые невозможно корректно
десериализовать или структурно обработать:

-   invalid JSON;
-   неизвестная schema version;
-   нарушение обязательной структуры.

Бизнес-ошибка (`DebtCase` не находится, конфликт статуса и т.п.) не
должна автоматически превращаться в DLQ: она обрабатывается как бизнес
failure с retry/compensation/reconciliation в зависимости от use case.

## 8. Deployment strategy

Для `debt-case-service` и `restructuring-workflow-service`:

``` text
5% canary
   |
metrics OK
   v
25%
   |
metrics OK
   v
100%
```

Автооткат выполняется при превышении согласованных error/latency
thresholds.
