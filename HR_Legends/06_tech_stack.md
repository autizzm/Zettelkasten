# ROP --- Technology Stack

## 1. Backend

  Technology           Usage
  -------------------- -----------------------------
  Java 17/21           Backend development/runtime
  Spring Boot          Microservices
  Spring Web           REST APIs
  Spring Kafka         Kafka producers/consumers
  Spring Data / JDBC   Persistence
  Flyway               DB schema migrations
  Resilience4j         Circuit breaker / bulkhead
  Camunda              Workflow orchestration

## 2. Data

  Technology      Usage
  --------------- --------------------------------
  PostgreSQL      Service-owned relational state
  Oracle          Legacy system
  Kafka           Commands/events
  Outbox tables   Reliable event publication
  Inbox tables    Command deduplication

## 3. Messaging

### Kafka

Used for:

-   Saga commands;
-   domain events;
-   integration events;
-   asynchronous service communication.

Delivery model:

``` text
at-least-once
+
idempotent consumers
```

### Contract format

JSON Schema:

``` text
contracts/kafka/rop.debt.case.*.schema.json
```

Contract checks execute in CI.

## 4. Workflow

Camunda provides:

-   BPMN process execution;
-   message correlation;
-   service tasks;
-   boundary events;
-   timers;
-   compensation;
-   process instance state.

Camunda does not replace domain persistence. `DebtCase` remains owned by
`debt-case-service`.

## 5. Testing

### Unit

Domain logic and application services.

### Component

``` text
Spring application
+ Testcontainers PostgreSQL
+ embedded/test Kafka
```

### Integration

Full workflow through a test Camunda engine.

### Contract

Kafka producer/consumer compatibility checks.

### Failure-path

Examples:

-   duplicate Kafka command;
-   consumer restart;
-   external source timeout;
-   broken network;
-   malformed event;
-   Camunda restart;
-   orphan workflow lock.

## 6. Local environment

``` text
docker-compose
├── debt-case-service
├── postgres
├── kafka
├── mock-camunda
└── wiremock
```

Local Kafka uses a single broker because the goal is development, not
production topology simulation.

## 7. Environments

  Environment   Purpose
  ------------- ----------------------------------
  local         Developer feedback loop
  dev           Shared integration
  test/QA       Regression and failure scenarios
  stage         Pre-production validation
  prod          Production traffic

Production data is not copied to non-production without masking.

## 8. CI/CD

``` text
PR
 |
 +-- unit tests
 +-- component tests
 +-- contract tests
 +-- static analysis
 |
merge
 |
develop
 |
dev deployment
 |
release branch
 |
QA
 |
stage
 |
canary
 |
100% production
```

Git workflow:

``` text
feature/ROP-XXXX-...
       |
       v
develop
       |
       +--> release/x.y
       |
       +--> hotfix/x.y.z
```

Conventional Commits + squash merge.

## 9. Observability

Key metrics:

-   HTTP error rate;
-   latency;
-   Kafka consumer lag;
-   age of `FAILED` inbox records;
-   `evt.newTermsApplied(success=false)` ratio;
-   DLQ depth;
-   circuit breaker state;
-   reconciliation mismatch count;
-   canary error rate.

Logs should include:

``` text
traceId
correlationId
caseId
commandId
processInstanceId
aggregateVersion
```

Audit records additionally distinguish:

``` text
initiatorType = USER | SAGA
initiatorId   = userId | commandId/processInstanceId
```
