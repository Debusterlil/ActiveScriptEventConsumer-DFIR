# Hunting checklist: Permanent WMI Subscription

## Цель

Обнаружить постоянную WMI-подписку, восстановить связь между
Filter, Consumer и Binding, определить процесс регистрации
и последствия срабатывания.

## 1. Инвентаризация WMI

Команды выполняются в Windows PowerShell от имени администратора.
Они только читают объекты и ничего не удаляют.

### Event Filters

```powershell
Get-CimInstance `
    -Namespace root/subscription `
    -ClassName __EventFilter |
    Select-Object Name, EventNamespace, Query, CreatorSID
```

### Event Consumers

```powershell
Get-CimInstance `
    -Namespace root/subscription `
    -ClassName __EventConsumer |
    Select-Object __CLASS, Name, CreatorSID
```

### ActiveScriptEventConsumer

```powershell
Get-CimInstance `
    -Namespace root/subscription `
    -ClassName ActiveScriptEventConsumer |
    Select-Object Name, ScriptingEngine, CreatorSID
```

Полный `ScriptText` следует сохранить как доказательство,
но необязательно публиковать в статье.

### Filter-to-Consumer Binding

```powershell
Get-CimInstance `
    -Namespace root/subscription `
    -ClassName __FilterToConsumerBinding |
    Select-Object Filter, Consumer
```

## 2. Что требует проверки

- неизвестные Filter, Consumer и Binding;
- класс `ActiveScriptEventConsumer`;
- запросы `Win32_ProcessStartTrace`;
- запросы `__InstanceCreationEvent`;
- запуск по созданию процесса, файла или таймеру;
- имена, похожие на системные компоненты;
- Consumer, создающий файлы или запускающий программы;
- несколько компонентов, созданных почти одновременно;
- неизвестный пользователь или `CreatorSID`.

Постоянная WMI-подписка сама по себе не доказывает компрометацию.
Этот механизм может применяться легитимными средствами управления.

## 3. Sysmon: регистрация подписки

Ключевые события:

- Event ID 19 — WMI Event Filter;
- Event ID 20 — WMI Event Consumer;
- Event ID 21 — Filter-to-Consumer Binding.

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Sysmon/Operational'
    Id      = 19,20,21
} |
    Select-Object TimeCreated, Id, RecordId, Message
```

Высокую уверенность даёт появление событий 19, 20 и 21
на одном компьютере в коротком временном окне.

## 4. Запуск scrcons.exe

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Sysmon/Operational'
    Id      = 1
} |
    Where-Object {
        $_.Message -match '\\scrcons\.exe'
    } |
    Select-Object TimeCreated, Id, RecordId, Message
```

Проверять:

- пользователя;
- `ParentImage`;
- `ProcessId`;
- время относительно триггера;
- наличие аргумента `-Embedding`.

## 5. WMI-Activity

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-WMI-Activity/Operational'
    Id      = 5857,5859,5861
} |
    Select-Object TimeCreated, Id, RecordId, Message
```

Сопоставлять:

- Namespace;
- Query;
- ClientProcessId;
- HostProcessId;
- User;
- ResultCode;
- время относительно Sysmon.

## 6. PowerShell Script Block Logging

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-PowerShell/Operational'
    Id      = 4104
} |
    Where-Object {
        $_.Message -match (
            '__EventFilter|' +
            'ActiveScriptEventConsumer|' +
            '__FilterToConsumerBinding|' +
            'root\\subscription'
        )
    } |
    Select-Object TimeCreated, Id, RecordId, Message
```

В публичном отчёте вместо полного ScriptBlockText допустимо указывать:

- Record ID;
- время;
- длину блока;
- SHA-256;
- обнаруженные ключевые признаки.

## 7. Рекомендуемая корреляция

1. PowerShell 4104 или другой процесс регистрации.
2. Sysmon Event ID 19, 20 и 21.
3. WMI-Activity Event ID 5859 и 5861.
4. Событие, соответствующее запросу Filter.
5. Запуск `scrcons.exe`.
6. Результат работы Consumer.
7. Следы в памяти, Prefetch и WMI-репозитории.

## 8. Отрицательные результаты

Отсутствие `scrcons.exe` среди активных процессов не исключает
его более ранний кратковременный запуск.

FILE_OBJECT, строки в RAM и путь Prefetch являются дополнительными
свидетельствами, но не заменяют журналы исполнения.

Отсутствие Sysmon Event ID 11 не доказывает, что файл не создавался:
событие могло не попасть под конфигурацию Sysmon.

## 9. Минимальный набор для эскалации

- имя Filter;
- WQL-запрос;
- имя и класс Consumer;
- Binding;
- CreatorSID или пользователь;
- время и процесс регистрации;
- запуск `scrcons.exe`;
- созданные файлы или другие последствия;
- назначение системы;
- известные разрешённые административные средства.

## 10. Результаты текущего кейса

В эксперименте обнаружены:

- `BlackCatFilter`;
- `BlackCatConsumer`;
- Filter-to-Consumer Binding;
- Sysmon Event ID 19, 20 и 21;
- PowerShell Event ID 4104;
- запуск `scrcons.exe` от SYSTEM;
- создание `black_cat.txt`;
- маркеры эксперимента в памяти;
- следы `OBJECTS.DATA` и SCRCONS Prefetch.

Для отделения текущего эксперимента от старой лабораторной активности
использовались уникальные имена объектов и временное окно 20 июля 2026 года.
