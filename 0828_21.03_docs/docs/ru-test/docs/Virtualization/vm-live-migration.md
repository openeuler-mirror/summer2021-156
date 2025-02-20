# Горячая миграция виртуальных машин

- [Горячая миграция виртуальных машин](#vm-live-migration)
  - [Введение](#introduction-1)
  - [Сценарии применения](#application-scenarios)
  - [Меры предосторожности и ограничения](#precautions-and-restrictions)
  - [Операции горячей миграции](#live-migration-operations)

## Введение

### Обзор

Из-за неравномерного распределения ресурсов возможны ситуации перегрузки или недостаточной загруженности физической машины, на которой работает виртуальная машина. Кроме того, на физической машине выполняются такие операции, как замена аппаратного обеспечения, обновление программного обеспечения, настройка сети и устранение ошибок и неисправностей. Поэтому важным вопросом становится выполнение этих операций без прерывания сервисов. Технология «горячей» миграции (live migration) или онлайн-миграции виртуальной машины позволяет сбалансировать нагрузку или выполнять вышеупомянутые операции без остановки сервисов, улучшая впечатления пользователей и повышая эффективность работы. Горячая миграция позволяет сохранить рабочий статус всей виртуальной машины и быстро восстановить ее состояние на исходной платформе или даже на разных аппаратных платформах. После восстановления виртуальная машина продолжит работу, и пользователи эти действия не заметят. Поскольку данные виртуальной машины могут храниться как на текущем хосте, так и на общем удаленном устройстве хранения, openEuler поддерживает горячую миграцию с общим хранилищем и без общего хранилища.

## Сценарии применения

Горячая миграция с общим хранилищем и без общего хранилища применяется в следующих сценариях:

- В условиях неисправности или перегрузки физической машины действующую виртуальную машину можно перенести на другую физическую машину, не допустив, таким образом, остановку и сбои работы сервисов.
- В условиях недостаточной загрузки большей части физических машин перенос и объединение виртуальных машин позволит сократить количество физических машин и повысить эффективность использования ресурсов.
- Если узким местом производительности становится аппаратное обеспечение физического сервера — процессор, память и жесткий диск — компоненты можно заменить более производительными устройствами или добавить новые устройства. Однако остановка виртуальной машины или сервисов недопустимы для работы предприятия.
- При обновлении серверного программного обеспечения, например платформы виртуализации, виртуальную машину можно переносить в горячем режиме со старой платформы виртуализации на новую.

Горячая миграция без общего хранилища применяется также и в следующих сценариях:

- В условиях неисправности физической машины или нехватки места на диске хранения действующую виртуальную машину можно перенести на другую физическую машину, не допустив, таким образом, остановку и сбои работы сервисов.
- Устаревающие устройства хранения физической машины не поддерживают производительность, которая требуется для выполнения актуальных задач обработки и хранения, становясь узким местом системы. В этом случае необходимо заменить старое оборудование новыми устройствами хранения с более высокой производительностью, но, при этом, нельзя останавливать или отключать виртуальную машину. Работающую виртуальную машину необходимо перенести на физическую машину с более высокой производительностью.

## Меры предосторожности и ограничения

- Перед выполнением горячей миграции убедитесь в хорошем состоянии сети. Если связь в сети будет прервана, процесс горячей миграции остановится, ожидая восстановления связи. По истечении времени ожидания восстановления сетевого соединения процесс горячей миграции завершается с ошибкой.

- Во время миграции нельзя выполнять операции, связанные с управлением жизненным циклом и аппаратными устройствами виртуальной машины.

- Перед выполнением горячей миграции убедитесь, что исходный сервер и сервер назначения не выключены и не находятся в процессе перезапуска. В противном случае произойдет сбой горячей миграции или выключение виртуальной машины.

- Во время миграции нельзя отключать, перезапускать и восстанавливать виртуальную машину. В противном случае произойдет сбой процесса горячей миграции. Если выполняется перенос в горячем режиме виртуальной машины, которая повторно загружена в режиме ACPI, работа этой машины будет завершена.

- Поддерживается горячая миграция только гомогенных ресурсов. То есть модели процессора источника и назначения должны совпадать.

- Виртуальную машину можно успешно перенести по сервисным сетевым сегментам. Однако после миграции виртуальной машины на физическую машину назначения в сети могут возникнуть ошибки. Чтобы не допустить появления этой проблемы, убедитесь, что сервисные сетевые сегменты, используемые для миграции, одинаковы.

- Если количество vCPU в исходной виртуальной машине больше, чем на физической машине назначения, производительность данной ВМ после миграции изменится. Убедитесь, что количество vCPU на физической машине назначения больше или равно количеству в исходной виртуальной машине.

Меры предосторожности при выполнении горячей миграции без общего хранилища:

- Источник и назначение переноса нельзя представить одним файлом образа диска. Для такой миграции необходимы специальные действия, которые предотвратят повреждение образов, вызванное перезаписью данных.
- Общие диски переносить нельзя. Для такой миграции необходимо выполнить операции с защитой от случайных ошибок.
- Образ машины назначения поддерживает только файлы и не поддерживает неформатированные устройства. Для миграции неформатированных устройств необходимо выполнить операции с защитой от случайных ошибок.
- Размер и количество образов диска, созданных на машине назначения, должны совпадать с размером и количеством исходной машины. В противном случае произойдет ошибка миграции.
- В сценариях гибридной миграции нельзя переносить общие диски и диски только для чтения.

## Операции горячей миграции

### Необходимые условия

- Перед выполнением горячей миграции убедитесь, что между хостом-источником и хостом назначения есть связь, и у хостов одинаковые разрешения на доступ к ресурсам. То есть хост-источник и хост назначения могут получить доступ к одним и тем же ресурсам хранения и сетевым ресурсам.
- Перед горячей миграцией виртуальной машины выполните проверку ее работоспособности и убедитесь, что на хосте назначения достаточно ресурсов процессора, памяти и хранения.

### (Опционально) Прогнозирование скорости грязных страниц (Dirty Page) во время горячей миграции

Перед миграцией виртуальной машины можно использовать функцию dirtyrate, чтобы получить скорость изменения грязных страниц при горячей миграции и определить, является ли виртуальная машина подходящей для миграции, или настроить надлежащие параметры миграции на основе использования памяти ВМ.

Процедура:

В этом примере имя ВМ — openEulerVM, а время расчета — 1 с. Выполните следующую команду:

```
virsh qemu-monitor-command openEulerVM '{"execute":"calc-dirty-rate", "arguments": {"calc-time": 1}}
```

Через 1 секунду выполните следующую команду для запроса скорости изменения грязных страниц:

```
virsh qemu-monitor-command openEulerVM '{"execute":"query-dirty-rate"}'
```

### (Опционально) Настройка параметров процесса горячей миграции

Перед процедурой выполните команду **virsh migrate-setmaxdowntime**, чтобы задать максимально допустимое время простоя во время миграции виртуальной машины. Это необязательные действия по настройке.

Например, чтобы установить максимальное время простоя **500 ms** для виртуальной машины с именем **openEulerVM**, выполните следующую команду:

```
# virsh migrate-setmaxdowntime openEulerVM 500
```

Кроме того, с помощью команды **virsh migrate-setspeed** можно ограничить полосу пропускания, занимаемую процессом горячей миграции виртуальной машины. Это действие предотвратит занятие виртуальной машиной чрезмерно большого объема ресурсов, в результате которого возможно ухудшение работы других виртуальных машин или сервисов на хосте. Эта операция также необязательна для проведения горячей миграции.

Например, чтобы установить полосу пропускания **500 Mbit/s** для горячей миграции виртуальной машины с именем **openEulerVM**, выполните следующую команду:

```
# virsh migrate-setspeed openEulerVM --bandwidth 500
```

С помощью команды **migrate-getspeed** можно запросить информацию о максимальной величине полосы пропускания, занимаемой процессом горячей миграции виртуальной машины.

```
# virsh migrate-getspeed openEulerVM
500
```

Можно использовать migrate-set-parameters для установки параметров, связанных с горячей миграцией. Ниже в таблице перечислены параметры, относящиеся к сжатию горячей миграции:

1. compress-level: уровень сжатия. Значение по умолчанию — 1.
2. compress-threads: число потоков сжатия. Значение по умолчанию — 8.
3. compress-wait-thread: следует ли ждать поток сжатия. Значение по умолчанию — true.
4. decompress-threads: число потоков распаковки. Значение по умолчанию — 2.
5. compress-method: алгоритм сжатия (zlib или zstd). Значение по умолчанию — zlib.

Например, установите для алгоритма горячей миграции ВМ с именем _openEulerVM_ значение zstd и сохраните значения по умолчанию для других параметров.

```
# virsh qemu-monitor-command openeulerVM '{ "execute": "migrate-set-parameters", "arguments": {"compress-method": "zstd"}}'
```

Можно запросить параметры, связанные с горячей миграцией, выполнив команду query-migrate-parameters.

```
# virsh qemu-monitor-command openeulerVM '{ "execute": "query-migrate-parameters"}' --pretty

{
  "return": {
    "xbzrle-cache-size": 67108864,
    "cpu-throttle-initial": 20,
    "announce-max": 550,
    "decompress-threads": 2,
    "compress-threads": 8,
    "compress-level": 1,
    "compress-method": "zstd",
    "multifd-channels": 2,
    "announce-initial": 50,
    "block-incremental": false,
    "compress-wait-thread": true,
    "downtime-limit": 300,
    "tls-authz": "",
    "announce-rounds": 5,
    "announce-step": 100,
    "tls-creds": "",
    "max-cpu-throttle": 99,
    "max-postcopy-bandwidth": 0,
    "tls-hostname": "",
    "max-bandwidth": 33554432,
    "x-checkpoint-delay": 20000,
    "cpu-throttle-increment": 10
  },
  "id": "libvirt-18"
}
```

### Процедура горячей миграции (сценарий с общим хранилищем)

1. Убедитесь, что устройство хранения является общим ресурсом.
   
   ```
   # virsh domblklist <VMInstanse>
    Target   Source
   --------------------------------------------
   sda      /dev/mapper/open_euleros_disk
   sdb      /mnt/nfs/images/openeuler-test.qcow2
   ```
   
   Чтобы запросить информацию об устройстве хранения виртуальной машины, выполните команду **virsh domblklist.** В вышеприведенном примере результат запроса показывает, что виртуальная машина сконфигурирована с двумя устройствами хранения: sda и sdb. Затем проверьте тип внутреннего хранилища данных двух устройств — локальное или удаленное хранилище. Если все устройства хранения находятся на удаленном общем хранилище, данная машина представляет собой ВМ с общим хранилищем. В противном случае машина является ВМ без общего хранилища.

2. Выполните горячую миграцию ВМ следующей командой.
   
   Например, выполните команду **virsh migrate**, чтобы перенести в горячем режиме виртуальную машину **openEulerVM** на хост назначения.
   
   ```
   # virsh migrate --live --unsafe openEulerVM qemu+ssh://<destination-host-ip>/system
   ```
   
   **\<destination-host-ip>** — это IP-адрес хоста назначения. Перед выполнением горячей миграции необходимо получить разрешение на управление хостом-источником, пройдя аутентификацию SSH.
   
   Для обеспечения успешной миграции предусмотрены дополнительные опции команды **virsh migrate**: **--auto-converge** и **--timeout**.
   
   Дополнительные опции:
   
   Команда с опцией **--unsafe** принудительно выполняет горячую миграцию, пропуская шаг проверки безопасности операции.
   
   Команда с опцией **--auto-converge** снижает частоту процессора, гарантируя конвергенцию процесса горячей миграции.
   
   Команда **--timeout** задает время ожидания горячей миграции. В случае превышения заданного времени ожидания виртуальная машина принудительно останавливается, сокращая данный процесс.

3. После завершения горячей миграции виртуальная машина корректно работает на хосте назначения.

### Процедура горячей миграции (сценарий без общего хранилища)

1. Чтобы убедиться в том, что виртуальная машина не использует общие хранилища, запросите список устройств хранения данной машины.
   
   Например, данный результат выполнения команды **virsh domblklist** показывает, что на мигрируемой виртуальной машине имеется диск SDA в формате qcow2. Конфигурация диска SDA в файле XML выглядит следующим образом:
   
   ```
       <disk type='file' device='disk'>
         <driver name='qemu' type='qcow2' cache='none' io='native'/>
         <source file='/mnt/sdb/openeuler/openEulerVM.qcow2'/>
         <target dev='sda' bus='scsi'/>
         <address type='drive' controller='0' bus='0' target='0' unit='0'/>
       </disk>
   ```
   
   Перед горячей миграцией создайте файл виртуального диска в том же каталоге диска на хосте назначения. Убедитесь, что формат и размер диска совпадают.
   
   ```
   # qemu-img create -f qcow2 /mnt/sdb/openeuler/openEulerVM.qcow2 20G
   ```

2. Запустите процесс горячей миграции командой **virsh migrate**. Во время миграции хранилище также переносится в хост назначения.
   
   ```
   # virsh migrate --live  --unsafe --copy-storage-all --migrate-disks sda \
   openEulerVM qemu+ssh://<dest-host-ip>/system
   ```

3. После завершения процесса горячей миграции в командном выводе содержится информация, что виртуальная машина корректно работает на хосте назначения, и устройство хранения перенесено на хост назначения.

### Процедура горячей миграции (сценарий зашифрованной передачи данных)

1. Обзор

Для лучшего шифрования данных во время горячей миграции ВМ openEuler предоставляет функцию шифрования TLS. Почти все сетевые сервисы QEMU могут использовать TLS для шифрования данных сеанса и сертификаты X509 для проведения простой аутентификации клиентов.

2. Сценарии применения

Типичный сценарий применения: обеспечивается безопасность данных при передаче данных ВМ между сторонами источника и назначения во время горячей миграции.

3. Меры предосторожности

Перед использованием TLS для горячей миграции виртуальных машин необходимо подать заявку на получение сертификатов и установить сертификаты на стороне источника и назначения. Перед использованием функции TLS необходимо включить пункт конфигурации одноранговой аутентификации и установить для параметра migrate_tls_x509_verify значение 1 в файле /etc/libvirt/qemu.conf.

Продолжительность прерывания сервиса и продолжительность миграции при одноканальной горячей миграции TLS значительно увеличиваются. Верхний предел пропускной способности миграции составляет от 100 МБ/с до 200 МБ/с. В результате может произойти сбой миграции.

multiFd можно использовать для выполнения многоканальной миграции TLS. Однако при этом увеличивается загрузка ЦП ( включены еще два потока миграции), что может повлиять на работу ВМ. Рекомендуется установить сходство ЦП (CPU affinity) потока горячей миграции, чтобы изолировать ресурсы ЦП, используемые потоком горячей миграции, от ресурсов ЦП, привязанных к процессу ВМ. Рекомендуется привязывать два ЦП к каждой ВМ, подлежащей миграции.

4. Способ применения

Команда для зашифрованной передачи при одноканальной горячей миграции

```
virsh migrate --live --unsafe --tls --domain openEulerVM --desturi qemu+tcp://<destination-host-ip>/system --migrateuri tcp://<destionation-host-ip>
```

Команда для зашифрованной передачи при многоканальной горячей миграции

```
virsh migrate --live --unsafe --parallel --tls --domain openEulerVM --desturi qemu+tcp://<destination-host-ip>/system --migrateuri tcp://<destionation-host-ip>
```

