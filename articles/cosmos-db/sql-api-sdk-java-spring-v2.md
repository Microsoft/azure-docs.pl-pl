---
title: Dokumentacja usługi Spring Data Azure Cosmos DB 2 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o interfejsie API Spring Data Azure Cosmos DB v2 for SQL, w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu AZURE COSMOS DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4cd92efb7b6596288e4b6ef8e81f82f775a24e01
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363448"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Interfejs API spring Data Azure Cosmos DB v2 for Core (SQL): informacje o wersji i zasoby
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

 Program Spring Data Azure Cosmos DB w wersji 2 dla bazy danych Core (SQL) umożliwia deweloperom korzystanie Azure Cosmos DB w aplikacjach Spring. Interfejs Spring Data Azure Cosmos DB udostępnia interfejs Spring Data do manipulowania bazami danych i kolekcjami, pracy z dokumentami i wystawiania zapytań. Interfejsy API synchronizacji i asynchronicznej (reaktywnej) są obsługiwane w tym samym artefaktze maven. 

Spring [Framework to](https://spring.io/projects/spring-framework) model programowania i konfiguracji, który usprawnia tworzenie aplikacji w języku Java. Aplikacja Spring usprawnia "wychylianie" aplikacji przy użyciu wstrzykiwania zależności. Wielu deweloperów, takich jak Spring, sprawia, że tworzenie i testowanie aplikacji jest prostsze. [Spring Boot](https://spring.io/projects/spring-boot) rozszerza tę obsługę instalacji sieciowej, mając na celu opracowywanie aplikacji internetowej i mikrousług. [Spring Data](https://spring.io/projects/spring-data) to model programowania do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu aplikacji Spring Spring Boot aplikacji. 

Możesz użyć aplikacji Spring Data Azure Cosmos DB w swoich [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) aplikacjach.

> [!IMPORTANT]  
> Te informacje o wersji są dla wersji 2 programu Spring Data Azure Cosmos DB. Informacje o wersji [dla wersji 3](sql-api-sdk-java-spring-v3.md)można znaleźć tutaj. 
>
> Usługa Spring Data Azure Cosmos DB obsługuje tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje o danych spring w innych Azure Cosmos DB API:
> * [Spring Data for Apache Cassandra z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Baza danych MongoDB spring data z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Chcesz szybko rozpocząć?
> 1. Zainstaluj minimalne [obsługiwane środowisko uruchomieniowe Java ( JDK 8),](/java/azure/jdk/)aby można było korzystać z zestawu SDK.
> 2. Utwórz aplikację Spring Data Azure Cosmos DB przy użyciu [początkowego .](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) To proste!
> 3. Aby przejść przez przewodnik dewelopera dotyczący Azure Cosmos DB [Spring Data,](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)który zawiera podstawowe Azure Cosmos DB żądania.
>
> Możesz szybko Spring Boot starter przy użyciu [narzędzia Spring Initializr!](https://start.spring.io/)
>

## <a name="resources"></a>Zasoby

| Zasób | Link |
|---|---|
| **Zestaw SDK do pobrania** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Dokumentacja interfejsu API** | [Dokumentacja referencyjna Azure Cosmos DB Spring Data]() |
|**Współtwomentuj zestaw SDK** | [Repozytorium Spring Data Azure Cosmos DB w witrynie GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Azure Cosmos DB Spring Boot klienta Starter dla języka Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Przykład aplikacji Spring TODO z Azure Cosmos DB**| [End-to-end Java Experience in App Service Linux (Part 2) (End-to-end Java Experience in App Service Linux (Część 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Przewodnik dewelopera** | [Przewodnik dewelopera usługi Azure Cosmos DB w rozwiązaniu Spring Data](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Korzystanie z pakietu startowego** | [Jak używać programu Spring Boot Starter z interfejsem API AZURE COSMOS DB SQL](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repozytorium GitHub dla Azure Cosmos DB Spring Boot Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Przykład z Azure App Service** | [Jak używać platformy Spring i usługi Cosmos DB z usługą App Service w systemie Linux](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Przykład aplikacji TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historia wersji

### <a name="230-may-21-2020"></a>2.3.0 (21 maja 2020 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizacje Spring Boot wersji do 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (19 maja 2020 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizacje Azure Cosmos DB wersji 3.7.3.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Zawiera poprawki przecieku pamięci i uaktualnienia wersji Netty z Azure Cosmos DB SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 kwietnia 2020 r.)
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Flaga `allowTelemetry` poprawek do uwzględnienia z `CosmosDbConfig` .
* Poprawiono `TTL` właściwość w kontenerze.

### <a name="223-february-25-2020"></a>2.2.3 (25 lutego 2020 r.)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje nowy interfejs `findAll` API klucza partycji.
* Aktualizacje Azure Cosmos DB wersji do 3.7.0.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Poprawki `collectionName`  ->  `containerName` .
* Poprawki `entityClass` i `domainClass`  ->  `domainType` .
* Poprawiono "Zwracanie kolekcji jednostek zapisanych przez repozytorium zamiast jednostki wejściowej".

### <a name="2110-february-25-2020"></a>2.1.10 (25 lutego 2020 r.)
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Poprawka "Zwracanie kolekcji jednostek zapisanych przez repozytorium zamiast jednostki wejściowej"

### <a name="222-january-15-2020"></a>2.2.2 (15 stycznia 2020 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizacje Azure Cosmos DB wersji do 3.6.0.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek

### <a name="221-december-31-2019"></a>2.2.1 (31 grudnia 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizacje Azure Cosmos DB SDK do wersji 3.5.0.
* Dodaje pole adnotacji, aby włączyć lub wyłączyć automatyczne tworzenie kolekcji.
* Ulepsza obsługę wyjątków. Uwidacznia `CosmosClientException` za pośrednictwem `CosmosDBAccessException` .
* Uwidacznia `requestCharge` i `activityId` za pośrednictwem `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Aktualizacja zestawu SDK 3.5.0 naprawia "Wyjątek, gdy nagłówek odpowiedzi HTTP usługi Cosmos DB jest większy niż 8192 bajty", "ConsistencyPolicy.defaultConsistencyLevel() fails on Bounded Staleness and Consistent Prefix".
* Naprawia `findById` zachowanie metody. Wcześniej ta metoda zwracała pustą, jeśli jednostka nie została znaleziona, zamiast zgłaszać wyjątek.
* Usunięto usterkę, w której sortowanie nie było stosowane na następnej stronie, gdy `CosmosPageRequest` było używane.

### <a name="219-december-26-2019"></a>2.1.9 (26 grudnia 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje pole adnotacji, aby włączyć lub wyłączyć automatyczne tworzenie kolekcji.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
*  Naprawia `findById` zachowanie metody. Wcześniej ta metoda zwracała pustą, jeśli jednostka nie została znaleziona, zamiast zgłaszać wyjątek.

### <a name="220-october-21-2019"></a>2.2.0 (21 października 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Pełna obsługa reaktywnego repozytorium Cosmos.
* Azure Cosmos DB żądania obsługi ciągów diagnostycznych i metryk zapytań.
* Azure Cosmos DB zestawu SDK do wersji 3.3.1.
* Uaktualnienie wersji programu Spring Framework do wersji 5.2.0.RELEASE.
* Uaktualnienie wersji spring Data Commons do wersji 2.2.0.RELEASE.
* Dodaje `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` interfejsy API i .
* Usuwa zależność z polecenia azure-documentdb.
* Zmiany marki bazy danych DocumentDB na Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Naprawiono "Sortowanie zgłasza wyjątek, gdy wartość pageSize jest mniejsza niż łączna liczba elementów w repozytorium".

### <a name="218-october-18-2019"></a>2.1.8 (18 października 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Oznacza interfejsy API bazy danych DocumentDB jako przestarzałe.
* Dodaje `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` interfejsy API i .
* Dodaje optymistyczne blokowanie na podstawie `_etag` .
* Włącza wyrażenie SpEL dla nazwy kolekcji dokumentów.
* Dodaje `ObjectMapper` ulepszenia.

### <a name="217-october-18-2019"></a>2.1.7 (18 października 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje Azure Cosmos DB zestawu SDK w wersji 3.
* Dodaje reaktywne repozytorium Cosmos.
* Aktualizacja implementacji programu `DocumentDbTemplate` w celu używania Azure Cosmos DB SDK w wersji 3.
* Dodaje inne zmiany konfiguracji dotyczące obsługi reaktywnego repozytorium Cosmos.

### <a name="212-march-19-2019"></a>2.1.2 (19 marca 2019 r.)
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Usuwa `applicationInsights` zależność dla:
    * Potencjalne ryzyko wystąpienia zależności.
    * Niezgodność środowiska Java 11.
    * Unikanie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="207-march-20-2019"></a>2.0.7 (20 marca 2019 r.)
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Backport usuwa `applicationInsights` zależność dla:
    * Potencjalne ryzyko wystąpienia zależności.
    * Niezgodność środowiska Java 11.
    * Unikanie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="211-march-7-2019"></a>2.1.1 (7 marca 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje wersję główną do wersji 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 marca 2019 r.)
#### <a name="new-features"></a>Nowe funkcje
* Ignoruj wszystkie wyjątki z telemetrii.

### <a name="210-december-17-2018"></a>2.1.0 (17 grudnia 2018 r.)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje wersję do 2.1.0, aby rozwiązać problem.

### <a name="205-september-13-2018"></a>2.0.5 (13 września 2018 r.)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje słowa kluczowe `exists` i `startsWith` .
* Updates Readme.
#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Poprawki "Nie można wywołać własnego href bezpośrednio dla jednostki".
* Poprawki "findAll will fail if collection is not created".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (wstępna) (23 sierpnia 2018 r.)
#### <a name="new-features"></a>Nowe funkcje
* Zmienia nazwę pakietu z documentdb na cosmosdb.
* Dodaje nową funkcję słowa kluczowego metody zapytania. Obsługiwanych jest teraz 16 słów kluczowych z interfejsu API SQL.
* Dodano nową funkcję zapytania z funkcją stronicowania i sortowania.
* Upraszcza konfigurację spring-data-cosmosdb.
* Dodaje `deleteCollection` `deleteAll` interfejsy API i .

#### <a name="key-bug-fixes"></a>Poprawki kluczowych usterek
* Naprawa usterek i ograniczanie ryzyka błędów.

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej o [platformie Spring Framework.](https://spring.io/projects/spring-framework)

Dowiedz się więcej o [Spring Boot](https://spring.io/projects/spring-boot).

Dowiedz się więcej na [temat danych Spring Data.](https://spring.io/projects/spring-data)