---
title: Architektura lambda z Azure Cosmos DB i Apache Spark
description: W tym artykule opisano sposób implementacji architektury lambda przy użyciu Azure Cosmos DB, usługi HDInsight i platformy Spark
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76719742"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: implementowanie architektury lambda na platformie Azure 

Architektury lambda umożliwiają wydajne przetwarzanie danych w dużych zestawach danych. Architektury lambda używają przetwarzania wsadowego, przetwarzania strumieniowego i warstwy obsługującej, aby zminimalizować opóźnienia związane z wykonywaniem zapytań dotyczących danych Big Data. 

Aby zaimplementować architekturę lambda na platformie Azure, możesz połączyć następujące technologie, aby przyspieszyć analizę danych Big Data w czasie rzeczywistym:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)— najpierw dystrybuowana w branży globalnie, wielomodelowa usługa bazy danych. 
* [Apache Spark dla usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), struktura przetwarzania, która uruchamia aplikacje do analizy danych na dużą skalę
* Azure Cosmos DB [źródła zmian](change-feed.md), które przesyła strumieniowo nowe dane do warstwy wsadowej usługi HDInsight do przetworzenia
* [Łącznik platformy Spark do Azure Cosmos DB](spark-connector.md)

W tym artykule opisano podstawowe informacje o architekturze lambda w oparciu o oryginalny projekt wielowarstwowy oraz zalety architektury lambda "rearchitecture", która upraszcza operacje.  

## <a name="what-is-a-lambda-architecture"></a>Co to jest architektura lambda?
Architektura lambda to ogólna, skalowalna i odporna na błędy architektura przetwarzania danych, która umożliwia rozwiązywanie scenariuszy związanych z usługą Batch i szybkością opóźnienia, zgodnie z opisem w [Nathana marza](https://twitter.com/nathanmarz).

![Diagram przedstawiający architekturę lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Źródło: http://lambda-architecture.net/

Podstawowe zasady architektury lambda są opisane na powyższym diagramie, zgodnie z oczekiwaniami [http://lambda-architecture.net](http://lambda-architecture.net/).

 1. Wszystkie **dane** są wypychane do *warstwy* *wsadowej* i *warstwy szybkość*.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) i wstępnie oblicza widoki partii.
 3. **Warstwa obsługująca** zawiera widoki wsadowe dla szybkich zapytań. 
 4. **Warstwa szybkości** pozwala kompensować czas przetwarzania (do obsługi warstwy) i obsługiwać tylko najnowsze dane.
 5. Odpowiedzi na wszystkie zapytania można uzyskać, Scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub wysyłając je pojedynczo.

Po dalszej odczytaniu będziemy mogli zaimplementować tę architekturę tylko przy użyciu następujących funkcji:

* Kontenery usługi Azure Cosmos
* Klaster HDInsight (Apache Spark 2,1)
* Łącznik platformy Spark [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Warstwa szybkości

Z perspektywy operacji, utrzymując dwa strumienie danych przy jednoczesnym zapewnieniu prawidłowego stanu danych, może to być skomplikowany Endeavor. Aby uprościć operacje, użyj [obsługi kanału informacyjnego zmiany Azure Cosmos DB](change-feed.md) , aby zachować stan dla *warstwy usługi Batch* podczas ujawniania Azure Cosmos DB dzienniku zmian za pomocą *interfejsu API źródła zmian* dla *warstwy szybkości*.  
![Diagram wyróżniania nowych danych, warstwy szybkości i wzorca zestawu danych w architekturze lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Co jest ważne w następujących warstwach:

 1. Wszystkie **dane** są wypychane *tylko* do Azure Cosmos DB, co pozwala uniknąć problemów z rozrzutem.
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) i wstępnie oblicza widoki partii.
 3. W następnej sekcji omówiono **obsługę warstwy** .
 4. **Warstwa szybkości** wykorzystuje usługi HDInsight (Apache Spark) do odczytywania Azure Cosmos DB źródła zmian. Dzięki temu można przechowywać dane, a także wykonywać zapytania i przetwarzać je współbieżnie.
 5. Odpowiedzi na wszystkie zapytania można uzyskać, Scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub wysyłając je pojedynczo.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Przykład kodu: strukturalne przesyłanie strumieniowe platformy Spark do Azure Cosmos DB źródła zmian
Aby uruchomić szybki prototyp kanału informacyjnego zmiany Azure Cosmos DB jako część **warstwy szybkości**, można przetestować go przy użyciu danych usługi Twitter w ramach [przetwarzania strumienia przy użyciu Azure Cosmos DB zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) przykładu. Aby szybko rozpocząć pracę z danymi wyjściowymi w usłudze Twitter, zobacz przykładowy kod w [strumieniowym źródle danych z usługi Twitter do Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed). W powyższym przykładzie załadujesz dane z usługi Twitter do Azure Cosmos DB i możesz skonfigurować klaster usługi HDInsight (Apache Spark), aby połączyć się ze źródłem zmian. Aby uzyskać więcej informacji na temat konfigurowania tej konfiguracji, zobacz [Apache Spark do Azure Cosmos DB konfiguracji łącznika](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

Poniższy fragment kodu przedstawia sposób konfigurowania `spark-shell` programu w celu uruchomienia zadania tworzenia strukturalnego przesyłania strumieniowego w celu nawiązania Azure Cosmos DB połączenia ze strumieniowym źródłem danych usługi Twitter w czasie rzeczywistym, aby wykonać liczbę uruchomionych interwałów.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Aby zapoznać się z kompletnymi przykładami kodu, zobacz temat [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), w tym:
* [Zapytanie przesyłane strumieniowo ze źródła danych zmian Cosmos DB. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Zapytanie o Tagi przesyłane strumieniowo ze źródła danych zmian Cosmos DB. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Dane wyjściowe tego programu to `spark-shell` konsola, która ciągle uruchamia zadanie przesyłania strumieniowego ze strukturą, które wykonuje liczbę interwałów względem danych usługi Twitter ze źródła zmian Azure Cosmos DB. Na poniższej ilustracji przedstawiono dane wyjściowe zadania przesyłania strumieniowego i liczby interwałów.

![Dane wyjściowe przesyłania strumieniowego przedstawiające liczbę interwałów względem danych usługi Twitter ze źródła zmian Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Aby uzyskać więcej informacji na temat Azure Cosmos DB źródła zmian, zobacz:

* [Praca z obsługą kanału informacyjnego zmian w Azure Cosmos DB](change-feed.md)
* [Wprowadzenie do biblioteki procesora kanału informacyjnego zmiany CosmosDB platformy Azure](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Zmiany przetwarzania strumienia: usługa Azure CosmosDB zmiana źródła danych i Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Przetwarzanie wsadowe i Obsługa warstw
Ze względu na to, że nowe dane są ładowane do Azure Cosmos DB (gdzie Źródło zmian jest używane dla warstwy szybkości), jest to miejsce, w którym znajduje się **główny zestaw** danych (niemodyfikowalny zestaw tylko do dołączania). Od tego momentu Użyj usługi HDInsight (Apache Spark), aby wykonać funkcje wstępnego obliczania z **warstwy wsadowej** w celu obsługi **warstwy**, jak pokazano na poniższej ilustracji:

![Diagram wyróżniania warstwy wsadowej i obsługujący warstwę architektury lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Co jest ważne w następujących warstwach:

 1. Wszystkie **dane** są wypychane tylko do Azure Cosmos dB (w celu uniknięcia problemów z multiemisją).
 2. **Warstwa wsadowa** ma główny zestaw danych (niezmienny zestaw danych RAW) przechowywanych w Azure Cosmos DB. Korzystając z HDI Spark, można wstępnie obliczyć agregacje, aby były przechowywane w widokach partii obliczanej.
 3. **Warstwa obsługująca** to baza danych usługi Azure Cosmos z kolekcjami dla głównego zestawu danych i obliczanego widoku partii.
 4. **Warstwa szybkości** została omówiona w dalszej części tego artykułu.
 5. Odpowiedzi na wszystkie zapytania można uzyskać, Scalając wyniki z widoków wsadowych i widoków w czasie rzeczywistym lub wysyłając je pojedynczo.

### <a name="code-example-pre-computing-batch-views"></a>Przykład kodu: wstępnie Obliczanie widoków wsadowych
Aby zaprezentować sposób wykonywania wstępnie obliczonych widoków względem **głównego zestawu danych** z Apache Spark do Azure Cosmos DB, Użyj poniższych fragmentów kodu z notesu reprezentowanych przez [architekturę lambda](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) [na podstawie](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)platformy W tym scenariuszu należy użyć danych usługi Twitter przechowywanych w Azure Cosmos DB.

Zacznijmy od utworzenia połączenia konfiguracji z danymi usługi Twitter w Azure Cosmos DB przy użyciu poniższego kodu PySpark.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Następnie Uruchommy następującą instrukcję Spark SQL, aby określić 10 najważniejszych hasztagów zestawu tweetów. W przypadku tego zapytania Spark SQL uruchamiamy to w notesie Jupyter bez wykresu słupkowego Output bezpośrednio po tym fragmencie kodu.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Wykres przedstawiający liczbę tweetów na listę](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Teraz, gdy masz zapytanie, zapiszemy je ponownie w kolekcji przy użyciu łącznika Spark, aby zapisać dane wyjściowe w innej kolekcji.  W tym przykładzie Użyj Scala, aby zaprezentować połączenie. Podobnie jak w poprzednim przykładzie, Utwórz połączenie konfiguracji, aby zapisać Apache Spark ramki danych w innym kontenerze usługi Azure Cosmos.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Po określeniu `SaveMode` (wskazującym czy `Overwrite` `Append` dokumenty) Utwórz `tweets_bytags` ramkę danych podobną do zapytania Spark SQL w poprzednim przykładzie.  `tweets_bytags` Po utworzeniu ramki danych można ją zapisać przy użyciu `write` metody używającej wcześniej określonego `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Ta ostatnia instrukcja zapisała teraz swoją ramkę danych platformy Spark w nowym kontenerze usługi Azure Cosmos. w perspektywie architektury lambda jest to **Widok wsadu** w ramach **warstwy obsługującej**.
 
#### <a name="resources"></a>Zasoby

Aby zapoznać się z kompletnymi przykładami kodu, zobacz [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) , w tym:
* Przeprojektowana architektura lambda —[ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | warstwy wsadowej
* Przeprojektowana architektura lambda — przetwarzanie wsadowe obsługujące warstwę [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Warstwa szybkości
Jak wspomniano wcześniej, przy użyciu biblioteki źródeł zmian Azure Cosmos DB można uprościć operacje między warstwami partii i szybkością. W tej architekturze należy używać Apache Spark (za pośrednictwem usługi HDInsight) do wykonywania zapytań *strukturalnych dotyczących przesyłania strumieniowego* na dane. Możesz również chcieć tymczasowo zachować wyniki zapytań dotyczących przesyłania strumieniowego ze strukturą, tak aby inne systemy mogły uzyskać dostęp do tych danych.

![Diagram wyróżniania warstwy prędkości w architekturze lambda](./media/lambda-architecture/lambda-architecture-speed.png)

W tym celu Utwórz oddzielny kontener usługi Azure Cosmos, aby zapisać wyniki zapytań dotyczących przesyłania strumieniowego ze strukturą.  Dzięki temu inne systemy mogą uzyskać dostęp do tych informacji nie tylko Apache Spark. Wraz z Cosmos DB funkcją Time-to-Live (TTL) można skonfigurować dokumenty do automatycznego usuwania po upływie określonego czasu.  Aby uzyskać więcej informacji na temat funkcji TTL Azure Cosmos DB, zobacz [Automatyczne wygasanie danych w kontenerach usługi Azure Cosmos z użyciem czasu](time-to-live.md) wygaśnięcia

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Architektura lambda: rearchitektura
Jak wspomniano w poprzednich sekcjach, można uprościć oryginalną architekturę lambda przy użyciu następujących składników:
* Azure Cosmos DB
* Biblioteka kanału informacyjnego zmiany Azure Cosmos DB, aby uniknąć konieczności wielokrotnego rzutowania danych między warstwami partii i szybkości
* Apache Spark w usłudze HDInsight
* Łącznik platformy Spark dla Azure Cosmos DB

![Diagram przedstawiający rearchitekturę architektury lambda przy użyciu Azure Cosmos DB, Spark i interfejsu API kanału informacyjnego zmiany Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

W tym projekcie potrzebne są tylko dwie zarządzane usługi, Azure Cosmos DB i HDInsight. Razem zawierają one partię, obsługę i szybkość warstw architektury lambda. Upraszcza to nie tylko operacje, ale również przepływ danych. 
 1. Wszystkie dane są przekazywane do Azure Cosmos DB do przetworzenia
 2. Warstwa wsadowa ma główny zestaw danych (niezmienny zestaw danych RAW) i wstępnie oblicza widoki partii
 3. Warstwa obsługująca zawiera widoki wsadowe danych na potrzeby szybkich zapytań.
 4. Warstwa szybkości pozwala kompensować czas przetwarzania (do obsługi warstwy) i obsługiwać tylko najnowsze dane.
 5. Wszystkie zapytania można odpowiadać przez scalanie wyników z widoków wsadowych i widoków w czasie rzeczywistym.

### <a name="resources"></a>Zasoby

* **Nowe dane**: [strumień strumieniowy z usługi Twitter do CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), który jest mechanizmem do wypychania nowych danych do Azure Cosmos DB.
* **Warstwa partii:** Warstwa wsadowa składa się z *wzorca zestawu* danych (niemodyfikowalny zestaw danych RAW) i możliwość wstępnego obliczania widoków wsadowych danych, które są przekazywane do **warstwy obsługującej**.
   * Przeprojektowana **Architektura lambda — Notes warstwy wsadu** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) wysyła zapytanie do zestawu *głównych zestawów danych* widoków wsadowych.
* **Obsługa warstwy:** **Warstwa obsługująca** składa się z wstępnie obliczonych danych w widokach wsadowych (na przykład agregacji, określonych fragmentatorów itp.) dla szybkich zapytań.
  * Przeprojektowana **Architektura lambda — przetwarzanie wsadowe w celu obsługi notesu warstwowego** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) wypychanie danych wsadowych do warstwy obsługującej; oznacza to, że platforma Spark wysyła zapytanie do kolekcji partii tweetów, przetwarza je i zapisuje w innej kolekcji (obliczanej partii).
    * **Warstwa szybkości:** **Warstwa szybkości** składa się z platformy Spark korzystającej ze źródła danych Azure Cosmos DB zmian w celu natychmiastowego odczytu i działania. Dane można również zapisać na *obliczonym RT* , tak aby inne systemy mogły wysyłać zapytania do przetworzonych danych w czasie rzeczywistym, zamiast wykonywać zapytania w czasie rzeczywistym.
  * [Kwerenda przesyłania strumieniowego ze źródła danych Cosmos DB zmian](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala wykonuje zapytanie strumieniowe ze źródła danych Azure Cosmos DB zmiany, aby obliczyć liczbę interwałów z powłoki Spark.
  * [Zapytanie o Tagi przesyłania strumieniowego Cosmos DB ze skryptu Scala zmian kanału informacyjnego](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) wykonuje zapytanie strumieniowe ze źródła danych Azure Cosmos DB zmiany, aby obliczyć liczbę znaczników tagów z powłoki Spark.
  
## <a name="next-steps"></a>Następne kroki
Jeśli jeszcze tego nie zrobiono, Pobierz program Spark do Azure Cosmos DB Connector z repozytorium GitHub [usługi Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) i Eksploruj dodatkowe zasoby w repozytorium:
* [Architektura lambda](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Przykłady rozproszonej agregacji](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Przykładowe skrypty i notesy](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Pokazy strukturalnego przesyłania strumieniowego](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Pokazy źródła zmian](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Przetwarzanie strumienia zmian za pomocą Azure Cosmos DB zmian i Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Warto również zapoznać się z [podręcznikiem Apache Spark SQL, Dataframes i zestawami danych](https://spark.apache.org/docs/latest/sql-programming-guide.html) oraz [Apache Spark w artykule usługi Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) .
