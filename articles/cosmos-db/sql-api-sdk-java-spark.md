---
title: Łącznik Azure Cosmos DB Apache Spark dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat łącznika Azure Cosmos DB Apache Spark dla interfejsu API SQL, w tym dat wydania, dat wycofania i zmian wprowadzonych między poszczególnymi wersjami Azure Cosmos DB usługi SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590013"
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

Przyspieszenie analizy danych Big Data za pomocą łącznika Azure Cosmos DB Apache Spark for Core (SQL). Łącznik platformy Spark umożliwia uruchamianie zadań [platformy Spark ](https://spark.apache.org/) na danych przechowywanych w Azure Cosmos DB. Przetwarzanie wsadowe i przesyłanie strumieniowe są obsługiwane.

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

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Łącze |
|---|---|
| **Pobieranie zestawu SDK** | [Pobierz z Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Dokumentacja interfejsu API** | [Odwołanie do łącznika Spark]() |
|**Współtworzenie zestawu SDK** | [Łącznik Azure Cosmos DB dla Apache Spark w witrynie GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Wprowadzenie** | [Przyspieszenie analizy danych Big Data przy użyciu Apache Spark do Azure Cosmos DB łącznika](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Używanie Apache Spark strukturalnych przesyłania strumieniowego z Apache Kafka i Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Historia wersji

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje przypadek graniczny punktu kontrolnego przesyłania strumieniowego, gdzie w "ID" znajduje się znak "|" z zastosowanym konfiguracją "ChangeFeedMaxPagesPerBatch"

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nowe funkcje
* Dodaje obsługę aktualizacji zbiorczych podczas korzystania z zagnieżdżonych kluczy partycji
* Dodaje obsługę typów danych dziesiętnych i zmiennoprzecinkowych podczas zapisu do Cosmos DB.
* Dodaje obsługę typów sygnatur czasowych, gdy jako wartość jest używana długa (Epoka systemu UNIX)
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia wyjątek rzutowania typu podczas korzystania z konfiguracji "WriteThroughputBudget".

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nowe funkcje
* Dodaje informacje o błędzie dla błędów zbiorczych do wyjątku i dziennika.
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje problemy z punktem kontrolnym przesyłania strumieniowego.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawianie poziomu dziennika wiadomości pozostawionej przypadkowo z BŁĘDem poziomu w celu zmniejszenia szumu

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia usterkę w przypadku przesyłania strumieniowego ze strukturą podczas dzielenia partycji — prawdopodobnie w wyniku braku niektórych rekordów źródła zmian lub zobaczenie wyjątków o wartości null dla operacji zapisywania punktów kontrolnych

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawia usterkę, gdy niestandardowy schemat dostarczony dla readStream jest ignorowany

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawki regresji (odcienie JAR zawierają wszystkie zacienione zależności), które zwiększyły czas kompilacji o 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązuje problem zależności powodujący bezpośredni transport przez TCP, aby zakończyć się niepowodzeniem z RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nowe funkcje
* Usprawnia zarządzanie połączeniami i pule połączeń, aby zmniejszyć liczbę wywołań metadanych
#### <a name="key-bug-fixes"></a>Poprawki klucza

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Aby dowiedzieć się więcej na temat Apache Spark, zobacz [stronę główną](https://spark.apache.org/).