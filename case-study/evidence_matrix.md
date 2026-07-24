# Evidence matrix

| Артефакт | Наблюдение | Что подтверждает | Чего не доказывает | Роль |
|---|---|---|---|---|
| PowerShell Operational — Event ID 4104 | В памяти журнала зафиксирован блок регистрации BlackCatFilter, BlackCatConsumer и Binding | PowerShell-код регистрации WMI-подписки был выполнен | Сам по себе не доказывает последующее срабатывание consumer и создание black_cat.txt | Primary |
| Sysmon Event ID 19 | Создан BlackCatFilter с запросом Win32_ProcessStartTrace для notepad.exe | В постоянном пространстве WMI создан EventFilter | Не доказывает, что фильтр впоследствии сработал | Primary |
| Sysmon Event ID 20 | Создан BlackCatConsumer типа Script | Зарегистрирован ActiveScriptEventConsumer | Не доказывает исполнение сценария или результат его работы | Primary |
| Sysmon Event ID 21 | BlackCatFilter связан с BlackCatConsumer | Создано постоянное FilterToConsumerBinding | Не доказывает последующее срабатывание этой связи | Primary |
| WMI-Activity Event ID 5861 | Зафиксированы Filter, Consumer и Binding в root\subscription | WMI зарегистрировала постоянную подписку | Не подтверждает создание файла полезной нагрузкой | Primary |
| WMI-Activity Event ID 5859 | Зарегистрирован постоянный WQL-запрос на запуск notepad.exe | WMI приняла запрос уведомления Win32_ProcessStartTrace | Не доказывает, что конкретный запуск notepad.exe активировал consumer | Primary |
| Sysmon — запуск notepad.exe | В 22:44:54.575 UTC+3 создан процесс notepad.exe | Возникло событие, соответствующее условию экспериментального фильтра | Само по себе не связывает процесс с конкретной WMI-подпиской | Corroborating |
| Sysmon — запуск scrcons.exe | В 22:44:55.326 UTC+3 запущен scrcons.exe от SYSTEM | Хост ActiveScriptEventConsumer был запущен вскоре после триггера | Не раскрывает самостоятельно, какой сценарий исполнялся и успешно ли он завершился | Primary |
| WMI-Activity Event ID 5857 | Провайдер ActiveScriptEventConsumer запущен с Result 0x0 | WMI успешно инициализировала соответствующий провайдер | Result 0x0 не является самостоятельным доказательством создания black_cat.txt | Primary |
| black_cat.txt — метаданные и SHA-256 | Файл существует; LastWriteTime соответствует окну исполнения consumer | На диске появился ожидаемый результат эксперимента | Одна только метка времени не устанавливает процесс-создатель | Primary |
| Volatility windows.info | Определена Windows x64; SystemTime совпадает с началом захвата памяти | Дамп корректно читается и имеет установленную временную привязку | Не подтверждает наличие конкретной вредоносной активности | Validation |
| Volatility pslist / psscan | scrcons.exe к моменту дампа не найден | На момент захвата пригодная структура процесса scrcons.exe не обнаружена | Не доказывает, что scrcons.exe ранее не запускался | Negative finding |
| Volatility pstree / cmdline | PowerShell породил go-winpmem с командой acquire memory.raw | Процесс получения памяти и его аргументы зафиксированы непосредственно в RAM | Не относится к механизму исполнения WMI-consumer | Validation |
| Volatility filescan | Найдены FILE_OBJECT для OBJECTS.DATA, scrcons.exe, SCRCONS Prefetch и black_cat.txt | В памяти сохранились файловые объекты, связанные с исследуемой активностью | FILE_OBJECT сам по себе не подтверждает исполнение или содержимое файла | Corroborating |
| Volatility dumpfiles — OBJECTS.DATA | Извлечены SharedCacheMap и DataSectionObject фрагменты WMI-репозитория | Части OBJECTS.DATA присутствовали в файловом кэше памяти | Извлечённые файлы нельзя считать полной копией WMI-репозитория | Corroborating |
| Strings внутри извлечённого OBJECTS.DATA | Найдены ActiveScriptEventConsumer, root\subscription и Win32_ProcessStartTrace | Фрагменты содержат общие маркеры постоянной WMI-подписки | В извлечённых фрагментах не восстановлены точные имена BlackCatFilter и BlackCatConsumer | Corroborating |
| Поиск строк по полному memory.raw | Найдены BlackCatFilter, BlackCatConsumer и black_cat.txt в ASCII и UTF-16LE | Точные маркеры эксперимента сохранились в RAM спустя около 40 минут | Количество совпадений не соответствует количеству подписок и не устанавливает источник каждой копии строки | Corroborating |
| SHA-256 memory.raw | Хеш совпал в Windows VM, на хосте и в Kali | Рабочая копия побайтово совпадает с первоначально захешированным дампом | Хеш не устанавливает происхождение данных до момента первой фиксации | Validation |
