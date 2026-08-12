
12-08-2026 11:50

Status:

Tags: [[SQL]]

---
# `WITH` (CTE — Common Table Expression)


#### Summary

**Идея:** именованный промежуточный результат внутри **одного SQL-запроса**. Делает сложные запросы читаемее; используется с `SELECT/INSERT/UPDATE/DELETE`.

```sql
WITH active_users AS (
    SELECT *
    FROM users
    WHERE active = true
)
SELECT *
FROM active_users
WHERE age > 18;
```

**Что знать:**

- Несколько CTE: `WITH a AS (...), b AS (...)`.
    
- CTE может использовать предыдущие CTE.
    
- `WITH ... RETURNING` — удобно для атомарных операций и **Inbox/Outbox polling**.
    
- `WITH RECURSIVE` — обход деревьев/графов.
    
- CTE **не обязательно материализуется**: PostgreSQL может inline-ить его.
    
- `MATERIALIZED` — принудительная материализация.
    
- `NOT MATERIALIZED` — разрешить inline.
    
- CTE ≠ `TEMP TABLE`: CTE существует только в рамках одного statement.
    

**Senior nuance:** не стоит автоматически считать CTE оптимизационным преимуществом — иногда он улучшает читаемость, но материализация или особенности оптимизации могут ухудшить performance.







----
#### [[WITH - SQL - Flashcards|Link to flashcards]]



---
### References:

