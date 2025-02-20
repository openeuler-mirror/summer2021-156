# Управление службами

В данном разделе представлен метод управления операционной системой и службами с помощью systemd.

\[\[toc]]

## Краткий обзор systemd

systemd — это менеджер систем и служб для использования в операционных системах Linux. Менеджер, разработанный с сохранением обратной совместимости с SysV и LSB, предоставляет ряд таких функций, как активация служб на основе сокета (программный интерфейс для обмена данными между процессами) и D-Bus (система межпроцессного взаимодействия приложений в операционной системе), активация демонов по запросу, получение мгновенных снимков состояния системы и управление точками монтирования и автоматического монтирования. С помощью systemd улучшается логика управления службами и эффект распараллеливания.

### Компоненты systemd

В systemd большинство действий направлено на компоненты, выполняющие роль ресурсов, которыми systemd управляет. Компоненты классифицируются по типу ресурсов, роль которых они выполняют, и определяются в конфигурационных файлах компонентов. Например, компонент avahi.service представляет демон Avahi, который определен в файле **avahi.service**. В [Табл. 1](#en-us_topic_0151921012_t2dcb6d973cc249ed9ccd56729751ca6b) перечислены доступные типы компонентов systemd.

**Табл. 1** Доступные типы компонентов systemd

| Тип компонента | Расширение файла | Описание                                                     |
| -------------- | ---------------- | ------------------------------------------------------------ |
| Service unit   | .service         | Системная служба.                                            |
| Target unit    | .target          | Группа компонентов systemd.                                  |
| Automount unit | .automount       | Точка автоматического монтирования файловой системы.         |
| Device unit    | .device          | Файл устройства, распознанный ядром.                         |
| Mount unit     | .mount           | Точка монтирования файловой системы.                         |
| Path unit      | .path            | Файл или каталог в файловой системе.                         |
| Scope unit     | .scope           | Созданный внешний процесс.                                   |
| Slice unit     | .slice           | Группа иерархически организованных компонентов, которые управляют системными процессами. |
| Socket unit    | .socket          | Программный интерфейс для обмена данными между процессами.   |
| Swap unit      | .swap            | Устройство замены или файл замены.                           |
| Timer unit     | .timer           | Таймер systemd.                                              |

Все доступные типы компонентов systemd размещаются в одном из каталогов, перечисленных в [Табл. 2](#en-us_topic_0151921012_t2523a0a9a0c54f9b849e52d1efa0160c).

**Табл. 2** Местоположения доступных компонентов systemd

| Каталог                  | Описание                                                     |
| ------------------------ | ------------------------------------------------------------ |
| /usr/lib/systemd/system/ | Компоненты systemd, распространяемые вместе с устанавливаемыми пакетами RPM. |
| /run/systemd/system/     | Компоненты systemd, создаваемые во время прогона программы.  |
| /etc/systemd/system/     | Компоненты systemd, создаваемые и управляемые системным администратором. |


## Функции

### Быстрая активация

systemd обеспечивает более агрессивное распараллеливание, чем UpStart. Активация на основе сокета и D-Bus сокращает время, необходимое для загрузки операционной системы.

Для ускорения загрузки системы systemd применяет следующие подходы:

- Активирует только необходимые процессы.
- Активирует параллельно как можно больше процессов.

### Активация по запросу

Во время инициализации система SysVinit активирует все возможные фоновые процессы, которые могут быть использованы. Пользователи могут войти в систему только после активации всех этих служебных процессов. Недостатки системы инициализации SysVinit очевидны: медленная загрузка и неэффективное использование системных ресурсов.

Некоторые службы используются редко или вообще не используются во время прогона программы. Например, служба печати CUPS редко используется на большинстве серверов. Обращения к службе SSHD также редки на многих серверах. Нет необходимости тратить время и системные ресурсы на запуск таких служб.

systemd может активироваться только при запросе доступа к данной службе. По завершении запроса служба systemd останавливает свою работу.

### Управление жизненным циклом службы Cgroups

Важная роль системы инициирования заключается в отслеживании и управлении жизненным циклом служб. Система может запустить и остановить службу. Однако закодировать систему инициализации для остановки служб труднее, чем это можно себе представить.

Служебные процессы часто запускаются в фоновом режиме как демоны, и иногда порождаются новые процессы-ответвления дублированием родительского процесса (функция fork). В UpStart необходимо корректно настроить строку expect в конфигурационном файле. В противном случае UpStart не сможет получить PID демона подсчетом количества ответвлений процесса.

Все стало проще с появлением функции контрольной группы (Cgroups), которая используется для управления квотами на системные ресурсы. Простота использования во многом объясняется его пользовательским интерфейсом, похожим на файловую систему. Когда родительская служба создает дочернюю службу, последняя наследует все атрибуты Cgroup, которой принадлежит данная родительская служба. Это означает, что все соответствующие службы помещаются в одну контрольную группу Cgroup. systemd получает PID всех соответствующих служб, проходя через их контрольную группу, а затем последовательно останавливает их.

### Управление точками монтирования и автоматического монтирования

В традиционных системах Linux пользователи могут использовать файл **/etc/fstab** для сохранения фиксированных точек монтирования файловой системы. Эти точки автоматически устанавливаются во время запуска системы. После завершения запуска точки монтирования становятся доступны. Эти точки являются файловыми системами, которые очень важны для работы системы, например, каталог **HOME**. Как и SysVinit, служба systemd управляет этими точками, обеспечивая их автоматическое монтирование при запуске системы. Служба systemd также совместима с файлом **/etc/fstab**. Этот файл можно использовать для управления точками монтирования.

Иногда необходимо организовать монтирование или размонтирование по запросу. Например, для доступа к контенту DVD требуется временная точка монтирования, которая аннулируется (с помощью команды **ummount**), как только отпадает необходимость в данном контенте. Данный подход позволяет экономить ресурсы. Это традиционно достигается с помощью службы **autofs**.

systemd позволяет автоматически монтировать без необходимости установки **autofs**.

### Управление транзакционной зависимостью

Загрузка системы включает в себя целый ряд отдельных заданий, при этом, некоторые задания зависят друг от друга. Например, сетевую файловую систему (Network File System; NFS) можно установить только после активации сетевого соединения. systemd может параллельно выполнять большое количество зависимых заданий, но не все из них. Возвращаясь к примеру с NFS, невозможно параллельно монтировать NFS и активировать сеть. Перед запуском любого задания systemd вычисляет его зависимости, создает временную транзакцию и проверяет, чтобы эта транзакция была совместима (то есть все соответствующие службы можно было активировать без зависимости друг от друга).

### Совместимость со скриптами SysVinit

Подобно UpStart, служба systemd позволяет использовать новые методы настройки и имеет новые требования к разработке приложений. Если необходимо заменить текущую систему инициализации systemd, служба systemd должна быть совместима с существующей программой. Сложно за короткое время изменить весь служебный код в любом дистрибутиве Linux с целью использования systemd.

Система предоставляет функции, совместимые со скриптами инициализации SysVinit и LSB. Существующие службы и процессы в системе не требуется изменять. Это снижает затраты на миграцию системы в systemd, за счет чего пользователи могут заменить существующую систему инициализации системой systemd.

### Мгновенные снимки состояния системы и восстановление системы

systemd можно запускать по запросу. Таким образом, рабочее состояние системы меняется динамически, и пользователю не известно, какие службы работают в системе. Снимки состояния systemd позволяют сохранять и восстанавливать текущее состояние работы системы.

Например, в системе работают службы A и B. Командой **systemd** создайте мгновенный снимок текущего состояния работы системы. Затем остановите процесс A или внесите любые другие изменения в систему, например, запустите процесс C. После этих изменений выполните команду восстановления через мгновенный снимок systemd, чтобы вернуть систему в состояние, зафиксированное в момент, в который был сделан данный снимок. То есть работают только службы A и B. Возможный сценарий применения — отладка. Например, когда на сервере возникает ошибка, пользователь сохраняет текущее состояние в виде мгновенного снимка для отладки, а затем выполняет любую операцию, например, останавливает службу. После завершения отладки система возвращается в состояние, зафиксированное мгновенным снимком.

## Управление системными службами

Служба systemd предоставляет команду systemctl для запуска, остановки, перезапуска, просмотра, включения и отключения системных служб.

### Сравнение команд SysVinit и systemd

Команда **systemctl** из команды **systemd** имеет функции, аналогичные команде **SysVinit**. Обратите внимание, что в этой версии поддерживаются команды **service** и **chkconfig**. Более подробная информация приведена в [Табл. 3](#en-us_topic_0151920917_ta7039963b0c74b909b72c22cbc9f2e28). Рекомендуется управлять системными службами через команду **systemctl**.

**Табл. 3** Сравнение команд SysVinit и systemd

| Команда sysvinit| Команда systemd| Примечания|
|:----------|:----------|:----------|
| service network start| systemctl start network.service| Запуск службы.|
| service network stop| systemctl stop network.service| Остановка службы.|
| service network restart| systemctl restart network.service| Перезапуск службы.|
| service network reload| systemctl reload network.service| Перезагрузка конфигурационного файла без прерывания операции.|
| service network condrestart| systemctl condrestart network.service| Перезапуск службы только в том случае, если она работает.|
| service network status| systemctl status network.service| Проверка состояния работы службы.|
| chkconfig network on| systemctl enable network.service| Активация службы при наступлении времени активации или выполнения условия триггера для включения услуги.|
| chkconfig network off| systemctl disable network.service| Отключение службы при наступлении времени отключения или выполнения условия триггера для отключения услуги.|
| chkconfig network| systemctl is-enabled network.service| Проверка с целью удостоверения, что служба включена.|
| chkconfig --list| systemctl list-unit-files --type=service| Вывод списка всех служб на каждом уровне выполнения и проверка с целью удостоверения, что они включены.|
| chkconfig network --list| ls /etc/systemd/system/\*.wants/network.service| Вывод списка уровней выполнения, в которых служба включена, и уровней, в которых служба отключена.|
| chkconfig network --add | systemctl daemon-reload | Используется при создании файла службы или изменении настроек. |
### Вывод списка служб

Чтобы отобразить список всех загруженных на данный момент служб, выполните следующую команду:

```
systemctl list-units --type service
```

Чтобы отобразить список всех служб, независимо от того, загружены они или нет, выполните следующую команду (с параметром **all**):

```
systemctl list-units --type service --all
```

Пример списка всех загруженных на данный момент служб:

```
$ systemctl list-units --type service
UNIT                        LOAD   ACTIVE     SUB     JOB   DESCRIPTION  
atd.service                 loaded active     running       Deferred execution scheduler  
auditd.service              loaded active     running       Security Auditing Service  
avahi-daemon.service        loaded active     running       Avahi mDNS/DNS-SD Stack  
chronyd.service             loaded active     running       NTP client/server  
crond.service               loaded active     running       Command Scheduler  
dbus.service                loaded active     running       D-Bus System Message Bus  
dracut-shutdown.service     loaded active     exited        Restore /run/initramfs on shutdown  
firewalld.service           loaded active     running       firewalld - dynamic firewall daemon  
getty@tty1.service          loaded active     running       Getty on tty1  
gssproxy.service            loaded active     running       GSSAPI Proxy Daemon  
irqbalance.service          loaded active     running       irqbalance daemon  
iscsid.service              loaded activating start   start Open-iSCSI
```

### Вывод информации о состоянии служб

Чтобы отобразить информацию о состоянии любой службы, выполните следующую команду:

```
systemctl status name.service
```

В [Табл. 4](#en-us_topic_0151920917_t36cd267d69244ed39ae06bb117ed8e62) приведены параметры, содержащиеся в командном выводе.

**Табл. 4** Параметры в командном выводе

| Параметр | Описание                                                     |
| -------- | ------------------------------------------------------------ |
| Loaded   | Информация о том, загружена служба или нет, абсолютный путь к файлу службы и примечание, указывающее на то, что служба включена. |
| Active   | Информация о том, работает служба или нет, и метка времени.  |
| Main PID | PID службы.                                                  |
| CGroup   | Дополнительная информация о соответствующих контрольных группах. |
Чтобы проверить, работает ли конкретная служба, выполните следующую команду:

```
systemctl is-active name.service
```

Результат выполнения команды **is-active** следующий:

**Табл. 5** Выходные данные команды **is-active**

| Состояние       | Описание                                                     |
| --------------- | ------------------------------------------------------------ |
| active(running) | В системе работает одна служба или несколько.                |
| active(exited)  | Служба, которая корректно завершается после однократного выполнения. В настоящее время в системе нет работающих программ. Например, функция установления квоты выполняется только при запуске или монтировании программы. |
| active(waiting) | Программе необходимо дождаться продолжения выполнения других заданий. Например, запускается служба распределения очереди на печать. Но для того, чтобы эта служба смогла активировать службу принтера для печати следующего задания, задания на печать должны быть поставлены в очередь. |
| inactive        | Служба не работает.                                          |
Чтобы проверить, работает или не работает конкретная служба, выполните следующую команду:

```
systemctl is-enabled name.service
```

Результат выполнения команды **is-enabled** следующий:

**Табл. 6** Выходные данные команды **is-enabled**

| Состояние       | Описание                                                     |
| --------------- | ------------------------------------------------------------ |
| enabled         | Служба включена на постоянной основе через символическую ссылку Alias= Alias, .wants/ или .requires/ в каталоге /etc/systemd/system/. |
| enabled-runtime | Служба временно включена через символическую ссылку Alias= Alias, .wants/ или .requires/ в каталоге /run/systemd/system/. |
| linked          | Хотя файл компонента не находится в стандартном каталоге компонента, одна символическая ссылка или несколько ссылок, указывающих на файл компонента, существуют в каталоге /etc/systemd/system/permanent. |
| linked-runtime  | Хотя файл компонента не находится в стандартном каталоге компонента, одна символическая ссылка или несколько ссылок, указывающих на файл компонента, существуют в каталоге /run/systemd/system/temporary. |
| masked          | Служба скрыта на постоянной основе каталогом /etc/systemd/system/ (символическая ссылка на /dev/null). Поэтому происходит сбой запуска. |
| masked-runtime  | Служба временно скрыта каталогом /run/systemd/systemd/ (символическая ссылка на /dev/null). Поэтому происходит сбой запуска. |
| static          | Служба не включена. В разделе \[Install] файла компонента нет опции для команды **enable**. |
| indirect        | Служба не включена. Но список значений для параметра Also= в разделе \[Install] файла компонента не пустой (то есть, возможно, что некоторые компоненты списка активированы), или файл компонента имеет псевдоним символической ссылки alias, которая не включена в список Also=. Для компонента шаблонов это означает, что включен экземпляр, отличающийся от DefaultInstance=. |
| disabled        | Служба не включена. Но раздел \[Install] файла компонента содержит параметры, доступные для команды **enable**. |
| generated       | Файл компонента динамически создается генератором компонент. Сгенерированный файл компонента нельзя активировать напрямую, а только неявно генератором компонент. |
| transient       | Файл компонента динамически и временно создается API выполнения. Временный компонент нельзя активировать. |
| bad             | Некорректный файл компонента или другая ошибка. Команда **is-enabled** не возвращает этот статус, но выводит сообщение об ошибке. Команда **list-unit-files** может отобразить этот компонент. |
Например, чтобы отобразить информацию о статусе службы gdm.service, выполните команду **systemctl status gdm.service**.

```
# systemctl status gdm.service
gdm.service - GNOME Display Manager   Loaded: loaded (/usr/lib/systemd/system/gdm.service; enabled)   Active: active (running) since Thu 2013-10-17 17:31:23 CEST; 5min ago
 Main PID: 1029 (gdm)
   CGroup: /system.slice/gdm.service
           ├─1029 /usr/sbin/gdm
           ├─1037 /usr/libexec/gdm-simple-slave --display-id /org/gno...           
           └─1047 /usr/bin/Xorg :0 -background none -verbose -auth /r...Oct 17 17:31:23 localhost systemd[1]: Started GNOME Display Manager.
```

### Запуск службы

Чтобы запустить любую службу, выполните следующую команду как пользователь **root**:

```
systemctl start name.service
```

Например, для запуска службы httpd выполняется команда:

```
# systemctl start httpd.service
```

### Остановка службы

Чтобы остановить любую службу, выполните следующую команду как пользователь **root**:

```
systemctl stop name.service
```

Например, для остановки службы Bluetooth выполняется команда:

```
# systemctl stop bluetooth.service
```

### Перезапуск службы

Чтобы перезапустить любую службу, выполните следующую команду как пользователь **root**:

```
systemctl restart name.service
```

Эта команда останавливает выбранную службу в текущем сеансе и немедленно запускает ее снова. Если выбранная служба не запущена, эта команда также запускает ее.

Например, для перезапуска службы Bluetooth выполняется команда:

```
# systemctl restart bluetooth.service
```

### Включение службы

Чтобы настроить службу на автоматический запуск во время загрузки системы, выполните следующую команду как пользователь **root**:

```
systemctl enable name.service
```

Например, для настройки службы httpd на автоматический запуск во время загрузки системы выполняется команда:

```
# systemctl enable httpd.service
ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
```

### Отключение службы

Чтобы служба не запускалась автоматически во время загрузки системы, выполните следующую команду как пользователь **root**:

```
systemctl disable name.service
```

Например, для отключения функции автоматического запуска службы Bluetooth во время загрузки системы выполняется команда:

```
# systemctl disable bluetooth.service
Removed /etc/systemd/system/bluetooth.target.wants/bluetooth.service.
Removed /etc/systemd/system/dbus-org.bluez.service.
```

## Изменение уровня выполнения

### Цели и уровни выполнения

Для повышения гибкости в службе systemd уровни выполнения заменены целями systemd. Например, унаследовав существующую цель, можно создать на ее основе свою собственную цель, добавив другие службы. В [Табл. 7](#en-us_topic_0151920939_t9af92c282ad240ea9a79fb08d26e8181) представлен полный список уровней выполнения и соответствующих им целей systemd.

**Табл. 7** Соответствие между уровнями и целями

| Уровень выполнения | Цель systemd                                          | Описание                                                     |
| ------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| 0                  | runlevel0.target，poweroff.target                     | Операционная система выключена.                              |
| 1, s, single       | runlevel1.target，rescue.target                       | Операционная система находится в однопользовательском режиме. |
| 2, 4               | runlevel2.target，runlevel4.target，multi-user.target | Операционная система находится на уровне выполнения, заданном пользователем или определяемого по домену (по умолчанию цель эквивалентна уровню выполнения 3). |
| 3                  | runlevel3.target，multi-user.target                   | Операционная система находится в неграфическом многопользовательском режиме и доступна с разных консолей или сетей. |
| 5                  | runlevel5.target，graphical.target                    | Операционная система находится в графическом многопользовательском режиме. Все службы, работающие на уровне 3, доступны через графическое окно входа. |
| 6                  | runlevel6.target，reboot.target                       | Операционная система перезагружена.                          |
| emergency          | emergency.target                                      | Аварийный режим.                                             |
### Просмотр цели запуска по умолчанию

Проверьте информацию о цели запуска по умолчанию следующей командой:

```
systemctl get-default
```

### Просмотр всех целей запуска

Проверьте информацию о всех целях запуска следующей командой:

```
systemctl list-units --type=target
```

### Изменение цели по умолчанию

Чтобы изменить цель по умолчанию, выполните следующую команду как пользователь **root**:

```
systemctl set-default name.target
```

### Изменение текущей цели

Чтобы изменить текущую цель, выполните следующую команду как пользователь **root**:

```
systemctl isolate name.target
```

### Переключение в режим восстановления

Чтобы переключить операционную систему в режим восстановления (rescue mode), выполните следующую команду как пользователь **root**:

```
systemctl rescue
```

Эта команда аналогична команде **systemctl isolate rescue.target.** После выполнения команды на последовательном порте отображается следующая информация:

```
You are in rescue mode. After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" or "exit"to boot into default mode.
Give root password for maintenance
(or press Control-D to continue):
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:  
Чтобы перейти в нормальный рабочий режим из режима восстановления, необходимо перезапустить систему.

### Переход в аварийный режим

Чтобы переключить операционную систему в аварийный режим (emergency mode), выполните следующую команду как пользователь **root**:

```
systemctl emergency
```

Эта команда аналогична команде **systemctl isolate emergency.target**. После выполнения команды на последовательном порте отображается следующая информация:

```
You are in emergency mode. After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" or "exit"to boot into default mode.
Give root password for maintenance
(or press Control-D to continue):
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:  
Чтобы перейти в нормальный рабочий режим из аварийного режима, необходимо перезапустить систему.

## Завершение работы, приостановка работы и переключение в режим гибернации операционной системы

### Команда systemctl

Для завершение работы операционной системы, приостановки работы и переключения в режим гибернации служба systemd запускает команду systemctl вместо ранее используемых управляющих команд Linux. Хотя ранее используемые управляющие команды Linux по-прежнему доступны в службе systemd в целях обеспечения совместимости, по возможности рекомендуется использовать команду **systemctl**. Соответствие между старыми и новыми командами представлено в [Табл. 8](#en-us_topic_0151920964_t3daaaba6a03b4c36be9668efcdb61f3b).

**Табл. 8** Соответствие между старыми управляющими командами Linux и командами systemctl

| Управляющая команда Linux | Команда systemctl  | Описание                                |
| ------------------------- | ------------------ | --------------------------------------- |
| halt                      | systemctl halt     | Завершение работы операционной системы. |
| poweroff                  | systemctl poweroff | Выключение операционной системы.        |
| reboot                    | systemctl reboot   | Перезагрузка операционной системы.      |
### Завершение работы операционной системы

Чтобы завершить работу операционной системы и выключить питание, выполните следующую команду как пользователь **root**:

```
systemctl poweroff
```

Чтобы завершить работу операционной системы, не выключая питание, выполните следующую команду как пользователь **root**:

```
systemctl halt
```

По умолчанию выполнение любой из этих команд инициирует службу systemd на отправку информативного сообщения всем пользователям, вошедшим в систему. Чтобы служба systemd не отправляла данное сообщение, выполните эту команду с опцией **--no-wall**. Команда выглядит следующим образом:

```
systemctl --no-wall poweroff
```

### Перезагрузка операционной системы

Чтобы перезагрузить операционную систему, выполните следующую команду как пользователь **root**:

```
systemctl reboot
```

По умолчанию выполнение любой из этих команд инициирует службу systemd на отправку информативного сообщения всем пользователям, вошедшим в систему. Чтобы служба systemd не отправляла данное сообщение, выполните эту команду с опцией **--no-wall**. Команда выглядит следующим образом:

```
systemctl --no-wall reboot
```

### Приостановка работы операционной системы

Чтобы приостановить работу операционной системы, выполните следующую команду как пользователь **root**:

```
systemctl suspend
```

### Переключение операционной системы в режим гибернации

Чтобы переключить операционную систему в режим гибернации, выполните следующую команду как пользователь **root**:

```
systemctl hibernate
```

Чтобы приостановить работу операционной системы и переключить ее в режим гибернации, выполните следующую команду как пользователь **root**:

```
systemctl hybrid-sleep
```