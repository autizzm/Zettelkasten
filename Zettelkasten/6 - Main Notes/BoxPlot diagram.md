
28-09-2025 10:46

Status: 

Tags: [[ТВиМС]] [[Мат Стат]]

---
# BoxPlot diagram

(Eviews User Guide I, page 798)


![[Pasted image 20250928104741.png]]

**Box** - представляет собой 1-й и 3-й квартиль (средние 50% данных).
	
**Hinges** → **квартильные границы** (по сути: нижний и верхний «край коробки», то есть первый квартиль $Q_1$ и третий квартиль $Q_3$​).
	
**Whiskers** → **усы** (линии, выходящие из коробки к минимальному и максимальному значению без выбросов).
	
**Staples** →  **границы усов** или «концы усов» (points at the ends of whiskers).


> [!note] **Shaded region - доверительный интервал медианы**
> ## $-\frac{1,57*IQR}{\sqrt{N}} < Median < +\frac{1,57*IQR}{\sqrt{N}}$,
> где $N$ - количество наблюдений

> [!note]
> - расстояние от коробки до конца уса **максимум** может быть $1.5 \cdot IQR$, но ограничивается фактическими данными.

---

## IQR (InterQuartile Range)

**IQR (межквартильный размах)** вычисляется так:

$IQR = Q_3 - Q_1$​

- $Q_1$ (первый квартиль) — значение, ниже которого находится 25% выборки.
    
- $Q_3$​ (третий квартиль) — значение, ниже которого находится 75% выборки.
    

👉 То есть **IQR — это длина коробки** в BoxPlot.


## Квалификация выбросов


**Upper inner fence** = $Q_1 - 1,5*IQR$ (короче на пол $IQR$ ниже **Box**)
**Lower inner fence** = $Q_3 + 1,5*IQR$ (короче, на пол $IQR$ выше **Box**)

***Выбросы*** - всё что за пределами **inner fence**

**Upper outer fence** = $Q_1 - 3*IQR$ (короче на $2IQR$ ниже **Box**)
**Lower outer fence** = $Q_3 + 3*IQR$ (короче на $2IQR$ выше **Box**)



#### Близкие выбросы (Near outliers)

$\text{inner fences } < \text{ near outliers } \le \text{ outer fences}$

#### Далёкие выбросы

$\text{outer fences } < \text{ far outliers }$



----
#### [[BoxPlot diagram - Flashcards|Link to flashcards]]



---
### References:

