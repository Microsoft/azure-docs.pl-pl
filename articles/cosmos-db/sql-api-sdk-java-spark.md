---
title: Łącznik Cosmos DB Apache Spark dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat łącznika Azure Cosmos DB Apache Spark dla interfejsu API SQL, w tym dat wydania, dat wycofania i zmian wprowadzonych między poszczególnymi wersjami Azure Cosmos DB usługi SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: afee95f6a8776c3506e10c29cfd8e776734a915a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326681"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark łącznik dla programu Core (SQL) API: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zmiany źródła .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Wiosenne dane v2](sql-api-sdk-java-spring-v2.md)
> * [Wiosenne dane v3](sql-api-sdk-java-spring-v3.md)
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Można przyspieszyć analizę danych Big Data, korzystając z łącznika Azure Cosmos DB Apache Spark for Core (SQL). Łącznik platformy Spark umożliwia uruchamianie zadań [platformy Spark](https://spark.apache.org/) na danych przechowywanych w Azure Cosmos DB. Przetwarzanie wsadowe i przesyłanie strumieniowe są obsługiwane.

Możesz użyć łącznika z usługą [Azure Databricks](https://azure.microsoft.com/services/databricks) lub [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), która udostępnia zarządzane klastry platformy Spark na platformie Azure. W poniższej tabeli przedstawiono obsługiwane wersje:

| Składnik | Wersja |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x*i 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (wersja środowiska uruchomieniowego) | Później niż 3,4 |

> [!WARNING]
> Ten łącznik obsługuje podstawowy interfejs API (SQL) Azure Cosmos DB.
> Dla Cosmos DB API for MongoDB Użyj [łącznika MongoDB dla platformy Spark](https://docs.mongodb.com/spark-connector/master/).
> Dla interfejs API Cassandra Cosmos DB Użyj [łącznika Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Zasoby

| Zasób | Łącze |
|---|---|
| **Pobieranie zestawu SDK** | [Pobierz najnowszą wersję. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Dokumentacja interfejsu API** | [Odwołanie do łącznika Spark]() |
|**Współtworzenie zestawu SDK** | [Łącznik Azure Cosmos DB dla Apache Spark w witrynie GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Wprowadzenie** | [Przyspieszenie analizy danych Big Data przy użyciu Apache Spark do Azure Cosmos DB łącznika](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Używanie Apache Spark strukturalnych przesyłania strumieniowego z Apache Kafka i Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historia wersji

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nowe funkcje
- Dodaje nową opcję konfiguracji, `changefeedstartfromdatetime` która może służyć do określania czasu rozpoczęcia, dla którego ma zostać przetworzony changefeed. Aby uzyskać więcej informacji, zobacz [Opcje konfiguracji](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Poprawki klucza
- Naprawia regresję, która spowodowała nadmierne zużycie pamięci w modułach wykonujących dla dużych zestawów wyników (na przykład z milionami wierszy), co jest wynikiem błędu `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje przypadek graniczny punktu kontrolnego przesyłania strumieniowego, w którym `ID` znajduje się znak potoku (|) z `ChangeFeedMaxPagesPerBatch` zastosowanym konfiguracją.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nowe funkcje
* Dodaje obsługę aktualizacji zbiorczych w przypadku używania kluczy partycji zagnieżdżonej.
* Dodaje obsługę typów danych dziesiętnych i zmiennoprzecinkowych podczas zapisu do Azure Cosmos DB.
* Dodaje obsługę typów sygnatur czasowych, jeśli jako wartość jest używana długa (Epoka systemu UNIX).

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia wyjątek rzutowanie, który występuje, gdy `WriteThroughputBudget` Konfiguracja jest używana.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nowe funkcje
* Dodaje informacje o błędzie dla błędów zbiorczych do wyjątku i dziennika.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje problemy z punktem kontrolnym przesyłania strumieniowego.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Aby zmniejszyć szum, poprawki poziomu dziennika wiadomości pozostawione przypadkowo z powodu błędu poziomu.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia usterkę w przypadku przesyłania strumieniowego ze strukturą podczas dzielenia partycji. Usterka może spowodować brak niektórych rekordów kanału informacyjnego zmian lub wyjątków o wartości null dla operacji zapisu punktów kontrolnych.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia usterkę, która powoduje ignorowanie niestandardowego schematu dostarczonego dla readStream.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawki regresji (odcienie JAR zawiera wszystkie zacieniowane zależności), które zwiększają czas kompilacji o 50 procent.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje problem zależności, który powoduje, że bezpośredni transport za pośrednictwem protokołu TCP kończy się niepowodzeniem z RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nowe funkcje
* Usprawnia zarządzanie połączeniami i pule połączeń, aby zmniejszyć liczbę wywołań metadanych.

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej o platformie [Apache Spark](https://spark.apache.org/).
