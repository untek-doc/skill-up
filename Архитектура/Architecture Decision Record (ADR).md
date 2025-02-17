**Architecture Decision Record (ADR)** — это документ, который фиксирует архитектурные решения, принятые при разработке проекта, а также их обоснование и последствия. ADR помогает команде понять, почему были выбраны определённые технологии, паттерны и подходы, какие варианты рассматривались и какие компромиссы были сделаны.

### Зачем нужен ADR?

ADR полезен для:
- **Прозрачности и последовательности**. Все члены команды, включая новые разработчиков, могут понять, почему проект устроен именно так.
- **Истории изменений**. ADR позволяет отслеживать изменения в архитектурных решениях по мере развития проекта.
- **Минимизации дублирования обсуждений**. С фиксированными решениями у команды не возникает необходимости пересматривать одни и те же вопросы.
- **Принятия осознанных решений**. Убедившись в наличии документации, команда вынуждена анализировать решения и обоснования более детально.

### Структура ADR

Обычно каждый ADR — это короткий документ, охватывающий одно решение. Структура документа может быть такой:

1. **Заголовок**: краткое название решения.
2. **Статус**: статус решения, например, "Предложено", "Принято", "Отклонено", "Устарело".
3. **Контекст**: описание проблемы, которую необходимо решить, с пояснением, почему это важно.
4. **Решение**: описание выбранного решения, включая основные принципы и краткое изложение архитектурного паттерна или технологии.
5. **Альтернативы**: краткое описание других рассматриваемых вариантов и объяснение, почему они были отвергнуты.
6. **Последствия**: положительные и отрицательные последствия решения. Включает возможные риски и компромиссы.
7. **Дата**: дата принятия решения.

### Пример ADR

```markdown
# ADR #1: Использование REST API для обмена данными между сервисами

## Статус
Принято (дата: 2023-09-12)

## Контекст
Проект состоит из нескольких микросервисов, которые должны обмениваться данными. Необходимо выбрать протокол для коммуникации между сервисами.

## Решение
Использовать REST API поверх HTTP для взаимодействия между микросервисами.

## Альтернативы
1. **GraphQL**: Было отклонено, так как проект не требует сложных запросов данных. REST проще в реализации и поддержке.
2. **gRPC**: Было отклонено из-за дополнительной сложности в инфраструктуре и необходимости поддерживать бинарный формат, что затруднит дебаггинг.

## Последствия
### Положительные
- REST API широко используется, поэтому команда знакома с подходом и сможет быстро внедрить решение.
- Легкость интеграции с уже существующими системами и клиентами.

### Отрицательные
- Ограничения REST API могут затруднить добавление сложных запросов в будущем, что потребует написания дополнительных эндпоинтов.
- Использование HTTP увеличивает накладные расходы по сравнению с бинарными протоколами, такими как gRPC.
```

### Когда создавать ADR?

ADR полезно создавать при:
- Выборе или смене архитектурного подхода.
- Принятии критически важных технических решений.
- Выборе технологий и фреймворков.
- Изменениях, которые могут затронуть несколько команд или модулей.

### Инструменты для ADR

ADR-документы можно хранить в репозитории проекта (например, в папке `docs/adr/`) или использовать специализированные инструменты и форматы, такие как Markdown, текстовые файлы, или даже поддерживаемые GitHub Wiki.

### Принципы успешного ADR

- **Краткость**: ADR должен быть понятен без излишней детализации.
- **Актуальность**: регулярно обновляйте статус решений (например, если что-то устарело).
- **Целенаправленность**: каждый ADR документирует одно архитектурное решение, чтобы избегать путаницы.

ADR помогает сохранить структурированный и последовательный подход к разработке, делая архитектурные решения понятными и прозрачными для команды.