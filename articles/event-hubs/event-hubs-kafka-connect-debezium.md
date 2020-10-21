---
title: Integrowanie Apache Kafka nawiązywanie połączenia z usługą Azure Event Hubs (wersja zapoznawcza) z usługą Debezium na potrzeby przechwytywania zmian danych
description: Ten artykuł zawiera informacje dotyczące korzystania z programu Debezium z usługą Azure Event Hubs dla Kafka.
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: e4bd6cdf6d3a5dc30b90abc5094202360181ae0b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318517"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>Integrowanie usługi Apache Kafka Connect Support w usłudze Azure Event Hubs (wersja zapoznawcza) z usługą Debezium na potrzeby przechwytywania zmian danych

**Przechwytywanie zmian (Reporting Data Capture)** to technika służąca do śledzenia zmian na poziomie wierszy w tabelach bazy danych w odpowiedzi na tworzenie, aktualizowanie i usuwanie operacji. [Debezium](https://debezium.io/) to platforma rozproszona, która kompiluje się na podstawie funkcji przechwytywania zmian danych dostępnych w różnych bazach danych (np. [dekodowanie logiczne w PostgreSQL](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)). Udostępnia zestaw [łączników połączeń Kafka](https://debezium.io/documentation/reference/1.2/connectors/index.html) , które wybierają zmiany na poziomie wierszy w tabelach bazy danych i konwertują je na strumienie zdarzeń, które następnie są wysyłane do [Apache Kafka](https://kafka.apache.org/).

Ten samouczek przeprowadzi Cię przez proces konfigurowania systemu opartego na funkcji przechwytywania zmian danych na platformie Azure przy użyciu [usługi azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (dla Kafka), [usługi Azure DB dla PostgreSQL](../postgresql/overview.md) i Debezium. Użycie [łącznika Debezium PostgreSQL](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) do przesyłania strumieniowego modyfikacji bazy danych z PostgreSQL do Kafka tematów na platformie Azure Event Hubs

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Instalowanie i Konfigurowanie Azure Database for PostgreSQL
> * Konfigurowanie i uruchamianie łącznika Kafka Connect with Debezium PostgreSQL
> * Przechwytywanie zmian danych testowych
> * Obowiązkowe Korzystanie z zdarzeń zmiany danych za pomocą `FileStreamSink` łącznika

## <a name="pre-requisites"></a>Wymagania wstępne
Do wykonania tych czynności w programie wymagane są:

- Subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Linux/MacOS
- Platforma Kafka (wersja 1.1.1, Scala w wersji 2.11), dostępna na stronie [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Przeczytaj artykuł z wprowadzeniem [Usługa Event Hubs dla platformy Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Pobierz parametry połączenia usługi Event Hubs i w pełni kwalifikowaną nazwę domeny (FQDN) w celu późniejszego użycia. Aby uzyskać instrukcje, zobacz [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Pobieranie parametrów połączenia usługi Event Hubs). 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Instalowanie i Konfigurowanie Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md) jest usługą relacyjnej bazy danych opartą na wersji społecznościowej aparatu bazy danych PostgreSQL typu open source, która jest dostępna w dwóch opcjach wdrażania: pojedynczy serwer i funkcja do skalowania (Citus). [Postępuj zgodnie z poniższymi instrukcjami](../postgresql/quickstart-create-server-database-portal.md) , aby utworzyć serwer Azure Database for PostgreSQL przy użyciu Azure Portal. 

## <a name="setup-and-run-kafka-connect"></a>Konfigurowanie i uruchamianie programu Kafka Connect
Ta sekcja będzie obejmować następujące tematy:

- Instalacja łącznika Debezium
- Konfigurowanie Kafka Connect dla Event Hubs
- Uruchom Kafka Połącz klaster z łącznikiem Debezium

### <a name="download-and-setup-debezium-connector"></a>Pobierz i skonfiguruj łącznik Debezium
Aby pobrać i skonfigurować łącznik, postępuj zgodnie z najnowszymi instrukcjami w [dokumentacji Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) .

- Pobierz archiwum wtyczek łącznika. Na przykład aby pobrać wersję `1.2.0` łącznika, użyj tego linku — https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- Wyodrębnij pliki JAR i skopiuj je do [Kafka Connect plugin. Path](https://kafka.apache.org/documentation/#connectconfigs).


### <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurowanie narzędzia Kafka Connect dla usługi Event Hubs
Przekierowywanie przepływności narzędzia Kafka Connect z platformy Kafka do usługi Event Hubs wymaga minimalnej rekonfiguracji.  W poniższym przykładowym pliku `connect-distributed.properties` pokazano, jak skonfigurować narzędzie Connect do uwierzytelnienia i komunikowania się z punktem końcowym platformy Kafka w usłudze Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

### <a name="run-kafka-connect"></a>Uruchamianie narzędzia Kafka Connect
W tym kroku proces roboczy narzędzia Kafka Connect został uruchomiony lokalnie w trybie rozproszonym przy użyciu usługi Event Hubs w celu zachowania stanu klastra.

1. Zapisz powyższy plik `connect-distributed.properties` lokalnie.  Zamień wszystkie wartości w nawiasach klamrowych.
2. Przejdź do lokalizacji platformy Kafka w maszynie.
3. Uruchom `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` i poczekaj na uruchomienie klastra.

> [!NOTE]
> Kafka Connect używa interfejsu API Kafka AdminClient do automatycznego tworzenia tematów z zalecanymi konfiguracjami, w tym kompaktowania. Z szybkiego sprawdzenia przestrzeni nazw w witrynie Azure Portal wynika, że tematy wewnętrzne procesu roboczego narzędzia Connect zostały utworzone automatycznie.
>
> Tematy wewnętrzne Kafka Connect **muszą używać kompaktowania**.  Zespół Event Hubs nie jest odpowiedzialny za naprawianie nieprawidłowych konfiguracji, jeśli wewnętrzne tematy dotyczące połączenia zostały niepoprawnie skonfigurowane.

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Konfigurowanie i uruchamianie łącznika źródła Debezium PostgreSQL

Utwórz plik konfiguracji ( `pg-source-connector.json` ) dla łącznika źródła PostgreSQL — Zastąp wartości zgodnie z wystąpieniem usługi Azure PostgreSQL.

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` atrybut jest logiczną nazwą, która identyfikuje i udostępnia przestrzeń nazw dla monitorowanego serwera bazy danych PostgreSQL lub klastra. Aby uzyskać szczegółowe informacje, zobacz [dokumentację usługi Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

Aby utworzyć wystąpienie łącznika, użyj punktu końcowego interfejsu API REST Kafka Connect:

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

Aby sprawdzić stan łącznika:

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>Przechwytywanie zmian danych testowych
Aby wyświetlić działanie funkcji przechwytywania zmian danych, należy utworzyć/zaktualizować/usunąć rekordy w bazie danych Azure PostgreSQL.

Zacznij od nawiązywania połączenia z bazą danych usługi Azure PostgreSQL (Poniższy przykład używa [PSQL](https://www.postgresql.org/docs/12/app-psql.html))

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**Tworzenie tabeli i wstawianie rekordów**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

Łącznik powinien teraz znajdować się w akcji i wysyłać zdarzenia zmiany danych do Event Hubs tematu z następującą na, e `my-server.public.todos` , przy założeniu, że masz `my-server` jako wartość dla `database.server.name` i `public.todos` to tabela, której zmiany są śledzone (zgodnie z `table.whitelist` konfiguracją).

**Sprawdź Event Hubs tematu**

Introspectmy zawartość tematu, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami. W poniższym przykładzie użyto [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) , ale możesz również [utworzyć odbiorcę przy użyciu dowolnej z opcji wymienionych w tym miejscu](apache-kafka-developer-guide.md)

Utwórz plik o nazwie `kafkacat.conf` z następującą zawartością:

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> Aktualizowanie `metadata.broker.list` i `sasl.password` atrybuty w `kafkacat.conf` jak Event Hubs informacji. 

W innym terminalu uruchom odbiorcę:

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

Powinny być widoczne ładunki JSON reprezentujące zdarzenia zmiany danych wygenerowane w PostgreSQL w odpowiedzi na wiersze, które zostały właśnie dodane do `todos` tabeli. Oto fragment kodu ładunku:


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

Zdarzenie składa się z programu `payload` wraz z jego `schema` (pominięty dla zwięzłości). W `payload` sekcji Zwróć uwagę na to, jak jest reprezentowana operacja tworzenia ( `"op": "c"` ) — `"before": null` oznacza, że był to nowo utworzony `INSERT` wiersz, `after` zawiera wartości kolumn w wierszu, `source` zawiera metadane wystąpienia PostgreSQL, z którego można pobrać to zdarzenie itp.

Możesz wypróbować te same operacje aktualizowania lub usuwania, a także Introspect zdarzenia zmiany danych. Na przykład, aby zaktualizować stan zadania dla `configure and install connector` (przy założeniu, że `id` jest to `3` ):

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>Obowiązkowe Zainstaluj łącznik FileStreamSink
Teraz, gdy wszystkie `todos` zmiany tabeli są przechwytywane w temacie Event Hubs, użyjemy łącznika FileStreamSink (który jest domyślnie dostępny w programie Kafka Connect), aby korzystać z tych zdarzeń.

Utwórz plik konfiguracji ( `file-sink-connector.json` ) dla łącznika — Zastąp `file` atrybut jako system plików

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

Aby utworzyć łącznik i sprawdzić jego stan:

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

Wstawianie/aktualizowanie/usuwanie rekordów bazy danych i monitorowanie rekordów w skonfigurowanym wyjściowym pliku ujścia:

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>Czyszczenie
Narzędzie Kafka Connect tworzy tematy centrum zdarzeń do przechowywania konfiguracji, przesunięć i stanu — te dane są trwale przechowywane nawet po wyłączeniu klastra narzędzia Connect. Jeśli zależy Ci na takiej trwałości, zaleca się usunięcie tych tematów. Możesz również usunąć `my-server.public.todos` centrum zdarzeń, które zostały utworzone w ramach tego przewodnika.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Event Hubs Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)