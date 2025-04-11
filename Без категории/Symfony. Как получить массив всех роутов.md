В Symfony получить массив всех роутов можно несколькими способами — вот основные:

---

### ✅ **Через `RouterInterface` (в коде PHP)**

```php
use Symfony\Component\Routing\RouterInterface;

class YourService
{
    public function __construct(private RouterInterface $router) {}

    public function getRoutes(): array
    {
        $routeCollection = $this->router->getRouteCollection();
        $routes = [];

        foreach ($routeCollection as $name => $route) {
            $routes[$name] = [
                'path' => $route->getPath(),
                'methods' => $route->getMethods(),
                'defaults' => $route->getDefaults(),
            ];
        }

        return $routes;
    }
}
```

---

### 🛠️ **Через консоль Symfony**

Если просто посмотреть список маршрутов:

```bash
php bin/console debug:router
```

Если нужен вывод в формате JSON/CSV для дальнейшей обработки:

```bash
php bin/console debug:router --format=json
```

---

### 📦 **Через Twig (например, отладка)**

Если хочешь получить доступ к маршрутам в Twig (например, для вывода всех маршрутов — обычно в debug-режиме):

```twig
{% for name, route in app.router.getRouteCollection() %}
    {{ name }}: {{ route.getPath() }}<br>
{% endfor %}
```

Но это работает **только при включённом `app.debug` и если доступен `router` в Twig** (нужна настройка).

---

Если у тебя есть конкретная цель — например, экспорт всех роутов, фильтрация по префиксу, или создание документации — могу подсказать более точное решение.