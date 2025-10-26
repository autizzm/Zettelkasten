
Theory for the cards: [[Column definition options (Constraints) - SQL]] 

FILE TAGS: sql

Q: Что делает SERIAL под капотом? Как сгенерировать авотинкрементное значение, если нужно инкрементировать не INTEGER, а другие числовые типы?
A: SERIAL- синтаксический сахар (это макрос):
    - Создаёт колонку `id integer NOT NULL`.
	- Создаёт последовательность (sequence) с именем вроде `users_id_seq`.
	- Назначает для `id` значение по умолчанию `nextval('users_id_seq'::regclass).`
	
Автоинкремент работает только с целыми типами (`SMALLINT`, `INT`, `BIGINT`) через `SERIAL`, `BIGSERIAL` или `GENERATED ... AS IDENTITY`.
<!--ID: 1761474628940-->
