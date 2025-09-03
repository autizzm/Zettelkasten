
03-09-2025 14:05

Status:

Tags: [[Number classes Java]] [[Java Core]] [[Small Important Moments Java]]

---
# Неизменяемость наследников Number


```java
Integer a = 1;
Integer b = 2;

void modify(Integer x, Integer y){
	x = 5;
	y = 3;
}

// вызов функции:
modify(a, b);

```

Здесь, a как было = 1, так и останется (b тоже не изменится). Посколь Integer неизменяем попытка присвоить указателю новое значение приведет к созданию нового объекта Integer.
x - указывает на новый объект Integer
a - указывает на старый объект Integer
(Это два разных указателя, т.к. ~={red}в функцию передается КОПИЯ УКАЗАТЕЛЯ=~)
