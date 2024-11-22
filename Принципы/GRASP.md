### **GRASP: General Responsibility Assignment Software Patterns**

**GRASP (Общие шаблоны распределения ответственности)** — это набор из девяти принципов, которые помогают проектировать объектно-ориентированные системы, распределяя ответственность между классами и объектами. Эти принципы были впервые описаны Крэгом Ларманом (Craig Larman) в книге **"Applying UML and Patterns"**. GRASP фокусируется на создании устойчивой и понятной архитектуры за счёт правильного распределения обязанностей.

---

### **Основные принципы GRASP**

1. **Information Expert (Информационный эксперт)**  
   - **Суть:** Назначайте ответственность тому классу, который обладает необходимыми данными для выполнения задачи.  
   - **Цель:** Сокращение связности и упрощение реализации.  
   - **Пример:**  
     Если объект `Order` знает свои `OrderItems`, то он должен рассчитывать общую стоимость заказа.

   ```php
   class Order {
       private array $items;

       public function calculateTotal(): float {
           $total = 0;
           foreach ($this->items as $item) {
               $total += $item->getPrice();
           }
           return $total;
       }
   }
   ```

---

2. **Creator (Создатель)**  
   - **Суть:** Класс, который содержит или использует другие объекты, отвечает за их создание.  
   - **Цель:** Уменьшить зависимость между классами.  
   - **Пример:**  
     Класс `Order` создаёт свои `OrderItems`, потому что он их использует.

   ```php
   class Order {
       private array $items;

       public function addItem(string $name, float $price): void {
           $this->items[] = new OrderItem($name, $price);
       }
   }
   ```

---

3. **Controller (Контроллер)**  
   - **Суть:** Определяет объект, который будет обрабатывать запросы от пользовательского интерфейса.  
   - **Цель:** Отделить логику управления от интерфейса.  
   - **Пример:**  
     Контроллер в MVC-паттерне принимает входящий запрос и передаёт его соответствующим сервисам.

   ```php
   class OrderController {
       public function createOrder(array $data): Order {
           $order = new Order();
           foreach ($data['items'] as $item) {
               $order->addItem($item['name'], $item['price']);
           }
           return $order;
       }
   }
   ```

---

4. **Low Coupling (Низкая связность)**  
   - **Суть:** Минимизируйте зависимость между классами, чтобы изменения в одном классе не требовали изменения других.  
   - **Цель:** Повысить устойчивость системы.  
   - **Пример:**  
     Использование интерфейсов и зависимостей через конструктор (Dependency Injection).

   ```php
   interface PaymentProcessor {
       public function processPayment(float $amount): void;
   }

   class Order {
       private PaymentProcessor $paymentProcessor;

       public function __construct(PaymentProcessor $paymentProcessor) {
           $this->paymentProcessor = $paymentProcessor;
       }

       public function pay(): void {
           $this->paymentProcessor->processPayment($this->calculateTotal());
       }
   }
   ```

---

5. **High Cohesion (Высокая связность)**  
   - **Суть:** Разделите ответственность так, чтобы класс фокусировался на чётко определённой задаче.  
   - **Цель:** Сделать классы более понятными и удобными для сопровождения.  
   - **Пример:**  
     Класс `Order` отвечает только за обработку заказов, а класс `Inventory` — за управление запасами.

---

6. **Polymorphism (Полиморфизм)**  
   - **Суть:** Используйте полиморфизм для выбора поведения на основе типа объекта.  
   - **Цель:** Избежать сложных ветвлений (`if`/`switch`).  
   - **Пример:**  
     Разные способы оплаты обрабатываются через интерфейсы.

   ```php
   interface PaymentMethod {
       public function process(float $amount): void;
   }

   class CreditCardPayment implements PaymentMethod {
       public function process(float $amount): void {
           // Логика оплаты картой
       }
   }

   class PayPalPayment implements PaymentMethod {
       public function process(float $amount): void {
           // Логика оплаты через PayPal
       }
   }

   class Order {
       public function pay(PaymentMethod $paymentMethod): void {
           $paymentMethod->process($this->calculateTotal());
       }
   }
   ```

---

7. **Pure Fabrication (Чистая фабрикация)**  
   - **Суть:** Создавайте классы, если это улучшает проект, даже если они не представляют реальные элементы доменной области.  
   - **Цель:** Уменьшить связность и повысить повторное использование.  
   - **Пример:**  
     Создание сервиса для обработки платежей.

   ```php
   class PaymentService {
       public function processPayment(float $amount): void {
           // Логика обработки платежа
       }
   }
   ```

---

8. **Indirection (Посредничество)**  
   - **Суть:** Используйте посредников для снижения зависимости между классами.  
   - **Цель:** Упрощение взаимодействия между компонентами.  
   - **Пример:**  
     Использование репозитория для работы с базой данных.

   ```php
   class UserRepository {
       public function findById(int $id): User {
           // Достать пользователя из базы данных
       }
   }
   ```

---

9. **Protected Variations (Защита вариаций)**  
   - **Суть:** Изолируйте части системы, которые могут изменяться, используя абстракции или интерфейсы.  
   - **Цель:** Минимизировать влияние изменений.  
   - **Пример:**  
     Использование интерфейсов для взаимодействия с внешними API.

   ```php
   interface NotificationService {
       public function send(string $message): void;
   }

   class EmailNotification implements NotificationService {
       public function send(string $message): void {
           // Отправка email
       }
   }
   ```

---

### **Почему важно знать GRASP?**

GRASP помогает создавать устойчивую архитектуру и писать код, который:
- Легко поддерживать и расширять.
- Чётко разделяет обязанности между классами.
- Устойчив к изменениям требований.

Эти принципы особенно полезны в **объектно-ориентированном программировании**, но их идеи применимы и к другим стилям разработки.