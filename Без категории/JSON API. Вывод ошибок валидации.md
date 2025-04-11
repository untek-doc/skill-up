В `JSON:API` ошибки должны соответствовать спецификации `JSON:API Error Objects`. Если у вас есть ошибки **в заголовках запроса** или **в query-параметрах**, их можно вернуть в следующем формате:

---

## ✅ **1. Общий формат ошибки в JSON:API**

Ошибки должны быть представлены массивом `errors`:

```json
{
  "errors": [
    {
      "status": "400",
      "source": {
        "parameter": "page[number]"
      },
      "title": "Invalid Query Parameter",
      "detail": "The page number must be a positive integer."
    },
    {
      "status": "400",
      "source": {
        "header": "X-Api-Version"
      },
      "title": "Missing Required Header",
      "detail": "The X-Api-Version header is required."
    }
  ]
}
```

Где:

- `status` — HTTP-код ошибки (например, `400` для неверных запросов).
- `source.parameter` — если ошибка связана с `query`-параметром.
- `source.header` — если ошибка связана с заголовком запроса.
- `title` — краткое описание ошибки.
- `detail` — детальное объяснение проблемы.

---

## ✅ **2. Как отобразить ошибку в Symfony**

Если используете **Symfony**, можете выбрасывать `HttpException` в контроллере:

```php
use Symfony\Component\HttpKernel\Exception\HttpException;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpFoundation\Request;

public function index(Request $request): JsonResponse
{
    // Проверка заголовка X-Api-Version
    if (!$request->headers->has('X-Api-Version')) {
        throw new HttpException(400, json_encode([
            'errors' => [
                [
                    'status' => '400',
                    'source' => ['header' => 'X-Api-Version'],
                    'title' => 'Missing Required Header',
                    'detail' => 'The X-Api-Version header is required.'
                ]
            ]
        ]));
    }

    // Проверка query-параметра page[number]
    $page = $request->query->get('page[number]');
    if ($page !== null && (!ctype_digit($page) || (int)$page <= 0)) {
        throw new HttpException(400, json_encode([
            'errors' => [
                [
                    'status' => '400',
                    'source' => ['parameter' => 'page[number]'],
                    'title' => 'Invalid Query Parameter',
                    'detail' => 'The page number must be a positive integer.'
                ]
            ]
        ]));
    }

    return new JsonResponse(['data' => []]); // Данные ответа
}
```

---

## ✅ **3. Настройка обработки ошибок в Symfony**

Чтобы `HttpException` автоматически возвращались в `JSON:API`-формате, настройте **Event Listener**:

```php
namespace App\EventListener;

use Symfony\Component\HttpKernel\Event\ExceptionEvent;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpKernel\Exception\HttpException;
use Symfony\Component\HttpKernel\Exception\HttpExceptionInterface;

class JsonApiExceptionListener
{
    public function onKernelException(ExceptionEvent $event): void
    {
        $exception = $event->getThrowable();

        if ($exception instanceof HttpExceptionInterface) {
            $response = new JsonResponse(
                json_decode($exception->getMessage(), true),
                $exception->getStatusCode()
            );
            $event->setResponse($response);
        }
    }
}
```

Зарегистрируйте его в `services.yaml`:

```yaml
services:
    App\EventListener\JsonApiExceptionListener:
        tags:
            - { name: kernel.event_listener, event: kernel.exception }
```

---

## 🚀 **Вывод**

Теперь: ✔ Если в заголовках или query-параметрах ошибка, Symfony автоматически вернёт `JSON:API`-ошибку.  
✔ Все ошибки будут в корректном формате.  
✔ Нет необходимости вручную формировать JSON-ответы в контроллерах.