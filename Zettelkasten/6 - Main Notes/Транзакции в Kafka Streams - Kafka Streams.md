
11-08-2026 19:40

Status:

Tags: [[Kafka Streams]] [[Kafka]] [[Java+]]

---
# Транзакции в Kafka Streams - Kafka Streams


**Да**, `processing.guarantee = exactly_once_v2` использует **именно классические Kafka Transactions** (transactional producer + Transaction Coordinator + `__transaction_state`).

### Как это работает

Kafka Streams при включении `exactly_once_v2` под капотом:

1. Создаёт **transactional producers** (с `transactional.id`).
2. Оборачивает в одну транзакцию:
   - запись результатов обработки в output-топики;
   - запись в changelog-топики state stores;
   - коммит consumer offsets (через `sendOffsetsToTransaction`).
3. Использует `isolation.level = read_committed` на стороне consumer’а.
4. Включает idempotence (`enable.idempotence = true`).

То есть это полноценный механизм транзакций Kafka (тот самый, который появился в 0.11), а не какая-то отдельная реализация.

### Чем `exactly_once_v2` отличается от старого `exactly_once`

| | `exactly_once` (v1) | `exactly_once_v2` |
|---|---|---|
| Когда появился | Kafka 0.11 / Streams 0.11 | Kafka 2.5 / Streams 2.6 (KIP-447) |
| Сколько transactional producers | Один на **task** (много) | Один на **stream thread** (значительно меньше) |
| Нагрузка на Transaction Coordinator | Высокая | Значительно ниже |
| Требования к брокерам | ≥ 0.11 | ≥ 2.5 |
| Статус | Deprecated с Kafka 3.0 | Рекомендуемый и актуальный вариант |

Разница только в том, **как** Streams управляет жизненным циклом транзакций и сколько producers создаёт. Сами транзакции — классические.

### Важные нюансы

- Нужен кластер минимум из 3 брокеров (из-за replication factor топика `__transaction_state`).
- `commit.interval.ms` по умолчанию становится 100 мс (вместо 30 сек).
- Гарантия end-to-end exactly-once работает только в рамках Kafka → Kafka (включая state stores). При записи во внешние системы (БД, REST и т.д.) нужны дополнительные меры (outbox, idempotent sink и т.п.).

**Итог**: да, это именно классические Kafka transactions, только с более эффективной реализацией в Streams (EOS v2).

----
#### [[Транзакции в Kafka Streams - Kafka Streams - Flashcards|Link to flashcards]]



---
### References:

