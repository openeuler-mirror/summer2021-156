# Постоянно выполняемая переменная среды

- [Постоянно выполняемая переменная среды](#environment-variable-persisting)

## Описание функционала

В системном контейнере можно сделать переменную **env** постоянно выполняемой в конфигурационном файле в каталоге rootfs контейнера, указав параметр интерфейса **--env-target-file**.

## Описание параметров

| Команда          | Параметр          | Описание значений                                            |
| ---------------- | ----------------- | ------------------------------------------------------------ |
| isula create/run | --env-target-file | ·      Переменная строкового типа. <br />·      Файл **env** должен находиться в каталоге rootfs и должен иметь абсолютный путь. |

## Ограничения

- Если целевой файл, указанный в **--env-target-file**, существует, его размер не должен превышать 10 МБ.
- Параметр, указанный в **--env-target-file**, должен быть абсолютным путем в каталоге rootfs.
- Если значение **--env** конфликтует со значением **env** в целевом файле, действует значение **--env**.

## Пример

В приведенном примере осуществлен запуск системного контейнера с указанием переменной среды **env** и параметра **--env-target-file.**

```
[root@localhost ~]# isula run -tid -e abc=123 --env-target-file /etc/environment --system-container --external-rootfs /root/myrootfs none init
b75df997a64da74518deb9a01d345e8df13eca6bcc36d6fe40c3e90ea1ee088e
[root@localhost ~]# isula exec b7 cat /etc/environment
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
TERM=xterm
abc=123
```

В приведенной информации переменная среды **env** (**abc=123**) контейнера стала постоянно выполняемой в конфигурационном файле **/etc/environment**.