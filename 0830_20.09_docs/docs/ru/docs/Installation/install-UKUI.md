# Установка UKUI

UKUI — это рабочий стол Linux, создаваемый на протяжении многих лет командой разработчиков программного обеспечения KylinSoft, в основном на базе GTK и QT. В сравнении с другими пользовательскими интерфейсами UKUI прост в использовании. Облегченные компоненты UKUI отличаются слабой связностью, могут работать независимо от других компонентов. Этот рабочий стол очень удобен в использовании.

UKUI поддерживает архитектуры x86\_64 и aarch64.

Перед установкой UKUI рекомендуется создать нового пользователя-администратора.

1. [Скачайте](https://openeuler.org/zh/download/) ISO-файл openEuler 20.09 и обновите источник программного обеспечения.

```
sudo dnf update
```

2. Установите UKUI.

```
sudo dnf install ukui
```

Примечание. Чтобы установить UKUI, необходим пакет libdbusmenu. Этот пакет требует python2, который конфликтует с пакетом python3-unversioned-command (этот пакет предоставляет символическую ссылку на /usr/bin/python3). Удалите python3-unversioned-command с помощью `rpm -e --nodeps python3-unversioned-command`. После завершения установки можно восстановить настройки пакета следующей командой.

```
ln -s /usr/bin/python3 /usr/bin/python
```

3. Установите шрифты.

```
sudo dnf groupinstall fonts
```

4. Если пользователь планирует запустить графический интерфейс после подтверждения установки, необходимо ввести этот код и перезагрузиться.

```
systemctl set-default graphical.target
```

В настоящее время версия UKUI периодически обновляется. Проверьте последнюю версию: https://gitee.com/openkylin/ukui-issues.