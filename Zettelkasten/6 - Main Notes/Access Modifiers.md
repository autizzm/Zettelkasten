
03-09-2025 16:40

Status: #child 

Tags: [[Java Core]] [[Java Classes]]

---
# Access Modifiers

#### Top-level classes:


| modifier    | Внутри самого класса ~={orange}Class=~ | В том же пакете НЕ ПОДКЛАССЫ ~={orange}Package=~ | В подклассах (same package) ~={orange}Subclasses=~ | В подклассах (different package) ~={orange}Subclass=~ | ~={orange}World=~ |
| ----------- | -------------------------------------- | ------------------------------------------------ | -------------------------------------------------- | ----------------------------------------------------- | ----------------- |
| public      | ✅                                      | ✅                                                | ✅                                                  | ✅                                                     | ✅                 |
| protected   | ✅                                      | ✅                                                | ✅                                                  | ✅                                                     | ❌                 |
| no modifier | ✅                                      | ✅                                                | ✅                                                  | ❌                                                     | ❌                 |
| private     | ✅                                      | ❌                                                | ❌                                                  | ❌                                                     | ❌                 |

~={green}No modifier=~ ещё называют ***~={green}package-private=~*** -> виден только в package, зато во всём.

#### Nested/inner classes:


| modifier    | ~={orange}Enclosing Class=~ | В том же пакете НЕ ПОДКЛАССЫ ~={orange}Package=~ | В подклассах внешнего класса (same package) ~={orange}Subclasses of Enclosing class=~ | В подклассах внешнего класса (different package) ~={orange}Subclasses of Enclosing class=~ | ~={orange}World=~ |
| ----------- | --------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ | ----------------- |
| public      | ✅                           | ✅                                                | ✅                                                                                     | ✅                                                                                          | ✅                 |
| protected   | ✅                           | ✅                                                | ✅                                                                                     | ✅                                                                                          | ❌                 |
| no modifier | ✅                           | ✅                                                | ✅                                                                                     | ❌                                                                                          | ❌                 |
| private     | ✅                           | ❌                                                | ❌                                                                                     | ❌                                                                                          | ❌                 |

#### ~={yellow}По сути - то же самое=~





----
#### [[ |Link to flashcards]]



---
### References:

