В PHP также можно реализовать шаблон **Mediator** для упрощения взаимодействия между компонентами. Хотя в PHP нет стандартной библиотеки вроде MediatR в .NET, подобный функционал можно создать вручную, используя паттерн **Медиатор** с возможностью добавления *PipelineBehavior*, чтобы управлять процессами и обработчиками запросов.

### Основные компоненты Mediator и PipelineBehavior в PHP

#### Mediator

Mediator в PHP выступает как центральный компонент, который принимает запросы и передаёт их на обработку соответствующим обработчикам. Его задача — координировать взаимодействие между отправителями и получателями запросов.

1. **Запросы и Команды** — классы, представляющие различные действия.
2. **Обработчики** — классы, реализующие логику обработки запросов.
3. **Медиатор** — класс, который находит и вызывает нужный обработчик для конкретного запроса.

#### PipelineBehavior

**PipelineBehavior** можно добавить для выполнения действий до или после обработки запроса. Например, такие действия, как валидация, логирование, кэширование и обработка ошибок, можно оформить как промежуточные шаги, через которые проходит запрос.

### Пример реализации Mediator с PipelineBehavior на PHP

#### 1. Создаём интерфейсы для запросов и обработчиков

```php
// Интерфейс для запросов
interface Request {}

// Интерфейс для обработчика
interface RequestHandler
{
    public function handle(Request $request);
}
```

#### 2. Пример команды и обработчика

Команда, представляющая действие (например, создание пользователя), и обработчик для её выполнения.

```php
// Команда для создания пользователя
class CreateUserCommand implements Request
{
    public $name;
    public function __construct($name)
    {
        $this->name = $name;
    }
}

// Обработчик для команды
class CreateUserHandler implements RequestHandler
{
    public function handle(Request $request)
    {
        echo "User '{$request->name}' created!\n";
    }
}
```

#### 3. Реализация класса Mediator

Mediator хранит список обработчиков и передаёт запросы соответствующему обработчику.

```php
class Mediator
{
    private $handlers = [];

    // Регистрация обработчика для запроса
    public function registerHandler($requestType, RequestHandler $handler)
    {
        $this->handlers[$requestType] = $handler;
    }

    // Метод для передачи запроса на обработку
    public function send(Request $request)
    {
        $requestType = get_class($request);
        if (isset($this->handlers[$requestType])) {
            return $this->handlers[$requestType]->handle($request);
        }
        throw new Exception("No handler found for request type {$requestType}");
    }
}
```

#### 4. Добавление PipelineBehavior

PipelineBehavior реализуется в виде декораторов, которые могут выполнять действия до и после обработки запроса. Например, добавим логирование.

```php
class LoggingBehavior implements RequestHandler
{
    private $next;

    public function __construct(RequestHandler $next)
    {
        $this->next = $next;
    }

    public function handle(Request $request)
    {
        echo "Logging: Handling " . get_class($request) . "\n";
        $response = $this->next->handle($request);
        echo "Logging: Finished " . get_class($request) . "\n";
        return $response;
    }
}
```

#### 5. Использование Mediator с PipelineBehavior

Создадим медиатор, зарегистрируем обработчики и добавим `LoggingBehavior` в качестве промежуточного шага.

```php
// Создаём Mediator и регистрируем обработчики
$mediator = new Mediator();
$createUserHandler = new CreateUserHandler();

// Оборачиваем обработчик в LoggingBehavior
$loggingBehavior = new LoggingBehavior($createUserHandler);

// Регистрируем команду CreateUserCommand с обработчиком через logging behavior
$mediator->registerHandler(CreateUserCommand::class, $loggingBehavior);

// Отправляем запрос
$mediator->send(new CreateUserCommand("Alice"));
```

Вывод:
```plaintext
Logging: Handling CreateUserCommand
User 'Alice' created!
Logging: Finished CreateUserCommand
```

### Заключение

Эта реализация позволяет:
- **Чётко разделить ответственность** между запросами, командами и обработчиками.
- **Добавлять PipelineBehavior** для выполнения действий до и после основного обработчика.
- **Расширять логику** без изменения основных обработчиков, добавляя декораторы для поведения, как валидация, логирование или кэширование.

Хотя в PHP нет встроенной поддержки Mediator, реализация этого паттерна вручную, как в примере выше, помогает добиться более чистой и структурированной архитектуры.