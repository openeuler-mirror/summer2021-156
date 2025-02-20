# Управление пользователями и группами пользователей

В ОС Linux каждый обычный пользователь имеет учетную запись, включающую имя пользователя, пароль и домашний каталог. В данной операционной системе предусмотрены также особые категории пользователей, созданные для определенных целей, и среди них самой важной является учетная запись admin, у которой имя пользователя по умолчанию — root. Кроме того, в Linux предоставлены группы пользователей, то есть каждый пользователь принадлежит хотя бы одной группе, что облегчает управление разрешениями.

Контроль действий, совершаемых пользователями и группами пользователей, является ключевым элементом в управлении безопасностью openEuler. В этом разделе описываются команды управления пользователями и группами пользователей с пояснениями, как назначать привилегии обычным пользователям через графический интерфейс пользователя и через командную строку.

<!-- TOC -->
- [Управление пользователями и группами пользователей](#user-and-user-group-management)
  - [Управление пользователем](#managing-users)
    - [Добавление пользователя](#adding-a-user)
    - [Изменение учетной записи пользователя](#modifying-a-user-account)
    - [Удаление пользователя](#deleting-a-user)
    - [Предоставление прав обычному пользователю](#granting-rights-to-a-common-user)
  - [Управление группой пользователей](#managing-user-groups)
    - [Добавление группы пользователей](#adding-a-user-group)
    - [Изменение группы пользователей](#modifying-a-user-group)
    - [Удаление группы пользователей](#deleting-a-user-group)
    - [Добавление пользователя в группу и удаление пользователя из группы](#adding-a-user-to-a-group-or-removing-a-user-from-a-group)
    - [Изменение текущей группы, которой принадлежит пользователь, на заданную группу](#changing-the-current-group-of-a-user-to-a-specified-group)

<!-- /TOC -->
## Управление пользователем

### Добавление пользователя

#### Команда useradd

Чтобы добавить информацию о пользователе в систему, выполните команду **useradd** как пользователь **root**. В данной команде *options* — это параметры, а *username* — имя пользователя.

```
useradd [options] username
```

#### Файлы с информацией о пользователе

Следующие файлы содержат информацию об учетной записи пользователя:

- /etc/passwd: данные учетной записи
- /etc/shadow: шифруемая информация об учетной записи
- /etc/group: информация о группе
- /etc/default/useradd: конфигурация по умолчанию
- /etc/login.defs: настройки всей системы
- /etc/skel: каталог по умолчанию, содержащий исходные конфигурационные файлы

#### Пример

Создайте пользователя с именем userexample, выполнив следующую команду как пользователь **root**:

```
# useradd userexample
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ:**  
Если никаких сообщений не появляется, значит, пользователь успешно создан. Создав пользователя, выполните команду **passwd**, чтобы назначить ему пароль. В отсутствии пароля новая учетная запись будет заблокирована.

Для просмотра информации о новом пользователе выполните команду **id**:

```
# id userexample
uid=502(userexample)    gid=502(userexample)    groups=502(userexample)
```

Чтобы изменить пароль userexample, выполните следующую команду:

```
# passwd userexample
```

Пароль пользователя должен иметь заданную сложность. Требования к сложности пароля следующие:

1. Пароль должен содержать не менее восьми символов.
2. Пароль должен содержать как минимум три типа символов: прописные буквы, строчные буквы, цифры и специальные символы.
3. Пароль должен отличаться от имени учетной записи.
4. Пароль не должен содержать слова, включенные в словарь.
   - Запрос словаря. В установленной среде openEuler можно экспортировать файл библиотеки словаря **dictionary.txt**, выполнив следующую команду, а затем проверить, включен или не включен в данный словарь пароль.
     
     ```
     cracklib-unpacker /usr/share/cracklib/pw_dict > dictionary.txt
     ```
   
   - Изменение словаря
     
     1. Измените экспортированный файл библиотеки словаря, а затем обновите библиотеку, выполнив следующую команду:
        ```
        # create-cracklib-dict dictionary.txt
        ```
     2. Добавьте другой файл словаря **custom.txt** в исходную библиотеку, выполнив следующую команду:
        ```
        # create-cracklib-dict dictionary.txt custom.txt
        ```

Введите и подтвердите пароль, следуя подсказкам:

```
# passwd userexample
Changing password for user userexample.
New password:
Retype new password:
passwd: all authentication tokens updated successfully.
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ:**  
Если в выходных данных команды содержится информация **BAD PASSWORD**: ******The password fails the dictionary check - it is too simplistic/sytematic**, значит, пароль слишком простой и необходимо задать другой.

### Изменение учетной записи пользователя

#### Изменение пароля

Обычные пользователи могут изменять свои пароли с помощью команды **passwd**. Изменять пароли других пользователей командой **passwd** **username** может только администратор (пользователь admin).

#### Изменение оболочки окна входа пользователя

Обычные пользователи могут изменить свою оболочку окна входа в систему командой **chsh**. Изменять оболочки входа других пользователей командой **chsh username** может только администратор (пользователь admin).

Также можно изменить информацию оболочки, выполнив команду **usermod** как пользователь **root**. В данной команде _new\_shell\_path_ означает путь к новой оболочке, _username_ — это имя пользователя, которое требуется изменить. Измените их в соответствии с требованиями площадки.

```
usermod -s new_shell_path username
```

Например, чтобы изменить оболочку пользователя userexample на csh, необходимо выполнить следующую команду:

```
# usermod -s /bin/csh userexample
```

#### Изменение домашнего каталога

- Чтобы изменить домашний каталог, выполните следующую команду как пользователь **root**. В данной команде _new\_home\_path_ означает создаваемый новый домашний каталог, _username_ — это имя пользователя, которое требуется изменить. Измените их в соответствии с требованиями площадки.
  
  ```
  usermod -d new_home_directory username
  ```

- Чтобы переместить содержимое текущего домашнего каталога в новый каталог, выполните следующую команду с параметром -m:
  
  ```
  usermod -d new_home_directory -m username
  ```

#### Изменение UID

Чтобы изменить идентификатор пользователя, выполните следующую команду как пользователь **root**. В данной команде _UID_ означает новый идентификатор пользователя, _username_ — это имя пользователя, идентификатор которого требуется изменить. Измените их в соответствии с требованиями площадки.

```
usermod -u UID username
```

С помощью команды **usermod** можно изменить идентификаторы пользователей во всех файлах и каталогах, содержащихся в домашнем каталоге пользователя. Однако имена владельцев файлов, расположенных в другом месте, не в домашнем каталоге пользователя, можно изменить только командой **chown**.

#### Изменение срока действия учетной записи

Чтобы изменить срок действия учетной записи, в которой используется теневой пароль, выполните следующую команду как пользователь **root**. В данной команде _MM_, _DD_ и _YY_ обозначают месяц, день и год соответственно, а *username* — это имя пользователя. Измените их в соответствии с требованиями площадки.

```
usermod -e MM/DD/YY username
```

### Удаление пользователя

Чтобы удалить существующего пользователя, выполните команду **userdel** как пользователь **root**.

Например, удалите пользователя Test, выполнив следующую команду:

```
# userdel Test
```

Если также необходимо удалить домашний каталог пользователя и все содержимое каталога, выполните команду **userdel** c параметром -r, чтобы удалить их в рекурсивной последовательности.

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ:**  
Не рекомендуется сразу удалять пользователя, который вошел в систему. Для принудительного удаления пользователя выполните команду **userdel -f** *Test*.

### Предоставление прав обычному пользователю

С помощью команды **sudo** обычные пользователи выполняют команды, которые разрешено выполнять только под учетными записями администраторов.

Командой **sudo** и полномочиями администратора на выполнение команд может воспользоваться пользователь, указанный в файле **/etc/sudoers**. Например, авторизованный обычный пользователь может выполнить команду:

```
sudo /usr/sbin/useradd newuserl
```

С помощью данной команды **sudo** обычный пользователь, добавленный в файл **/etc/sudoers**, может выполнять требуемые задачи.

Информация в файле **/etc/sudoers** конфигурируется следующим образом:

- Пустые строки или строки с комментариями, начинающиеся с символа #: Нет каких-то особых функций.

- Необязательные строки с псевдонимами хоста. Присвойте имя списку хостов. Строки должны начинаться с **Host\_Alias**. Имена хостов в списке должны разделяться запятыми (,). Например:
  
  ```
  Host_Alias  linux=ted1,ted2
  ```
  
  **ted1** и **ted2** — имена двух хостов с псевдонимом **linux**.

- Необязательные строки с псевдонимами пользователя. Присвойте имя списку пользователей. Строки должны начинаться с **User\_Alias**. Имена пользователей в списке должны разделяться запятыми (,). Строки с псевдонимами пользователя имеют тот же формат, что и строки с псевдонимами хоста.

- Необязательные строки с псевдонимами команды. Присвойте имя списку команд. Строки должны начинаться с **Cmnd\_Alias**. Команды в списке должны разделяться запятыми (,).

- Необязательные строки с псевдонимами с режимов работы: Присвойте имя списку пользователей. Разница заключается в том, что такой псевдоним позволяет пользователю из списка выполнять команду **sudo**.

- Обязательные строки объявления для доступа пользователя:
  
  Синтаксис объявления для доступа пользователя выглядит следующим образом:
  
  ```
  user host = [ run as user ] command list
  ```
  
  Для пользователя задайте реальное имя или определенный псевдоним пользователя, а для хоста задайте реальное имя или определенный псевдоним хоста. По умолчанию все команды через **sudo** выполняются с правами пользователя **root**. Если необходимо использовать другую учетную запись, укажите ее. **command list** — это список команд, разделенных запятыми (,), или определенный псевдоним команды. Например:
  
  ```
  ted1   ted2=/sbin/shutdown
  ```
  
  В этом примере хост **ted1** может выполнять команду отключения на хосте **ted2**.
  
  ```
  newuser1 ted1=(root) /usr/sbin/useradd,/usr/sbin/userdel
  ```
  
  Это означает, что пользователь **newuser1** на хосте **ted1** может выполнять команды **useradd** и **userdel** как пользователь с правами **root**.
  
  > ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ:**
  > 
  > - В одной строке можно задать несколько псевдонимов, разделив их двоеточиями (:).
  > - Если перед командой или псевдонимом команды добавить восклицательный знак (!), данная команда или псевдоним команды станут недействительными.
  > - Используются два ключевых слова: ALL и NOPASSWD. ALL означает все файлы, хосты или команды, а NOPASSWD означает, что пароль не требуется.
  > - Изменяя разрешения на доступ, можно обычного пользователя наделить правами пользователя **root** .

Ниже приведен пример файла **sudoers**:

```
#sudoers files
#User alias specification
User_Alias ADMIN=ted1:POWERUSER=globus,ted2
#user privilege specification
ADMIN ALL=ALL
POWERUSER ALL=ALL,!/bin/su
```

Параметры:

- User\_Alias ADMIN=ted1:POWERUSER=globus,ted2
  
  Определены два псевдонима ADMIN и POWERUSER.

- ADMIN ALL=ALL
  
  ADMIN может выполнять все команды как пользователь **root** на всех хостах.

- POWERUSER ALL=ALL,!/bin/su
  
  POWERUSER может выполнять все команды, кроме команды **su**, как пользователь **root** на всех хостах.

## Управление группой пользователей

### Добавление группы пользователей

#### Команда groupadd

Чтобы добавить информацию о группе пользователей в систему, выполните команду **groupadd** как пользователь **root**. В данной команде *options* — это параметры, а *groupname* — имя группы.

```
groupadd [options] groupname
```

#### Файлы с информацией о группе пользователей

Следующие файлы содержат информацию о группе пользователей:

- /etc/gshadow: шифруемая информация о группе пользователей
- /etc/group: информация о группе
- /etc/login.defs: настройки всей системы

#### Пример

Создайте группу пользователей с именем groupexample, выполнив следующую команду как пользователь **root**:

```
# groupadd groupexample
```

### Изменение группы пользователей

#### Изменение GID

Чтобы изменить идентификатор группы пользователей, выполните следующую команду как пользователь **root**. В данной команде _GID_ означает новый идентификатор группы пользователей, _groupname_ — это имя группы, идентификатор которой требуется изменить. Измените их в соответствии с требованиями площадки.

```
groupmod -g GID groupname
```

#### Изменение имени группы пользователей

Чтобы изменить имя группы пользователей, выполните следующую команду как пользователь **root**. В данной команде *newgroupname* означает новое имя группы пользователей, _oldgroupname_ — это имя группы, которое требуется изменить. Измените их в соответствии с требованиями площадки.

```
groupmod -n newgroupname oldgroupname
```

### Удаление группы пользователей

Чтобы удалить существующую группу пользователей, выполните команду **groupdel** как пользователь **root**.

Например, удалите группу пользователей Test, выполнив следующую команду:

```
# groupdel Test
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ:**  
Первоначальную группу пользователей нельзя удалить напрямую. Для принудительного удаления группы пользователей выполните команду **groupdel -f** _Test_ .

### Добавление пользователя в группу и удаление пользователя из группы

Добавление пользователя в группу и удаление пользователя из группы выполняется командой **gpasswd**.

Пример: добавьте пользователя userexample в группу Test, выполнив следующую команду:

```
# gpasswd -a userexample Test
```

Пример: удалите пользователя userexample из группы Test, выполнив следующую команду:

```
# gpasswd -d userexample Test
```

### Изменение текущей группы, которой принадлежит пользователь, на заданную группу

Чтобы переключить пользователя, включенного в состав нескольких групп, на другую группу после входа в систему, выполните команду **newgrp**. После этого пользователь получает разрешения другой группы.

Пример: измените текущую группу пользователя userexample на группу Test, выполнив следующую команду:

```
$ newgrp Test
```