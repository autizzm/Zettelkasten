
Theory for the cards: [[Идемпотентные Producer & Consumer - Kafka]]

FILE TAGS: kafka java_interview

Q: Что такое идемптентный Producer? Могут ли idempotent Producer + idempotent Consumer гарантировать Exactly-Once?
A: Идмпотентный producer - гарантирует, что повторная доставка не создаст дубликатов.
	
**Producer получает**:
	
1. **PID** (Producer ID) — уникальный идентификатор Producer'а на брокере
    
2. **Sequence Number** — монотонно растущий номер для каждого сообщения в partition
	
```
Producer → Kafka:
Message1: PID=1001, Seq=1
Message2: PID=1001, Seq=2  
Message3: PID=1001, Seq=3
```
	
Как брокер определяет дубликаты
	
```
Брокер хранит: (PID + partition) → last_sequence_number
	
Producer retry Message2 (Seq=2):
1. Брокер видит: Seq=2 == last_sequence=1 + 1 ✅
2. Записывает Message2 (Seq=2)
3. Обновляет last_sequence=2
	
Producer retry Message1 (Seq=1) снова:
1. Брокер видит: Seq=1 < last_sequence=2 ❌
2. **Игнорирует дубликат**
```
	
---
**В Kafka нет idempotent Consumer**
	
---
> [!warning] **Idempotent Producer не делает Exactly-Once!**
> ```
> ❌ Без Outbox (At-Least-Once + Idempotent Producer):
>1. INSERT INTO orders (id=123, status='created') ✅
>2. producer.send("orders-created", event) ❌ Kafka упал
>
>Результат:
>- Заказ в БД ✅
>- Событие в Kafka ❌ ← PaymentService не узнает!
> ```
	
Это проблема Dual Writes.
<!--ID: 1769955471468-->


Q: В чем преимущества OUTBOX перед простым idempotent producer в Kafka? В каких случаях OUTBOX обязателен, а в каких - допустимо использовать idempotent Producer?
A: **Outbox Pattern** гарантирует **атомарность** бизнес-данных + события, **idempotent producer** — только дедупликацию на Kafka уровне.
	
**Ключевая разница**
	
|Аспект|Idempotent Producer|Outbox Pattern|
|---|---|---|
|**Гарантия**|Exactly-once **от одного producer**|**Атомарность** БД + Kafka|
|**Дубли**|✅ Устраняет (PID + sequence)|✅ + потерянные события|
|**Fail сценарий**|БД записалась, Kafka нет|❌ НЕТ таких сценариев|
|**Сложность**|Низкая (`enable.idempotence=true`)|Высокая (таблица + publisher)|
	
---
**Преимущества Outbox**
	
**1. Атомарность транзакции**
	
```java
// ❌ Idempotent producer (race condition)
@Transactional
updateBalance(-100);
kafkaTemplate.send("payments", event); // rollback не сработает!
	
// ✅ Outbox (атомарно)
@Transactional
updateBalance(-100);
outboxRepository.save(event); // Всё или ничего
```
	
**2. Восстановление после сбоев**
	
```
БД закоммитилась → событие в outbox → publisher ДОШЛЁТ
Kafka упал → publisher подождёт и отправит
App крашнулся → события НЕ потеряны
```
	
**3. Audit trail**
	
```
outbox_events: id, aggregate_id, payload, status, created_at, sent_at
- Отслеживание всех событий
- Replay при ошибках
- Debug дубликатов
```
	
---
**Когда Outbox обязателен**
	
✅ **Денежные операции** (балансы, платежи)  
✅ **Консистентность** aggregate → event  
✅ **Регуляторные требования** (audit)  
✅ **High stakes** бизнес-логика
	
**Когда хватит idempotent producer**
	
✅ **Некритичные уведомления**  
✅ **Статистика** (можно пересчитать)  
✅ **Fire-and-forget** события
	
**Вывод**: Outbox = **страховка от потерянных событий**. Idempotent producer = **оптимизация**, но не панацея. Для платежей/балансов — Outbox must have.
<!--ID: 1773846001363-->

Q: В каких случаях idempotent producer всё равно приведёт к дубликации сообщения?
A: В случаях:
- рестарта приложения (рестарт сессии продюсера)
- отправка копий в разные партиции (например так будет по дефолту, если не указать ключ, посольку без ключа используется batched round robin)
<!--ID: 1783497013225-->
