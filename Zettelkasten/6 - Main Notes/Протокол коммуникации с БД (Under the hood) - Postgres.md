
18-10-2025 18:20

Status: [[Java+]] [[SQL]]

Tags:

---
# Протокол коммуникации с БД (Under the hood) - Postgres

##  Подключение к БД

1. открытие TCP соединения
2. Клиент отправляет первое сообщение (варианты первого сообщения):
	- "startup packet"
	- SSL request
	- GSS encryption request
	- Query cancel packet
	- [[TLS]] hello
	На этом этапе устанавливаются имя БД, которой подключаемся, имя пользователя, протокол соединения (используем TLS или по чистому TCP)
3. аутентификация - сервер отправляет один из видов запросов на получение пароля.
	
![[Pasted image 20251018183756.png]]
	


## **TL;DR**

- коммуникация с БД идёт по TCP

----
#### [[Протокол коммуникации с БД (Under the hood) - Postgres - Flashcards|Link to flashcards]]



---
### References:

