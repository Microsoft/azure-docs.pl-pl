---
title: Łączenie aplikacji Apache Spark z usługą Azure Cosmos DB
description: Dowiedz się więcej o łączniku platformy Azure Cosmos DB Spark, który umożliwia połączenie platformy Apache Spark z usługą Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: edfaf50b701f64b12f9cf5fcc9ab8d2c6d241d0a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482174"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Przyspiesz analizę dużych zbiorów danych przy użyciu łącznika Apache Spark do usługi Azure Cosmos DB

Zadania [platformy Spark](https://spark.apache.org/) można uruchamiać z danymi przechowywanymi w usłudze Azure Cosmos DB przy użyciu łącznika platformy Spark usługi Cosmos DB. Usługa Cosmos może służyć do przetwarzania wsadowego i strumienia oraz jako warstwa służąca dla dostępu o małym opóźnieniu.

Łącznika można używać za pomocą [usługi Azure Databricks](https://azure.microsoft.com/services/databricks) lub [usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), które zapewniają zarządzane klastry platformy Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane wersje platformy Spark.

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x i 2.1.x |
| Scala | 2.11 |
| Wersja środowiska uruchomieniowego usługi Azure Databricks | > 3.4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowy interfejs API (SQL) usługi Azure Cosmos DB.
> W przypadku usługi Cosmos DB dla interfejsu API MongoDB użyj [łącznika MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> W przypadku interfejsu API Cassandra usługi Cosmos DB należy użyć [łącznika Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Szybki start

* Wykonaj kroki opisane w aplikacji Wprowadzenie do [zestawu Java SDK](sql-api-async-java-get-started.md) w celu skonfigurowania konta usługi Cosmos DB i wypełnienia niektórych danych.
* Wykonaj kroki opisane w [usłudze Azure Databricks wprowadzenie](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) do konfigurowania obszaru roboczego i klastra usługi Azure Databricks.
* Teraz można tworzyć nowe notesy i importować bibliotekę łączników usługi Cosmos DB. Przejdź do [pracy ze łącznikiem usługi Cosmos DB,](#bk_working_with_connector) aby uzyskać szczegółowe informacje na temat konfigurowania obszaru roboczego.
* W poniższej sekcji przedstawiono fragmenty kodu dotyczące sposobu odczytywania i zapisu przy użyciu łącznika.

### <a name="batch-reads-from-cosmos-db"></a>Odczyty wsadowe z usługi Cosmos DB

Poniższy fragment kodu pokazuje, jak utworzyć spark DataFrame do odczytu z usługi Cosmos DB w PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

I ten sam fragment kodu w Scali:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Zapisy wsadowe w usłudze Cosmos DB

Poniższy fragment kodu pokazuje, jak zapisać ramki danych do usługi Cosmos DB w PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

I ten sam fragment kodu w Scali:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Przesyłanie strumieniowe odczytów z usługi Cosmos DB

Poniższy fragment kodu pokazuje, jak połączyć się z i odczytać z usługi Azure Cosmos DB Change Feed.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
I ten sam fragment kodu w Scali:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Przesyłanie strumieniowe zapisuje do usługi Cosmos DB

Poniższy fragment kodu pokazuje, jak zapisać ramki danych do usługi Cosmos DB w PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

I ten sam fragment kodu w Scali:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Więcej urywków i próbek end to end, zobacz [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Praca ze złączem

Możesz zbudować łącznik ze źródła w GitHub lub pobrać uber słoiki z Maven w poniższych linkach.

| platforma Spark | Scala | Najnowsza wersja |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Korzystanie z notesów Databricks

Tworzenie biblioteki przy użyciu obszaru roboczego Databricks, postępuje zgodnie ze wskazówkami zawartymi w przewodniku usługi Azure Databricks > [użyj łącznika platformy Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Uwaga: **Strona Użyj łącznika platformy Azure Cosmos DB Spark** connector nie jest obecnie aktualna. Zamiast pobierać sześć oddzielnych słoików do sześciu różnych bibliotek, możesz pobrać słoik https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) uber z maven at i zainstalować ten jeden słoik / bibliotekę.
> 

### <a name="using-spark-cli"></a>Korzystanie z iskry-cli

Aby pracować z łącznikiem przy użyciu iskrowego interfejsu "cli" (czyli `spark-shell` `pyspark` `spark-submit`, można użyć parametru `--packages` ze [współrzędnymi maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)łącznika .

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Korzystanie z notesów Jupyter

Jeśli używasz notebooków Jupyter w programie HDInsight, `%%configure` możesz użyć komórki spark-magic, aby określić współrzędne maven łącznika.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Uwaga: włączenie jest `spark.jars.excludes` specyficzne, aby usunąć potencjalne konflikty między łącznikiem, Apache Spark i Livy.

### <a name="build-the-connector"></a>Zbuduj łącznik

Obecnie ten projekt łącznika `maven` używa tak do tworzenia bez zależności, można uruchomić:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Praca z naszymi próbkami

[Repozytorium Usługi Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) ma następujące przykładowe notesy i skrypty, które można wypróbować.

* **Wydajność lotu na czas z Spark i Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)Połącz spark do usługi Cosmos DB przy użyciu usługi notesu HDInsight Jupyter do prezentowania spark SQL, GraphFrames i przewidywania opóźnień lotu przy użyciu potoków ML.
* **Twitter Źródło z Apache Spark i Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Korzystanie z Apache Spark do wykonywania zapytań Cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Łączenie usługi Azure Databricks z usługą Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** przy użyciu programu `azure-cosmosdb-spark`.  Połączone tutaj jest również wersja Usługi Azure Databricks [notesu wydajności lotu na czas.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Architektura Lambda z usługą Azure Cosmos DB i HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** Można zmniejszyć obciążenie operacyjne związane z utrzymaniem potoków dużych zbiorów danych przy użyciu usługi Cosmos DB i Spark.

## <a name="more-information"></a>Więcej informacji

Mamy więcej informacji `azure-cosmosdb-spark` na [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) w tym:

* [Przewodnik użytkownika łącznika spark usługi Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Przykłady agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguracja i instalacja

* [Konfiguracja łącznika platformy Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Instalacja łącznika programu Spark to Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (w toku)
* [Konfigurowanie bezpośredniego zapytania usługi Power BI do usługi Azure Cosmos DB za pośrednictwem platformy Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Korzystanie z agregatów bazy danych Cosmos](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Znane problemy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Wydajność

* [Wskazówki dotyczące wydajności](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Przebiegi testu kwerendy](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Pisanie przebiegów testowych](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Zestawienie zmian

* [Strumieniowanie zmian przetwarzania przy użyciu kanału informacyjnego Usługi Azure Cosmos DB Change Feed i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Zmienianie wersji demonstracyjnych kanału informacyjnego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Ustrukturyzowane pokazy strumienia](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorowanie

* [Monitorowanie zadań platformy Spark za pomocą szczegółowych informacji o aplikacjach](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, pobierz łącznik platformy Spark do usługi Azure Cosmos DB z repozytorium GitHub [azure-cosmosdb-spark.](https://github.com/Azure/azure-cosmosdb-spark) Zapoznaj się z następującymi dodatkowymi zasobami w repozytorium:

* [Przykłady agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Można również przejrzeć [Apache Spark SQL, DataFrames i Zestawy danych Przewodnik](https://spark.apache.org/docs/latest/sql-programming-guide.html)i [Apache Spark na platformie Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) artykułu.
