
27-09-2025 13:32

Status: #child 

Tags: [[SQL]]

---
# UPDATE - SQL

### Синтаксис

```sql
UPDATE table_name
SET table_field1 = table_field_value1,
    table_fieldN = table_field_valueN
[WHERE the_conditions_of_the_limitations]
```

> [!warning] **Не пропускать WHERE**
> При пропуске WHERE будут обновлены ВСЕ строки.



> [!note] **Assigning Calculated Values**
> Можно присваивать рассчитываемые значения:
> ```sql
> UPDATE Payments
>SET unit_price = unit_price * 2;
> ```
#### Пример

```sql
UPDATE FamilyMembers
SET member_name = "Andie Anthony"
WHERE member_name = "Andie Quincey";
```

----
#### [[UPDATE - SQL - Flashcards|Link to flashcards]]



---
### References:

