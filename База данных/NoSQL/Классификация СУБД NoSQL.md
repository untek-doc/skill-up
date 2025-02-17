Вот список популярных систем управления базами данных (СУБД) NoSQL, классифицированных по их типам:

### 1. Документные базы данных
- **MongoDB**: Одна из самых популярных документных СУБД, использующая BSON (Binary JSON) для хранения данных.
- **CouchDB**: Система, которая позволяет работать с документами JSON и поддерживает распределенные репликации.
- **RavenDB**: Документная база данных с поддержкой ACID-транзакций и встроенным поиском.

### 2. Ключ-значение базы данных
- **Redis**: Высокопроизводительная хранилище данных в памяти, поддерживающее структуры данных, такие как строки, списки и множества.
- **DynamoDB**: Управляемая ключ-значение база данных от Amazon, обеспечивающая масштабируемость и производительность.
- **Riak**: Распределенная ключ-значение база данных, оптимизированная для высокой доступности.

### 3. Колонковые базы данных
- **Apache Cassandra**: Распределенная колонковая база данных, обеспечивающая высокую доступность и отказоустойчивость.
- **HBase**: База данных, работающая на основе Hadoop, поддерживающая большие объемы данных и колоночную модель хранения.
- **ScyllaDB**: Высокопроизводительная колонковая база данных, совместимая с Apache Cassandra, написанная на C++.

### 4. Графовые базы данных
- **Neo4j**: Одна из самых популярных графовых СУБД, использующая свой язык запросов Cypher для работы с графами.
- **Amazon Neptune**: Управляемая графовая база данных от Amazon, поддерживающая как модели RDF, так и Property Graph.
- **ArangoDB**: Многофункциональная база данных, которая поддерживает документные, графовые и ключ-значение модели.

### 5. Мульти-модельные базы данных
- **Couchbase**: Мульти-модельная база данных, поддерживающая как документное, так и ключ-значение хранилища.
- **OrientDB**: СУБД, поддерживающая документную, графовую и объектную модели.
- **MarkLogic**: Платформа для управления документами и семантическими данными.

### 6. Специальные NoSQL базы данных
- **TimescaleDB**: База данных для временных рядов, основанная на PostgreSQL, которая обеспечивает мощные возможности работы с временными данными.
- **InfluxDB**: Система управления временными рядами, специально оптимизированная для обработки данных с временными метками.
- **ClickHouse**: Колонковая аналитическая база данных, ориентированная на выполнение сложных запросов с высокой производительностью.

### Заключение
Существует множество различных СУБД NoSQL, каждая из которых имеет свои особенности и лучшие практики применения. Выбор конкретной системы должен зависеть от требований вашего проекта, включая объем данных, требования к производительности, структуре данных и необходимости в масштабировании.