# 03. Реестр сервисов

См. также [[02_architecture]], [[04_data_flows]].

## Конвенция именования топиков

```
rop.<домен>.<сущность>.<событие>          — доменные события (факт, прошедшее время)
rop.<домен>.<процесс>.cmd.<действие>      — команды от BPMN-оркестратора исполнителю
rop.<домен>.<процесс>.evt.<результат>     — событие-результат исполнения команды, для корреляции обратно в процесс
rop.audit.event                            — единый топик аудита (все сервисы пишут сюда)
```

Ключ партиционирования — везде `caseId` / `requestId` (идентификатор агрегата): гарантирует порядок событий по одному делу/заявке **в рамках одного топика**. Межтопиковый порядок — см. [[04_data_flows#Порядок событий]].

### Межтопиковый порядок и `aggregateVersion`

Партиционирование по `caseId` гарантирует порядок **только внутри одного топика**. Три топика домена `DebtCase` (`case.opened`, `case.status-changed`, `case.payment-received`) не имеют между собой гарантии межтопикового порядка.

- Каждое событие `DebtCase` несёт **`aggregateVersion`** — монотонный счётчик, инкрементируемый в той же транзакции, что и мутация агрегата.
- Consumer’ы, которым нужен причинный порядок между событиями разных топиков одного агрегата (в первую очередь `legacy-collection-migration-service`), обязаны буферизовать входящие события по `caseId` и применять их в порядке `aggregateVersion`, а не полагаться на порядок доставки Kafka.
- Consumer’ы с подпиской на один тип события (`collection-notification-service`, `restructuring-workflow-service`) в буферизации не нуждаются.

### Совместимость схем — без Schema Registry

Решение сознательно отложено до появления измеримой боли (например, инцидент из-за пропущенного PR — см. [[08_developer_experience#Провал]]). Вместо Registry:

- Обязательное поле `schemaVersion: int` в конверте каждого события (наравне с `eventType`, `aggregateVersion`, `occurredAt`).
- Новые поля — только optional с дефолтом на стороне consumer’а; **переименование или смена типа поля запрещены** — breaking change оформляется как новый `eventType`/топик-суффикс.
- JSON Schema для каждого топика в общем репозитории (`contracts/kafka/rop.debt.case.*.schema.json`), CI обеих сторон валидирует сообщения против схемы на этапе PR.
- PR с меткой `contract-change` — обязательное ревью владельцев всех consumer-сервисов топика.

Правило сформировано по итогам инцидента с несовместимым изменением поля `overdueAmount` без ревью контракта — см. [[08_developer_experience#Инцидент несовместимая схема события]].

## Реестр топиков

| Топик                                                 | Producer                            | Consumer(ы)                                                                   | Ключ         | Семантика                                                                                                                                                                                          |
| ----------------------------------------------------- | ----------------------------------- | ----------------------------------------------------------------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rop.docs.request.created`                            | document-request-service            | document-generation-engine                                                    | requestId    | Заявка принята и провалидирована                                                                                                                                                                   |
| `rop.docs.request.cancelled`                          | document-request-service            | document-generation-engine                                                    | requestId    | Отмена до/во время генерации                                                                                                                                                                       |
| `rop.docs.document.generated`                         | document-generation-engine          | document-archive-service, document-delivery-service, document-request-service | requestId    | PDF собран и подписан                                                                                                                                                                              |
| `rop.docs.document.generation-failed`                 | document-generation-engine          | document-request-service                                                      | requestId    | Ошибка генерации (`retryable`/`fatal`)                                                                                                                                                             |
| `rop.docs.template.activated`                         | document-template-registry          | document-generation-engine                                                    | documentType | Инвалидация кэша активной версии шаблона                                                                                                                                                           |
| `rop.docs.document.archived`                          | document-archive-service            | document-request-service                                                      | requestId    | Документ сохранён в объектное хранилище                                                                                                                                                            |
| `rop.docs.document.delivered`                         | document-delivery-service           | document-request-service                                                      | requestId    | Доставка подтверждена                                                                                                                                                                              |
| `rop.docs.document.delivery-failed`                   | document-delivery-service           | document-request-service                                                      | requestId    | Ошибка доставки, требуется ретрай/эскалация                                                                                                                                                        |
| `rop.risk.overdue-signal.detected`                    | Risk Monitoring System (внешняя)    | debt-case-service                                                             | clientId     | Триггер открытия дела                                                                                                                                                                              |
| `rop.debt.case.opened`                                | debt-case-service                   | restructuring-workflow-service, legacy-collection-migration-service           | caseId       | Открыто новое проблемное дело. Несёт `aggregateVersion`                                                                                                                                            |
| `rop.debt.case.status-changed`                        | debt-case-service                   | collection-notification-service, legacy-collection-migration-service          | caseId       | Любая смена статуса дела. Несёт `aggregateVersion`                                                                                                                                                 |
| `rop.debt.case.payment-received`                      | debt-case-service                   | restructuring-workflow-service                                                | caseId       | Клиент внёс платёж. Несёт `aggregateVersion`                                                                                                                                                       |
| `rop.debt.restructuring.cmd.proposeTerms`             | restructuring-workflow-service      | collection-notification-service                                               | caseId       | Команда: отправить клиенту предложение                                                                                                                                                             |
| `rop.debt.restructuring.evt.termsProposed`            | collection-notification-service     | restructuring-workflow-service                                                | caseId       | Уведомление поставлено в очередь notification-платформы (не подтверждение фактической доставки, см. [[04_data_flows#SLA таймер эскалации]])                                                        |
| `rop.debt.restructuring.evt.clientResponded`          | collection-notification-service     | restructuring-workflow-service                                                | caseId       | Клиент принял/отклонил условия                                                                                                                                                                     |
| `rop.debt.restructuring.cmd.applyNewTerms`            | restructuring-workflow-service      | debt-case-service                                                             | caseId       | Команда: применить новые условия. Обрабатывается через inbox                                                                                                                                       |
| `rop.debt.restructuring.evt.newTermsApplied`          | debt-case-service                   | restructuring-workflow-service                                                | caseId       | Результат применения (success/fail)                                                                                                                                                                |
| `rop.debt.restructuring.cmd.closeOldCase`             | restructuring-workflow-service      | debt-case-service                                                             | caseId       | Финальный шаг happy path                                                                                                                                                                           |
| `rop.debt.restructuring.cmd.compensateApplyNewTerms`  | restructuring-workflow-service      | debt-case-service                                                             | caseId       | Компенсация: откатить применённые условия                                                                                                                                                          |
| `rop.debt.restructuring.cmd.compensateProposeTerms`   | restructuring-workflow-service      | collection-notification-service                                               | caseId       | Компенсация: уведомить клиента об откате                                                                                                                                                           |
| `rop.legal.hold.detected`                             | legal-holds-integration-service     | debt-case-service, collection-notification-service                            | clientId     | Новый арест/взыскание                                                                                                                                                                              |
| `rop.legal.hold.released`                             | legal-holds-integration-service     | debt-case-service                                                             | clientId     | Арест снят                                                                                                                                                                                         |
| `rop.legal.hold.partially-paid`                       | legal-holds-integration-service     | debt-case-service                                                             | clientId     | Частичное погашение по аресту                                                                                                                                                                      |
| `rop.payments.legal-hold.payment-confirmed`           | Платёжный шлюз банка (внешний)      | legal-holds-integration-service                                               | clientId     | Оплата ареста прошла                                                                                                                                                                               |
| `rop.migration.batch.extracted`                       | legacy-collection-migration-service | — (статус доступен через `GET /migration/batches/{id}`)                       | batchId      | Батч выгружен из legacy                                                                                                                                                                            |
| `rop.migration.batch.validated`                       | legacy-collection-migration-service | —                                                                             | batchId      | Батч прошёл валидацию                                                                                                                                                                              |
| `rop.migration.reconciliation.mismatch-found`         | legacy-collection-migration-service | — (аналитик работает через `GET /reconciliation-report`)                      | batchId      | Найдено расхождение при сверке                                                                                                                                                                     |
| `rop.migration.case.migrated`                         | legacy-collection-migration-service | debt-case-service                                                             | caseId       | Дело перенесено в целевую систему                                                                                                                                                                  |
| `rop.migration.batch.cutover-completed`               | legacy-collection-migration-service | —                                                                             | batchId      | Класс дел переключён на целевую систему                                                                                                                                                            |
| `rop.notifications.sent` / `rop.notifications.failed` | collection-notification-service     | debt-case-service (журналирование)                                            | caseId       | Технический статус уведомления (обёртка над notification-platform)                                                                                                                                 |
| `rop.crm.customer.updated`                            | CRM (внешняя)                       | customer-profile-adapter                                                      | clientId     | Инвалидация локального кэша профиля                                                                                                                                                                |
| `rop.audit.event`                                     | все сервисы платформы               | audit-logging-service                                                         | entityId     | Единый контракт аудита, обязателен для комплаенс                                                                                                                                                   |
| `rop.legal.fssp-check.completed`                      | legal-holds-integration-service     | Risk Monitoring System, Онбординг/KYC (вне ROP)                               | clientId     | **Generic**-результат скрининга по ФССП, не привязан к домену задолженности. Исправленное имя — было недокументированным `client.fssp-check.completed`, см. [[00_domain_boundaries_and_use_cases]] |

> `audit-logging-service` подписан **только** на `rop.audit.event` — он не является прямым consumer'ом доменных топиков (в т.ч. `rop.migration.*`). Любой сервис, чьи операции подлежат аудиту, обязан сам публиковать соответствующее событие в `rop.audit.event`.

## Домен «Документы»

### document-request-service

Владелец жизненного цикла заявки на документ (`DocumentRequest`).

- `POST /api/v1/document-requests` — создать заявку. Обязательный заголовок `Idempotency-Key` (см. [[04_data_flows#Идемпотентность]])
- `GET /api/v1/document-requests/{id}` — статус
- `GET /api/v1/document-requests?subjectId=&status=` — список
- `POST /api/v1/document-requests/{id}/cancel` — отмена
- Produces: `rop.docs.request.created`, `rop.docs.request.cancelled`
- Consumes: `rop.docs.document.generated`, `rop.docs.document.generation-failed`, `rop.docs.document.archived`, `rop.docs.document.delivered`, `rop.docs.document.delivery-failed`

### document-generation-engine

Двухфазный pipeline сборки и рендеринга документа — подробности архитектуры в [[04_data_flows#Генерация документов]].

- `GET /api/v1/generation-jobs/{requestId}` — статус джобы
- `POST /api/v1/generation-jobs/{requestId}/retry` — ручной перезапуск для отказов, не помеченных `retryable`
- Consumes: `rop.docs.request.created`, `rop.docs.template.activated`
- Produces: `rop.docs.document.generated`, `rop.docs.document.generation-failed`
- Two-phase pipeline (Assembly → Render), детали — [[04_data_flows#document-generation-engine]]

### document-template-registry
- `GET /api/v1/templates/{documentType}/active`
- `POST /api/v1/templates` — загрузка новой версии (включает `schemaDefinition`)
- `PUT /api/v1/templates/{id}/activate`
- `GET /api/v1/templates/{documentType}/versions`
- Produces: `rop.docs.template.activated`

### document-delivery-service
- `GET /api/v1/deliveries/{documentId}`
- Consumes: `rop.docs.document.generated`
- Produces: `rop.docs.document.delivered`, `rop.docs.document.delivery-failed`

### document-archive-service
- `GET /api/v1/documents/{id}`
- `GET /api/v1/documents/{id}/download` — доступ проверяется по владельцу заявки, (`subjectId` = вызывающий) либо оператору с явным правом на дело/клиента (RBAC/ABAC, см. [[04_data_flows#Комплаенс]])
- `GET /api/v1/documents?subjectId=&type=&from=&to=` — для комплаенс-выгрузок
- Consumes: `rop.docs.document.generated`
- Produces: `rop.docs.document.archived`

## Домен «Проблемные активы»

### debt-case-service

Агрегат-корень домена: `DebtCase`. Владеет также `RestructuringWorkflowLock`.

- `POST /api/v1/debt-cases` — ручное создание оператором. Исключительный путь: штатно дело открывается по `rop.risk.overdue-signal.detected`, ручное создание — не альтернатива saga/operator-path из §[[03_services#Разделение путей изменения статуса]], а отдельный, более редкий сценарий (например, дело, обнаруженное вне автоматического risk-сигнала)
- `GET /api/v1/debt-cases/{id}`
- `GET /api/v1/debt-cases?clientId=&status=`
- `PATCH /api/v1/debt-cases/{id}/status` — **только** operator/client path, сагой не используется
- `POST /api/v1/debt-cases/{id}/payments`
- `POST /api/v1/debt-cases/{id}/restructuring-lock` — атомарный захват `RestructuringWorkflowLock` (`INSERT ... ON CONFLICT (caseId) DO NOTHING`); единственный синхронный REST-вызов от `restructuring-workflow-service`, меняющий состояние `debt-case-service` — документированное исключение из правила «изменения состояния только через Kafka», см. [[02_architecture#Ключевые архитектурные решения]]
- `DELETE /api/v1/debt-cases/{id}/restructuring-lock` — снятие лока (терминальное состояние процесса либо orphaned-лок по таймауту)
- Consumes: `rop.risk.overdue-signal.detected`, `rop.legal.hold.detected`, `rop.legal.hold.released`, `rop.migration.case.migrated`
- **Consumes (saga cmd-топики, отдельный inbox-consumer, не через HTTP):** `rop.debt.restructuring.cmd.applyNewTerms`, `rop.debt.restructuring.cmd.closeOldCase`, `rop.debt.restructuring.cmd.compensateApplyNewTerms`
- Produces: `rop.debt.case.opened`, `rop.debt.case.status-changed`, `rop.debt.case.payment-received`, `rop.debt.restructuring.evt.newTermsApplied`

#### Разделение путей изменения статуса

Два независимых пути, не пересекающиеся в реализации:

1. **Operator/client path** — `PATCH /api/v1/debt-cases/{id}/status` через HTTP. Инициатор — человек или явный клиентский сценарий. Аудит фиксирует `userId`.
2. **Saga path** — исключительно через Kafka cmd-топики, отдельный consumer с inbox-дедупликацией. Аудит фиксирует `processInstanceId`/`commandId`.

Разделение принципиально для аудита: без него нельзя отличить в логах ручную правку статуса от отката сагой — критично при комплаенс-разборе.

### restructuring-workflow-service (Camunda)
- `POST /api/v1/restructuring/{caseId}/start` — перед стартом вызывает `POST /debt-cases/{id}/restructuring-lock` - устанавливает лок
- `GET /api/v1/restructuring/{caseId}/status`
- Consumes (evt-топики, корреляция по caseId): `rop.debt.case.opened`, `rop.debt.restructuring.evt.termsProposed`, `rop.debt.restructuring.evt.clientResponded`, `rop.debt.restructuring.evt.newTermsApplied`, `rop.debt.case.payment-received`
- Produces (cmd-топики): `rop.debt.restructuring.cmd.proposeTerms`, `rop.debt.restructuring.cmd.applyNewTerms`, `rop.debt.restructuring.cmd.closeOldCase`, `rop.debt.restructuring.cmd.compensateApplyNewTerms`, `rop.debt.restructuring.cmd.compensateProposeTerms`
- Saga/компенсации — [[04_data_flows#Saga command event pattern]]

### legal-holds-integration-service

Асинхронная batch/poll-интеграция с публичным API ФССП (`api-ip.fssp.gov.ru`), не синхронный запрос-ответ: источник нестабилен по времени отклика, без sandbox, с суточной квотой (50 записей/группа, до 5000 групп/сутки). Изоляция — circuit breaker + bulkhead + выделенный rate-limiter (Redis, т.к. инстансов несколько). Полный поток — [[04_data_flows#Скрининг ФССП]].

- `GET /api/v1/legal-holds/{clientId}` — текущий известный статус (idempotent, из локального кэша/БД, не блокирующий вызов к ФССП)
- `POST /api/v1/legal-holds/check-requests` — инициировать асинхронную проверку по `clientId` (добавлено — отсутствовало в исходном реестре, без него нечем было запустить сценарий скрининга)
- `POST /api/v1/legal-holds/{id}/mark-paid`
- **Consumes:** `rop.payments.legal-hold.payment-confirmed`
- **Produces:** `rop.legal.fssp-check.completed` (generic, потребители вне ROP), `rop.legal.hold.detected`, `rop.legal.hold.released`, `rop.legal.hold.partially-paid` (доменная проекция через внутренний ACL — переводит текстовую классификацию предмета исполнения в доменные события)
- Собственные (не доменные, не выносятся в C4-модель) таблицы: `fssp_check_request` (статусная модель `PENDING→IN_PROGRESS→DONE/ERROR`, ФИО хранится хэшированным), `fssp_case` (`is_account_related`, `requires_manual_review`)
- Fail-open: недоступность ФССП не блокирует клиента, результат помечается `fssp_check_stale`
- Источник данных спрятан за портом `FsspProvider` — замена на платный агрегатор не требует изменений в бизнес-логике выше порта
- Внешняя интеграция — poll-based со стороны ROP (шедулеры), не Kafka-consumer; сам источник вне банковского контура (см. [[00_domain_boundaries_and_use_cases#2. Данные]])
### legacy-collection-migration-service
- `POST /api/v1/migration/batches`
- `GET /api/v1/migration/batches/{id}`
- `GET /api/v1/migration/batches/{id}/reconciliation-report`
- `POST /api/v1/migration/batches/{id}/cutover`
- Consumes: `rop.debt.case.status-changed`, `rop.debt.case.opened` (буферизация и упорядочивание по `aggregateVersion`, см. [[04_data_flows#Порядок событий]])
- Produces: `rop.migration.batch.extracted`, `rop.migration.batch.validated`, `rop.migration.reconciliation.mismatch-found`, `rop.migration.case.migrated`, `rop.migration.batch.cutover-completed`

### collection-notification-service
- `POST /api/v1/notifications/send`
- `GET /api/v1/notifications/{caseId}/history`
- Consumes: `rop.debt.restructuring.cmd.proposeTerms`, `rop.debt.restructuring.cmd.compensateProposeTerms`, `rop.legal.hold.detected`, `rop.debt.case.status-changed`
- Produces: `rop.debt.restructuring.evt.termsProposed`, `rop.debt.restructuring.evt.clientResponded`, `rop.notifications.sent`, `rop.notifications.failed`

## Разделение путей изменения `DebtCase.status`

Два независимых, физически разделённых пути:

1. **Operator/client path** — `PATCH /api/v1/debt-cases/{id}/status`, HTTP. Аудит фиксирует `userId` инициатора.
2. **Saga path** — исключительно Kafka cmd-топики, отдельный consumer с inbox-дедупликацией. Аудит фиксирует `processInstanceId`/`commandId`.

Разделение принципиально для комплаенс-разбора инцидентов — без него невозможно отличить в логах ручное изменение статуса от отката сагой.

## Инфраструктурные сервисы

**auth-gateway** — только REST (OAuth2/OIDC), в событийной модели не участвует.

**customer-profile-adapter**
- `GET /api/v1/customers/{id}/profile`
- Consumes: `rop.crm.customer.updated` (инвалидация кэша)

**audit-logging-service**
- `GET /api/v1/audit?entityId=&from=&to=` — для комплаенс-запросов
- Consumes: `rop.audit.event`
