
10-10-2025 08:58

Status: #not_even_born 

Tags: [[Spring]]

---
# yaml file - Spring


## 🧭 1. Подготовим YAML-файл

Пусть в `src/main/resources/config.yml` у нас есть:

```yaml
my:
  text: |
    Привет!
    Это длинный текст
    из YAML файла.
```

---

## 🧰 2. Определяем `YamlPropertiesFactoryBean` в `ApplicationContext.xml`

Мы создадим бин, который будет:

- читать YAML-файл,
    
- превращать его в `java.util.Properties`,
    
- отдавать дальше в контейнер.
    

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Загружаем YAML и конвертируем его в Properties -->
    <bean id="yamlProperties" class="org.springframework.beans.factory.config.YamlPropertiesFactoryBean">
        <property name="resources">
            <array>
                <value>classpath:config.yml</value>
            </array>
        </property>
    </bean>

    <!-- PropertyPlaceholderConfigurer позволяет использовать ${...} -->
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="properties" ref="yamlProperties"/>
    </bean>

</beans>
```

📌 Здесь происходит следующее:

- `YamlPropertiesFactoryBean` читает `config.yml` → превращает в `Properties`.
    
- `PropertyPlaceholderConfigurer` регистрирует эти свойства → теперь можно использовать `${my.text}`.
    

---

## 🧭 3. Используем значение в другом бине

Пусть есть класс:

```java
package com.example;

public class TextHolder {
    private String text;

    public void setText(String text) {
        this.text = text;
    }

    public void printText() {
        System.out.println(text);
    }
}
```

В `ApplicationContext.xml` добавляем бин:
```java
<bean id="textHolder" class="com.example.TextHolder">
    <property name="text" value="${my.text}"/>
</bean>
```

📌 `${my.text}` подставится из `config.yml`.

---

## 🧪 4. Тестируем

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.example.TextHolder;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        TextHolder holder = context.getBean("textHolder", TextHolder.class);
        holder.printText();
    }
}
```

✅ На выходе в консоли будет многострочный текст из YAML-файла.

----
#### [[yaml file - Spring - Flashcards|Link to flashcards]]



---
### References:

