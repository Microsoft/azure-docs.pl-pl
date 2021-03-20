---
title: Kopiuj łącze Synapse Azure Cosmos DB danych do dedykowanej puli SQL przy użyciu Apache Spark
description: Załaduj dane do ramki danych platformy Spark, zanadzoruj dane i załaduj je do dedykowanej tabeli puli SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 13891f9614e658be39adbb69fed1503a0c66d5e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309214"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Kopiowanie danych z Azure Cosmos DB do dedykowanej puli SQL przy użyciu Apache Spark

Link Synapse platformy Azure dla Azure Cosmos DB umożliwia użytkownikom uruchamianie analizy niemal w czasie rzeczywistym za pośrednictwem danych operacyjnych w Azure Cosmos DB. Istnieją jednak przypadki, w których niektóre dane muszą zostać zagregowane i wzbogacone w celu umożliwienia użytkownikom hurtowni danych. Opieka i eksportowanie danych łącza Synapse można wykonać za pomocą zaledwie kilku komórek w notesie.

## <a name="prerequisites"></a>Wymagania wstępne
* [Zainicjuj obsługę obszaru roboczego Synapse](../quickstart-create-workspace.md) przy użyciu:
    * [Bezserwerowa Pula Apache Spark](../quickstart-create-apache-spark-pool-studio.md)
    * [dedykowana Pula SQL](../quickstart-create-sql-pool-studio.md)
* [Inicjowanie obsługi konta Cosmos DB z użyciem kontenera HTAP z danymi](../../cosmos-db/configure-synapse-link.md)
* [Łączenie kontenera Azure Cosmos DB HTAP z obszarem roboczym](./how-to-connect-synapse-link-cosmos-db.md)
* [Skonfigurowanie odpowiedniej konfiguracji w celu zaimportowania danych do dedykowanej puli SQL z platformy Spark](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Kroki
W ramach tego samouczka nawiążesz połączenie z magazynem analitycznym, więc nie ma to wpływu na magazyn transakcyjny (nie zużywa żadnych jednostek żądania). Przejdziemy przez następujące kroki:
1. Odczytywanie kontenera Cosmos DB HTAP w ramce Dataframe platformy Spark
2. Agreguj wyniki w nowej ramce Dataframe
3. Pozyskiwanie danych w dedykowanej puli SQL

[![Kroki z platformy Spark do SQL 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Dane
W tym przykładzie używamy kontenera HTAP o nazwie **RetailSales**. Jest to część połączonej usługi o nazwie **ConnectedData** i ma następujący schemat:
* _rid: ciąg (nullable = true)
* _ts: Long (nullable = true)
* logQuantity: Double (nullable = true)
* productCode: ciąg (nullable = true)
* ilość: Long (nullable = true)
* Cena: Long (nullable = true)
* ID: ciąg (nullable = true)
* Reklama: Long (nullable = true)
* storeId: Long (nullable = true)
* weekStarting: Long (nullable = true)
* _etag: ciąg (nullable = true)

Będziemy agregować sprzedaż (*ilość*, *przychód* (cena x ilość) według *productCode* i *weekStarting* na potrzeby raportowania. Na koniec będziemy eksportować te dane do dedykowanej tabeli puli SQL o nazwie **dbo. ProductSales**.

## <a name="configure-a-spark-notebook"></a>Konfigurowanie notesu platformy Spark
Utwórz Notes Spark z Scala jako Spark (Scala) jako język główny. Używamy domyślnego ustawienia notesu dla sesji.

## <a name="read-the-data-in-spark"></a>Odczytywanie danych w usłudze Spark
Odczytaj kontener Cosmos DB HTAP z platformą Spark do ramki Dataframe w pierwszej komórce.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Agreguj wyniki w nowej ramce Dataframe

W drugiej komórce uruchamiamy transformację i agregacje niezbędne dla nowej ramki danych przed załadowaniem jej do dedykowanej usługi puli SQL.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>Załaduj wyniki do dedykowanej puli SQL

W trzeciej komórce ładujemy dane do dedykowanej puli SQL. Spowoduje to automatyczne utworzenie tymczasowej tabeli zewnętrznej, zewnętrznego źródła danych i zewnętrznego formatu pliku, który zostanie usunięty po zakończeniu zadania.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Wykonywanie zapytań dotyczących wyników przy użyciu języka SQL

Wyniki można badać przy użyciu prostego zapytania SQL, takiego jak poniższy skrypt SQL:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Zapytanie będzie przedstawiać następujące wyniki w trybie wykresu: kroki od [ ![ Spark do SQL 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Następne kroki
* [Zapytanie Azure Cosmos DB magazyn analityczny z Apache Spark](./how-to-query-analytical-store-spark.md)