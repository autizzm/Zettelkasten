
07-07-2026 09:37

Status:

Tags: [[RabbitMQ]] [[Java+]]

---
# Overview - RabbitMQ

![[Pasted image 20260707093751.png]]

**~={red}?!=~** RabbitMQ реализует push модель: он сам пашит сообщения консюмерам.

RabbitMQ реализует synchronous-like communication, поскольку при получении event он сразу пшит его консбмеру, который в ответ обязан прислать ACK.

Можем получить асинхронность убрав ACK, сделав fire and forget.


### Message Persistence

**RabbitMQ can persist messages to disk**, but **persistence is not enabled by default**. There are three related concepts that are easy to confuse:

1. **Durable queue** – the queue survives a broker restart.
2. **Persistent message** – the message is written to disk (eventually).
3. **Publisher confirms** – the producer knows the broker has safely accepted the message.

You typically need **all three** if you don't want to lose messages.


----
#### [[Overview - RabbitMQ - Flashcards|Link to flashcards]]



---
### References:

