---
title: Azure Cosmos DB Apache Spark 2 ŁĄCZNIK OLTP dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o łączniku OLTP Azure Cosmos DB Apache Spark 2 dla interfejsu API SQL, w tym o datach wydania, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK Java programu Azure Cosmos DB SQL Async.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: bd948814b4b647bcc3fbfe58b090b1e794504232
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363635"
---
# <a name="azure-cosmos-db-apache-spark-2-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark 2 ŁĄCZNIK OLTP dla podstawowego interfejsu API (SQL): informacje o wersji i zasoby
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zestawienia zmian platformy .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Spring Data 2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data 3](sql-api-sdk-java-spring-v3.md)
> * [Łącznik OLTP platformy Spark 3](sql-api-sdk-java-spark-v3.md)
> * [Łącznik OLTP platformy Spark 2](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

Analizę danych big data można przyspieszyć przy użyciu łącznika Azure Cosmos DB Apache Spark 2 OLTP dla bazy danych Core (SQL). Łącznik platformy Spark umożliwia uruchamianie zadań [spark na](https://spark.apache.org/) danych przechowywanych w Azure Cosmos DB. Obsługiwane jest przetwarzanie wsadowe i strumieniowe.

Łącznika można używać z [Azure Databricks](https://azure.microsoft.com/services/databricks) [lub Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), które zapewniają zarządzane klastry Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane wersje:

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2.4.*x*, 2.3. *x,* 2.2. *x*, i 2.1. *x (x)* |
| Scala | 2,11 |
| Azure Databricks (wersja środowiska uruchomieniowego) | Nowsze niż 3.4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowy interfejs API (SQL) Azure Cosmos DB.
> W przypadku Cosmos DB API dla bazy danych MongoDB użyj łącznika [bazy danych MongoDB dla platformy Spark.](https://docs.mongodb.com/spark-connector/master/)
> Na Cosmos DB interfejs API Cassandra użyj [łącznika Cassandra Spark.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="resources"></a>Zasoby

| Zasób | Link |
|---|---|
| **Zestaw SDK do pobrania** | [Pobierz najnowszy plik JAR,](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Dokumentacja interfejsu API** | [Informacje o łączniku platformy Spark]() |
|**Współtwomentuj zestaw SDK** | [Azure Cosmos DB Connector for Apache Spark w witrynie GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Wprowadzenie** | [Przyspieszanie analizy danych big data przy użyciu łącznika Apache Spark do Azure Cosmos DB danych](./spark-connector.md#bk_working_with_connector) <br> [Używanie Apache Spark przesyłania strumieniowego ze strukturą Apache Kafka i Azure Cosmos DB](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historia wersji

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nowe funkcje
- Dodaje nową opcję konfiguracji , która może służyć do określania czasu rozpoczęcia `changefeedstartfromdatetime` przetwarzania zmiany. Aby uzyskać więcej informacji, zobacz [Config options (Opcje konfiguracji).](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
- Naprawia regresję, która powodowała nadmierne zużycie pamięci w funkcji wykonawczej w przypadku dużych zestawów wyników (na przykład z milionami wierszy), co ostatecznie powodowało błąd `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Poprawiono przypadek krawędzi punktu kontrolnego przesyłania strumieniowego, w którym zawiera znak `ID` potoku (|) z `ChangeFeedMaxPagesPerBatch` zastosowaną konfiguracją.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nowe funkcje
* Dodaje obsługę aktualizacji zbiorczych w przypadku korzystania z zagnieżdżonych kluczy partycji.
* Dodaje obsługę typów danych dziesiętnych i zmiennoprzecinkowy podczas zapisu w Azure Cosmos DB.
* Dodaje obsługę typów znaczników czasu, gdy jako wartości są one używające długiej (epoki systemu Unix).

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Naprawia wyjątek emisji typu, który występuje, gdy `WriteThroughputBudget` używana jest konfiguracja.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nowe funkcje
* Dodaje informacje o błędach dla niepowodzeń zbiorczych do wyjątków i dziennika.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Rozwiązuje problemy z punktami kontrolnymi przesyłania strumieniowego.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Aby zmniejszyć szum, naprawiono poziom dziennika komunikatu pozostawiony w sposób niezamierzony z błędem poziomu.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Usunięto usterkę w strumieniu strukturalnym podczas dzielenia partycji. Usterka może spowodować brakujące rekordy zestawienia zmian lub wyjątki wartości null dla zapisów punktów kontrolnych.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Naprawiono usterkę, która powoduje ignorowanie schematu niestandardowego dostarczonego dla strumienia readStream.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Naprawia regresję (nieuchłoniony jar zawiera wszystkie zacienione zależności), która wydłuża czas kompilacji o 50 procent.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Rozwiązuje problem z zależnością, który powoduje niepowodzenie transportu bezpośredniego za pośrednictwem protokołu TCP z RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nowe funkcje
* Ulepsza zarządzanie połączeniami i pule połączeń, aby zmniejszyć liczbę wywołań metadanych.

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej o platformie [Apache Spark](https://spark.apache.org/).
