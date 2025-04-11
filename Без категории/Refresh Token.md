Алгоритм работы **Refresh Token** основан на обновлении **Access Token**, когда он истекает. Это повышает безопасность, так как не нужно держать долгоживущие Access Token'ы.

---

## 🔄 **Общий алгоритм работы Refresh Token**

### 🔹 **1. Аутентификация пользователя**

- Пользователь вводит **логин и пароль**.
- Сервер проверяет данные и выдает два токена:
    - **Access Token** (краткоживущий, например, 15 минут).
    - **Refresh Token** (долгоживущий, например, 7-30 дней).
- Refresh Token **хранится в защищенном хранилище** (например, HttpOnly cookie или БД).

### 🔹 **2. Использование Access Token**

- Клиент отправляет **Access Token** в `Authorization: Bearer <token>`.
- Сервер проверяет токен:
    - Если **валиден** → выдаёт ответ.
    - Если **просрочен** → клиент отправляет Refresh Token.

### 🔹 **3. Обновление Access Token через Refresh Token**

- Клиент отправляет Refresh Token на сервер (`POST /refresh`).
- Сервер проверяет Refresh Token:
    - Если **валиден** → создаёт новый **Access Token** и **новый Refresh Token**.
    - Если **НЕ валиден** → требует повторной аутентификации.
- Клиент получает новые токены и продолжает работу.

### 🔹 **4. Выход из системы**

- Клиент отправляет `POST /logout`.
- Сервер **удаляет Refresh Token** (если он хранится в БД) или делает его неактивным.

---

## 🛡 **Как хранить Refresh Token?**

1. **HttpOnly Cookie** – безопасно от XSS, но уязвимо для CSRF.
2. **LocalStorage** – уязвимо для XSS, не рекомендуется.
3. **База данных** – безопасный способ, позволяет **аннулировать токены**.

---

## 🔥 **Пример реализации в PHP (Symfony)**

### 📌 **Генерация Access и Refresh Token**

```php
use Firebase\JWT\JWT;

$privateKey = "your-secret-key";

$payloadAccess = [
    'sub' => $user->getId(),
    'exp' => time() + 900 // 15 минут
];

$payloadRefresh = [
    'sub' => $user->getId(),
    'exp' => time() + 2592000 // 30 дней
];

$accessToken = JWT::encode($payloadAccess, $privateKey, 'HS256');
$refreshToken = JWT::encode($payloadRefresh, $privateKey, 'HS256');

return ['access_token' => $accessToken, 'refresh_token' => $refreshToken];
```

### 📌 **Обновление Access Token**

```php
use Firebase\JWT\JWT;
use Firebase\JWT\Key;

$refreshToken = $_POST['refresh_token'];

try {
    $decoded = JWT::decode($refreshToken, new Key($privateKey, 'HS256'));

    // Проверяем, есть ли этот refresh token в базе
    if (!isValidRefreshToken($decoded->sub, $refreshToken)) {
        throw new Exception("Invalid refresh token");
    }

    // Генерируем новый Access Token
    $newAccessToken = JWT::encode([
        'sub' => $decoded->sub,
        'exp' => time() + 900
    ], $privateKey, 'HS256');

    return ['access_token' => $newAccessToken];
} catch (Exception $e) {
    return ['error' => 'Invalid refresh token'];
}
```

---

## 🚀 **Вывод**

- **Access Token** используется для запросов (живет 10-15 минут).
- **Refresh Token** нужен для продления сессии (живет 7-30 дней).
- **Безопасность**: Refresh Token лучше хранить в базе и отзывать при выходе.
- **Выход**: Refresh Token удаляется, пользователь должен заново войти.

🔹 **Выбирай подходящий способ хранения в зависимости от проекта!** 🔒