**DKIM (DomainKeys Identified Mail)** — это технология для проверки подлинности электронной почты, которая используется для защиты от фальсификации отправителей и предотвращения мошенничества, например, спуфинга. С помощью DKIM отправляющий сервер подписывает исходящие письма цифровой подписью, и получающий сервер проверяет эту подпись, чтобы удостовериться, что письмо действительно отправлено из домена, указанного в адресе отправителя, и что его содержимое не было изменено.

### Как работает DKIM?

1. **Создание ключей**: Владелец домена создает пару ключей — **открытый** и **закрытый**:
   - Закрытый ключ хранится на почтовом сервере и используется для создания цифровой подписи в исходящих письмах.
   - Открытый ключ публикуется в DNS-записях домена, чтобы почтовые серверы получателей могли использовать его для проверки подписей.

2. **Подпись исходящих писем**: Когда письмо отправляется, почтовый сервер подписывает его часть (заголовки и, иногда, тело письма) с помощью закрытого ключа. Подпись добавляется в заголовок письма в виде строки DKIM-Signature.

3. **Проверка на стороне получателя**: Получающий сервер извлекает открытый ключ домена отправителя из его DNS-записей и использует его для проверки DKIM-подписи. Если подпись совпадает, сервер может доверять письму и считать, что оно отправлено действительно с указанного домена и не было изменено в пути.

### Преимущества DKIM

- **Защита от подделки**: Помогает предотвратить фальсификацию отправителя, так как только владелец домена имеет доступ к закрытому ключу, используемому для подписания писем.
- **Снижение риска спама**: Письма с действительными DKIM-подписями с меньшей вероятностью будут помечены как спам.
- **Улучшение репутации домена**: Постоянное использование DKIM помогает почтовым провайдерам доверять домену, что улучшает доставляемость писем.

### Настройка DKIM

1. **Сгенерировать ключи**: Используйте инструменты почтового сервера (например, для Postfix или Google Workspace) для создания открытого и закрытого ключей.
2. **Опубликовать открытый ключ**: Добавьте его в DNS-записи домена как тип записи TXT. Формат записи включает в себя текстовый тег DKIM и открытый ключ.
3. **Конфигурировать почтовый сервер**: Настройте сервер так, чтобы он подписывал исходящие письма с использованием закрытого ключа.
4. **Тестирование и проверка**: Отправьте тестовое письмо и используйте инструменты проверки DKIM (например, DKIMCore) для удостоверения, что подпись корректна.

### Пример DNS-записи для DKIM

Пример записи TXT в DNS для DKIM может выглядеть так:

```
default._domainkey.example.com IN TXT "v=DKIM1; k=rsa; p=МИССИЯ_ОТКРЫТОГО_КЛЮЧА"
```

Здесь:
- **default** — это селектор DKIM (вместо него можно использовать любое уникальное имя, чтобы различать ключи),
- **_domainkey** — это обязательный суффикс для DKIM-записи,
- **v=DKIM1** — версия протокола,
- **k=rsa** — алгоритм шифрования,
- **p=МИССИЯ_ОТКРЫТОГО_КЛЮЧА** — это сам открытый ключ, закодированный в base64.

### Заключение

DKIM — важный метод безопасности, который помогает защитить отправителей и получателей электронной почты от подделки и улучшает доставляемость сообщений. Однако DKIM лучше всего работает в сочетании с другими методами защиты, такими как **SPF** и **DMARC**, чтобы обеспечить многоуровневую защиту от мошенничества и спуфинга.