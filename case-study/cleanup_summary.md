# Cleanup validation

Точечная очистка лабораторной постоянной WMI-подписки выполнялась
в следующем порядке:

1. Filter-to-Consumer Binding.
2. ActiveScriptEventConsumer.
3. Event Filter.

Результат проверки после очистки:

- Event Filter: 0;
- Event Consumer: 0;
- Binding: 0;
- новый `black_cat.txt` не создан;
- новый запуск `scrcons.exe` не зарегистрирован;
- контрольный тест пройден.

Полные исходные журналы и внутренние доказательства не публикуются.
