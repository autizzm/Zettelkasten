


![[Pasted image 20260902200602.png]]

**Технический дизайн: Расширенный soft-collection + PromiseToPay**  
(версия 2.0 — с явной интеграцией в основной поток и триггером продолжения через API)

---

### 1. Цель

Добавить управляемый soft-collection этап в жизненный цикл `DebtCase`:

- Автоматические серии напоминаний.
- Клиент может дать структурированное обещание заплатить через кнопку в мобильном приложении / веб-кабинете.
- Вызов специального эндпоинта является **триггером продолжения** BPMN-процесса.
- Camunda после получения обещания ждёт до `dueDate` и дальше идёт по веткам fulfilled / broken.

Реализация — без нового сервиса, за 1–2 спринта.

---

### 2. Высокоуровневый поток (после нажатия кнопки клиентом)

1. Клиент в приложении/на сайте нажимает «Обещаю заплатить».
2. Frontend вызывает защищённый эндпоинт `debt-case-service`.
3. `debt-case-service`:
   - создаёт `PromiseToPay` в статусе `ACTIVE`;
   - публикует доменное событие `rop.debt.soft.evt.promiseCreated`;
   - синхронно (или через команду) уведомляет `restructuring-workflow-service` о необходимости продолжить процесс.
4. `restructuring-workflow-service` коррелирует сообщение в Camunda по `caseId` + `processInstanceId`.
5. Camunda получает триггер, выходит из состояния ожидания ответа клиента и ставит timer до `dueDate`.
6. Дальше процесс идёт по стандартным gateway’ам (платёж пришёл / не пришёл).

Именно вызов эндпоинта (через создание Promise + сообщение в workflow) является триггером продолжения BPMN.

---
### 4. Изменения по сервисам

#### debt-case-service

Новый эндпоинт (триггер продолжения):

```
POST /api/v1/debt-cases/{caseId}/promises
```

Тело:
```json
{
  "amount": "15000.00",
  "dueDate": "2026-09-15",
  "source": "CLIENT_APP"
}
```

Логика:
1. Проверяет права (клиент = владелец дела или оператор).
2. Создаёт `PromiseToPay` (status = `ACTIVE`).
3. В одной транзакции пишет outbox-событие `rop.debt.soft.evt.promiseCreated`.
4. Отправляет команду/сообщение в `restructuring-workflow-service` для корреляции Camunda (или workflow сам слушает событие и коррелирует).

Дополнительно:
- `GET /api/v1/debt-cases/{caseId}/promises`
- `POST /api/v1/debt-cases/{caseId}/promises/{id}/cancel` (оператор)

#### restructuring-workflow-service

- Слушает `rop.debt.soft.evt.promiseCreated` (или принимает внутреннюю команду).
- Выполняет message correlation в Camunda по `caseId`.
- Camunda продолжает процесс и ставит timer.

#### collection-notification-service

- Обрабатывает `cmd.sendReminder`.
- Не отвечает за приём promise (это делает debt-case-service через клиентский эндпоинт).

---

### 5. Новая доменная сущность

**PromiseToPay**

| Поле            | Тип          | Описание                          |
|-----------------|--------------|-----------------------------------|
| id              | UUID         | PK                                |
| caseId          | UUID         | FK                                |
| amount          | Money        | Обещанная сумма                   |
| dueDate         | LocalDate    | Дата исполнения                   |
| status          | Enum         | ACTIVE → FULFILLED / BROKEN / CANCELLED |
| source          | String       | CLIENT_APP / OPERATOR             |
| createdAt       | Instant      |                                   |
| fulfilledAt     | Instant?     |                                   |
| brokenAt        | Instant?     |                                   |
| aggregateVersion| Long         |                                   |

---

### 6. Потоки данных

1. Camunda → `rop.debt.soft.cmd.sendReminder` → collection-notification-service → Notification Platform.
2. Клиент нажимает кнопку → `POST .../promises` → debt-case-service.
3. debt-case-service → `rop.debt.soft.evt.promiseCreated` + триггер в workflow.
4. workflow коррелирует сообщение в Camunda.
5. Camunda ставит timer.
6. Приходит `rop.debt.case.payment-received` → debt-case-service проверяет активные Promise и публикует `promiseFulfilled` / позже `promiseBroken`.

---

### 7. Декомпозиция задач и оценка

**Спринт 1 (основной) — 9–11 SP**

| Задача | Оценка | Описание |
|--------|--------|----------|
| Сущность PromiseToPay + миграция | 2 SP | |
| Эндпоинт POST /promises + создание + outbox | 2 SP | Именно этот вызов — триггер |
| Слушатель promiseCreated + correlation в Camunda | 2 SP | |
| BPMN soft-collection (напоминания + Message Catch + timer) | 3 SP | |
| cmd.sendReminder в notification-service | 1 SP | |
| Contract-тесты новых топиков | 1 SP | |

**Спринт 2 (завершение) — 4–6 SP**

| Задача | Оценка |
|--------|--------|
| Реакция на payment-received → FULFILLED/BROKEN | 2 SP |
| Интеграционные тесты полного флоу (кнопка → timer → ветки) | 2 SP |
| API для оператора + права | 1 SP |
| Метрики + документация | 1 SP |

---

### 8. Критерии готовности

- Клиент может нажать кнопку в приложении и этим продолжить BPMN-процесс.
- Camunda корректно выходит из Message Catch Event и ставит timer до dueDate.
- При поступлении платежа Promise переходит в FULFILLED.
- При истечении timer без платежа — BROKEN + эскалация.
- Оператор может создать/отменить Promise вручную.
- Все новые события покрыты contract-тестами.
- Существующая сага реструктуризации не сломана и запускается после soft-collection при необходимости.

Этот вариант полностью соответствует текущей архитектуре: эндпоинт на `debt-case-service` является явным триггером продолжения, Camunda остаётся центром оркестрации, а notification-service остаётся только исполнителем отправки.