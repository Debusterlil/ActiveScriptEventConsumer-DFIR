# Master timeline

| UTC | Local UTC+3 | Source | Event | Artifact | Interpretation | Confidence |
|---|---|---|---|---|---|---|
| 2026-07-20 19:36:26.274 | 2026-07-20 22:36:26.274 | PowerShell Operational | 4104 / Record 7749 | BlackCat registration script block | Выполнен основной блок регистрации постоянной WMI-подписки | High |
| 2026-07-20 19:36:26.396 | 2026-07-20 22:36:26.396 | Sysmon | ID 20 / Record 12338 | BlackCatConsumer | Создан ActiveScriptEventConsumer | High |
| 2026-07-20 19:36:26.399 | 2026-07-20 22:36:26.399 | WMI-Activity | ID 5861 / Record 505 | Filter, Consumer, Binding | Зарегистрирована постоянная WMI-подписка в root\subscription | High |
| 2026-07-20 19:36:26.401 | 2026-07-20 22:36:26.401 | Sysmon | ID 19 / Record 12339 | BlackCatFilter | Создан фильтр для запуска notepad.exe | High |
| 2026-07-20 19:36:26.401 | 2026-07-20 22:36:26.401 | WMI-Activity | ID 5859 / Record 506 | Win32_ProcessStartTrace query | WMI зарегистрировала постоянный запрос уведомлений | High |
| 2026-07-20 19:36:26.402 | 2026-07-20 22:36:26.402 | Sysmon | ID 21 / Record 12341 | FilterToConsumerBinding | BlackCatFilter связан с BlackCatConsumer | High |
| 2026-07-20 19:36:27.343 | 2026-07-20 22:36:27.343 | WMI-Activity | ID 5857 / Record 507 | scrcons.exe provider | Провайдер ActiveScriptEventConsumer успешно инициализирован | High |
| 2026-07-20 19:44:54.575 | 2026-07-20 22:44:54.575 | Sysmon | Process Create | notepad.exe | Запущен процесс, соответствующий условию WMI-фильтра | High |
| 2026-07-20 19:44:55.326 | 2026-07-20 22:44:55.326 | Sysmon | Process Create | scrcons.exe / PID 7128 / SYSTEM | Запущен исполнитель ActiveScriptEventConsumer | High |
| 2026-07-20 19:44:55.370 | 2026-07-20 22:44:55.370 | WMI-Activity | ID 5857 / Record 511 | scrcons.exe / result 0x0 | WMI зафиксировала успешный запуск провайдера | High |
| 2026-07-20 19:45:03 | 2026-07-20 22:45:03 | File metadata | LastWriteTime | black_cat.txt | Зафиксирован результат выполнения consumer | High |
| 2026-07-20 20:15:45 | 2026-07-20 23:15:45 | Volatility pstree/cmdline | PID 5560 | notepad.exe 00_experiment_notes.txt | Аналитик открыл заметки; это не исходный триггер | High |
| 2026-07-20 20:25:18 | 2026-07-20 23:25:18 | Volatility windows.info + pstree | PID 8708 | go-winpmem acquire memory.raw | Началось получение образа оперативной памяти | High |
| 2026-07-20 20:25:34 | 2026-07-20 23:25:34 | memory.raw metadata | LastWriteTime | memory.raw | Получение образа памяти завершено | High |
