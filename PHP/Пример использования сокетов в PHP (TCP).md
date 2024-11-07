Вот простой пример использования сокетов в PHP для создания TCP-соединения. В этом примере мы создадим сервер и клиент, которые смогут обмениваться данными.

### 1. Сервер на сокетах (TCP)

Создадим TCP-сервер, который будет ожидать входящих соединений на определённом порту и отвечать клиенту.

```php
<?php
// Параметры сервера
$host = "127.0.0.1";
$port = 12345;

// Создаем сокет
$socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
if (!$socket) {
    die("Не удалось создать сокет: " . socket_strerror(socket_last_error()));
}

// Привязываем сокет к адресу и порту
if (!socket_bind($socket, $host, $port)) {
    die("Не удалось привязать сокет: " . socket_strerror(socket_last_error($socket)));
}

// Начинаем прослушивание порта
if (!socket_listen($socket, 5)) {
    die("Не удалось запустить прослушивание сокета: " . socket_strerror(socket_last_error($socket)));
}

echo "Сервер запущен на {$host}:{$port}\n";

while (true) {
    // Принимаем входящее соединение
    $client = socket_accept($socket);
    if (!$client) {
        echo "Не удалось принять соединение: " . socket_strerror(socket_last_error($socket)) . "\n";
        continue;
    }

    // Читаем сообщение от клиента
    $input = socket_read($client, 1024);
    echo "Сообщение от клиента: $input\n";

    // Отправляем ответ
    $response = "Сообщение получено: $input";
    socket_write($client, $response, strlen($response));

    // Закрываем соединение с клиентом
    socket_close($client);
}

// Закрываем серверный сокет
socket_close($socket);
?>
```

### 2. Клиент на сокетах (TCP)

Теперь создадим TCP-клиент, который подключится к серверу, отправит сообщение и получит ответ.

```php
<?php
// Параметры сервера
$host = "127.0.0.1";
$port = 12345;

// Создаем сокет
$socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);
if (!$socket) {
    die("Не удалось создать сокет: " . socket_strerror(socket_last_error()));
}

// Подключаемся к серверу
if (!socket_connect($socket, $host, $port)) {
    die("Не удалось подключиться к серверу: " . socket_strerror(socket_last_error($socket)));
}

// Отправляем сообщение серверу
$message = "Привет, сервер!";
socket_write($socket, $message, strlen($message));

// Читаем ответ от сервера
$response = socket_read($socket, 1024);
echo "Ответ от сервера: $response\n";

// Закрываем сокет
socket_close($socket);
?>
```

### Как это работает:

1. **Серверный скрипт**: Запустите его, и он будет ожидать подключений на порту 12345.
2. **Клиентский скрипт**: Подключается к серверу, отправляет сообщение и получает ответ.

### Запуск:
- Запустите сервер в одном терминале:
  ```bash
  php server.php
  ```
- Затем в другом терминале запустите клиент:
  ```bash
  php client.php
  ```

В консоли сервера появится сообщение, что клиент отправил данные, а в консоли клиента — ответ от сервера.

#socket #tcp 