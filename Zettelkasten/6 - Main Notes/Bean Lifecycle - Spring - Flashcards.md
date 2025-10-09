
Theory for the cards: [[Bean Lifecycle - Spring]]

FILE TAGS: spring

Q: Сколько раз Spring вызовет метод destroy бинов со scope="prototype"?
A: Ни разу, Spring вызывает destroy метод толькоу Singleton бинов.
<!--ID: 1760034186981-->


Q: Сколько раз Spring вызовет метод init у бина со scope="prototype"?
A: Столько раз, сколько бинов требуется создать.
<!--ID: 1760034186987-->


Q: Сколько раз Spring вызовет метод destroy бинов со scope="singleton"?
A: Один раз, т.к. бин со scope="singleton" создаётся в единственном экземпляре.
<!--ID: 1760034186994-->


Q: Сколько раз Spring вызовет метод init у бина со scope="singleton"?
A: Один раз, т.к. бин со scope="singleton" создаётся в единственном экземпляре.
<!--ID: 1760034187001-->


Q: Какой scope бинов используется по умолчанию (Spring)?
A: По умолчанию (без указания в ApplicationContext.xml) используется singleton.
<!--ID: 1760034187008-->
