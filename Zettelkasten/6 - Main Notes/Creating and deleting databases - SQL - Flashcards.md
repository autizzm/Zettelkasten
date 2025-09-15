
Theory for the cards: [[Creating and deleting databases - SQL]]

FILE TAGS: sql

Q: Как создать БД скриптом sql (запросом)?
A:   
```sql
CREATE DATABASE database_name;
```
<!--ID: 1757954053409-->


Q: Как удалить БД sql скриптом (запросом)?
A:   
```sql
DROP DATABASE database_name;
```
<!--ID: 1757954053424-->


Q: Как удалить БД sql скриптом (запросом) и при этом избежать ошибки, если эта БД уже была удалена?
A:   
```sql
DROP DATABASE IF EXISTS database_name;
```
<!--ID: 1757954053432-->


Q: Как создать БД sql скриптом (запросом) и при этом избежать ошибки, если эта БД уже была создана (имя занято)?
A:    
```sql
CREATE DATABASE IF NOT EXISTS database_name; 
```
<!--ID: 1757954053440-->
