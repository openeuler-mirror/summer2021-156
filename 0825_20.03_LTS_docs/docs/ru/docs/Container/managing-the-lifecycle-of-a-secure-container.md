# Управление жизненным циклом безопасного контейнера

- [Управление жизненным циклом безопасного контейнера](#managing-the-lifecycle-of-a-secure-container)
  - [Запуск безопасного контейнера](#starting-a-secure-container)
  - [Остановка безопасного контейнера](#stopping-a-secure-container)
  - [Удаление безопасного контейнера](#deleting-a-secure-container)
  - [Запуск новой команды в контейнере](#running-a-new-command-in-the-container)

## Запуск безопасного контейнера

В качестве контейнерного движка безопасного контейнера можно использовать Docker или iSulad. Методы инициирования обоих движков идентичны. Для запуска безопасного контейнера можно выбрать любой из движков.

Чтобы запустить безопасный контейнер, выполните следующие действия:

1. Убедитесь, что компонент безопасного контейнера корректно установлен и развернут.

2. Подготовьте образ контейнера. Если образом контейнера является образ busybox, выполните следующие команды, чтобы загрузить его с помощью движка Docker или iSulad:
   
   ```
   docker pull busybox
   ```
   
   ```
   isula pull busybox
   ```

3. Запустите безопасный контейнер. Выполните следующие команды, чтобы запустить безопасный контейнер с помощью движка Docker и iSulad:
   
   ```
   docker run -tid --runtime kata-runtime --network none busybox <command>
   ```
   
   ```
   isula run -tid --runtime kata-runtime --network none busybox <command>
   ```
   
   > ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:  
Безопасный контейнер поддерживает только сеть CNI и не поддерживает сеть CNM. Параметры **-p** и **--expose** не используются для предоставления контейнерных портов. При использовании безопасного контейнера необходимо указать параметр **--net=none**.

4. Запустите объект pod.
   
   1. Запустите приостановленный контейнер (pause-контейнер) и получите идентификатор песочницы объекта pod из командного вывода. Выполните следующие команды, чтобы запустить pause-контейнер с помощью движка Docker и iSulad:
      
      ```
      docker run -tid --runtime kata-runtime --network none --annotation io.kubernetes.docker.type=podsandbox <pause-image> <command>
      ```
      
      ```
      isula run -tid --runtime kata-runtime --network none --annotation io.kubernetes.cri.container-type=sandbox <pause-image> <command>
      ```
      
        
   
   2. Создайте сервисный контейнер и добавьте его к объекту pod. Выполните следующие команды, чтобы создать сервисный контейнер с помощью движка Docker и iSulad:
      
      ```
      docker run -tid --runtime kata-runtime --network none --annotation io.kubernetes.docker.type=container --annotation io.kubernetes.sandbox.id=<sandbox-id> busybox <command>
      ```
      
      ```
      isula run -tid --runtime kata-runtime --network none --annotation io.kubernetes.cri.container-type=container --annotation io.kubernetes.cri.sandbox-id=<sandbox-id> busybox <command>
      ```
      
      Параметр **--annotation** используется для обозначения типа контейнера, который предоставляется движками Docker и iSulad, но не предоставляется движком Docker с открытым исходным кодом в исходном сообществе разработчиков.

## Остановка безопасного контейнера

- Чтобы остановить работу безопасного контейнера, выполните следующую команду:
  
  ```
  docker stop <contaienr-id>
  ```

- Остановите объект pod.
  
  Останавливая pod, обратите внимание, что жизненный цикл pause-контейнера такой же, как у объекта pod. Поэтому перед pause-контейнером необходимо остановить сервисные контейнеры.

## Удаление безопасного контейнера

Убедитесь, что контейнер остановлен.

```
docker rm <container-id>
```

Чтобы принудительно удалить работающий контейнер, выполните команду **-f**.

```
docker rm -f <container-id>
```

## Запуск новой команды в контейнере

Приостановленный контейнер (pause-контейнер) используется только как временно-замещающий контейнер. Поэтому, если запуская объект pod, необходимо выполнить новую команду в сервисном контейнере. pause-контейнер самостоятельно не выполняет соответствующую команду. Если запущен только один контейнер, выполните сразу следующую команду:

```
docker exec -ti <container-id> <command>
```

> ![](./public_sys-resources/icon-note.gif) **ПРИМЕЧАНИЕ**:
> 
> 1. В отсутствии отклика данной команды в связи с тем, что другой хост запускает команду **docker restart** или **docker stop**, чтобы получить доступ к тому же контейнеру, нажмите Ctrl+P+Q для выхода из операции.
> 2. Если используется параметр **-d**, команда выполняется в фоновом режиме, и информация об ошибках не выводится. Для определения корректности выполнения команды код выхода не используется.