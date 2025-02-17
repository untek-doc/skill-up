**Bearer Token** — это токен, который используется для авторизации и доступа к защищенным ресурсам API. Bearer токены широко применяются в API на основе **OAuth 2.0** и позволяют клиентам отправлять запросы с правами доступа без необходимости повторно вводить учетные данные пользователя. В таких системах токен действует как «носитель» права доступа, и его наличие в запросе подтверждает авторизацию.

### Как работает Bearer Token?

Bearer Token работает следующим образом:
1. **Авторизация**: Клиент (например, пользовательское приложение) сначала проходит процесс авторизации и получает токен от сервера авторизации.
2. **Передача токена**: Этот токен используется в каждом последующем запросе к API для получения доступа к защищенным ресурсам.
3. **Проверка токена на сервере**: API проверяет токен и предоставляет доступ к ресурсам, если токен валиден и не истек.

### Использование Bearer Token в API-запросах

Bearer токен отправляется в HTTP-заголовке **Authorization**. Структура заголовка такова:

```
Authorization: Bearer <токен>
```

Пример запроса с использованием токена:
```http
GET /protected/resource HTTP/1.1
Host: api.example.com
Authorization: Bearer your_token_here
```

Здесь:
- `Authorization` — заголовок HTTP, где передается токен.
- `Bearer` — схема авторизации, указывающая, что используется Bearer токен.
- `your_token_here` — сам токен, выданный сервером авторизации.

### Как получить Bearer Token?

Bearer токен может быть получен через процесс авторизации, например, с помощью OAuth 2.0:
1. **Запрос на авторизацию** — приложение пользователя направляет запрос серверу авторизации с учетными данными пользователя или другим методом (например, refresh токеном).
2. **Получение токена** — сервер возвращает токен, который будет использоваться для последующих запросов к API.

### Особенности и меры безопасности

- **Срок действия токена**: Токены имеют ограниченный срок действия, после которого они становятся недействительными.
- **Refresh токены**: Обычно используются вместе с Bearer токенами, чтобы получить новый токен без повторной аутентификации.
- **Безопасность передачи**: Bearer токен должен передаваться только по защищённому каналу (HTTPS), чтобы предотвратить перехват токена.

### Пример использования Bearer Token в API Platform

Если вы используете **API Platform**, вы можете настроить авторизацию через Bearer токены для обеспечения безопасности API.

Пример конфигурации для Symfony (который лежит в основе API Platform) с поддержкой Bearer токенов может выглядеть так:

```yaml
# config/packages/security.yaml
security:
    firewalls:
        main:
            pattern: ^/api/
            stateless: true
            jwt: # Использование JWT в качестве Bearer токена
                authorization_header:
                    prefix: Bearer
                token_fetcher: security.token_fetcher.jwt
```

### Заключение

**Bearer Token** — это удобный и эффективный способ управления доступом в API, который позволяет клиентам получать защищенные данные, используя временные токены. Bearer токены широко применяются и поддерживаются различными стандартами авторизации, обеспечивая безопасность и гибкость для разработки современных API.