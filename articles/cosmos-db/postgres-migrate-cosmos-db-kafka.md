---
title: Migrowanie danych z PostgreSQL do konta interfejs API Cassandra Azure Cosmos DB przy użyciu Apache Kafka
description: Dowiedz się, jak za pomocą programu Kafka Connect synchronizować dane z PostgreSQL w celu Azure Cosmos DB interfejs API Cassandra w czasie rzeczywistym.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98203069"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Migrowanie danych z PostgreSQL do konta interfejs API Cassandra Azure Cosmos DB przy użyciu Apache Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Interfejs API Cassandra w Azure Cosmos DB został doskonały wybór dla obciążeń przedsiębiorstwa działających na platformie Apache Cassandra z różnych powodów, takich jak:

* **Znaczne oszczędności kosztów:** Możesz zaoszczędzić koszt w Azure Cosmos DB, który obejmuje koszt maszyn wirtualnych, przepustowość i wszelkie odpowiednie licencje firmy Oracle. Ponadto nie trzeba zarządzać centrami danych, serwerami, magazynem SSD, siecią i kosztami energii elektrycznej.

* **Lepsza skalowalność i dostępność:** Eliminuje to pojedyncze punkty awarii, lepszą skalowalność i dostępność dla aplikacji.

* **Brak nakładów związanych z zarządzaniem i monitorowaniem:** Jako w pełni zarządzana usługa w chmurze Azure Cosmos DB eliminuje obciążenie związane z zarządzaniem i monitorowaniem wyposażono ustawień.

[Kafka Connect](https://kafka.apache.org/documentation/#connect) to platforma do przesyłania strumieniowego danych między [Apache Kafka](https://kafka.apache.org/) i innymi systemami w sposób skalowalny i niezawodny. Obsługuje kilka łączników półkowych, co oznacza, że nie potrzebujesz niestandardowego kodu do integrowania systemów zewnętrznych z Apache Kafka.

W tym artykule przedstawiono sposób użycia kombinacji łączników Kafka do skonfigurowania potoku danych w celu ciągłego synchronizowania rekordów z relacyjnej bazy danych, takiej jak [PostgreSQL](https://www.postgresql.org/) , do [Azure Cosmos DB interfejs API Cassandra](cassandra-introduction.md).

## <a name="overview"></a>Omówienie

Poniżej przedstawiono ogólne omówienie przepływu końca do końca przedstawionego w tym artykule.

Dane w tabeli PostgreSQL zostaną wypchnięte do Apache Kafka przy użyciu [łącznika PostgreSQL Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html), który jest łącznikiem Kafka **Source** Connect. Wstawiania, aktualizacji lub usuwania rekordów w tabeli PostgreSQL będą przechwytywane jako `change data` zdarzenia i wysyłane do tematów Kafka. [Łącznik Apache Kafka DataStax](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) ( **Łącznik połączenia Kafka** ), stanowi drugą część potoku. Zsynchronizuje zdarzenia zmiany danych z Kafka w celu Azure Cosmos DB interfejs API Cassandra tabelach.

> [!NOTE]
> Korzystanie z określonych funkcji łącznika Apache Kafka DataStax umożliwia wypychanie danych do wielu tabel. W tym przykładzie łącznik pomoże nam utrzymać zmiany rekordów danych w dwóch Cassandra tabelach, które mogą obsługiwać różne wymagania dotyczące zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

* [Inicjowanie obsługi administracyjnej konta interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)
* [Aby sprawdzić poprawność, użyj cqlsh lub powłoki hostowanej](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 lub nowszy
* [Docker](https://www.docker.com/) (opcjonalnie)

## <a name="base-setup"></a>Konfiguracja podstawowa

### <a name="set-up-postgresql-database-if-you-havent-already"></a>Skonfiguruj bazę danych PostgreSQL, jeśli jeszcze tego nie zrobiono. 

Może to być istniejąca lokalna baza danych lub można [ją pobrać i zainstalować](https://www.postgresql.org/download/) na komputerze lokalnym. Istnieje również możliwość użycia [kontenera Docker](https://hub.docker.com/_/postgres).

Aby uruchomić kontener:

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

Połącz się z wystąpieniem usługi PostgreSQL przy użyciu [`psql`](https://www.postgresql.org/docs/current/app-psql.html) klienta:

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

Utwórz tabelę do przechowywania przykładowych informacji o zamówieniu:

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Korzystając z Azure Portal, Utwórz przestrzeń kluczy Cassandra i tabele wymagane dla aplikacji demonstracyjnej.

> [!NOTE]
> Użyj tej samej nazwy przestrzeni kluczy i tabeli co poniżej

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka Instalatora 

W tym artykule jest stosowany klaster lokalny, ale można wybrać dowolną inną opcję. [Pobierz Kafka](https://kafka.apache.org/downloads), rozpakuj go, uruchom klaster dozorcy i Kafka.

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>Łączniki Instalatora

Zainstaluj łącznik Debezium PostgreSQL i DataStax Apache Kafka. Pobierz archiwum wtyczek łącznika Debezium PostgreSQL. Na przykład aby pobrać wersję 1.3.0 łącznika (Najnowsza w momencie pisania), użyj [tego linku](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz). Pobierz łącznik DataStax Apache Kafka z [tego linku](https://downloads.datastax.com/#akc).

Rozpakuj zarówno archiwa łączników, jak i skopiuj pliki JAR do [Kafka Connect plugin. Path](https://kafka.apache.org/documentation/#connectconfigs).


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) i [DataStax](https://docs.datastax.com/en/kafka/doc/) .

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Konfigurowanie potoku połączeń i uruchamiania Kafka

### <a name="start-kafka-connect-cluster"></a>Uruchom klaster łączenia Kafka

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>Uruchom wystąpienie łącznika PostgreSQL

Zapisz konfigurację łącznika (JSON) w przykładowym pliku `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

Aby uruchomić wystąpienie łącznika PostgreSQL:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> Aby usunąć, można użyć: `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>Wstawianie danych

`orders_info`Tabela zawiera szczegółowe informacje o zamówieniach, takie jak identyfikator zamówienia, identyfikator klienta, miasto itp. Wypełnij tabelę losowymi danymi przy użyciu poniższego języka SQL.

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

Powinien zostać wstawiony 10 rekordów do tabeli. Pamiętaj, aby zaktualizować liczbę rekordów w `generate_series(1, 10)` poniższym przykładzie zgodnie z wymaganiami, aby wstawić `100` rekordy, użyj `generate_series(1, 100)`

Aby potwierdzić:

```bash
select * from retail.orders_info;
```

Sprawdź zdarzenia funkcji przechwytywania zmian danych w temacie Kafka

> [!NOTE]
> Należy zauważyć, że nazwa tematu jest taka sama jak w przypadku `myserver.retail.orders_info` [Konwencji łącznika](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

Powinny być widoczne zdarzenia zmiany danych w formacie JSON.

### <a name="start-datastax-apache-kafka-connector-instance"></a>Uruchom wystąpienie łącznika Apache Kafka DataStax

Zapisz konfigurację łącznika (JSON) w przykładowym pliku `cassandra-sink-config.json` i zaktualizuj właściwości zgodnie z danym środowiskiem.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

Aby uruchomić wystąpienie łącznika:

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Łącznik powinien znajdować się w działaniu, a końcowy potok od PostgreSQL do Azure Cosmos DB będzie działać.

### <a name="query-azure-cosmos-db"></a>Wykonywanie zapytań do bazy danych Azure Cosmos DB

Sprawdź tabele Cassandra w Azure Cosmos DB. Poniżej przedstawiono niektóre zapytania, które można wypróbować:

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

Możesz kontynuować Wstawianie większej ilości danych do PostgreSQL i upewnić się, że rekordy są zsynchronizowane z Azure Cosmos DB.

## <a name="next-steps"></a>Następne kroki

* [Integrowanie Apache Kafka i Azure Cosmos DB interfejs API Cassandra za pomocą Kafka Connect](cassandra-kafka-connect.md)
* [Integrowanie Apache Kafka nawiązywanie połączenia z usługą Azure Event Hubs (wersja zapoznawcza) z usługą Debezium na potrzeby przechwytywania zmian danych](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Migrowanie danych z programu Oracle do Azure Cosmos DB interfejs API Cassandra za pomocą Blitzz](oracle-migrate-cosmos-db-blitzz.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Oszacowanie ru/s przy użyciu artykułów Azure Cosmos DB planisty wydajności](estimate-ru-with-capacity-planner.md)

