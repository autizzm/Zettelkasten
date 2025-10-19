
18-10-2025 19:23

Status: #baby 

Tags: [[Maven]] [[Java+]]

---
# Archetypes - Maven

Архетипы - типы структуры проекта.

Создание через терминал:
```bash
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false
```

Можно использовать и другие архетипы, указав их `groupId` и `artifactId`. Список доступных архетипов:

```bash
mvn archetype:generate
```

(если не указывать параметры, Maven предложит выбрать из списка).

----
#### [[Archetypes - Maven - Flashcards|Link to flashcards]]



---
### References:

