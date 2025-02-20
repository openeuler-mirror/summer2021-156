# Взаимодействие с сетью CNI

- Взаимодействие с сетью CNI
  - [Обзор](#overview-0)
  - [Стандартные CNI](#common-cnis)
    - [Описание конфигурации сети CNI](#cni-network-configuration-description)
    - [Добавление pod в список сетей CNI](#adding-a-pod-to-the-cni-network-list)
    - [Удаление pod из списка сетей CNI](#removing-a-pod-from-the-cni-network-list)
  - [Ограничения по использованию](#usage-restrictions)

## Обзор

Интерфейс среды выполнения контейнера (Container Runtime Interface; CRI) предоставляется для подключения к сети CNI, в том числе для выполнения синтаксического анализ конфигурационного файла сети CNI и добавления объекта pod в сеть CNI или удаления его из сети. Если необходимо, чтобы объект pod поддерживал сеть через подключаемый модуль контейнерной сети, например Canal, CRI должен быть подключен к Canal, чтобы предоставить данную возможность поддержки объекту pod.

## Стандартные CNI

Общие CNI включают элементы конфигурации сети CNI в настройках сети CNI и объекта pod. Такие CNI видны пользователям.

- Элементы конфигурации сети CNI в настройках сети CNI используются для указания пути к конфигурационному файлу сети CNI, пути к бинарному файлу подключаемого модуля сети CNI и сетевого режима. Более подробная информация приведена в [Табл. 1](#en-us_topic_0183259146_table18221919589).
- Элементы конфигурации сети CNI в настройках объекта pod используются для установки дополнительного списка сетей CNI, в который добавлен pod. По умолчанию pod добавляется только к плоскости сети CNI, заданной по умолчанию. По необходимости можно добавлять pod к разным плоскостям сети CNI.

**Табл. 1** Элементы конфигурации сети CNI

| **Функции**                                                  | Команда          | Конфигурационный   файл | Описание                                                     |
| ------------------------------------------------------------ | ---------------- | ----------------------- | ------------------------------------------------------------ |
| Путь к  бинарному файлу подключаемого модуля (плагина) сети CNI | --cni-bin-dir    | "cni-bin-dir":  "",     | Значение по  умолчанию — **/opt/cni/bin**.                   |
| Путь к  конфигурационному файлу сети CNI                     | --cni-conf-dir   | "cni-conf-dir":  "",    | Система проходится по всем  файлам с расширениями .conf, .conflist или .json в каталоге. Значение по  умолчанию — **/etc/cni/net.d**. |
| Сетевой режим                                                | --network-plugin | "network-plugin":  "",  | Указывает  сетевой плагин. Нулевое значение по умолчанию указывает на то, что  конфигурация сети не доступна, а созданная песочница имеет только карту NIC  для шлейфового соединения. Поддерживаются символы CNI и нуль. Другие  недопустимые значения приведут к сбою запуска iSulad. |

Дополнительный режим настройки сети CNI:

Добавьте элемент конфигурации плоскости сети «network.alpha.kubernetes.io/network» к аннотациям в конфигурационном файле pod.

Плоскость сети конфигурируется в файле формата JSON, в том числе указываются следующие параметры:

- **name**: имя плоскости сети CNI.
- **interface**: имя сетевого интерфейса.

Ниже приведен пример настройки сети CNI:

```
"annotations" : {
        "network.alpha.kubernetes.io/network": "{\"name\": \"mynet\", \"interface\": \"eth1\"}"
 }
```

  

### Описание конфигурации сети CNI

Сеть CNI конфигурируется двумя методами. В обоих методах используется формат файла .json.

- Конфигурационный файл для одноплоскостной сети с расширением .conf или .json. Более подробная информация о настройке элементов конфигурации приведена в [Табл. 1](#cni-parameters.md#en-us_topic_0184347952_table425023335913) приложения.
- Конфигурационный файл для многоплоскостной сети с расширением .conflist. Более подробная информация о настройке элементов конфигурации приведена в [Табл. 3](#cni-parameters.md#en-us_topic_0184347952_table657910563105) приложения.

### Добавление pod в список сетей CNI

Если для iSulad настроен параметр  **--network-plugin=cni** и сконфигурирована сетевая плоскость по умолчанию, объект pod автоматически добавляется к данной плоскости во время запуска этого устройства. Если в конфигурации объекта pod настроена дополнительная плоскость сети, то данный pod добавляется к этой плоскости во время запуска.

**port\_mappings** в конфигурации pod также является элементом конфигурации сети, который используется для сопоставления портов pod. Для сопоставления портов выполните следующие действия:

```
"port_mappings":[
     { 
         "protocol": 1,
         "container_port": 80,
         "host_port": 8080
      }
]
```

- **protocol**: протокол, используемый для сопоставления. Значением может быть **tcp** (задается посредством 0) или **udp** (задается посредством 1).
- **container\_port:** порт, через который сопоставляется контейнер.
- **host\_port:** порт, сопоставленный с хостом.

### Удаление pod из списка сетей CNI

Во время вызова StopPodSandbox инициируется интерфейс, используемый для удаления объекта pod из списка сетей CNI, который выполняет сброс сетевых ресурсов.

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:
> 
> 1. Перед вызовом интерфейса RemovePodSandbox необходимо как минимум один раз инициировать интерфейс StopPodSandbox.
> 2. Если StopPodSandbox не сможет инициировать CNI, могут остаться некоторые сетевые ресурсы.

## Ограничения по использованию

- В настоящий момент поддерживаются только версии CNI 0.3.0 и 0.3.1. В более поздних версиях может потребоваться поддержка CNI 0.1.0 и CNI 0.2.0. Поэтому в журналах ошибок выводится информация о CNI 0.1.0 и CNI 0.2.0.
- name: значение должно содержать строчные буквы, цифры, дефисы (-) и точки (.) и не может начинаться или заканчиваться дефисом или точкой. Значение может состоять максимум из 200 символов.
- Количество конфигурационных файлов не может превышать 200, а размер одного конфигурационного файла не может превышать 1 МБ.
- Необходимо настроить расширенные параметры в соответствии с фактическими сетевыми требованиями. Необязательные параметры не требуется вносить в файл netconf.json.