# 06. Технологический стек

См. также [[02_architecture]], [[03_services]], [[04_data_flows]].

## Языки и рантайм

| Компонент | Технология | Комментарий |
|---|---|---|
| Backend-сервисы | Java / Kotlin | Основной язык backend-разработки в команде |
| Оркестрация процессов | Camunda (BPMN) | Java-based движок, естественно ложится на существующий стек |
| Документ-шаблоны | Thymeleaf | HTML/CSS-шаблонизация, см. [[#Документогенерация]] |
| Frontend (операторский UI) | — | Внутренние панели для оператора контакт-центра |

## Данные и хранилища

| Назначение | Технология | Комментарий |
|---|---|---|
| Основное хранилище сервисов | PostgreSQL | Inbox/outbox используют `SELECT ... FOR UPDATE SKIP LOCKED` |
| Legacy-хранилище | Oracle (PL/SQL) | Источник миграции, см. [[04_data_flows#Reconciliation при миграции]] |
| Объектное хранилище | S3-совместимое | Готовые PDF и опциональные чекпоинты `StatementNode` (префикс `checkpoints/`) |
| Миграции схемы БД | Flyway | Применяется при каждом изменении полей |
| Сериализация чекпоинтов | Protobuf / MessagePack | Компактнее JSON при большой глубине дерева документа |
| Сериализация Kafka-событий | JSON + JSON Schema контракты | `contracts/kafka/*.schema.json`, обязательный `schemaVersion` в конверте |
| JSON-десериализация (пример) | Jackson | Строгий маппинг без толерантности к смене типа поля — источник инцидента, см. [[08_developer_experience#Инцидент несовместимая схема события]] |

## Messaging & Orchestration

| Компонент | Технология | Комментарий |
|---|---|---|
| Брокер событий | Apache Kafka | Партиционирование по `caseId`/`requestId`, реестр топиков — [[03_services#Реестр топиков]] |
| Schema Registry | **Не развёрнут** | Сознательно отложенное решение, см. [[02_architecture#Ключевые архитектурные решения]] |
| Оркестрация саги | Camunda BPMN, command/event pattern | [[04_data_flows#Saga command event pattern]] |

## Устойчивость (resilience)

| Паттерн | Технология | Где применяется |
|---|---|---|
| Circuit breaker + bulkhead | Resilience4j | `legal-holds-integration-service` — изоляция нестабильного внешнего источника (ФССП) |
| Идемпотентность команд | Inbox-паттерн (Postgres, `commandId` PK) | `debt-case-service` |
| Атомарная публикация событий | Outbox-паттерн (Postgres, `SELECT ... FOR UPDATE SKIP LOCKED`) | `debt-case-service` |
| Компенсация без тупиков | At-least-once retry без SLA-таймаута | Compensation Service Task'и саги |

## Документогенерация

**Технология зафиксирована:** HTML/CSS-шаблонизация (Thymeleaf) с рендерингом в PDF через headless-браузер — вместо JasperReports и вместо ручного layout-движка на PDFBox.

Обоснование:
- Рекурсивная вложенность (`Период → Под-период → Группа → Транзакция`) нативна для HTML — вложенные `<table>`/`<div>` произвольной глубины без специального движка.
- Page-break на границах узла декларативен (`break-inside: avoid`), не требует ручного расчёта на этапе рендера.
- HTML/CSS-шаблон — текстовый файл, нормально ревьюится в PR построчно (в отличие от бинарных `.jrxml` в Jasper Studio).
- Низкий порог входа и bus factor: HTML/CSS/Thymeleaf знаком любому разработчику команды.
- Тестируемость: результат Assembly-фазы можно отрендерить как обычный HTML и открыть в браузере без полного пайплайна до PDF.

**Открытые вопросы (требуют PoC до реализации):**
- Конкретный headless-рендерер: `wkhtmltopdf` исключён (unmaintained). Кандидаты — headless Chromium (Playwright/Puppeteer, хорошая поддержка CSS `@page`) либо специализированные paged-media движки (WeasyPrint, Prince XML).
- Валидность PDF/A на выходе — требуется для архивного хранения банковских документов, не все headless-рендереры генерируют его из коробки.

## Тестирование

| Уровень | Технология |
|---|---|
| Unit | JUnit |
| Компонентные | Embedded Kafka + Testcontainers (Postgres) |
| Интеграционные | Полный цикл через тестовый Camunda-движок |
| Contract-тесты | JSON Schema валидация в CI (producer и consumer) |
| Локальные внешние зависимости | WireMock (мок ФССП/внешнего источника арестов) |

## Безопасность и комплаенс

| Мера | Технология / подход |
|---|---|
| Транспорт | mTLS между сервисами, включая внутренний Kafka-трафик |
| Хранение ПДн | Field-level encryption в БД (сверх disk encryption) |
| Файлы | Server-side encryption в S3, проверка владения на `/download` |
| Право на удаление vs аудит-лог | Псевдонимизация / crypto-shredding per-subject ключом |
| Доступ | RBAC/ABAC «need to know», row-level доступ |
| Идентификация | OAuth2/OIDC (`auth-gateway`) |

Подробности — [[04_data_flows#Комплаенс и защита персональных данных]].

## Наблюдаемость

- Consumer lag по ключевым consumer-группам (в первую очередь `collection-notification-service`, `restructuring-workflow-service`).
- Доля `evt.newTermsApplied(success=false)`.
- Глубина инфраструктурного DLQ-топика (только структурно повреждённые сообщения).
- Возраст `FAILED`-записей в inbox — алертинг в ops/комплаенс.
