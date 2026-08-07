
Theory for the cards: [[ACL (Access Control List) - Kafka]]

FILE TAGS: kafka

Q: Что такое ACL в Kafka?  
A: ACL (Access Control List) — механизм авторизации Kafka, который определяет, какой principal (пользователь) может выполнять какие операции над какими ресурсами.
<!--ID: 1785998553799-->


Q: На каком этапе используется ACL в Kafka?  
A: После аутентификации клиента. Сначала Kafka определяет principal (например, User:orders-service), затем проверяет ACL перед выполнением операции.
<!--ID: 1785998553807-->


Q: Какие основные типы ресурсов существуют в Kafka ACL?  
A: Topic, Group (consumer group), Cluster, TransactionalId и DelegationToken.
<!--ID: 1785998553811-->


Q: Какие операции чаще всего используются в Kafka ACL?  
A: Read, Write, Describe, Create, Delete, Alter, DescribeConfigs, AlterConfigs, IdempotentWrite и All.
<!--ID: 1785998553816-->


Q: Какие права обычно нужны producer (Kafka ACL)?  
A: Write на соответствующий topic. Иногда также Describe для получения метаданных.
<!--ID: 1785998553821-->


Q: Какие права обычно нужны consumer (Kafka ACL)?  
A: Read на topic и Read на consumer group.
<!--ID: 1785998553825-->


Q: Почему consumer недостаточно только права Read на topic (Kafka ACL)?  
A: Потому что consumer взаимодействует с consumer group и коммитит offsets, поэтому требуется Read на ресурс Group.
<!--ID: 1785998553830-->


Q: Какую ошибку получит consumer без прав на consumer group (Kafka ACL)?  
A: GroupAuthorizationException.
<!--ID: 1785998553834-->


Q: Какую ошибку получит клиент без прав на topic (Kafka ACL)?  
A: TopicAuthorizationException.
<!--ID: 1785998553839-->


Q: Что делает операция Describe (Kafka ACL)?  
A: Разрешает получать метаданные ресурса (например, информацию о topic и его partition).
<!--ID: 1785998553843-->


Q: Что означает операция All (Kafka ACL)?  
A: Предоставляет все операции для указанного ресурса.
<!--ID: 1785998553848-->


Q: Для чего используется операция IdempotentWrite (Kafka ACL)?  
A: В Kafka для работы с идемпотентной записью продюсера (enable.idempotence=true) нужно выдавать отдельный тип доступа в ACL: IdempotentWrite. 
<!--ID: 1785998553852-->


Q: Что такое principal в Kafka ACL?  
A: Идентификатор аутентифицированного клиента, например `User:orders-service`.
<!--ID: 1785998553857-->


Q: Какая команда добавляет ACL?  
A:   
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:producer \
  --operation Write \
  --topic orders
```
<!--ID: 1785998553861-->


Q: Какая команда показывает ACL?  
A: `kafka-acls.sh --bootstrap-server --list`
<!--ID: 1785998553866-->


Q: Какая команда удаляет ACL?  
A:  
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --remove \
  --allow-principal User:producer \
  --operation Write \
  --topic orders
```
<!--ID: 1785998553870-->


Q: Что означает resource-pattern-type prefixed?  
A: ACL применяется ко всем ресурсам, имя которых начинается с указанного префикса.
Пример:
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:order-service \
  --operation Read \
  --topic orders. \
  --resource-pattern-type prefixed
```
Теперь ACL действует на все topics, начинающиеся с `orders.`.
<!--ID: 1785998553875-->


Q: Когда полезно использовать prefixed ACL?  
A: Когда сервису нужен доступ ко всем topics своего домена, например orders.created, orders.updated и orders.deleted.
Пример:
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:order-service \
  --operation Read \
  --topic orders. \
  --resource-pattern-type prefixed
```
Теперь ACL действует на все topics, начинающиеся с `orders.`.
<!--ID: 1785998553880-->


Q: Какие типы шаблонов ресурсов существуют в Kafka ACL?  
A: literal (точное совпадение), prefixed (по префиксу) и wildcard (`*` - типо доступ ко всему) .
Пример:
```bash
kafka-acls.sh \
  --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:order-service \
  --operation Read \
  --topic orders. \
  --resource-pattern-type prefixed
```
Теперь ACL действует на все topics, начинающиеся с `orders.`.
<!--ID: 1785998553884-->


Q: Где хранятся ACL в Kafka с ZooKeeper?  
A: В ZooKeeper.
<!--ID: 1785998553889-->


Q: Где хранятся ACL в Kafka с KRaft?  
A: Во внутреннем metadata log контроллеров KRaft.
<!--ID: 1785998553894-->


Q: Как Kafka принимает решение о доступе?  
A: Сравнивает principal, операцию и ресурс с существующими ACL. Если найдено подходящее правило Allow — операция разрешается.
При запросе клиента Kafka знает:
	
- principal (например, `User:consumer`)
    
- тип операции (`Read`)
    
- ресурс (`orders`)
    
И выполняет проверку: `User:consumer + Read + Topic:orders`
	
Если найдено правило `Allow` → операция разрешена.
	
Если нет подходящего правила → TopicAuthorizationException или GroupAuthorizationException.
<!--ID: 1785998553899-->


Q: Какой минимальный набор ACL нужен микросервису payment-service, который читает orders и пишет payments?  
A: Read на topic orders, Write на topic payments и Read на consumer group payment-group.
<!--ID: 1785998553904-->


Q: Какие типы Access нужны Consumer (Kafka ACL)?  
A: Consumer почти всегда требует две ACL: Read на topic и Read на consumer group.
<!--ID: 1785998553908-->


Q: Какие типы Access нужны priducer (Kafka ACL)?  
A: Producer обычно требует Write на topic; при использовании идемпотентного producer также необходима операция IdempotentWrite на Cluster.
<!--ID: 1785998764666-->
