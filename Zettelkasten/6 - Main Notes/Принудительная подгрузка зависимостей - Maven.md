
19-10-2025 09:21

Status: #adult 

Tags: [[Maven]]

---
# Принудительная подгрузка зависимостей - Maven

Для принудительной подгрузки всех зависимостей проекта Maven можно использовать:

```bash
mvn dependency:resolve
```

или более надёжный способ — очистить локальный кэш и пересобрать проект:
```bash
mvn clean install -U
```

Где:

- `clean` — удаляет папку `target`
- `install` — собирает проект и подтягивает зависимости
- `-U` — принудительно обновляет снапшоты и зависимости

**После этого Maven -> Reload All Maven Projects (в IntellijIdea справа)** 


Для проверки подгруженных зависимостей:

```bash
mvn dependency:tree
```

Усли в dependency tree отображается, а в проекте не видно -> **Maven -> Reload All Maven Projects**

----
#### [[Принудительная подгрузка зависимостей - Maven - Flashcards|Link to flashcards]]



---
### References:

