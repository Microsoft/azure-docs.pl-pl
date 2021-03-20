---
title: Integrowanie Apache Kafka i Azure Cosmos DB interfejs API Cassandra za pomocą Kafka Connect
description: Dowiedz się, jak pozyskiwać dane z usługi Kafka do interfejs API Cassandra Azure Cosmos DB przy użyciu łącznika DataStax Apache Kafka
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803633"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Pozyskiwanie danych z Apache Kafka do Azure Cosmos DB interfejs API Cassandra przy użyciu połączenia Kafka
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Istniejące aplikacje Cassandra mogą łatwo współdziałać z [interfejs API Cassandra Azure Cosmos DB](cassandra-introduction.md) ze względu na jego [zgodność ze sterownikiem CQLv4](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). Ta funkcja umożliwia integrację z platformami przesyłania strumieniowego, takimi jak [Apache Kafka](https://kafka.apache.org/) i przenoszenie danych do Azure Cosmos DB.

Dane w Apache Kafka (tematy) są użyteczne tylko wtedy, gdy są używane przez inne aplikacje lub pozyskiwane w innych systemach. Możliwe jest skompilowanie rozwiązania przy użyciu interfejsów API [producent/odbiorca Kafka](https://kafka.apache.org/documentation/#api) [przy użyciu wybranego języka i zestawu SDK klienta](https://cwiki.apache.org/confluence/display/KAFKA/Clients). Kafka Connect oferuje alternatywne rozwiązanie. Jest to platforma do przesyłania strumieniowego danych między Apache Kafka i innymi systemami w sposób skalowalny i niezawodny. Ponieważ program Kafka Connect obsługuje łączniki półek, które zawierają Cassandra, nie trzeba pisać kodu niestandardowego, aby zintegrować Kafka z Azure Cosmos DB interfejs API Cassandra. 

W tym artykule będziemy używać [łącznika Apache Kafka](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html)typu "open source", który działa w oparciu o program Kafka Connect Framework w celu pozyskiwania rekordów z tematu Kafka w wierszach z co najmniej jedną tabelą Cassandra. W przykładzie przedstawiono konfigurację wielokrotnego użytku przy użyciu Docker Compose. Jest to dość wygodne, ponieważ umożliwia uruchamianie wszystkich wymaganych składników lokalnie za pomocą jednego polecenia. Te składniki to: Kafka, dozorcy, Kafka Connect Worker i Przykładowa aplikacja generatora danych.

Poniżej przedstawiono podział składników i ich definicji usług — można odwołać się do kompletnego `docker-compose` pliku [w repozytorium GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml).

- Kafka i dozorcy używają obrazów [debezium](https://hub.docker.com/r/debezium/kafka/) .
- Aby można było uruchomić program jako kontener platformy Docker, łącznik DataStax Apache Kafka jest rozszerzania na istniejącym obrazie platformy Docker — [debezium/Connect-Base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2). Ten obraz obejmuje instalację Kafka i jej bibliotek połączeń Kafka, dzięki czemu można dodawać łączniki niestandardowe. Możesz odwołać się do [pliku dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile).
- `data-generator`Dane o losowo generowanych materiałach (JSON) w `weather-data` temacie Kafka. Możesz odwołać się do kodu i `Dockerfile` w [repozytorium GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)

## <a name="prerequisites"></a>Wymagania wstępne

* [Inicjowanie obsługi administracyjnej konta interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Aby sprawdzić poprawność, użyj cqlsh lub powłoki hostowanej](cassandra-support.md#hosted-cql-shell-preview)

* Zainstaluj [platformę Docker](https://docs.docker.com/get-docker/) i [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>Utwórz przestrzeń kluczy, tabele i uruchom potok integracji

Korzystając z Azure Portal, Utwórz przestrzeń kluczy Cassandra i tabele wymagane dla aplikacji demonstracyjnej.

> [!NOTE]
> Użyj tej samej nazwy przestrzeni kluczy i tabeli co poniżej

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

Klonuj repozytorium GitHub:

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

Uruchom wszystkie usługi:

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> Pobieranie i uruchamianie kontenerów może potrwać trochę czasu. jest to tylko jeden proces.

Aby sprawdzić, czy wszystkie kontenery zostały rozpoczęte:

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

Aplikacja generatora danych rozpocznie pompowanie danych w `weather-data` temacie w Kafka. Możesz również wykonać szybkie Sanity sprawdzanie, aby potwierdzić. Wgląd w kontener platformy Docker z uruchomionym pracownikiem programu Kafka Connect:


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

Po pobraniu do powłoki kontenerów wystarczy uruchomić proces Kafkaego użytkownika konsoli programu, a następnie zobaczyć dane pogodowe (w formacie JSON) przepływające w.

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Konfiguracja łącznika ujścia Cassandra

Skopiuj poniżej zawartość JSON do pliku (możesz ją nazwać `cassandra-sink-config.json` ). Należy ją zaktualizować zgodnie z konfiguracją, a pozostała część tej sekcji zawiera wskazówki dotyczące tego tematu.

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

Poniżej znajduje się podsumowanie atrybutów:

**Łączność podstawowa**

- `contactPoints`: wprowadź punkt kontaktu dla Cosmos DB Cassandra
- `loadBalancing.localDc`: wprowadź region dla konta Cosmos DB, np. Azja Południowo-Wschodnia
- `auth.username`: Wprowadź nazwę użytkownika
- `auth.password`: wprowadź hasło
- `port`: wprowadź wartość portu (to `10350` nie `9042` . pozostaw je w postaci, w jakiej jest)

**Konfiguracja protokołu SSL**

Azure Cosmos DB wymusza [bezpieczną łączność za pośrednictwem protokołu SSL](database-security.md) , a łącznik połączenia Kafka obsługuje również protokół SSL.

- `ssl.keystore.path`: ścieżka do magazynu kluczy JDK w kontenerze- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`: JDK — hasło magazynu kluczy (domyślnie)
- `ssl.hostnameValidation`: Zmienimy walidację nazwy hosta węzła
- `ssl.provider`: `JDK` jest używany jako dostawca SSL

**Parametry ogólne**

- `key.converter`: Używamy konwertera ciągów `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`: ponieważ dane w Kafka są w formacie JSON, korzystamy z `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`: Ze względu na to, że naszym ładunkiem JSON nie jest skojarzony schemat (na potrzeby aplikacji demonstracyjnej), musimy wydać polecenie Kafka Connect, aby nie szukać schematu przez ustawienie tego atrybutu na `false` . To nie spowoduje błędów.

### <a name="install-the-connector"></a>Instalowanie łącznika

Zainstaluj łącznik przy użyciu punktu końcowego usługi Kafka Connect:

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

Aby sprawdzić stan:

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

Jeśli wszystko przebiegnie prawidłowo, łącznik powinien zacząć natkanić swój Magic. Powinien on być uwierzytelniany w celu Azure Cosmos DB i rozpoczynania pozyskiwania danych z tematu Kafka ( `weather-data` ) w tabelach Cassandra — `weather.data_by_state` i `weather.data_by_station`

Teraz można wykonywać zapytania dotyczące danych w tabelach. Przejdź do Azure Portal, aby wyświetlić hostowaną powłokę CQL dla konta Azure Cosmos DB.

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="Otwórz CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>Wykonywanie zapytań dotyczących danych z Azure Cosmos DB

Sprawdź `data_by_state` tabele i `data_by_station` . Oto kilka przykładowych zapytań, które umożliwiają rozpoczęcie pracy:

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md) 
* [Najlepsze rozwiązania dotyczące kluczy partycji](partitioning-overview.md#choose-partitionkey)
* [Oszacowanie ru/s przy użyciu artykułów Azure Cosmos DB planisty wydajności](estimate-ru-with-capacity-planner.md)
