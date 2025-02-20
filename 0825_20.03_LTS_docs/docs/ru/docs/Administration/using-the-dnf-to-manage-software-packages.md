# Управление пакетами программного обеспечения с помощью DNF

DNF — это инструмент Linux, используемый для управления пакетами программного обеспечения формата RPM. DNF запрашивает информацию о пакетах программного обеспечения, получает их из указанной библиотеки, автоматически обрабатывает зависимости для установки или удаления пакетов и обновляет систему до последней доступной версии.

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:
> 
> - Инструмент DNF полностью совместим с YUM и предоставляет совместимые с YUM командные строки и API для расширений и подключаемых модулей (плагинов).
> - Для использования DNF необходимо иметь права администратора. Все команды, приведенные в этой главе, должен выполнять пользователь-администратор.

\[\[toc]]

## Настройка DNF

### Конфигурационный файл DNF

Основной конфигурационный файл DNF — /etc/dnf/dnf.conf — состоит из двух разделов:

- В основном разделе (**main**) файла хранятся глобальные настройки DNF.

- В разделе репозитория (**repository**) файла хранятся настройки источника программного пакета. В файл можно добавить один или несколько разделов **repository**.

Также в каталоге /etc/yum.repos.d хранится один или несколько файлов с источниками репозитория, которые определяют различные репозитории.

Настроить источник программного обеспечения можно либо сконфигурировав файл /etc/dnf.conf, либо файл .repo в каталоге /etc/yum.repos.d.

#### Настройка основного раздела

Файл /etc/dnf/dnf.conf содержит основной раздел (**main**). Ниже приведен пример конфигурационного файла:

```
[main]
gpgcheck=1
installonly_limit=3
clean_requirements_on_remove=True
best=True
```

Стандартные параметры:

**Табл. 1** Описание параметров раздела **main**

| Параметр                        | Описание                                                     |
| ------------------------------- | ------------------------------------------------------------ |
| cachedir                        | Каталог с кэшем для хранения пакетов RPM и файлов базы данных. |
| keepcache                       | Значения 1 и 0 параметра устанавливают необходимость кэширования пакетов RPM и файлов заголовков, которые были успешно установлены. Значение по умолчанию — **0**, означающее, что пакеты RPM и файлы заголовков не кэшируются. |
| debuglevel                      | Установка отладочной информации, генерируемой инструментом DNF. Диапазон принимаемых значений — от 0 до 10. Чем больше значение, тем более подробна отладочная информация. Значение по умолчанию — **2**. Значение 0 означает, что отладочная информация не отображается. |
| clean\_requirements\_on\_remove | Удаление элементов зависимостей, которые больше не используются во время удаления DNF. Если пакет программного обеспечения устанавливается с помощью инструмента DNF, а не по прямому запросу пользователя, такой пакет можно будет удалить только через clean\_requirements\_on\_remove, то есть пакет программного обеспечения вводится как элемент зависимости. Значение по умолчанию — **True**. |
| best                            | Система всегда пытается установить последнюю версию пакета обновлений. В случае сбоя установки последней версии система выводит причину и останавливает процесс установки. Значение по умолчанию — **True**. |
| obsoletes                       | Значения 1 и 0 параметра устанавливают разрешение на обновление устаревших пакетов RPM. Значение по умолчанию **1** означает, что обновление разрешено. |
| gpgcheck                        | Значения 1 и 0 параметра устанавливают необходимость верификации GPG. Значение по умолчанию **1** означает, что верификация требуется. |
| plugins                         | Значения 1 и 0 параметра включают или отключают подключаемый модуль (плагин) инструмента DNF. Значение по умолчанию **1** означает, что плагин DNF включен. |
| installonly\_limit              | Настройка количества пакетов, которые можно установить одновременно с помощью команды installonlypkgs. Значение по умолчанию — **3**. Данное значение не рекомендуется уменьшать. |
#### Настройка раздела репозитория

Раздел репозитория (**repository**) позволяет настраивать репозитории источников программного обеспечения openEuler. Имя каждого репозитория должно быть уникальным. В противном случае могут возникнуть конфликты. Настроить источник программного обеспечения можно либо сконфигурировав файл /etc/dnf.conf, либо файл .repo в каталоге /etc/yum.repos.d.

- Настройка файла /etc/dnf/dnf.conf
  
  Ниже приведен пример минимальной конфигурации раздела \[repository]:
  
  ```
  [repository]
  name=repository_name
  baseurl=repository_url
  ```
  
  > ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:  
openEuler предоставляет источник с образами в онлайн по ссылке [https://repo.openeuler.org/.](https://repo.openeuler.org/) Например, если версия openEuler 20.03 LTS является версией с архитектурой aarch64, **baseurl** установить в [https://repo.openeuler.org/openEuler-20.03-LTS/OS/aarch64/](https://repo.openeuler.org/openEuler-20.03-LTS/OS/aarch64/).
  
  Стандартные параметры:
  
  **Табл. 2** Описание параметров раздела **repository**
  
  | Параметр                | Описание                                                     |
  | ----------------------- | ------------------------------------------------------------ |
  | name=repository\_name   | Строка имени репозитория программного обеспечения.           |
  | baseurl=repository\_url | Адрес репозитория программного обеспечения.<br/>Например, <br/>Сетевой каталог с использованием протокола HTTP http://path/to/repo<br/>Сетевой каталог с использованием протокола FTP ftp://path/to/repo<br/>Локальный путь: file:///path/to/local/repo. |
  


- Настройка файла .repo в каталоге /etc/yum.repos.d
  
  openEuler предоставляет несколько источников репозитория, которые пользователи могут загрузить онлайн. Для получения подробной информации об источниках репозитория см. раздел [Установка системы](./../Releasenotes/installing-the-os.md.html).
  
  Например, выполните следующую команду от имени пользователя **root**, чтобы добавить источник репозитория openeuler в файл openEuler.repo.
  
  ```
  # vi /etc/yum.repos.d/openEuler.repo
  ```
  
  ```
  [OS]
  name=openEuler-$releasever - OS
  baseurl=https://repo.openeuler.org/openEuler-20.03-LTS/OS/$basearch/
  enabled=1
  gpgcheck=1
  gpgkey=https://repo.openeuler.org/openEuler-20.03-LTS/OS/$basearch/RPM-GPG-KEY-openEuler
  ```
  
  > ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:
  > 
  > - Параметром **enabled** включается репозиторий источника программного обеспечения. Принимаемые значения — **1** или **0**. Значение по умолчанию **1** означает, что репозиторий источника программного обеспечения включен.
  > - **gpgkey** — открытый ключ, используемый для проверки подписи.

#### Отображение текущих настроек

- Чтобы отобразить информацию о текущих настройках, выполните следующую команду:
  
  ```
  dnf config-manager --dump
  ```

- Чтобы отобразить конфигурацию источника программного обеспечения, запросите идентификатор репозитория:
  
  ```
  dnf repolist
  ```
  
  Следующая команда выводит конфигурацию источника программного обеспечения с соответствующим идентификатором. В данной команде *repository* означает фактический идентификатор репозитория.
  
  ```
  dnf config-manager --dump repository
  ```

- Для отображения всех соответствующих конфигураций можно также воспользоваться глобальными регулярными выражениями.
  
  ```
  dnf config-manager --dump glob_expression
  ```

### Создание локального репозитория программного обеспечения

Чтобы создать локальный репозиторий источников программного обеспечения, выполните следующие шаги.

1. Установите программный пакет createrepo. Как пользователь **root** выполните следующую команду:
   
   ```
   dnf install createrepo
   ```

2. Скопируйте необходимые пакеты программного обеспечения в каталог, например /mnt/local\_repo/.

3. Выполните следующую команду, чтобы создать источник программного обеспечения:
   
   ```
   createrepo --database /mnt/local_repo
   ```

### Добавление, включение и отключение источников программного обеспечения

В этом разделе описывается метод добавления, включения и отключения репозитория источника программного обеспечения с помощью команды **dnf config-manager**.

#### Добавление источника программного обеспечения

Чтобы определить новый репозиторий программного обеспечения, можно добавить раздел repository в файл /etc/dnf/dnf.conf или добавить файл .repo в каталог /etc/yum.repos.d/. Рекомендуется воспользоваться вторым методом — добавлением файла .repo. Каждый источник программного обеспечения имеет свой собственный файл .repo. Далее описывается процедура добавления файла .repo.

Чтобы добавить такой источник в проектируемой системе, выполните следующую команду как пользователь **root**. После выполнения команды в каталоге **/etc/yum.repos.d/** генерируется соответствующий файл .repo. В данной команде *_repository\_url_* означает фактический адрес источника репозитория. Более подробная информация приведена в [Табл. 2](#en-us_topic_0151921080_t2df9dceb0ff64b2f8db8ec5cd779792a).

```
dnf config-manager --add-repo repository_url
```

#### Включение репозитория программного обеспечения

Чтобы включить источник программного обеспечения, выполните следующую команду как пользователь **root**. В данной команде _repository_ означает идентификатор репозитория в новом файле .repo. Запрос идентификатора репозитория осуществляется командой **dnf repolist**.

```
dnf config-manager --set-enable repository
```

Для включения всех соответствующих источников программного обеспечения можно также воспользоваться глобальными регулярными выражениями. В данной команде *glob\_expression* означает регулярное выражение, используемое для сопоставления идентификаторов репозиториев.

```
dnf config-manager --set-enable glob_expression
```

#### Отключение репозитория программного обеспечения

Чтобы отключить источник программного обеспечения, выполните следующую команду как пользователь **root**:

```
dnf config-manager --set-disable repository
```

Для отключения всех соответствующих источников программного обеспечения можно также воспользоваться глобальными регулярными выражениями.

```
dnf config-manager --set-disable glob_expression
```

## Управление пакетом программного обеспечения

С помощью инструмента DNF можно запрашивать, устанавливать и удалять пакеты программного обеспечения.

### Поиск пакетов программного обеспечения

Найти требуемый пакет RPM можно по его имени, сокращению или описанию. Команда выглядит следующим образом:

```
dnf search term
```

Пример:

```
$   dnf search httpd
========================================== N/S matched: httpd ==========================================
httpd.aarch64 : Apache HTTP Server
httpd-devel.aarch64 : Development interfaces for the Apache HTTP server
httpd-manual.noarch : Documentation for the Apache HTTP server
httpd-tools.aarch64  : Tools for use with the Apache HTTP Server
libmicrohttpd.aarch64  : Lightweight library for embedding a webserver in applications
mod_auth_mellon.aarch64  : A SAML 2.0 authentication module for the Apache Httpd Server
mod_dav_svn.aarch64  : Apache httpd module for Subversion server
```

### Вывод списка пакетов программного обеспечения

Чтобы вывести список всех установленных и доступных пакетов формата RPM в системе, выполните следующую команду:

```
dnf list all
```

Чтобы вывести конкретный пакет формата RPM в системе, выполните следующую команду:

```
dnf list glob_expression...
```

Пример:

```
$ dnf list httpd
Available Packages
httpd.aarch64              2.4.34-8.h5.oe1           Local
```

### Вывод информации о пакете RPM

Чтобы просмотреть информацию об одном или нескольких пакетах формата RPM, выполните следующую команду:

```
dnf info package_name...
```

Пример выполнения команды:

```
$ dnf info httpd
Available Packages
Name        : httpd
Version     : 2.4.34
Release     : 8.h5.oe1
Arch        : aarch64
Size        : 1.2 M
Repo        : Local
Summary     : Apache HTTP Server
URL         : http://httpd.apache.org/
License     : ASL 2.0
Description : The Apache HTTP Server is a powerful, efficient, and extensible
            : web server.
```

### Установка пакета RPM

Чтобы установить пакет программного обеспечения и все его зависимости, которые еще не были установлены, выполните следующую команду от имени пользователя **root**:

```
dnf install package_name
```

Также можно одновременно установить несколько пакетов программного обеспечения, добавляя имена пакетов. Добавьте параметр **strict=False** в конфигурационный файл /etc/dnf/dnf.conf и выполните команду **dnf** для добавления --setopt=strict=0. Как пользователь **root** выполните следующую команду:

```
dnf install package_name package_name... --setopt=strict=0
```

Пример:

```
# dnf install httpd
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:  
Если пакет RPM не удастся установить, см. раздел [Ошибка установки, вызванная конфликтом пакетов программного обеспечения, конфликтом файлов или отсутствием пакета программного обеспечения](./faqs.html#installation-failure-caused-by-software-package-conflict-file-conflict-or-missing-software-package).

### Загрузка пакетов программного обеспечения

Чтобы загрузить пакет программного обеспечения с помощью DNF, выполните следующую команду как пользователь **root**:

```
dnf download package_name
```

Если необходимо загрузить не установленные пакеты зависимостей, добавьте **--resolv**. Команда выглядит следующим образом:

```
dnf download --resolve package_name
```

Пример:

```
# dnf download --resolve httpd
```

### Удаление пакета программного обеспечения

Чтобы удалить пакет программного обеспечения и соответствующие пакеты зависимостей, выполните следующую команду как пользователь **root**:

```
dnf remove package_name...
```

Пример:

```
# dnf remove totem
```

## Управление группами пакетов программного обеспечения

Набор пакетов программного обеспечения представляет собой группу пакетов с общим предназначением, например таким является набор системных инструментов. Установка или удаление групп пакетов программного обеспечения с помощью DNF повышает эффективность данных операций.

### Вывод списка групп пакетов программного обеспечения

Для вывода списка всех установленных групп пакетов программного обеспечения, доступных групп и доступных групп среды в системе используется параметр сводки. Команда выглядит следующим образом:

```
dnf groups summary
```

Пример:

```
# dnf groups summary
Last metadata expiration check: 0:11:56 ago on Sat 17 Aug 2019 07:45:14 PM CST.
Available Groups: 8
```

Чтобы вывести список всех групп пакетов программного обеспечения и их идентификаторов, выполните следующую команду:

```
dnf group list
```

Пример:

```
# dnf group list
Last metadata expiration check: 0:10:32 ago on Sat 17 Aug 2019 07:45:14 PM CST.
Available Environment Groups:
   Minimal Install
   Custom Operating System
   Server
Available Groups:
   Development Tools
   Graphical Administration Tools
   Headless Management
   Legacy UNIX Compatibility
   Network Servers
   Scientific Support
   Security Tools
   System Tools

```

### Отображение информации о группе пакетов программного обеспечения

Чтобы вывести список обязательных и необязательных пакетов, содержащихся в группе пакетов программного обеспечения, выполните следующую команду:

```
dnf group info glob_expression...
```

Пример отображения информации о группе под названием «Development Tools»:

```
# dnf group info "Development Tools"
Last metadata expiration check: 0:14:54 ago on Wed 05 Jun 2019 08:38:02 PM CST.

Group: Development Tools
 Description: A basic development environment.
 Mandatory Packages:
   binutils
   glibc-devel
   make
   pkgconf
   pkgconf-m4
   pkgconf-pkg-config
   rpm-sign
 Optional Packages:
   expect
```

### Установка группы пакетов программного обеспечения

Каждая группа пакетов программного обеспечения имеет свое собственное имя и соответствующий идентификатор. Для групповой установки пакетов программного обеспечения можно использовать имя или идентификатор группы.

Чтобы установить пакеты программного обеспечения группой, выполните следующую команду как пользователь **root**:

```
dnf group install group_name
```

```
dnf group install groupid
```

Например, чтобы установить пакеты программного обеспечения, входящие в группу «Development Tools», выполните следующую команду:

```
# dnf group install "Development Tools"
```

```
# dnf group install development
```

### Удаление группы пакетов программного обеспечения

Чтобы удалить группу пакетов программного обеспечения, выполните следующую команду от имени пользователя **root**, используя имя или идентификатор группы:

```
dnf group remove group_name
```

```
dnf group remove groupid
```

Например, чтобы удалить пакеты программного обеспечения, входящие в группу «Development Tools», выполните следующую команду:

```
# dnf group remove "Development Tools"
```

```
# dnf group remove development
```

## Проверка и обновление

Для проверки необходимости обновления любого пакета программного обеспечения в своей системе можно использовать инструмент DNF. Для вывода списка пакетов программного обеспечения, требующих обновления, можно использовать инструмент DNF. Можно выбрать опцию одновременного обновления всех пакетов или обновления только определенных пакетов.

### Проверка обновления

Чтобы отобразить список всех доступных на данный момент обновлений, выполните следующую команду:

```
dnf check-update
```

Пример:

```
# dnf check-update
Last metadata expiration check: 0:02:10 ago on Sun 01 Sep 2019 11:28:07 PM  CST.

anaconda-core.aarch64       19.31.123-1.14             updates
anaconda-gui.aarch64        19.31.123-1.14             updates
anaconda-tui.aarch64        19.31.123-1.14             updates
anaconda-user-help.aarch64  19.31.123-1.14             updates
anaconda-widgets.aarch64    19.31.123-1.14             updates
bind-libs.aarch64           32:9.9.4-29.3              updates
bind-libs-lite.aarch64      32:9.9.4-29.3              updates
bind-license.noarch         32:9.9.4-29.3              updates
bind-utils.aarch64          32:9.9.4-29.3              updates
...
```

### Обновление версии

Чтобы обновить версию одного пакета программного обеспечения, выполните следующую команду как пользователь **root**:

```
dnf update package_name
```

Например, для обновления версии пакета RPM выполняется команда:

```
# dnf update anaconda-gui.aarch64
Last metadata expiration check: 0:02:10 ago on Sun 01 Sep 2019 11:30:27 PM  CST.
Dependencies Resolved
================================================================================
 Package                  Arch         Version              Repository     Size
================================================================================
Updating:
 anaconda-gui             aarch64      19.31.123-1.14       updates       461 k
 anaconda-core            aarch64      19.31.123-1.14       updates       1.4 M
 anaconda-tui             aarch64      19.31.123-1.14       updates       274 k
 anaconda-user-help       aarch64      19.31.123-1.14       updates       315 k
 anaconda-widgets         aarch64      19.31.123-1.14       updates       748 k

Transaction Summary
================================================================================
Upgrade  5 Package

Total download size: 3.1 M
Is this ok [y/N]:
```

Аналогично, чтобы обновить версию группы пакетов программного обеспечения, выполните следующую команду как пользователь **root**:

```
dnf group update group_name
```

### Обновление всех пакетов и их зависимостей

Чтобы обновить все пакеты программного обеспечения и их зависимости, выполните следующую команду как пользователь **root**:

```
dnf update
```