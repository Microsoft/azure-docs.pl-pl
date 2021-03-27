---
title: Korzystanie z Azure Cosmos DB przy użyciu Apache Spark w usłudze Azure Synapse link
description: Jak korzystać z Azure Cosmos DB przy użyciu Apache Spark w usłudze Azure Synapse link
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627578"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Korzystanie z Azure Cosmos DB przy użyciu Apache Spark w usłudze Azure Synapse link

W tym artykule dowiesz się, jak korzystać z Azure Cosmos DB przy użyciu Apache Spark Synapse. Wraz z pełną obsługą Scala, Python, SparkSQL i C#, Synapse Apache Spark to centrum analiz, Inżynieria danych, nauka danych i scenariusze eksploracji danych w [usłudze Azure Synapse link do Azure Cosmos DB](../../cosmos-db/synapse-link.md).

Podczas pracy z Azure Cosmos DBami obsługiwane są następujące możliwości:
* Synapse Apache Spark umożliwia analizowanie danych w kontenerach Azure Cosmos DB, które są włączane za pomocą usługi Azure Synapse link niemal w czasie rzeczywistym bez wpływu na wydajność obciążeń transakcyjnych. Dostępne są następujące dwie opcje wysyłania zapytań do [magazynu analitycznego](../../cosmos-db/analytical-store-introduction.md) Azure Cosmos DB z platformy Spark:
    + Załaduj do platformy Spark Dataframe
    + Utwórz tabelę platformy Spark
* Synapse Apache Spark umożliwia również pozyskiwanie danych w Azure Cosmos DB. Należy pamiętać, że dane są zawsze wprowadzane do Azure Cosmos DB kontenerów za pomocą magazynu transakcyjnego. Gdy łącze Synapse jest włączone, wszelkie nowe wstawiane, aktualizacje i usunięcia są automatycznie synchronizowane z magazynem analitycznym.
* Synapse Apache Spark również obsługuje przesyłanie strumieniowe platformy Spark ze strukturą Azure Cosmos DB jako źródło i ujścia. 

W poniższych sekcjach opisano składnię powyższych funkcji. Gesty w obszarze roboczym usługi Azure Synapse Analytics zostały zaprojektowane w taki sposób, aby można było łatwo rozpocząć pracę. Gesty są widoczne po kliknięciu prawym przyciskiem myszy kontenera Azure Cosmos DB na karcie **dane** w obszarze roboczym Synapse. Za pomocą gestów można szybko wygenerować kod i dostosować go do własnych potrzeb. Gesty są również idealne do odnajdywania danych jednym kliknięciem.

> [!IMPORTANT]
> Należy pamiętać o niektórych ograniczeniach w schemacie analitycznym, które mogą prowadzić do nieoczekiwanego zachowania w operacjach ładowania danych.
> Na przykład w schemacie analitycznym są dostępne tylko pierwsze 1000 właściwości ze schematu transakcyjnego, właściwości ze spacjami są niedostępne itd. Jeśli występują pewne nieoczekiwane wyniki, sprawdź [ograniczenia schematu magazynu analitycznego](../../cosmos-db/analytical-store-introduction.md#schema-constraints) , aby uzyskać więcej szczegółów.

## <a name="query-azure-cosmos-db-analytical-store"></a>Zapytanie Azure Cosmos DB magazyn analityczny

Przed zapoznaj się z dwoma możliwymi opcjami wykonywania zapytań dotyczących magazynu analitycznego Azure Cosmos DB, ładowania do usługi Spark Dataframe i tworzenia tabeli Spark, warto zapoznać się z różnicami w działaniu, aby wybrać opcję, która jest odpowiednia dla Twoich potrzeb.

Różnica w działaniu polega na tym, czy zmiany danych źródłowych w kontenerze Azure Cosmos DB powinny być automatycznie odzwierciedlane w analizie wykonywanej na platformie Spark. Gdy jest zarejestrowana ramka Dataframe lub tworzona jest tabela Spark dla magazynu analitycznego kontenera, metadane dotyczące bieżącej migawki danych w magazynie analitycznym są pobierane do platformy Spark w celu wydajnego przekazywania kolejnej analizy. Należy pamiętać, że ponieważ platforma Spark korzysta z zasad oceny z opóźnieniem, chyba że akcja jest wywoływana w ramce Dataframe lub SparkSQL zapytanie jest wykonywane względem tabeli Spark, rzeczywiste dane nie są pobierane z magazynu analitycznego kontenera źródłowego.

W przypadku **ładowania do ramki danych Spark** pobrane metadane są buforowane przez okres istnienia sesji Spark. Tym samym kolejne akcje wywoływane dla ramki danych są oceniane względem migawki magazynu analitycznego z czasu utworzenia ramki danych.

Z drugiej strony w przypadku **utworzenia tabeli Spark** metadane stanu magazynu analitycznego nie są buforowane na platformie Spark i są ponownie ładowane przy każdym wykonaniu zapytania SparkSQL względem tabeli Spark.

W związku z tym możesz wybrać między ładowaniem do ramki danych Spark i utworzeniem tabeli Spark w zależności od tego, czy analiza platformy Spark ma być oceniana względem, odpowiednio, stałej migawki magazynu analitycznego, czy najnowszej migawki magazynu analitycznego.

> [!NOTE]
> Aby wykonać zapytanie dotyczące Azure Cosmos DBgo interfejsu API kont usługi Mongo DB, Dowiedz się więcej o [pełnej reprezentacji schematu](../../cosmos-db/analytical-store-introduction.md#analytical-schema) w magazynie analitycznym i nazwach właściwości rozszerzonych, które mają być używane.

### <a name="load-to-spark-dataframe"></a>Załaduj do platformy Spark Dataframe

W tym przykładzie utworzysz ramkę danych Spark, która wskazuje na Azure Cosmos DB magazyn analityczny. Następnie można wykonać dodatkową analizę, wywołując działania platformy Spark względem ramki Dataframe. Ta operacja nie ma wpływu na magazyn transakcyjny.

Składnia w języku **Python** będzie następująca:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

Równoważna składnia w **Scala** będzie następująca:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Utwórz tabelę platformy Spark

W tym przykładzie utworzysz tabelę platformy Spark, która wskazuje Azure Cosmos DB magazyn analityczny. Następnie można wykonać dodatkową analizę, wywołując zapytania SparkSQL względem tabeli. Ta operacja nie ma wpływu na magazyn transakcyjny ani nie wiąże się z jakimkolwiek przenoszeniem danych. Jeśli zdecydujesz się usunąć tę tabelę platformy Spark, nie wpłynie to na kontener źródłowy Azure Cosmos DB i odpowiedni magazyn analityczny. 

Ten scenariusz jest wygodne do ponownego użycia tabel platformy Spark za pomocą narzędzi innych firm i zapewnienia dostępności danych źródłowych dla czasu wykonywania.

Składnia służąca do tworzenia tabeli Spark jest następująca:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> W scenariuszach, w których schemat podstawowego kontenera usługi Azure Cosmos DB zmienia się i chcesz, aby zaktualizowany schemat był automatycznie odzwierciedlany w zapytaniach względem tabeli Spark, możesz w tym celu ustawić opcję `spark.cosmos.autoSchemaMerge` na wartość `true` w opcjach tabeli Spark.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Zapisz ramkę danych Spark w kontenerze Azure Cosmos DB

W tym przykładzie napiszesz ramkę Spark do kontenera Azure Cosmos DB. Ta operacja będzie mieć wpływ na wydajność obciążeń transakcyjnych i zużywać jednostki żądań obsługiwane w kontenerze Azure Cosmos DB lub udostępnionej bazie danych.

Składnia w języku **Python** będzie następująca:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Równoważna składnia w **Scala** będzie następująca:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Załaduj ramkę danych przesyłania strumieniowego z kontenera
W tym gestie użyjesz funkcji przesyłania strumieniowego Spark do ładowania danych z kontenera do ramki danych. Dane będą przechowywane w podstawowym koncie Data Lake (i w systemie plików) podłączonym do obszaru roboczego. 
> [!NOTE]
> Jeśli zamierzasz odwoływać się do bibliotek zewnętrznych w Synapse Apache Spark, Dowiedz się więcej [tutaj](#external-library-management). Na przykład jeśli chcesz pozyskać ramkę danych Spark do kontenera Cosmos DB API for Mongo DB, możesz skorzystać z łącznika usługi Mongo DB dla platformy Spark [tutaj](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Załaduj ramkę danych przesyłania strumieniowego z kontenera Azure Cosmos DB
W tym przykładzie użyjesz funkcji przesyłania strumieniowego platformy Spark w celu załadowania danych z kontenera Azure Cosmos DB do ramki datastreaming strumienia przy użyciu funkcji zmiany źródła w Azure Cosmos DB. Dane punktu kontrolnego używane przez platformę Spark będą przechowywane w podstawowym koncie Data Lake (i systemie plików), które zostało połączone z obszarem roboczym.

Jeśli folder */localReadCheckpointFolder* nie zostanie utworzony (w poniższym przykładzie) zostanie automatycznie utworzony. Ta operacja będzie mieć wpływ na wydajność transakcyjnych obciążeń i zużywać jednostki żądań obsługiwane w kontenerze Azure Cosmos DB lub udostępnionej bazie danych.

Składnia w języku **Python** będzie następująca:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Równoważna składnia w **Scala** będzie następująca:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Zapisz ramkę danych przesyłania strumieniowego do kontenera Azure Cosmos DB
W tym przykładzie napiszesz ramkę danych przesyłania strumieniowego do kontenera Azure Cosmos DB. Ta operacja będzie mieć wpływ na wydajność transakcyjnych obciążeń i zużywać jednostki żądań obsługiwane w kontenerze Azure Cosmos DB lub udostępnionej bazie danych. Jeśli folder */localWriteCheckpointFolder* nie zostanie utworzony (w poniższym przykładzie) zostanie automatycznie utworzony. 

Składnia w języku **Python** będzie następująca:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Równoważna składnia w **Scala** będzie następująca:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Zarządzanie biblioteką zewnętrzną

W tym przykładzie dowiesz się, jak odwoływać się do bibliotek zewnętrznych z plików JAR podczas korzystania z notesów platformy Spark w obszarach roboczych Synpase Apache Spark. Pliki JAR można umieścić w kontenerze na podstawowym koncie Data Lake połączonym z obszarem roboczym, a następnie dodać następujące `%configure` instrukcje w notesie platformy Spark:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Jeśli chcesz przesłać definicje zadań zdalnej platformy Spark do puli bezserwerowej Apache Spark, możesz dowiedzieć się, jak odwoływać się do bibliotek zewnętrznych, wykonując czynności opisane w tym [samouczku](../spark/apache-spark-job-definitions.md).

## <a name="next-steps"></a>Następne kroki

* [Przykłady umożliwiające rozpoczęcie pracy z usługą Azure Synapse link w serwisie GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Dowiedz się, co jest obsługiwane w usłudze Azure Synapse link dla Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Połącz z linkiem Synapse dla Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
