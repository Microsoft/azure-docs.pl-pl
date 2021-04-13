---
title: Łączenie Apache Spark 2 z Azure Cosmos DB
description: Dowiedz się więcej Azure Cosmos DB OLTP platformy Spark 2, który umożliwia łączenie się z Apache Spark z Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 9d4a1a7d359bac83159d0e5ca4bddfc5935394a5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364281"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-v2-to-azure-cosmos-db-oltp-connector"></a>Przyspieszanie analizy danych big data przy użyciu Apache Spark v2 do Azure Cosmos DB OLTP
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zadania spark w [wersji 2 można uruchamiać](https://spark.apache.org/) z danymi przechowywanymi w Azure Cosmos DB przy użyciu Cosmos DB OLTP platformy Spark 2. Usługi Cosmos można używać do przetwarzania wsadowego i strumieniowego oraz jako warstwy obsługującej w celu uzyskania dostępu z małymi opóźnieniami.

Łącznika można używać z [Azure Databricks](https://azure.microsoft.com/services/databricks) [lub Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), które zapewniają zarządzane klastry Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane wersje platformy Spark.

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x i 2.1.x |
| Scala | 2,11 |
| Azure Databricks środowiska uruchomieniowego | > 3.4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowy interfejs API (SQL) Azure Cosmos DB.
> Aby Cosmos DB interfejsu API usługi MongoDB, użyj łącznika [Platformy Spark bazy danych MongoDB.](https://docs.mongodb.com/spark-connector/master/)
> Na Cosmos DB interfejs API Cassandra użyj [łącznika Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)

> [!IMPORTANT]
> Łącznik OLTP Azure Cosmos DB Spark nie jest obecnie obsługiwany na [kontach bez serwera.](serverless.md) Zostanie to rozwiązane w przypadku, gdy oferta bez serwera stanie się ogólnie dostępna.

## <a name="quickstart"></a>Szybki start

* Postępuj zgodnie z instrukcjami [z tematu Rozpoczynanie](./create-sql-api-java.md) pracy z zestawem Java SDK, aby skonfigurować Cosmos DB i wypełnić niektóre dane.
* Postępuj zgodnie z instrukcjami [Azure Databricks, aby rozpocząć](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) konfigurowanie Azure Databricks i klastra.
* Teraz możesz tworzyć nowe notesy i importować Cosmos DB łączników. Przejdź do [tematu Praca z łącznikiem Cosmos DB,](#bk_working_with_connector) aby uzyskać szczegółowe informacje na temat sposobu skonfigurowania obszaru roboczego.
* W poniższej sekcji przedstawiono fragmenty kodu dotyczące odczytywania i zapisu przy użyciu łącznika.

### <a name="batch-reads-from-cosmos-db"></a>Odczyty wsadowe z Cosmos DB

Poniższy fragment kodu przedstawia sposób tworzenia ramki danych platformy Spark do odczytu z Cosmos DB pySpark.

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

I ten sam fragment kodu w programie Scala:

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

### <a name="batch-writes-to-cosmos-db"></a>Wsadowe zapis do Cosmos DB

Poniższy fragment kodu pokazuje, jak zapisać ramkę danych w Cosmos DB PySpark.

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
flights.write.mode("overwrite").format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

I ten sam fragment kodu w programie Scala:

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

### <a name="streaming-reads-from-cosmos-db"></a>Przesyłanie strumieniowe odczytów z Cosmos DB

Poniższy fragment kodu przedstawia sposób nawiązywania połączenia i odczytywania z Azure Cosmos DB zestawienia zmian.

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
I ten sam fragment kodu w programie Scala:

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

### <a name="streaming-writes-to-cosmos-db"></a>Przesyłanie strumieniowe zapisu do Cosmos DB

Poniższy fragment kodu pokazuje, jak zapisać ramkę danych w Cosmos DB PySpark.

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

I ten sam fragment kodu w programie Scala:

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
Aby uzyskać więcej fragmentów kodu i przykładów, zobacz [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Praca z łącznikiem

Łącznik możesz skompilować ze źródła w usłudze GitHub lub pobrać pliki jar uber z narzędzia Maven, za pomocą poniższych linków.

| platforma Spark | Scala | Najnowsza wersja |
|---|---|---|
| 2.4.0 | 2,11 | [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Korzystanie z notesów usługi Databricks

Utwórz bibliotekę przy użyciu obszaru roboczego usługi Databricks, korzystając ze wskazówek w przewodniku Azure Databricks > Use the Azure Cosmos DB Spark connector (Korzystanie z łącznika [platformy Spark Azure Cosmos DB).](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Strona Use the Azure Cosmos DB Spark Connector (Korzystanie z łącznika platformy **Spark)** nie jest obecnie aktualny. Zamiast pobierać sześć oddzielnych plików jar do sześciu różnych bibliotek, możesz pobrać plik jar uber z narzędzia maven w witrynie [azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) i zainstalować ten plik jar/bibliotekę.
> 

### <a name="using-spark-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Spark

Aby pracować z łącznikiem przy użyciu interfejsu wiersza polecenia platformy Spark (czyli , , ), można użyć parametru ze współrzędnymi `spark-shell` `pyspark` `spark-submit` `--packages` [maven łącznika.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Korzystanie z notesów Jupyter

Jeśli używasz usługi Jupyter Notebook u usługi HDInsight, możesz użyć komórki spark-magic, aby określić współrzędne `%%configure` maven łącznika.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Należy pamiętać, że uwzględnienie obiektu jest specyficzne dla usuwania potencjalnych konfliktów między `spark.jars.excludes` łącznikiem, Apache Spark i Livy.

### <a name="build-the-connector"></a>Kompilowanie łącznika

Obecnie ten projekt łącznika używa `maven` ich do kompilowania bez zależności, dlatego można uruchomić:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Praca z naszymi przykładami

Repozytorium [Cosmos DB Spark w witrynie GitHub](https://github.com/Azure/azure-cosmosdb-spark) zawiera następujące przykładowe notesy i skrypty, które można wypróbować.

* **On-Time Flight Performance with Spark and Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)html : Connect Spark to Cosmos DB using  |  [](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)HDInsight Jupyter Notebook service to showcase Spark SQL, GraphFrames, and predicting flight delays using ML pipelines (Wydajność lotów na czas przy użyciu platformy Spark i ramek programu GraphFrame) : Łączenie platformy Spark z usługą Cosmos DB przy użyciu usługi HDInsight Jupyter Notebook w celu zaprezentowania zapytań Spark SQL i GraphFrames oraz przewidywania opóźnień lotów przy użyciu potoków uczenia maszynowego.
* **Źródło twittera z Apache Spark i Azure Cosmos DB Change Feed:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Używanie Apache Spark do wykonywania zapytań Cosmos DB Graphs:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Łączenie Azure Databricks z Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** przy użyciu programu `azure-cosmosdb-spark` .  Link znajduje się Azure Databricks [notesu On-Time Flight Performance .](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* Architektura lambda z Azure Cosmos DB i **[HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** można zmniejszyć obciążenie operacyjne związane z konserwacją potoków danych big data przy użyciu usług Cosmos DB i Spark.

## <a name="more-information"></a>Więcej informacji

W wiki mamy więcej informacji, `azure-cosmosdb-spark` [](https://github.com/Azure/azure-cosmosdb-spark/wiki) w tym:

* [Azure Cosmos DB użytkownika łącznika platformy Spark](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Przykłady agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguracja i instalacja

* [Konfiguracja łącznika platformy Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark to Cosmos DB Connector Setup](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (W toku)
* [Konfigurowanie Power BI direct query w celu Azure Cosmos DB za pośrednictwem Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Używanie Cosmos DB agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Znane problemy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Wydajność

* [Porady dotyczące wydajności](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Przebiegi testu zapytania](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Pisanie przebiegów testów](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Zestawienie zmian

* [Zmiany przetwarzania strumieniowego przy Azure Cosmos DB zestawienia zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Pokazy zestawienia zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Pokazy strumienia strukturalnego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorowanie

* [Monitorowanie zadań platformy Spark za pomocą usługi Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Następne kroki

Jeśli nie zostało to jeszcze zrobić, pobierz platformę Spark do łącznika Azure Cosmos DB z [repozytorium GitHub azure-cosmosdb-spark.](https://github.com/Azure/azure-cosmosdb-spark) Zapoznaj się z następującymi dodatkowymi zasobami w repo:

* [Przykłady agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Warto również zapoznać się z Apache Spark [SQL, DataFrames i Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)(Przewodnik po zestawach danych) oraz Apache Spark [w Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) danych.