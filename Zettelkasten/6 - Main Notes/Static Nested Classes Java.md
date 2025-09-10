
09-09-2025 12:34

Status: #baby

Tags: [[Java Core]] [[Java Classes]]

---
# Static Nested Classes Java

Static Nested Class - по поведению - ~={orange}то же самое=~, что и~={orange} top-level класс=~, который вложен в другой класс для удобства (~={cyan}по приколу=~).

1. Не зависит от экземпляра класса

2. Имеет доступ ТОЛЬКО к ~={green}static=~ переменным Enclosing класса ~={green}(включая `private`)=~. 

3. Подчиняется правилам доступа как member.

>[!note]
>С видимостью те же приколы, что и у Inner:
>1. **public class** -> **public default constructor** - виден везде
>2. **protected class** -> **protected defaul**t constructor (относительно самого static nested класса) - сам static nested class виден и в пакете и в подклассах Enclosing класса, а вот его ~={orange}конструктор виден=~ и в пакете и в ~={orange}подклассах ~={red}static nested=~ класса=~
>3. **no modifier class** -> **no modifier default** constructor - и класс и конструктор видны только в этом пакете
>4. **private class** -> **private default constructor** - видны только внутри класса (~={orange}конструктор=~ static nested тоже виден ~={orange}только внутри самого ~={red}static nested=~ класса=~ -> нужен factory method, или не ебать себе мозги и самому определить конструктор)

### Создание static nested класса

Static nested классы создаются без экземпляра Enclosing класса (`new Enclosing.StaticNested()`), но при создании класса за пределами Enclosing класса к нему надо обращаться по полному имени `Enclosing.StaticNested`:

```java
public class OuterClass {

    String outerField = "Outer field";
    static String staticOuterField = "Static outer field";

    class InnerClass {
        void accessMembers() {
            System.out.println(outerField);
            System.out.println(staticOuterField);
        }
    }

    static class StaticNestedClass {
        void accessMembers(OuterClass outer) {
            // Compiler error: Cannot make a static reference to the non-static
            //     field outerField
            // System.out.println(outerField);
            System.out.println(outer.outerField);
            System.out.println(staticOuterField);
        }
    }
}

class Runner{
    public static void main(String[] args) {
        System.out.println("Inner class:");
        System.out.println("------------");
        OuterClass outerObject = new OuterClass();
        OuterClass.InnerClass innerObject = outerObject.new InnerClass();
        innerObject.accessMembers();

        System.out.println("\nStatic nested class:");
        System.out.println("--------------------");
        OuterClass.StaticNestedClass staticNestedObject = new OuterClass.StaticNestedClass();    
        staticNestedObject.accessMembers(outerObject);
        
        System.out.println("\nTop-level class:");
        System.out.println("--------------------");
        TopLevelClass topLevelObject = new TopLevelClass();        
        topLevelObject.accessMembers(outerObject);                
    }
}
```

----
#### [[Static Nested Classes Java - Flashcards|Link to flashcards]]



---
### References:

- [[Inner Classes Java]]
- [[Member Classes Java]]