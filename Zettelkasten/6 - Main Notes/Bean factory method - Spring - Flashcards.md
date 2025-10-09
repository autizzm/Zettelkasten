
Theory for the cards: [[Bean factory method - Spring]]

FILE TAGS: spring

Q: Можно ли одновременно иметь и factory-method и init-method?
A: Да, можно. Сначала класс создаётся, а потом инициализируется.
	
Порядок вызова:
1. factory-method
2. init-method
3. ... (usage)
4. destroy-method
<!--ID: 1760034186970-->
