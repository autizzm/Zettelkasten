
07-07-2026 08:58

Status:

Tags: [[Kubernetes]] [[CI & CD]]

---
# Probes - Kubernetes

В Kubernetes есть 3 вида проб, и они отвечают на разные вопросы о состоянии контейнера.

Удобно запомнить так:

**~={green}livenessProbe=~** — «Жив ли процесс?»

**~={blue}readinessProbe=~** — «Готов ли принимать трафик?»

**~={purple}startupProbe=~** — «Завершился ли долгий старт приложения?»

> [!note]
> Со стороны сервиса это реализуется через Spring Actuator: поднимаем публичные эндпоинты для проверок


---
### Зачем нужна readinessProbe

Это самая важная проба для production-сервисов.

Она отвечает на вопрос:
> *Можно ли сейчас отправлять запросы в этот Pod?*


**Что происходит без readinessProbe**

Представь Spring Boot приложение.

Pod запустился, контейнер уже работает, но приложение еще инициализирует:

- подключение к БД;
    
- миграции;
    
- инициализацию кэша;
    
- загрузку конфигурации;
    
- подключение к Kafka.
    

Контейнер уже имеет статус Running, поэтому Service начинает слать в него трафик.

Проблема

> [!warning] Problem
> Приложение еще не готово
>
>Клиенты получают 500, 503, таймауты и ошибки подключения.

### Что делает readinessProbe

Например:

Kubernetes начинает периодически проверять endpoint.

Probe = FAIL -> NotReady -> Pod исключён из Service

Probe = OK -> Ready -> Pod добавлен в Service



### Ключевой момент

> [!note] readinessProbe НЕ перезапускает контейнер
>
>Она только говорит Kubernetes:
>
>«Этот Pod временно не обслуживает запросы».

Поэтому Pod может быть:

|Состояние|Что значит|
|---|---|
|Running + Ready|Трафик идет|
|Running + NotReady|Контейнер жив, но трафик не идет|

### Практический пример: Spring Boot

Во время деплоя:

![How I Stopped Downtime During Deployments Using Kubernetes Rolling Updates | by Olanipekun Adekunle Oluwole | Feb, 2026 | Medium](https://images.openai.com/static-rsc-4/nYY5KoSvEkPPTY3IepIid2mcxa_Nk6CmLixAld671kG_mI4WBjX1KSYgu4KW_gDKDct3YaHbCT-7gnB5nyZ5yhCScaOA8LpaHRyEUwu8gI87PW70tjFU47-gwom_QJaF-meeRxzqbhZhA16rOnMP5eKoiFxEHujpB3tP4X62wxMFh3fjmxQloTuuelzO17Wz?purpose=fullsize)

- Создается новый Pod.
    
- Приложение стартует 30 секунд.
    
- readinessProbe возвращает FAIL.
    
- Старые Pod'ы продолжают обслуживать трафик.
    
- Новый Pod становится Ready.
    
- Только после этого трафик начинает идти и на него.
    

Результат

Zero-downtime deployment.


---
### Чем readiness отличается от liveness

|Probe|Что делает при FAIL|
|---|---|
|readinessProbe|Убирает Pod из балансировки|
|livenessProbe|Перезапускает контейнер|



----
#### [[Probes - Kubernetes - Flashcards|Link to flashcards]]



---
### References:

