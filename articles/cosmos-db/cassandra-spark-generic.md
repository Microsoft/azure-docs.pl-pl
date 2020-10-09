---
title: Praca z Azure Cosmos DB interfejs API Cassandra z platformy Spark
description: Ten artykuł jest główną stroną Cosmos DB interfejs API Cassandra integrację z platformą Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: ffe9167bb155826eea3a1e7994469d378e5925fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260495"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Łączenie z interfejsem API Cassandra usługi Azure Cosmos DB za pomocą platformy Spark

Ten artykuł jest jednym z serii artykułów na Azure Cosmos DB interfejs API Cassandra integrację z platformą Spark. Artykuły obejmują operacje łączności i języka definicji danych (DDL), operacje języka Basic Data-Language (DML) i zaawansowane Azure Cosmos DB interfejs API Cassandra integrację z platformą Spark. 

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainicjuj obsługę konta interfejs API Cassandra Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Zainicjuj wybór środowiska Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Inne].

## <a name="dependencies-for-connectivity"></a>Zależności dotyczące łączności
* **Łącznik platformy Spark dla Cassandra:** Łącznik platformy Spark jest używany do nawiązywania połączenia z interfejs API Cassandra Azure Cosmos DB.  Zidentyfikuj i użyj wersji łącznika znajdującego się w [Maven centrali]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , która jest zgodna z wersjami platformy Spark i Scala w środowisku systemu.

* **Azure Cosmos DB bibliotekę pomocnika dla interfejs API Cassandra:** Oprócz łącznika Spark potrzebna jest inna Biblioteka o nazwie [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) z Azure Cosmos DB. Ta biblioteka zawiera niestandardową fabrykę połączeń i klasy zasad ponawiania.

  Zasady ponawiania w Azure Cosmos DB są skonfigurowane do obsługi wyjątków HTTP Code 429 ("Request rate Large"). Azure Cosmos DB interfejs API Cassandra tłumaczy te wyjątki na przeciążone błędy w protokole natywnym Cassandra i można ponowić próbę z powrotem. Ponieważ Azure Cosmos DB używa modelu przepływności z obsługą administracyjną, liczba wyjątków ograniczania żądań występuje po zwiększeniu szybkości transferu danych przychodzących/wychodzących. Zasady ponawiania chronią zadania platformy Spark względem danych, które znacznie przekraczają przepływność przydzieloną dla kontenera.

  > [!NOTE] 
  > Zasady ponawiania mogą chronić zadania platformy Spark wyłącznie przy użyciu tylko przyrostów czasu. Jeśli nie skonfigurowano wystarczającej liczby jednostek ru wymaganych do uruchomienia obciążenia, zasady ponawiania nie mają zastosowania, a Klasa zasad ponawiania generuje wyjątek.

* **Szczegóły połączenia z kontem Azure Cosmos DB:** Nazwa konta usługi Azure interfejs API Cassandra, punkt końcowy konta i klucz.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parametry konfiguracji przepływności łącznika Spark

W poniższej tabeli wymieniono Azure Cosmos DB specyficzne dla interfejs API Cassandra parametry konfiguracji przepływności dostarczone przez łącznik. Aby uzyskać szczegółową listę wszystkich parametrów konfiguracji, zobacz stronę [Informacje o konfiguracji](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) repozytorium usługi Spark Cassandra Connector.

| **Nazwa właściwości** | **Wartość domyślna** | **Opis** |
|---------|---------|---------|
| spark.cassandra.output.batch. size. Rows |  1 |Liczba wierszy na pojedynczą partię. Ustaw ten parametr na 1. Ten parametr służy do osiągnięcia wyższej przepływności dla dużych obciążeń. |
| spark.cassandra.connection.connections_per_executor_max  | Brak | Maksymalna liczba połączeń na węzeł na wykonawcę. 10 * n jest odpowiednikiem 10 połączeń na węzeł w klastrze Cassandra n węzła. Dlatego, jeśli wymagane jest 5 połączeń na węzeł każdego wykonawcy dla klastra Cassandra z 5 węzłów, należy ustawić tę konfigurację na 25. Zmodyfikuj tę wartość na podstawie stopnia równoległości lub liczby modułów wykonujących, dla których skonfigurowano zadania platformy Spark.   |
| Spark. Cassandra. Output. współbieżne. zapisy  |  100 | Określa liczbę zapisów równoległych, które mogą wystąpić na wykonawcę. Ponieważ ustawiasz wartość "Batch. size. Rows" na 1, pamiętaj o tym, aby odpowiednio skalować ją w górę. Zmodyfikuj tę wartość na podstawie stopnia równoległości lub przepływności, która ma zostać osiągnięta dla obciążenia. |
| Spark. Cassandra. współbieżne. odczyty |  512 | Określa liczbę odczytów równoległych, które mogą wystąpić na wykonawcę. Modyfikuj tę wartość na podstawie stopnia równoległości lub przepływności, która ma zostać osiągnięta dla obciążenia  |
| spark.cassandra.output.throughput_mb_per_sec  | Brak | Określa łączną przepustowość zapisu na wykonawcę. Ten parametr może służyć jako górny limit przepływności zadania platformy Spark i opierał się na zainicjowanej przepływności dla kontenera Cosmos.   |
| spark.cassandra.input.reads_per_sec| Brak   | Definiuje łączną przepływność odczytu na wykonawcę. Ten parametr może służyć jako górny limit przepływności zadania platformy Spark i opierał się na zainicjowanej przepływności dla kontenera Cosmos.  |
| spark.cassandra.output.batch. Group. Buffer. size |  1000  | Definiuje liczbę partii dla pojedynczego zadania platformy Spark, które mogą być przechowywane w pamięci przed wysłaniem do interfejs API Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Określa okres czasu, do którego są dostępne nieużywane połączenia. | 

Dostosuj przepływność i stopień równoległości tych parametrów na podstawie obciążenia, które są oczekiwane dla zadań platformy Spark, i przepływności zainicjowanej dla konta Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Nawiązywanie połączenia z usługą Azure Cosmos DB interfejs API Cassandra z platformy Spark

### <a name="cqlsh"></a>cqlsh
W poniższych poleceniach szczegółowo opisano sposób nawiązywania połączenia z usługą Azure CosmosDB interfejs API Cassandra z cqlsh.  Jest to przydatne w przypadku sprawdzania poprawności w trakcie wykonywania przykładów w platformie Spark.<br>
**W systemie Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
W poniższym artykule omówiono Inicjowanie obsługi klastrów Azure Databricks, konfigurację klastra w celu nawiązania połączenia z Azure Cosmos DB interfejs API Cassandra oraz kilka przykładowych notesów, które obejmują operacje DDL, operacje DML i nie tylko.<BR>
[Współpraca z Azure Cosmos DB interfejs API Cassandra z usługi Azure datakostks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. HDInsight-Spark platformy Azure
W poniższym artykule omówiono HDinsight-Spark usługi, aprowizacji, konfigurację klastra do łączenia się z Azure Cosmos DB interfejs API Cassandra oraz kilka przykładowych notesów, które obejmują operacje DDL, operacje DML i inne.<BR>
[Korzystanie z Azure Cosmos DB interfejs API Cassandra z usługi Azure HDInsight — Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. środowisko Spark ogólnie
Chociaż powyższe sekcje dotyczyły usług PaaS opartych na platformie Azure Spark, w tej sekcji omówiono wszystkie ogólne środowisko Spark.  Poniżej opisano szczegółowo zależności łączników, Importy i konfigurację sesji Spark. W sekcji "następne kroki" omówiono przykłady kodu dla operacji DDL, operacji DML i innych.  

#### <a name="connector-dependencies"></a>Zależności łącznika:

1. Dodaj współrzędne Maven, aby uzyskać [Łącznik Cassandra dla platformy Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Dodaj współrzędne Maven dla [Azure Cosmos DB biblioteki pomocniczej](cassandra-spark-generic.md#dependencies-for-connectivity) dla interfejs API Cassandra

#### <a name="imports"></a>Importowania

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Konfiguracja sesji platformy Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach przedstawiono integrację platformy Spark z usługą Azure Cosmos DB interfejs API Cassandra. 
 
* [Operacje DDL](cassandra-spark-ddl-ops.md)
* [Operacje tworzenia/wstawiania](cassandra-spark-create-ops.md)
* [Operacje odczytu](cassandra-spark-read-ops.md)
* [Operacje upsert](cassandra-spark-upsert-ops.md)
* [Operacje usuwania](cassandra-spark-delete-ops.md)
* [Operacje agregacji](cassandra-spark-aggregation-ops.md)
* [Operacje kopiowania tabel](cassandra-spark-table-copy-ops.md)
