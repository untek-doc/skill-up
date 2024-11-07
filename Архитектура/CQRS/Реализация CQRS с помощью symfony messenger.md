Реализация CQRS (Command Query Responsibility Segregation) с использованием компонента **Symfony Messenger** — это эффективный способ разделить логику чтения и записи в вашей системе. В этой архитектуре команды (команды на изменение) и запросы (запросы данных) обрабатываются разными компонентами. Symfony Messenger позволяет организовать передачу сообщений и асинхронную обработку команд через шины сообщений (message buses), что делает его хорошим выбором для реализации CQRS.

### Шаги для реализации CQRS с Symfony Messenger:

#### 1. Установите компонент Symfony Messenger

Для начала вам нужно установить Symfony Messenger, если он ещё не установлен:
```bash
composer require symfony/messenger
```
#### 2. Настройка шины сообщений

Для CQRS вам понадобятся две отдельные шины сообщений:
- Одна для **команд** (Command Bus).
- Другая для **запросов** (Query Bus).

Откройте ваш файл конфигурации `config/packages/messenger.yaml` и настройте две шины:
```yaml
framework:
    messenger:
        default_bus: command.bus

        buses:
            command.bus: ~
            query.bus: ~
```
#### 3. Создание команды (Command)

Команды предназначены для выполнения действий, которые изменяют состояние системы. Например, создание нового пользователя.

Создадим команду `CreateUserCommand`:
```php
// src/Message/Command/CreateUserCommand.php
namespace App\Message\Command;

class CreateUserCommand
{
    private string $name;
    private string $email;

    public function __construct(string $name, string $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function getEmail(): string
    {
        return $this->email;
    }
}
```
#### 4. Создание обработчика команды (Command Handler)

Обработчик команды будет отвечать за выполнение бизнес-логики, связанной с командой. Например, создание записи пользователя в базе данных.
```php
// src/MessageHandler/CreateUserCommandHandler.php
namespace App\MessageHandler;

use App\Message\Command\CreateUserCommand;
use Symfony\Component\Messenger\Handler\MessageHandlerInterface;

class CreateUserCommandHandler implements MessageHandlerInterface
{
    public function __invoke(CreateUserCommand $command)
    {
        // Ваша логика создания пользователя
        $name = $command->getName();
        $email = $command->getEmail();

        // Пример сохранения в базе данных
        // $this->entityManager->persist($user);
        // $this->entityManager->flush();

        // Логика обработки команды
    }
}
```
#### 5. Создание запроса (Query)

Запросы используются для получения данных, не изменяя состояние системы. Например, получение списка пользователей.

Создадим запрос `GetUserByIdQuery`:
```php
// src/Message/Query/GetUserByIdQuery.php
namespace App\Message\Query;

class GetUserByIdQuery
{
    private int $userId;

    public function __construct(int $userId)
    {
        $this->userId = $userId;
    }

    public function getUserId(): int
    {
        return $this->userId;
    }
}
```
#### 6. Создание обработчика запроса (Query Handler)

Обработчик запроса будет отвечать за получение и возвращение данных.
```php
// src/MessageHandler/GetUserByIdQueryHandler.php
namespace App\MessageHandler;

use App\Message\Query\GetUserByIdQuery;
use Symfony\Component\Messenger\Handler\MessageHandlerInterface;

class GetUserByIdQueryHandler implements MessageHandlerInterface
{
    public function __invoke(GetUserByIdQuery $query)
    {
        $userId = $query->getUserId();

        // Пример получения пользователя из базы данных
        // $user = $this->userRepository->find($userId);

        return $user; // Возвращаем данные о пользователе
    }
}
```
#### 7. Вызов команд и запросов в контроллере

Теперь можно вызывать команды и запросы через шины сообщений (`command.bus` для команд и `query.bus` для запросов). В контроллере можно инжектировать эти шины и использовать их.
```php
// src/Controller/UserController.php
namespace App\Controller;

use App\Message\Command\CreateUserCommand;
use App\Message\Query\GetUserByIdQuery;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\Messenger\MessageBusInterface;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class UserController extends AbstractController
{
    private MessageBusInterface $commandBus;
    private MessageBusInterface $queryBus;

    public function __construct(MessageBusInterface $commandBus, MessageBusInterface $queryBus)
    {
        $this->commandBus = $commandBus;
        $this->queryBus = $queryBus;
    }

    /**
     * @Route("/user/create", methods={"POST"})
     */
    public function createUser(): Response
    {
        // Пример создания нового пользователя
        $this->commandBus->dispatch(new CreateUserCommand('John Doe', 'john@example.com'));

        return new Response('User created', Response::HTTP_CREATED);
    }

    /**
     * @Route("/user/{id}", methods={"GET"})
     */
    public function getUserById(int $id): Response
    {
        // Получение пользователя по ID
        $user = $this->queryBus->dispatch(new GetUserByIdQuery($id));

        return $this->json($user);
    }
}
```
### Асинхронная обработка команд

Symfony Messenger позволяет обрабатывать команды асинхронно, используя очереди сообщений, например, через RabbitMQ или Redis.

1. В `messenger.yaml` укажите транспорт для асинхронной обработки:
```yaml
framework:
    messenger:
        transports:
            async: "%env(MESSENGER_TRANSPORT_DSN)%"
        
        routing:
            # Команды обрабатываются асинхронно
            'App\Message\Command\CreateUserCommand': async
```

2. Запустите воркер для обработки асинхронных задач:
```bash
php bin/console messenger:consume async
```
### Заключение

Используя Symfony Messenger, вы можете легко реализовать CQRS, разделив обработку команд и запросов. Это позволяет вам улучшить производительность системы, упрощая поддержку и развитие архитектуры. Асинхронная обработка команд также помогает масштабировать систему при увеличении нагрузки.
