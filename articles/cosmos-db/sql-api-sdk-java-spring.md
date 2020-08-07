---
title: Dane wiosenne Azure Cosmos DB dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat Azure Cosmos DB danych wiosennych dla interfejsu API SQL, w tym dat wydania, dat wycofania i zmian wprowadzonych między każdą wersją Azure Cosmos DB asynchronicznego zestawu Java SDK języka SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f94ea37966c4942f72ae2d7b701e742891fa6cd2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854306"
---
# <a name="spring-data-azure-cosmos-db-for-core-sql-api-release-notes-and-resources"></a>Informacje o sprężyny Azure Cosmos DB dla podstawowego (SQL) interfejsu API: informacje o wersji i zasoby
> [!div class="op_single_selector"]
> * [Zestaw SDK .NET w wersji 3](sql-api-sdk-dotnet-standard.md)
> * [Zestaw .NET SDK w wersji 2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK 2](sql-api-sdk-dotnet-core.md)
> * [Zestaw SDK zmiany źródła .NET w wersji 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK 4](sql-api-sdk-java-v4.md)
> * [Async Java SDK 2](sql-api-sdk-async-java.md)
> * [Sync Java SDK 2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Wykonawca zbiorczy — .NET V2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonawczy zbiorczej — Java](sql-api-sdk-bulk-executor-java.md)

Dane wiosenne Azure Cosmos DB dla rdzeń (SQL) umożliwiają deweloperom korzystanie z Azure Cosmos DB w aplikacjach wiosennych. Dane wiosenne Azure Cosmos DB uwidaczniają interfejs danych sprężyny do manipulowania bazami plików i kolekcjami, pracy z dokumentami i wystawiania zapytań. W tym samym artefaktie Maven są obsługiwane zarówno interfejsy API synchronizacji, jak i asynchroniczne (reaktywne). 

[Platforma wiosny](https://spring.io/projects/spring-framework) to model programowania i konfiguracji, który upraszcza tworzenie aplikacji Java. Aby obsłużyć witrynę sieci Web organizacji, wiosną usprawnia "instalację wodociągową" aplikacji przy użyciu iniekcji zależności. Wielu programistów, takich jak sprężyna, ponieważ aplikacje do kompilowania i testowania staną się bardziej proste. [Rozruch sprężynowy](https://spring.io/projects/spring-boot) stanowi ten pomysł, aby obsłużyć instalację wodociągową z oczami na potrzeby tworzenia aplikacji sieci Web i mikrousług. [Dane sprężynowe](https://spring.io/projects/spring-data) to model programowania służący do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu sieci sprężynowej lub sprężynowej. 

W aplikacjach w [chmurze ze sprężyną na platformie Azure](https://azure.microsoft.com/services/spring-cloud/) można używać danych wiosennych Azure Cosmos DB.

> [!IMPORTANT]  
> Te informacje o wersji dotyczą tylko Azure Cosmos DB interfejsu API SQL. 
>
> Poniższe przewodniki obsługują dane wiosenne na innych Azure Cosmos DB interfejsach API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Chcesz szybko zacząć?
> 1. Zainstaluj [minimalny obsługiwany środowisko uruchomieniowe Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable) aby można było użyć zestawu SDK.
> 2. Twórz dane sprężynowe Azure Cosmos DB aplikacji przy użyciu startera — jest [to proste](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Zapoznaj się z [przewodnikiem dotyczący danych wiosennych Azure Cosmos DB deweloperów](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) , który przeprowadzi Cię przez podstawowe żądania Azure Cosmos DB.
>
> Możesz szybko uruchamiać aplikacje z rozruchem z sprężyną, korzystając ze [sprężyny Initializr](https://start.spring.io/)!
>

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Link |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Dokumentacja interfejsu API** | [Informacje o sprężyny Azure Cosmos DB dokumentacji]() |
|**Współtworzenie zestawu SDK** | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Rozruch z sprężyną Starter**| [Azure Cosmos DB wiosny startowej biblioteki klienckiej dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Przykład aplikacji ze sprężyną do zrobienia przy użyciu Azure Cosmos DB**| [Kompleksowe środowisko Java w systemie App Service Linux (część 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Przewodnik dla deweloperów** | [Przewodnik dotyczący danych wiosennych Azure Cosmos DB deweloperów](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Przewodnik po użyciu Starter** | [Jak używać szablonu startowego Spring Boot z interfejsem API języka SQL usługi Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Repozytorium GitHub dla rozruchowego usługi Azure wiosny Starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Przykład z App Services** | [Jak używać platformy Spring i usługi Cosmos DB z usługą App Service w systemie Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Przykład aplikacji do zrobienia](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Historia wersji

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizuj wersję rozruchu sprężynowego do 2.3.0 
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizowano wersję Azure Cosmos DB do wersji v 3.7.3
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Zawiera poprawki przecieków pamięci i uaktualnienia wersji z sieci z Cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Stała flaga allowTelemetry do uwzględnienia z CosmosDbConfig
* Stała Właściwość TTL dla kontenera

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nowe funkcje
* Dodano nowy findAll według interfejsu API kluczy partycji
* Zaktualizowano wersję platformy Azure-Cosmos do 3.7.0
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Stała CollectionName-> ContainerName
* Stała entityClass & domainClass->
* Naprawiono "zwracaną kolekcję jednostek zapisaną przez repozytorium zamiast jednostki wejściowej"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawiono transportową poprawkę dla "zwracanej kolekcji jednostek zapisanej przez repozytorium zamiast jednostki wejściowej"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizowano wersję platformy Azure-Cosmos do wersji v 3.6.0
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizowano wersję zestawu SDK Cosmos DB do 3.5.0
* Dodano pole adnotacji umożliwiające włączenie/wyłączenie kolekcji AutoCreate
* Lepsza obsługa wyjątków, uwidoczniona CosmosClientException przez CosmosDBAccessException
* Uwidocznione requestCharge i activityId przez ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawka 3.5.0 aktualizacji zestawu SDK "wyjątek, gdy Cosmos DB nagłówek odpowiedzi HTTP jest większy niż 8192 bajtów", "ConsistencyPolicy. defaultConsistencyLevel () nie powiodło się na powiązana nieaktualność i spójny prefiks"
* Stałe interfejsy API findById, zwróć puste przy braku znalezienia, zamiast zgłaszania wyjątku
* Naprawiono usterkę polegającą na tym, że sortowanie nie zostało zastosowane na następnej stronie podczas korzystania z CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nowe funkcje
* Dodano pole adnotacji umożliwiające włączenie/wyłączenie kolekcji AutoCreate
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Stałe interfejsy API findById, zwróć puste przy braku znalezienia, zamiast zgłaszania wyjątku

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nowe funkcje
* Ukończ obsługę repozytorium Reactive Cosmos
* Obsługa parametrów diagnostyki żądania Cosmos DB i metryk zapytań
* Aktualizacja wersji zestawu SDK Cosmos DB do 3.3.1
* Uaktualnienie wersji platformy wiosennej do 5.2.0. RELEASE
* Uaktualnienie wersji Commons Attribution danych z 2.2.0. RELEASE
* Dodano interfejsy API findByIdAndPartitionKey, deleteByIdAndPartitionKey
* Usunięto zależność od platformy Azure — doumentdb
* Oznaczenie DocumentDb do Cosmos
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Naprawiono wyjątek "sortowanie throw, gdy pageSize jest mniejszy niż łączna liczba elementów w repozytorium"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nowe funkcje
* Przestarzałe interfejsy API bazy danych dokumentów
* Dodano interfejsy API findByIdAndPartitionKey, deleteByIdAndPartitionKey.
* Dodano optymistyczne blokowanie na podstawie _etag
* Włączone wyrażenie SPeL dla nazwy kolekcji dokumentów
* Udoskonalenia ObjectMapper.
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nowe funkcje
* Dodano zależność Cosmos zestawu SDK v3
* Dodano aktywne repozytorium Cosmos
* Zaktualizowano implementację DocumentDbTemplate w celu użycia zestawu Cosmos SDK v3.
* Inne zmiany w konfiguracji obsługi repozytorium Reactive Cosmos.
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Usuń zależność applicationInsights dla elementu
    * Potencjalne ryzyko zanieczyszczenia zależności
    * Niezgodność języka Java 11
    * Uniknięcie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nowe funkcje
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Backport usuwa zależność applicationInsights dla
    * Potencjalne ryzyko zanieczyszczenia zależności
    * Niezgodność języka Java 11
    * Uniknięcie potencjalnego wpływu na wydajność procesora CPU i/lub pamięci.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizuj wersję główną do 2.1.1
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nowe funkcje
* Ignoruj wszystkie wyjątki od danych telemetrycznych
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizuj wersję do 2.1.0, aby rozwiązać problem
#### <a name="key-bug-fixes"></a>Poprawki klucza

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nowe funkcje
* Dodaj słowo kluczowe EXISTS, startsWith
* Aktualizuj plik Readme
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Napraw element "Nie wywołuj bezpośredniego odwołania bezpośrednio dla jednostki"
* Poprawka "findAll zakończy się niepowodzeniem, jeśli kolekcja nie zostanie utworzona"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (wersja wstępna) (2018-08-23)
#### <a name="new-features"></a>Nowe funkcje
* Zmiana nazwy pakietu z DocumentDB na cosmosdb,
* Dodaj nową funkcję słowa kluczowego metody zapytania, 16 słów kluczowych z obsługiwanego interfejsu API SQL.
* Dodaj nową funkcję zapytania z stronicowaniem i sortowaniem.
* Uprość konfigurację danych wiosennych — cosmosdb.
* Dodawanie interfejsu API deletecollection i deleteAll.

#### <a name="key-bug-fixes"></a>Poprawki klucza
* Poprawka błędów i ulepszanie wad.

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Aby dowiedzieć się więcej na temat struktury sprężynowej, zobacz [stronę główną projektu](https://spring.io/projects/spring-framework).

Aby dowiedzieć się więcej na temat rozruchu sprężynowego, zobacz [stronę główną projektu](https://spring.io/projects/spring-boot).

Aby dowiedzieć się więcej na temat wiosennych danych, zobacz [stronę główną projektu](https://spring.io/projects/spring-data).