

Index: [[HR параша]]

---
# BrandNew - Legend

### [[РСХБ + Сбер - Resume]]


### EPM/IBP/BPM:


1. «Планета.» (разработчик IBS)

Это одна из наиболее технологически близких к Java платформ на рынке СНГ, позиционируемая как полноценный аналог _Anaplan_, _SAP IBP_ и _Oracle Hyperion_.

[Оф. сайт](https://planetaibs.ru/sectors/finance/)

[TAdviser: IBS: Планета Платформа](https://www.tadviser.ru/index.php/%D0%9F%D1%80%D0%BE%D0%B4%D1%83%D0%BA%D1%82:IBS:_%D0%9F%D0%BB%D0%B0%D0%BD%D0%B5%D1%82%D0%B0._%D0%9F%D0%BB%D0%B0%D1%82%D1%84%D0%BE%D1%80%D0%BC%D0%B0_(%D0%AD%D0%BA%D0%BE%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0_%D0%BF%D1%80%D0%BE%D0%B4%D1%83%D0%BA%D1%82%D0%BE%D0%B2_%D0%BA%D0%BE%D1%80%D0%BF%D0%BE%D1%80%D0%B0%D1%82%D0%B8%D0%B2%D0%BD%D0%BE%D0%B3%D0%BE_%D1%83%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F)

[Краткое описание технологий](https://itfederation.ru/participants/planeta-byudzhetirovanie/)


## [[Планета. Платформа]]


- **Архитектура и стек**: Построена на современной микросервисной архитектуре. Backend-сервисы написаны на **Java 17 / Spring Boot / Spring Cloud**. При этом расчетные in-memory компоненты ядра для максимальной производительности оптимизированы на **C++**.
- **Возможности расширения**: Платформа предоставляет открытый API и точки расширения (плагины), где пользователи могут писать кастомную логику на **Java, JavaScript и Groovy**. 
- **Для чего подходит**: Интегрированное бизнес-планирование (IBP), сквозное бюджетирование холдингов, инвестиционное моделирование.


Resume:
https://cloud.mail.ru/public/JeXZ/SZXwXXqur?weblink=JeXZ/SZXwXXqur

HR-платформа:
[Альберт Середа (albertsereda) — Хабр Карьера](https://career.habr.com/albertsereda)
[Сергей Здерев (sergeyzderev) — Хабр Карьера](https://career.habr.com/sergeyzderev)

  
Старший разработчик

Декабрь 2024 — По настоящее время (1 год и 8 месяцев)

Разработка и оптимизация высоконагруженной распределенной HR-платформы.

- Разработка и оптимизация высоконагруженной распределённой HR-платформы, анализ production-окружения для выявления и устранения узких мест.
    
- В паре с коллегой провели комплексную работу по оптимизации производительности: устранили хроническую утечку памяти, исправили N+1 запросы (@EntityGraph, JOIN FETCH), внедрили batching, переписали часть ночных задач синхронизации с другими системами и пересмотрели стратегию кеширования.
    
- Результат: пиковая нагрузка на CPU (6 ядер), ранее доходившая до 80% и выше в часы пиковой нагрузки (что приводило к падениям приложения), стабилизировалась на уровне 20–40% (в среднем ~30%); падения по причине нехватки ресурсов прекратились, расширение памяти больше не требуется.
    
- Настроили end-to-end мониторинг (OpenTelemetry, Jaeger, Grafana, Elasticsearch) для диагностики инцидентов и контроля состояния системы.
    
- Планирование и проведение нагрузочного тестирования (JMeter) критичных эндпоинтов перед выкаткой в production.
    
- Разработка ключевых системных компонентов и бизнес-сервисов платформы, покрытие кода тестами, участие в code review и в проработке технических решений с командой архитекторов.
    
- Кросс-функциональное взаимодействие с DevOps и аналитикой для выработки сквозных технических решений.
    

[Java](https://career.habr.com/resumes?skills%5B%5D=1012) • [Spring Boot](https://career.habr.com/resumes?skills%5B%5D=1274) • [PostgreSQL](https://career.habr.com/resumes?skills%5B%5D=537) • [Apache Kafka](https://career.habr.com/resumes?skills%5B%5D=1187) • [Redis](https://career.habr.com/resumes?skills%5B%5D=1) • [Apache JMeter](https://career.habr.com/resumes?skills%5B%5D=348) • [Микросервисная архитектура](https://career.habr.com/resumes?skills%5B%5D=1612) • [Jaeger](https://career.habr.com/resumes?skills%5B%5D=1687) • [Elasticsearch](https://career.habr.com/resumes?skills%5B%5D=482) • [Grafana](https://career.habr.com/resumes?skills%5B%5D=1270)

---


### [[Финплатформа Т1]]



3. Форсайт. Аналитическая платформа (разработчик «Форсайт»)

Хотя исторически ядро «Форсайта» (наследник Prognoz Platform) создавалось на C++, современная сервисная архитектура платформы для интеграций, веб-компонентов и серверных модулей активно использует **Java** и веб-стек. Платформа часто выступает технологической основой для построения заказных EPM- и CPM-решений в крупнейших корпорациях СНГ. [1](https://korusconsulting.ru/blog/tekhnologii/obzor-rynka-luchshikh-epm-sistem-dlya-biznes-planirovaniya/), [2](https://rep.bntu.by/bitstream/handle/data/137528/SNTK_79.pdf?sequence=1&isAllowed=y)





### Че решил

**Беру Планета.Бюджетирование - больше всего инфы по нему**


Второй проект: 

### [[Россельхозбанк - Свой бизнес]]


[App.Farm Оф. сайт инфо]([PaaS платформа App.Farm](https://rshbdigital.ru/products/paas-platforma-appfarm))

[Как мы создавали PaaS-платформу App.Farm — цифровое сердце РСХБ / Хабр](https://habr.com/ru/companies/rshb/articles/825816/)

[Как внедрить CI/CD для всех разработчиков в банке. CI/CD by App.Farm / Хабр](https://habr.com/ru/companies/rshb/articles/845430/)

[Конвейер CI/CD - отдельная хуйня от App.Farm (CICD4.pdf)](https://rshbdigital.ru/content/reestr/CICD4.pdf)



[Оф. Страница проекта Свой Бизнес (еще файлы по программе)](https://www.rshb.ru/business/svoy-business-dbo)

[Свой Бизнес - презентация проекта](https://globalcio.ru/upload/iblock/6b8/ndhr9v0ik154s3eul7jbcn85najarziz.pdf)

[Руководство пользователя РСХБ Свой Бизнес](https://rshbdigital.ru/content/reestr/Rukovodstvo_polzovatelya_Svoy_Biznes-SFCidlLHRyIypXse-FAqs.pdf)
- [Ещё чуть чуть инфы про пользование приложением](https://www.vbr.ru/biznes/help/raschetno-kassovoe-obsluzhivanie/svoy-biznes-rosselhozbank-lichnyy-kabinet/?utm_referrer=https%3A%2F%2Fduckduckgo.com%2F)

[Руководство администратора](https://rshbdigital.ru/content/reestr/rukovodstvo_administratora_nczk_yul_svoi_biznes.pdf)

[Функции НЦК ЮЛ Свой Бизнес](https://rshbdigital.ru/content/reestr/funkczionalnye-kharakteristiki.pdf)

[Краткий концепт архитектуры](https://rshbdigital.ru/content/reestr/opisanie-konczeptualnoi-arkhitektury-svoi-biznes.pdf)

- [Первичное представление всей архитектуры (claude)](file:///C:/ProfDev/Java/volchary/BrandNewLegend/SvoiBiznesRSHB/arch.html)

[Руководство по развертыванию](https://rshbdigital.ru/content/reestr/rukovodstvo-po-ustanovke---svoi-biznes.pdf)

[Чуть чуть про функционал Свой Бизнес](https://www.kommersant.ru/doc/5230752)


✔️Доступ к выписке и истории платежей  
  
Контролируйте финансы компании через историю платежей. Есть подробные отчеты по операциям и детальный фильтр. Поддерживается сортировка по дате, типу документа и виду расчётного счета. Сформируйте выписку в виде файла PDF и скачайте её на смартфон.  
  
✔️Управление счетами  
  
Переключайтесь между своими счетами в несколько кликов, контролируйте остатки и пересылайте реквизиты контрагентам. Установите любимый счёт — тогда он автоматически будет подтягиваться во все платёжные документы.  
  
✔️Своевременная информация о блокировках  
  
Узнавайте о незавершённых платежах, ограничениях и блокировках из уведомлений на главном экране. Там же подскажем, как решить проблему и что делать дальше.  
  
✔️Доступ к быстрым операциям  
  
Управляйте популярными операциями с главного экрана — закрепили доступ к платежам, реквизитам, выпискам и зарплатному проекту.  
  
✔️Полезные сторис  
  
Интересные предложения и новые возможности Банка — перед глазами. Если случится что-то важное, например, возникнут ограничения по счетам, также сообщим об этом в ленте сторис.  
  
✔️Вход по пин-коду или биометрическим данным  
Безопасная и современная система авторизации. Подключите вход по отпечатку пальца или систему распознавания лиц, чтобы не запоминать код доступа.  
***  
  
Работа над мобильным банком продолжается — и мы будем рады любой обратной связи. Если вы заметили ошибку, придумали идею или хотите поделиться впечатлениями, сообщите на почт info-mybusiness@rshb.ru. Мы читаем все письма и обязательно вам ответим.  
  
Recent changes:  
Улучшение производительности и стабильности приложения




ПРосто пример того, как они оптимизировали:
[Битва за миллисекунды: Как мы ускорили банковское приложение РСХБ в 60+ раз / Хабр](https://habr.com/ru/companies/rshb/articles/990094/)


[Платформа инвестиционного бизнеса. Как устроена IPS в РСХБ / Хабр](https://habr.com/ru/companies/rshb/articles/765838/)

----

Полезная инфа для погружения в EPM/BPM/IBP:
[Оценка инвестиционных проектов: как превратить гипотезу в обоснованное решение](https://datafinder.ru/solutions/finance/investment-projects-evaluation)


Пример банковского ядра опенсорсный (от Apache):
[apache/fineract: Apache Fineract](https://github.com/apache/fineract)


[Присоединяйся к команде EPM Платформа Cбера](https://developers.sber.ru/kak-v-sbere/teams/epm)





---

### [[Сбер - хз че за проект пока]]



Сервис аналитики, обработка и предоставление статистики:
[Алексей Марков-Бутырский (alex_java_er) — Хабр Карьера](https://career.habr.com/alex_java_er)

ERP (замена SAP):
[Егор Федоров (s0bor) — Хабр Карьера](https://career.habr.com/s0bor)

управления файловыми интеграциями Platform V Synapse File Exchange:
[Никита Ярославцев (hasbr0) — Хабр Карьера](https://career.habr.com/hasbr0)

----
#### [[BrandNew - Legend - Flashcards|Link to flashcards]]



---
### References:

