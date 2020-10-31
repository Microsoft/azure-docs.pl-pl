---
title: Połącz Apache Spark z Azure Cosmos DB
description: Dowiedz się więcej o łączniku Azure Cosmos DB Spark, który umożliwia łączenie Apache Spark z Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: df7792c37b1db330264554cf6d9c179cf9f22611
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098709"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Przyspieszenie analizy danych Big Data przy użyciu Apache Spark do Azure Cosmos DB łącznika
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zadania [platformy Spark](https://spark.apache.org/) można uruchamiać z danymi przechowywanymi w Azure Cosmos dB przy użyciu łącznika Cosmos DB Spark. Cosmos może służyć do przetwarzania wsadowego i przesyłania strumieniowego oraz do obsługi warstwy w celu uzyskania dostępu do małych opóźnień.

Możesz użyć łącznika z usługą [Azure Databricks](https://azure.microsoft.com/services/databricks) lub [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), która udostępnia zarządzane klastry platformy Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane wersje platformy Spark.

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x i 2.1. x |
| Scala | 2,11 |
| Wersja środowiska uruchomieniowego Azure Databricks | > 3,4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowy interfejs API (SQL) Azure Cosmos DB.
> Aby uzyskać Cosmos DB dla interfejsu API MongoDB, użyj [łącznika MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Aby uzyskać Cosmos DB interfejs API Cassandra, użyj [łącznika Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Szybki start

* Wykonaj kroki opisane w sekcji [wprowadzenie do zestawu Java SDK](./create-sql-api-java.md) , aby skonfigurować konto Cosmos DB i wypełnić pewne dane.
* Wykonaj kroki opisane w [Azure Databricks wprowadzenie](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) do konfiguracji Azure Databricks obszaru roboczego i klastra.
* Teraz można tworzyć nowe Notesy i importować biblioteki łączników Cosmos DB. Przejdź do [łącznika Cosmos DB,](#bk_working_with_connector) Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania obszaru roboczego.
* W poniższej sekcji znajdują się fragmenty ułatwiające odczytywanie i zapisywanie za pomocą łącznika.

### <a name="batch-reads-from-cosmos-db"></a>Odczyty partii z Cosmos DB

Poniższy fragment kodu przedstawia sposób tworzenia ramki Dataframe w celu odczytania Cosmos DB w PySpark.

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

I ten sam fragment kodu w Scala:

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

### <a name="batch-writes-to-cosmos-db"></a>Zapisy wsadowe w Cosmos DB

Poniższy fragment kodu przedstawia sposób pisania ramki danych do Cosmos DB w PySpark.

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

I ten sam fragment kodu w Scala:

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

### <a name="streaming-reads-from-cosmos-db"></a>Dane przesyłane strumieniowo z Cosmos DB

Poniższy fragment kodu przedstawia sposób nawiązywania połączenia z i odczytywania Azure Cosmos DB źródła zmian.

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
I ten sam fragment kodu w Scala:

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

### <a name="streaming-writes-to-cosmos-db"></a>Przesyłanie strumieniowe zapisów do Cosmos DB

Poniższy fragment kodu przedstawia sposób pisania ramki danych do Cosmos DB w PySpark.

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

I ten sam fragment kodu w Scala:

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
Więcej fragmentów kodu i kompleksowych przykładów, zobacz [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a> Praca z łącznikiem

Możesz skompilować łącznik ze źródła w usłudze GitHub lub pobrać Uber Jars z Maven w poniższych linkach.

| platforma Spark | Scala | Najnowsza wersja |
|---|---|---|
| 2.4.0 | 2,11 | [Azure-cosmosdb — spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)
| 2.3.0 | 2,11 | [Azure-cosmosdb-spark_2.3.0 _2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2,11 | [Azure-cosmosdb-spark_2.2.0 _2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2,11 | [Azure-cosmosdb-spark_2.1.0 _2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Korzystanie z notesów datakostek

Utwórz bibliotekę przy użyciu obszaru roboczego datakostki, postępując zgodnie ze wskazówkami w przewodniku Azure Databricks > [użyć łącznika Azure Cosmos DB Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Strona **Korzystanie z łącznika platformy Spark Azure Cosmos DB** jest obecnie nieaktualna. Zamiast pobierania sześciu oddzielnych Jars do sześciu różnych bibliotek, można pobrać Uber jar z Maven na [platformie Azure-cosmosdb-spark_lkg_version](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) i zainstalować ten plik JAR/Library.
> 

### <a name="using-spark-cli"></a>Korzystanie z platformy Spark — CLI

Aby współdziałać z łącznikiem przy użyciu interfejsu Spark-CLI (czyli,, `spark-shell` `pyspark` , `spark-submit` ), można użyć `--packages` parametru ze [współrzędnymi Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)łącznika.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Korzystanie z notesów Jupyter

Jeśli używasz Jupyter Notebook w usłudze HDInsight, możesz użyć komórki Spark-Magic, `%%configure` Aby określić współrzędne Maven łącznika.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Należy pamiętać, że włączenie programu `spark.jars.excludes` jest specyficzne dla usunięcia potencjalnych konfliktów między łącznikiem, Apache Spark i usługi Livy.

### <a name="build-the-connector"></a>Kompilowanie łącznika

Obecnie ten projekt łącznika służy `maven` do kompilowania bez zależności, można uruchomić następujące polecenie:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Praca z naszymi przykładami

[Repozytorium usługi GitHub Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) zawiera następujące przykładowe Notesy i skrypty, które można wypróbować.

* **Wydajność lotu w czasie z platformą Spark i Cosmos dB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connect Spark do Cosmos DB za pomocą usługi HDInsight Jupyter Notebook Service do zaprezentowania platformy Spark SQL, GraphFrames i przewidywania opóźnień lotów przy użyciu potoków ml.
* **Źródło usługi Twitter ze źródłem Apache Spark i Azure Cosmos DB źródła zmian** : [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Używanie Apache Spark do wykonywania zapytań na wykresach Cosmos DBowych** : [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Łączenie Azure Databricks z Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** przy użyciu `azure-cosmosdb-spark` .  Połączony tutaj to również Azure Databricks wersja [notesu wydajności w czasie](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architektura lambda z Azure Cosmos DB i HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : można zmniejszyć obciążenie operacyjne dla obsługi potoków danych Big Data za pomocą Cosmos DB i platformy Spark.

## <a name="more-information"></a>Więcej informacji

Więcej informacji znajduje się w `azure-cosmosdb-spark` [witrynie typu wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) , w tym:

* [Podręcznik użytkownika łącznika Azure Cosmos DB Spark](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Przykłady agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfiguracja i instalacja

* [Konfiguracja łącznika platformy Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Konfiguracja łącznika Spark do Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (w toku)
* [Konfigurowanie zapytania Power BI bezpośredniego do Azure Cosmos DB za pośrednictwem Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Rozwiązywanie problemów

* [Używanie agregacji Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Znane problemy](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Wydajność

* [Porady dotyczące wydajności](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Przebiegi testowe zapytania](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Zapisywanie przebiegów testowych](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Zestawienie zmian

* [Przetwarzanie strumienia zmian za pomocą Azure Cosmos DB zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Pokazy źródła zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Pokazy dla strumienia strukturalnego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitorowanie

* [Monitorowanie zadań platformy Spark za pomocą usługi Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, Pobierz łącznik platformy Spark do Azure Cosmos DB z repozytorium GitHub [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) . Zapoznaj się z następującymi dodatkowymi zasobami w repozytorium:

* [Przykłady agregacji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Warto również zapoznać się z [podręcznikiem Apache Spark SQL, Dataframes i zestawami danych](https://spark.apache.org/docs/latest/sql-programming-guide.html)oraz [Apache Spark w artykule usługi Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .