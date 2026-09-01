# ROP --- Overview

## 1. Назначение

**ROP (Restructuring & Problem Assets Platform)** --- целевая платформа
для управления проблемными кредитными делами (`DebtCase`) и процессами
реструктуризации.

Проект заменяет часть функциональности legacy-монолита на набор Spring
Boot-сервисов с событийным взаимодействием через Kafka и оркестрацией
длительных бизнес-процессов через Camunda.

Основные задачи проекта:

-   выделить владение данными о кредитном деле в `debt-case-service`;
-   вынести процесс реструктуризации из Oracle PL/SQL-транзакции в
    управляемую Saga;
-   обеспечить идемпотентную обработку Kafka-команд;
-   гарантировать атомарную публикацию доменных событий через Outbox;
-   изолировать нестабильные внешние источники;
-   выполнять поэтапную миграцию дел из legacy;
-   обеспечить аудит бизнес-изменений и контролируемую эскалацию.

Связанные документы: - \[\[02_architecture\]\] - \[\[03_services\]\] -
\[\[04_data_flows\]\] - \[\[05_team_structure\]\] -
\[\[06_tech_stack\]\] - \[\[07_timeline\]\] -
\[\[08_developer_experience\]\]

## 2. Scope

### In scope

-   жизненный цикл `DebtCase`;
-   предложения по реструктуризации;
-   блокировки процесса реструктуризации;
-   Saga orchestration;
-   Kafka command/event contracts;
-   inbox/outbox;
-   интеграция с источником данных об арестах;
-   уведомления;
-   миграция и reconciliation с legacy;
-   аудит;
-   observability и эксплуатационные runbook'и.

### Out of scope

-   реализация frontend/UI;
-   реализация внешней notification platform;
-   реализация внешнего источника данных об арестах;
-   реализация legacy Oracle-монолита;
-   самостоятельная реализация Kafka/Camunda infrastructure.

## 3. Ключевые бизнес-объекты

  -----------------------------------------------------------------------
  Объект                              Назначение
  ----------------------------------- -----------------------------------
  `DebtCase`                          Кредитное дело и его текущее
                                      состояние

  `RestructuringProposal`             Предложенные клиенту новые условия

  `LegalHold`                         Информация об аресте/ограничении
                                      операций по делу

  `CollectionEvent`                   Событие взыскания/изменение в
                                      процессе взыскания

  `RestructuringWorkflowLock`         Инвариант: не более одного
                                      активного workflow на `caseId`

  `MigrationBatch`                    Временная сущность миграционного
                                      процесса

  `ReconciliationRecord`              Результат сравнения legacy и target
  -----------------------------------------------------------------------

## 4. Ключевые инварианты

1.  Для одного `caseId` существует не более одного активного
    restructuring workflow.
2.  Одна Kafka-команда не должна повторно создавать бизнес-эффект.
3.  Изменение `DebtCase` и запись соответствующего Outbox-события
    выполняются в одной транзакции.
4.  Потребители Kafka не должны предполагать exactly-once delivery.
5.  Изменение существующего Kafka-контракта проходит contract review.
6.  Legacy migration/reconciliation имеет временный жизненный цикл и не
    является постоянным доменом.
7.  Аудит должен различать действие пользователя и действие Saga.

## 5. Архитектурный стиль

Проект использует комбинацию:

-   domain-oriented microservices;
-   synchronous REST для operator/client path;
-   asynchronous Kafka commands/events;
-   Saga orchestration;
-   Transactional Inbox;
-   Transactional Outbox;
-   state machines для доменных статусов;
-   strangler-fig migration;
-   resilience patterns для внешних интеграций.

## 6. Контекст

``` plantuml
@startuml
!include C4_Context.puml

Person(operator, "Operator", "Работает с проблемными кредитными делами")
Person(client, "Client", "Получает и принимает предложения по реструктуризации")

System(rop, "ROP Platform", "Платформа управления проблемными активами и реструктуризацией")
System_Ext(legacy, "Legacy Collection Monolith", "Oracle-based legacy system")
System_Ext(notification, "Collection Notification Service", "Внешняя система уведомлений")
System_Ext(legal, "Legal Holds Source", "Внешний источник данных об арестах")

Rel(operator, rop, "Управляет DebtCase и workflow")
Rel(client, rop, "Взаимодействует с предложением")
Rel(rop, legacy, "Миграция / reconciliation")
Rel(rop, notification, "Отправляет notification events")
Rel(rop, legal, "Получает данные об арестах")
@enduml
```
