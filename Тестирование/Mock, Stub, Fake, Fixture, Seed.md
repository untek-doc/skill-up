Вот примеры использования **Mock**, **Stub**, **Fake**, **Fixture** и **Seed** на языке PHP. Для моков и стабов в PHP часто используют библиотеку **PHPUnit**, которая предоставляет встроенные механизмы для работы с тестовыми двойниками.

### 1. **Mock** (Мок-объект)
Мок-объект имитирует взаимодействие с реальными объектами и проверяет, вызывались ли нужные методы с правильными параметрами.

```php
use PHPUnit\Framework\TestCase;

class NotificationServiceTest extends TestCase {
    public function testSendNotification() {
        // Создаём мок-объект для сервиса отправки уведомлений
        $notificationService = $this->createMock(NotificationService::class);

        // Устанавливаем ожидания, что метод send будет вызван с определёнными параметрами
        $notificationService->expects($this->once())
            ->method('send')
            ->with('test@example.com', 'Message');

        // Вызываем метод, который должен использовать мок-объект
        $notificationService->send('test@example.com', 'Message');
    }
}
```

### 2. **Stub** (Заглушка)
Заглушка возвращает предопределённые данные при вызове методов, не проверяя, как и сколько раз она была вызвана.

```php
use PHPUnit\Framework\TestCase;

class ApiStub {
    public function getData() {
        return ['key' => 'value']; // Возвращаем заранее заданные данные
    }
}

class ApiServiceTest extends TestCase {
    public function testGetData() {
        $apiStub = new ApiStub();

        // Используем заглушку в тестируемом методе
        $result = $apiStub->getData();
        $this->assertEquals(['key' => 'value'], $result);
    }
}
```

### 3. **Fake** (Фейк)
Фейковый объект заменяет реальный компонент, но с упрощённой логикой. Например, фейковая база данных может сохранять данные в памяти, а не в реальной базе.

```php
class FakeDatabase {
    private $storage = [];

    public function insert($key, $value) {
        $this->storage[$key] = $value;
    }

    public function get($key) {
        return $this->storage[$key] ?? null;
    }
}

class DatabaseTest extends TestCase {
    public function testInsertAndGet() {
        $db = new FakeDatabase();
        $db->insert('user_1', 'Alice');

        // Проверяем, что данные правильно сохраняются и извлекаются
        $this->assertEquals('Alice', $db->get('user_1'));
    }
}
```

### 4. **Fixture** (Фикстура)
Фикстура — это заранее подготовленные данные или состояние, которые устанавливаются перед запуском тестов. В PHPUnit фикстуры можно организовать с помощью метода `setUp()`.

```php
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase {
    private $db;

    // Фикстура для подготовки данных
    protected function setUp(): void {
        $this->db = new FakeDatabase();
        $this->db->insert('user_1', 'Alice');
    }

    public function testGetUser() {
        // Тестируем, что данные корректно загружены фикстурой
        $this->assertEquals('Alice', $this->db->get('user_1'));
    }
}
```

### 5. **Seed** (Сид)
Seed используется для предсказуемой инициализации данных. Например, это может быть предопределённый набор данных, который загружается в тестовую базу.

```php
class DatabaseSeeder {
    public static function seed(FakeDatabase $db) {
        // Заполняем базу данных предсказуемыми данными
        $db->insert('user_1', 'Alice');
        $db->insert('user_2', 'Bob');
    }
}

class SeederTest extends TestCase {
    public function testSeedDatabase() {
        $db = new FakeDatabase();

        // Используем сидер для начального заполнения базы данных
        DatabaseSeeder::seed($db);

        // Проверяем, что сид добавил нужные данные
        $this->assertEquals('Alice', $db->get('user_1'));
        $this->assertEquals('Bob', $db->get('user_2'));
    }
}
```

### Основные моменты:
- **Mock** используется для имитации объектов и проверки вызовов методов.
- **Stub** возвращает предопределённые данные без проверки взаимодействий.
- **Fake** реализует упрощённую логику реального компонента, такого как база данных.
- **Fixture** подготавливает данные и состояние перед тестами.
- **Seed** инициализирует данные для тестов с предсказуемыми значениями.

Эти примеры показывают, как создавать и использовать различные тестовые двойники и данные для улучшения тестирования кода в PHP.