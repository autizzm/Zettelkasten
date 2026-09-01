
Theory for the cards: [[Redis]]

FILE TAGS: redis redis_common

Q: Какой протокол используется для подключения к Redis?
A: Для подключения к Redis используется **Redis Serialization Protocol (RESP)** — собственный текстово-бинарный протокол Redis на прикладном уровне (P.S. Скорее уровень представления по OSI).
	
Важно разделять **протокол и транспорт**:
	
```
Java application
      │
      │ RESP
      ▼
    TCP
      │
      ▼
    Redis
```