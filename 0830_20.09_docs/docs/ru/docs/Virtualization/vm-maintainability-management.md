# Техобслуживание виртуальных машин

## NMI Watchdog: таймер наблюдения за немаскируемыми прерываниями виртуальной машины

### Обзор

NMI Watchdog — это механизм, используемый в Linux для обнаружения жесткой блокировки. Немаскируемое прерывание (Non-maskable Interrupt; NMI) может прервать выполнение кода и обнаружить жесткую блокировку, даже если обычные прерывания отключены. Текущая архитектура ARM не поддерживает встроенную функцию NMI, поэтому здесь используется режим Pseudo-NMI, включаемый в соответствии с приоритетом прерывания. В качестве NMI в этом случае настраивается функция прерывания, вызванного счетчиками мониторинга производительности (Performance Monitoring Interrupt; PMI), которая и выполняет роль таймера наблюдения (PMU Watchdog).

### Меры предосторожности

- Операционная система виртуальной машины должна поддерживать режим Pseudo-NMI, а также должны быть сконфигурированы соответствующие параметры ядра.
- Конфигурация PMU Watchdog виртуальной машины совпадает с конфигурацией PMU Watchdog хоста. Конфигурирование файла XML не требуется.
- Обе схемы — SDEI Watchdog и PMU Watchdog — являются таймерами наблюдения за NMI-прерываниями, но первая имеет более высокий приоритет, чем PMU Watchdog. Поэтому PMU Watchdog можно включить только в том случае, если таймер SDEI Watchdog отключен. Виртуальная машина не поддерживает SDEI Watchdog, поэтому необходимо отключить эту схему, настроив соответствующим образом параметры ядра.

### Процедура

Чтобы настроить NMI Watchdog для виртуальной машины в архитектуре ARM, выполните следующие шаги:

1. Добавьте следующую информацию в конфигурационный файл загрузки виртуальной машины — grub.cfg:

2. Убедитесь, что таймер PMU Watchdog успешно загружен на виртуальную машину. В случае успешной загрузки в журнале dmesg ядра отображается информация, аналогичная следующей:
   
   ```
    [2.1173222] NMI watchdog: CPU0 freq probed as 2399999942 HZ.
   ```