# Проверка состояния работоспособности контейнера

- [Проверка состояния работоспособности контейнера](#checking-the-container-health-status)
  - [Сценарии](#scenarios-7)
  - [Методы настройки](#configuration-methods)
  - [Правила проверки](#check-rules)
  - [Ограничения по использованию](#usage-restrictions-8)

## Сценарии

В рабочей среде неизбежно возникают ошибки в приложениях, предоставляемых разработчиками, или в работающих на платформах сервисах. Поэтому для периодической проверки и отладки приложений необходима система управления. В механизме проверки состояния работоспособности контейнеров предусмотрено добавление функции проверки, выбранной пользователем. При создании контейнера опция **--health-cmd** настраивается таким образом, чтобы в контейнере периодически выполнялись команды мониторинга его состояния на основе возвращаемых функцией значений.

## Методы настройки

Настройка при запуске контейнера:

```
isula run -itd --health-cmd "echo iSulad >> /tmp/health_check_file || exit 1" --health-interval 5m --health-timeout 3s --health-exit-on-unhealthy  busybox bash
```

Настраиваемые параметры:

- **--health-cmd**: данный параметр обязателен. Если после запуска команды в контейнере возвращается значение **0**, команда успешно выполняется. Если возвращается другое значение, значит, произошел сбой выполнения команды.
- **--health-interval:** интервал между двумя последовательными выполнениями команды. Значение по умолчанию — **30s**. Значение находится в диапазоне от **1s** до максимального значения **Int64** (единица измерения: наносекунды). Если входной параметр имеет значение **0s**, используется значение по умолчанию.
- **--health-timeout**: максимальная продолжительность выполнения одной команды проверки. По истечении времени ожидания выполнения команда завершается с неуспешным результатом. Значение по умолчанию — **30s**. Значение находится в диапазоне от **1s** до максимального значения **Int64** (единица измерения: наносекунды). Если входной параметр имеет значение **0s**, используется значение по умолчанию. Поддерживаются только контейнеры, время выполнения которых имеет тип LCR.
- **--health-start-period**: время инициализации контейнера. Значение по умолчанию — **0s**. Значение находится в диапазоне от **1s** до максимального значения **Int64** (единица измерения: наносекунды).
- **--health-retries**: максимальное количество повторов для проверки состояния работоспособности. Значение по умолчанию — **3**. Максимальное значение равно максимальному значению **Int32**.
- **--health-exit-on-unhealthy**: параметр определяет, требуется ли аннулировать контейнер в случае его нерабочего состояния. Значение по умолчанию — **false**.

## Правила проверки

1. После запуска контейнер имеет статус **health:starting**.
2. После настройки времени **start-period** команда **cmd** будет периодически выполняться в контейнере с интервалом, заданным параметром **interval**. То есть, после завершения выполнения данная команда будет исполнена повторно через указанный интервал времени.
3. Проверка считается успешно выполненной при условии успешного завершения выполнения команды **cmd** в течение времени, заданного параметром **timeout** и возврата функцией значения **0**. В противном случае произойдет сбой проверки. После успешного завершения проверки контейнер получает статус **health:healthy**.
4. Если команда **cmd** не выполняется заданное параметром **retries** количество раз, контейнер получает **статус health:unhealthy** и продолжает проверку состояния работоспособности.
5. Контейнер меняет статус **health:unhealthy** на статус **health:healthy** при успешном завершении проверки.
6. Если установлен параметр **--exit-on-unhealthy**, и контейнер завершает работу по другим причинам, а не в связи с аннулированием (возвращаемый код завершения работы — **137**), проверка состояния работоспособности возобновляется только после повторного запуска контейнера.
7. По завершении выполнения команды **cmd** или наступлении тайм-аута демон Docker вносит время начала выполнения, возвращенное функцией значение и стандартный результат проверки в конфигурационный файл контейнера. Вносятся максимум пять записей. Кроме того, в конфигурационном файле контейнера хранятся параметры проверки работоспособности.
8. Когда контейнер работает, статус проверки работоспособности записывается в конфигурацию контейнера. Просмотр статуса осуществляется командой **isula inspect**.

```
"Health": {
    "Status": "healthy",
    "FailingStreak": 0,
    "Log": [
        {
            "Start": "2018-03-07T07:44:15.481414707-05:00",
            "End": "2018-03-07T07:44:15.556908311-05:00",
            "ExitCode": 0,
            "Output": ""
        },
        {
            "Start": "2018-03-07T07:44:18.557297462-05:00",
            "End": "2018-03-07T07:44:18.63035891-05:00",
            "ExitCode": 0,
            "Output": ""
        },
        ......
}
```

## Ограничения по использованию

- В контейнере могут храниться максимум пять записей о состоянии проверки работоспособности. Хранятся последние пять записей.
- Если параметрам проверки работоспособности задано значение **0** во время запуска контейнера, используются значения по умолчанию.
- Если после запуска контейнера с настроенными параметрами проверки работоспособности демон iSulad завершает работу, проверка работоспособности не выполняется. После перезапуска демона iSulad состояние работоспособности текущего контейнера меняется на **starting**. После этого применяются вышеперечисленные правила проверки.
- При первом сбое проверки работоспособности статус **starting** данного процесса не изменится на **unhealthy**, пока не будет достигнуто заданное количество повторов проверки (**--health-retries**), или на статус **healthy**, пока проверка не завершится успешно.
- Необходимо улучшить функцию проверки состояния контейнеров, время выполнения которых имеет тип Open Container Initiative (OCI). Поддерживаются только контейнеры, время выполнения которых имеет тип LCR.