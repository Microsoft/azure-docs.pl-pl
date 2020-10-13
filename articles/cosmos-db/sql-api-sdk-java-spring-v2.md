---
title: Dane wiosenne Azure Cosmos DB v2 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o danych wiosennych Azure Cosmos DB v2 dla interfejsu API SQL, w tym daty wydania, daty wycofania i zmiany dokonane między każdą wersją Azure Cosmos DB asynchronicznej wersji zestawu Java SDK programu SQL Server.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d5a906e12df14c6c3ffec704310bbede742ad841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760829"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Dane wiosenne Azure Cosmos DB v2 for Core (SQL) API: informacje o wersji i zasoby
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

 Dane wiosenne Azure Cosmos DB wersja 2 dla rdzenia (SQL) umożliwiają deweloperom korzystanie z Azure Cosmos DB w aplikacjach wiosennych. Dane wiosenne Azure Cosmos DB uwidaczniają interfejs danych sprężyny do manipulowania bazami plików i kolekcjami, pracy z dokumentami i wystawiania zapytań. W tym samym artefaktie Maven są obsługiwane zarówno interfejsy API synchronizacji, jak i asynchroniczne (reaktywne). 

[Platforma wiosny](https://spring.io/projects/spring-framework) to model programowania i konfiguracji, który usprawnia Programowanie aplikacji Java. Sprężyna upraszcza "instalację wodociągową" aplikacji przy użyciu iniekcji zależności. Wielu programistów, takich jak wiosenne, sprawia, że tworzenie i testowanie aplikacji jest bardziej proste. [Rozruch sprężynowy](https://spring.io/projects/spring-boot) rozszerza ten sposób obsługi instalacji wodociągowej z oczami na rozwój aplikacji sieci Web i mikrousług. [Dane sprężynowe](https://spring.io/projects/spring-data) to model programistyczny umożliwiający uzyskiwanie dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu sieci sprężynowej lub sprężynowej. 

W aplikacjach w [chmurze ze sprężyną na platformie Azure](https://azure.microsoft.com/services/spring-cloud/) można używać danych wiosennych Azure Cosmos DB.

> [!IMPORTANT]  
> Te informacje o wersji są przeznaczone dla Azure Cosmos DB danych wiosennych w wersji 2. [Informacje o wersji 3](sql-api-sdk-java-spring-v3.md)można znaleźć tutaj. 
>
> Dane sprężynowe Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje na temat sprężyny danych na innych Azure Cosmos DB interfejsach API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Chcesz szybko zacząć?
> 1. Zainstaluj [minimalny obsługiwany środowisko uruchomieniowe Java, JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true), aby można było użyć zestawu SDK.
> 2. Utwórz aplikację ze sprężyną danych Azure Cosmos DB przy użyciu [Starter](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). To proste!
> 3. Zapoznaj się z [przewodnikiem po programie dla danych sprężyny Azure Cosmos DB deweloperem](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb), który przeprowadzi Cię przez podstawowe żądania Azure Cosmos DB.
>
> Możesz szybko uruchamiać aplikacje z rozruchem wiosny przy użyciu [sprężyny Initializr](https://start.spring.io/)!
>

## <a name="resources"></a>Zasoby

| Zasób | Łącze |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Dokumentacja interfejsu API** | [Informacje o sprężyny Azure Cosmos DB dokumentacji]() |
|**Współtworzenie zestawu SDK** | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Rozruch z sprężyną Starter**| [Azure Cosmos DB wiosny startowej biblioteki klienckiej dla języka Java](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Przykład aplikacji ze sprężyną do zrobienia przy użyciu Azure Cosmos DB**| [Kompleksowe środowisko Java w systemie App Service Linux (część 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Przewodnik dewelopera** | [Przewodnik dewelopera usługi Azure Cosmos DB w rozwiązaniu Spring Data](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Przy użyciu Starter** | [Jak używać funkcji sprężynowego rozruchu Starter z interfejsem API SQL Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repozytorium GitHub dla Azure Cosmos DB sprężynowego rozruchu Starter](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Przykład z Azure App Service** | [Jak używać platformy Spring i usługi Cosmos DB z usługą App Service w systemie Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Przykład aplikacji do zrobienia](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historia wersji

### <a name="230-may-21-2020"></a>2.3.0 (maj 21, 2020)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje wersję rozruchu sprężynowego do 2.3.0.


### <a name="225-may-19-2020"></a>2.2.5 (maj 19, 2020)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje Azure Cosmos DB wersji do 3.7.3.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Zawiera poprawki przecieków pamięci i uaktualnienia wersji z sieci na podstawie Azure Cosmos DB zestawu SDK 3.7.3.

### <a name="224-april-6-2020"></a>2.2.4 (6 kwietnia 2020)
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Usuwa `allowTelemetry` flagę, aby wziąć pod uwagę `CosmosDbConfig` .
* Naprawia `TTL` Właściwość w kontenerze.

### <a name="223-february-25-2020"></a>2.2.3 (25 lutego 2020)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje nowy `findAll` interfejs API kluczy partycji.
* Aktualizuje Azure Cosmos DB wersji do 3.7.0.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawki `collectionName`  ->  `containerName` .
* Poprawki `entityClass` i `domainClass`  ->  `domainType` .
* Poprawki "zwraca kolekcję jednostek zapisaną przez repozytorium zamiast jednostki wejściowej".

### <a name="2110-february-25-2020"></a>2.1.10 (25 lutego 2020)
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawka dla elementu "Reports Collection zapisana przez repozytorium zamiast jednostki wejściowej".

### <a name="222-january-15-2020"></a>2.2.2 (15 stycznia 2020)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje Azure Cosmos DB wersji do 3.6.0.
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="221-december-31-2019"></a>2.2.1 (31 grudnia 2019)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje Azure Cosmos DB wersja zestawu SDK do 3.5.0.
* Dodaje pole adnotacji do włączania lub wyłączania automatycznego tworzenia kolekcji.
* Poprawia obsługę wyjątków. Uwidacznia `CosmosClientException` `CosmosDBAccessException` .
* Uwidacznia `requestCharge` i `activityId` w programie `ResponseDiagnostics` .
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawka 3.5.0 aktualizacji zestawu SDK "wyjątek, gdy Cosmos DB nagłówek odpowiedzi HTTP jest większy niż 8192 bajtów" "ConsistencyPolicy. defaultConsistencyLevel () nie powiodło się w powiązanym nieaktualnym i spójnym prefiksie".
* Poprawia `findById` zachowanie metody. Wcześniej ta metoda zwróciła wartość pustą, jeśli nie znaleziono jednostki zamiast zgłaszać wyjątek.
* Naprawia usterkę, w której sortowanie nie zostało zastosowane na następnej stronie, gdy `CosmosPageRequest` została użyta.

### <a name="219-december-26-2019"></a>2.1.9 (26 grudnia 2019)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje pole adnotacji do włączania lub wyłączania automatycznego tworzenia kolekcji.
#### <a name="key-bug-fixes"></a>Poprawki klucza
*  Poprawia `findById` zachowanie metody. Wcześniej ta metoda zwróciła wartość pustą, jeśli nie znaleziono jednostki zamiast zgłaszać wyjątek.

### <a name="220-october-21-2019"></a>2.2.0 (21 października 2019)
#### <a name="new-features"></a>Nowe funkcje
* Ukończ obsługę repozytorium Reactive Cosmos.
* Obsługa Azure Cosmos DB ciągu diagnostyki żądań i metryk zapytania.
* Aktualizacja wersji zestawu SDK Azure Cosmos DB do 3.3.1.
* Uaktualnienie wersji struktury wiosennej do 5.2.0. RELEASE.
* Uaktualnij wersję Commons Attribution wersji do 2.2.0. RELEASE.
* Dodaje `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` interfejsy API i.
* Usuwa zależność od platformy Azure-DocumentDB.
* Odmarki DocumentDB do Azure Cosmos DB.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawki "sortowanie zgłasza wyjątek, gdy pageSize jest mniejsza niż łączna liczba elementów w repozytorium".

### <a name="218-october-18-2019"></a>2.1.8 (18 października 2019)
#### <a name="new-features"></a>Nowe funkcje
* Oznacza, że interfejsy API DocumentDB są przestarzałe.
* Dodaje `findByIdAndPartitionKey` `deleteByIdAndPartitionKey` interfejsy API i.
* Dodaje optymistyczne blokowanie na podstawie `_etag` .
* Włącza wyrażenie SpEL dla nazwy kolekcji dokumentów.
* Dodaje `ObjectMapper` ulepszenia.

### <a name="217-october-18-2019"></a>2.1.7 (18 października 2019)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje zależność Azure Cosmos DB zestawu SDK w wersji 3.
* Dodaje repozytorium Reactive Cosmos.
* Aktualizuje implementację programu, `DocumentDbTemplate` Aby używać zestawu SDK Azure Cosmos DB w wersji 3.
* Dodaje inne zmiany konfiguracji obsługi repozytorium Reactive Cosmos.

### <a name="212-march-19-2019"></a>2.1.2 (19 marca 2019)
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Usuwa `applicationInsights` zależność dla:
    * Potencjalne ryzyko zanieczyszczenia zależności.
    * Niezgodność języka Java 11.
    * Uniknięcie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="207-march-20-2019"></a>2.0.7 (20 marca 2019)
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Backport usuwa `applicationInsights` zależność dla:
    * Potencjalne ryzyko zanieczyszczenia zależności.
    * Niezgodność języka Java 11.
    * Uniknięcie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="211-march-7-2019"></a>2.1.1 (7 marca 2019)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje wersję główną do 2.1.1.

### <a name="206-march-7-2019"></a>2.0.6 (7 marca 2019)
#### <a name="new-features"></a>Nowe funkcje
* Ignoruj wszystkie wyjątki od danych telemetrycznych.

### <a name="210-december-17-2018"></a>2.1.0 (17 grudnia 2018)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje wersję do 2.1.0 w celu rozwiązania problemu.

### <a name="205-september-13-2018"></a>2.0.5 (13 września 2018)
#### <a name="new-features"></a>Nowe funkcje
* Dodaje słowa kluczowe `exists` i `startsWith` .
* Aktualizuje plik Readme.
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawki "nie można wywoływać samego odwołania bezpośredniego bezpośrednio dla jednostki".
* Poprawki "findAll zakończą się niepowodzeniem, jeśli kolekcja nie zostanie utworzona".

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (wersja wstępna) (23 sierpnia 2018)
#### <a name="new-features"></a>Nowe funkcje
* Zmienia nazwę pakietu z DocumentDB na cosmosdb.
* Dodaje nową funkcję słowa kluczowego metody zapytania. Obsługiwane są 16 słów kluczowych z interfejsu API SQL.
* Dodaje nową funkcję zapytania z stronicowaniem i sortowaniem.
* Upraszcza konfigurację danych wiosennych — cosmosdb.
* Dodaje `deleteCollection` `deleteAll` interfejsy API i.

#### <a name="key-bug-fixes"></a>Poprawki klucza
* Rozwiązywanie błędów i eliminowanie wad.

## <a name="faq"></a>Najczęściej zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej na temat [struktury sprężynowej](https://spring.io/projects/spring-framework).

Dowiedz się więcej na temat [rozruchu sprężynowego](https://spring.io/projects/spring-boot).

Dowiedz się więcej na temat [sprężyny danych](https://spring.io/projects/spring-data).