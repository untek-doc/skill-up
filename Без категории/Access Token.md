## 🔑 **Что такое Access Token?**

**Access Token** — это краткоживущий токен, который используется для аутентификации и авторизации пользователя. Он позволяет клиенту (например, браузеру или мобильному приложению) выполнять запросы к защищенным ресурсам без передачи логина и пароля.

---

## 🔄 **Как работает Access Token?**

1. **Аутентификация пользователя**
    
    - Пользователь вводит логин и пароль.
    - Сервер проверяет данные и выдаёт **Access Token**.
2. **Использование Access Token**
    
    - Клиент отправляет запрос с заголовком `Authorization: Bearer <token>`.
    - Сервер проверяет валидность токена и выполняет запрос.
3. **Истечение срока действия**
    
    - **Access Token живёт недолго** (например, 10-15 минут).
    - После истечения срока клиент запрашивает **новый Access Token** через **Refresh Token**.

---

## 📌 **Пример Access Token (JWT)**

Access Token часто создаётся в формате **JWT (JSON Web Token)**. Он состоит из **трёх частей**:

1. **Header** (заголовок) – алгоритм подписи
2. **Payload** (полезная нагрузка) – данные пользователя
3. **Signature** (подпись) – защита от подделки

Пример JWT-токена:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.eyJzdWIiOjEsImV4cCI6MTcwOTgwNzAwMCwiaWF0IjoxNzA5ODA2NDAwfQ
.Bd2pDlU9gLdwfMlzM0U2oW67qfIhPynYdxQ5GprJ0oU
```

---

## 🚀 **Генерация Access Token в PHP (Firebase JWT)**

```php
use Firebase\JWT\JWT;

$privateKey = "your-secret-key";

$payload = [
    'sub' => $user->getId(),
    'exp' => time() + 900, // 15 минут
    'iat' => time(),
];

$accessToken = JWT::encode($payload, $privateKey, 'HS256');

echo json_encode(['access_token' => $accessToken]);
```

---

## 🔍 **Проверка Access Token**

При каждом запросе сервер должен **проверять** Access Token:

```php
use Firebase\JWT\JWT;
use Firebase\JWT\Key;

$accessToken = $_SERVER['HTTP_AUTHORIZATION'] ?? '';

try {
    $decoded = JWT::decode($accessToken, new Key($privateKey, 'HS256'));
    echo "User ID: " . $decoded->sub;
} catch (Exception $e) {
    http_response_code(401);
    echo json_encode(['error' => 'Invalid token']);
}
```

---

## 🛡 **Как хранить Access Token?**

1. **Authorization Header (`Bearer Token`)** ✅ **(Рекомендуется)**
2. **Cookies (HttpOnly)**
3. **LocalStorage (⚠️ Небезопасно из-за XSS-атак)**

---

## 🔥 **Вывод**

- **Access Token** используется для авторизации.
- Он **действителен только ограниченное время** (обычно 10-15 минут).
- После истечения срока необходимо **обновить его через Refresh Token**.
- **Лучший способ хранения** — **в заголовке Authorization: Bearer**.

🔹 **Используйте JWT и защищённое хранение токенов для безопасной аутентификации!** 🔐