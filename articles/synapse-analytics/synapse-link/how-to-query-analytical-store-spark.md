---
title: Tworzenie zapytań dotyczących magazynu analitycznego Azure Cosmos DB przy użyciu Apache Spark usługi Azure Synapse Analytics
description: Jak badać Azure Cosmos DB analityczne za pomocą Apache Spark dla usługi Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 02d4b6a636bff5ef11686abba6efb52f45f04779
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83600069"
---
# <a name="query-azure-cosmos-db-analytical-store-with-apache-spark-for-azure-synapse-analytics"></a>Tworzenie zapytań dotyczących magazynu analitycznego Azure Cosmos DB przy użyciu Apache Spark usługi Azure Synapse Analytics

W tym artykule przedstawiono kilka przykładów, na których można korzystać z magazynu analitycznego z gestów Synapse. Gesty są widoczne po kliknięciu prawym przyciskiem myszy kontenera. Za pomocą gestów można szybko wygenerować kod i dostosować go do własnych potrzeb. Gesty są również idealne do odnajdywania danych za pomocą jednego kliknięcia.

## <a name="load-to-dataframe"></a>Załaduj do ramki Dataframe

W tym kroku odczytasz dane z Azure Cosmos DB magazynu analitycznego w ramce Dataframe platformy Spark. Zostanie wyświetlonych 10 wierszy z ramki danych o nazwie ***DF***. Po umieszczeniu danych w ramce Dataframe można przeprowadzić dodatkową analizę.

Ta operacja nie ma wpływu na magazyn transakcyjny.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Odpowiednikiem gestu odpowiadającego kodowi w **Scala** byłby następujący kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Utwórz tabelę platformy Spark

W tym gestie utworzysz tabelę Spark wskazującą wybrany kontener. Ta operacja nie wiąże się z przenoszeniem danych. Jeśli zdecydujesz się usunąć tę tabelę, nie będzie to miało wpływu na kontener źródłowy (i odpowiedni magazyn analityczny). 

Ten scenariusz jest wygodne do ponownego użycia tabel za pomocą narzędzi innych firm i zapewnienia dostępności danych dla czasu wykonywania.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Zapisz ramkę danych do kontenera

W tym gestie napiszesz ramkę danych do kontenera. Ta operacja będzie miała wpływ na wydajność transakcji i zużywać jednostki żądań. Używanie Azure Cosmos DB transakcyjnej wydajności jest idealnym rozwiązaniem w przypadku transakcji zapisu. Upewnij się, że zastąpisz **YOURDATAFRAME** przez ramkę danych, w której chcesz pisać z powrotem.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Odpowiednikiem gestu odpowiadającego kodowi w **Scala** byłby następujący kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Załaduj ramkę danych przesyłania strumieniowego z kontenera
W tym gestie użyjesz funkcji przesyłania strumieniowego Spark do ładowania danych z kontenera do ramki danych. Dane będą przechowywane w podstawowym koncie Data Lake (i w systemie plików) połączonym z obszarem roboczym. 

Jeśli folder */localReadCheckpointFolder* nie zostanie utworzony, zostanie automatycznie utworzony. Ta operacja będzie miała wpływ na wydajność transakcyjne Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Odpowiednikiem gestu odpowiadającego kodowi w **Scala** byłby następujący kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Zapisz ramkę danych przesyłania strumieniowego do kontenera
W tym gestie napiszesz ramkę danych przesyłania strumieniowego do wybranego kontenera Azure Cosmos DB. Jeśli folder */localReadCheckpointFolder* nie zostanie utworzony, zostanie automatycznie utworzony. Ta operacja będzie miała wpływ na wydajność transakcyjne Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Odpowiednikiem gestu odpowiadającego kodowi w **Scala** byłby następujący kod:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, co jest obsługiwane między Synapse i Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Połącz z linkiem Synapse dla Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
