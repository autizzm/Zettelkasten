
25-09-2025 21:43

Status: #baby 

Tags: [[Java+]]

---
# Порядок сериализации - Jackson Annotations

1. ✅ **Если есть `@JsonValue` — сериализация идёт только через него.**
    
2. ✅ **Сначала ищутся геттеры** (с учётом `@JsonAutoDetect`), исключая `@JsonIgnore`, `@JsonIgnoreProperties`, `@JsonIgnoreType`.  
    🔹 _Дополнение:_ `@JsonGetter` и `@JsonProperty` могут “включить” метод, даже если он приватный или по обычным правилам был бы невидим.
    
3. ✅ **Если геттеров нет — смотрит на поля** (с учётом `@JsonAutoDetect`).  
    🔹 _Дополнение:_ `@JsonProperty` на поле также может принудительно включить его.
    
4. 🔹 **После отбора свойств** учитываются:
    
    - имя (`@JsonProperty`, `@JsonNaming`, стратегия нейминга),
        
    - сериализатор (`@JsonSerialize`, `@JsonRawValue`, `@JsonFormat`),
        
    - включение/исключение (`@JsonInclude`).
        
5. 🔹 **Добавляются виртуальные свойства** через `@JsonAnyGetter` и `@JsonAppend`.
    
6. 🔹 **Сортировка свойств** по `@JsonPropertyOrder` или глобальным настройкам.

----
#### [[Порядок сериализации - Jackson Annotations - Flashcards|Link to flashcards]]



---
### References:

-  [[Serialization Annotations - Jackson Annotations]]