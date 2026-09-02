# 02. Архитектура

См. также [[01_overview]], [[03_services]], [[04_data_flows]].

## Архитектурный стиль

Event-driven, два доменных модуля («Документы», «Проблемные активы»), интеграция через Apache Kafka. Синхронные REST-вызовы между сервисами платформы допускаются только для:

- запросов текущего состояния (idempotent GET);
- атомарной проверки/установки распределённой блокировки, где по природе операции нужен синхронный ответ до старта длительного процесса (единственный документированный пример — захват `RestructuringWorkflowLock`, см. [[04_data_flows#Блокировка процесса реструктуризации]] и [[03_services#debt-case-service]]).

Всё остальное, что меняет состояние другого агрегата, передаётся Kafka-событием.

## C4: System Context

![[Pasted image 20260902161947.png]]

```plantuml
@startuml C4_Context
left to right direction
skinparam actorStyle awesome

actor "Оператор\nконтакт-центра" as operator
actor "Клиент банка" as customer

rectangle "Retail Operations\nPlatform (ROP)" as rop #LightBlue

rectangle "CRM /\nмастер-справочник клиентов" as crm
rectangle "Risk Monitoring\nSystem" as risk
rectangle "ФССП" as fssp
rectangle "Платёжный шлюз\nбанка" as payment
rectangle "Notification\nPlatform" as notification
rectangle "Legacy Collection\nSystem" as legacy

operator --> rop : работает с делами,\nреструктуризациями,\nдокументами (HTTPS)
customer --> rop : запрашивает документы,\nвзаимодействует по\nреструктуризации (HTTPS / Mobile / Web)

rop --> crm : получает данные клиентов /\nсобытия об изменениях (REST / Events)
risk --> rop : сигналы о просрочке (Kafka)
rop --> fssp : сведения об арестах\nи взысканиях (REST / Polling)
payment --> rop : подтверждения платежей\nпо арестам (Events)
rop --> notification : уведомления клиентам (Kafka / API)
rop --> legacy : миграция дел,\nсверка данных (Kafka / API)
@enduml
```


## Общая схема (event-driven core)


![[Pasted image 20260902161824.png]]

```plantuml
@startuml C4_Components
skinparam componentStyle rectangle
skinparam rectangle {
  BackgroundColor<<docs>> #E8F0FE
  BackgroundColor<<debt>> #FDEEE0
  BackgroundColor<<infra>> #ECECEC
  BorderColor #4A76A8
}

rectangle "API Gateway" as gw

package "Домен «Документы»" <<docs>> {
  rectangle "document-request-service" as drs
  rectangle "document-generation-engine" as dge
  rectangle "document-template-registry" as dtr
  rectangle "document-delivery-service" as dds
  rectangle "document-archive-service" as das
}

package "Домен «Проблемные активы»" <<debt>> {
  rectangle "debt-case-service" as debt
  rectangle "restructuring-workflow-service\n(Camunda BPMN)" as wf
  rectangle "legal-holds-integration-service" as legal
  rectangle "legacy-collection-migration-service" as migration
  rectangle "collection-notification-service" as notif
}

package "Инфраструктурные сервисы" <<infra>> {
  rectangle "audit-logging-service" as audit
  rectangle "customer-profile-adapter" as profile
  rectangle "auth-gateway" as auth
}

queue "Kafka cluster\n(топики rop.*)" as kafka

gw --> drs
gw --> debt
gw --> wf
gw --> legal
gw --> das

drs <--> kafka
dge <--> kafka
dtr --> kafka
dds <--> kafka
das <--> kafka

debt <--> kafka
wf <--> kafka
legal <--> kafka
migration <--> kafka
notif <--> kafka

kafka --> audit
kafka --> profile

auth ..> gw : OAuth2/OIDC\n(вне событийной модели)

note bottom of kafka
  Партиционирование — по caseId/requestId.
  Порядок гарантирован только внутри
  одного топика, не между топиками.
end note
@enduml
```


---
## Ключевые архитектурные решения

| Решение | Обоснование | Подробности |
|---|---|---|
| Command/event через Kafka вместо синхронных вызовов между сервисами | Процесс реструктуризации длится дни/недели — блокирующий синхронный вызов архитектурно неверен | [[04_data_flows#Saga command event pattern]] |
| Orchestration-based saga (Camunda) вместо хореографии | Единый source of truth по состоянию юридически чувствительного процесса, проще отладка и аудит | [[04_data_flows#Saga command event pattern]] |
| Inbox/outbox вместо распределённых транзакций | Kafka даёт at-least-once — exactly-once бизнес-эффект обеспечивается идемпотентностью на стороне потребителя/издателя | [[04_data_flows#Идемпотентность]] |
| Партиционирование Kafka-топиков по `caseId`/`requestId` + `aggregateVersion` в конверте события | Гарантия порядка есть только внутри одного топика; `aggregateVersion` даёт причинный порядок между топиками одного агрегата | [[04_data_flows#Порядок событий]] |
| HTML/CSS (Thymeleaf) → headless PDF вместо JasperReports | Рекурсивная вложенность документа нативно ложится на HTML/CSS, шаблон diff-able в PR | [[06_tech_stack]] |
| Schema Registry не разворачивается на текущем этапе | Дешёвая альтернатива (`schemaVersion` в конверте + контрактные тесты в CI) закрывает риск при текущем числе топиков; пересмотр — при росте числа consumer-групп | [[03_services#Формат сообщений и совместимость схем]] |
| Захват `RestructuringWorkflowLock` — единственный синхронный REST-вызов, меняющий состояние другого сервиса | Инвариант «не более одного активного процесса на caseId» физически не может проверяться асинхронно перед стартом длительного BPMN-процесса; лок физически хранится в `debt-case-service`, рядом с данными, которые защищает | [[04_data_flows#Блокировка процесса реструктуризации]] |

## Слои изоляции отказов

- `legal-holds-integration-service` изолирует нестабильный внешний источник (ФССП) через circuit breaker + bulkhead — см. [[06_tech_stack]].
- Компенсирующие обработчики саги — локальные идемпотентные операции без синхронных вызовов наружу — см. [[04_data_flows#Компенсации]].
- Структурно повреждённые Kafka-сообщения уходят в инфраструктурный DLQ-топик до попадания в бизнес-логику; бизнес-ошибки обрабатываются через inbox-статус `FAILED`, отдельного DLQ для них нет — см. [[03_services#Реестр топиков]].
