---
title: Dane wiosenne Azure Cosmos DB v3 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o danych wiosennych Azure Cosmos DB v3 dla interfejsu API SQL, w tym daty wydania, daty wycofania i zmiany wprowadzone między poszczególnymi wersjami zestawu SDK Azure Cosmos DB asynchronicznej platformy Java.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 4b2d474f25209034034db092ca971bff6b78d73a
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068744"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Dane wiosenne Azure Cosmos DB v3 for Core (SQL) API: informacje o wersji i zasoby
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

Dane wiosenne Azure Cosmos DB wersja 3 dla rdzenia (SQL) umożliwiają deweloperom korzystanie z Azure Cosmos DB w aplikacjach wiosennych. Dane wiosenne Azure Cosmos DB uwidaczniają interfejs danych sprężyny do manipulowania bazami plików i kolekcjami, pracy z dokumentami i wystawiania zapytań. W tym samym artefaktie Maven są obsługiwane zarówno interfejsy API synchronizacji, jak i asynchroniczne (reaktywne). 

Dane wiosenne Azure Cosmos DB są zależne od struktury danych wiosennych. Zespół SDK Azure Cosmos DB Maven artefakty dla danych wiosennych w wersjach 2,2 i 2,3.

[Platforma wiosny](https://spring.io/projects/spring-framework) to model programowania i konfiguracji, który usprawnia Programowanie aplikacji Java. Sprężyna upraszcza "instalację wodociągową" aplikacji przy użyciu iniekcji zależności. Wielu programistów, takich jak wiosenne, sprawia, że tworzenie i testowanie aplikacji jest bardziej proste. [Rozruch sprężynowy](https://spring.io/projects/spring-boot) rozszerza ten sposób obsługi instalacji wodociągowej z oczami na rozwój aplikacji sieci Web i mikrousług. [Dane sprężynowe](https://spring.io/projects/spring-data) to model programowania i platforma służąca do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu sprężynowej lub sprężynowej aplikacji rozruchowej. 

W aplikacjach w [chmurze ze sprężyną na platformie Azure](https://azure.microsoft.com/services/spring-cloud/) można używać danych wiosennych Azure Cosmos DB.

> [!IMPORTANT]  
> Te informacje o wersji dotyczą wersji 3 danych wiosennych Azure Cosmos DB. [Informacje o wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Dane sprężynowe Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje na temat sprężyny danych na innych Azure Cosmos DB interfejsów API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Zacznij tutaj

# <a name="explore"></a>[Eksplorowanie](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Te karty zawierają podstawowe dane Azure Cosmos DB próbki.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurowanie zależności

Dostępne są dwa dane wiosenne Azure Cosmos DB wersja 3 Maven artefakty.

- Artefakt, który jest zależny od struktury danych wiosennych w wersji 2,2:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-2-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

- Artefakt, który jest zależny od struktury danych wiosennych w wersji 2,3:
  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-2-3-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Połącz](#tab/connect)

### <a name="connect"></a>Connect

Określ konto Azure Cosmos DB i szczegóły kontenera. Dane sprężynowe Azure Cosmos DB automatycznie tworzą klienta i łączą się z kontenerem.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Doc Ops](#tab/docs)

### <a name="document-operations"></a>Operacje dokumentu

[Utwórz](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Usuń](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Zapytanie](#tab/queries)

[Zapytanie](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="resources"></a>Zasoby

| Zasób | Struktura danych wiosennych 2,2 | Struktura danych wiosennych 2,3 |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Współtworzenie zestawu SDK** | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Samouczek**| [Samouczek dotyczący sprężyny danych Azure Cosmos DB w witrynie GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Samouczek dotyczący sprężyny danych Azure Cosmos DB w witrynie GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Historia wersji

### <a name="300-beta2-unreleased"></a>3.0.0 — wersja beta. 2 (nieopublikowana)

### <a name="300-beta1-august-17-2020"></a>3.0.0 — beta. 1 (17 sierpnia 2020)
#### <a name="new-features"></a>Nowe funkcje
* Aktualizuje identyfikator grupy do `com.azure` .
* Aktualizuje identyfikator artefaktu do `azure-spring-data-2-3-cosmos` .
* Aktualizuje zależność od zestawu SDK platformy Azure-Cosmos `4.3.2-beta.2` .
* Dodaje obsługę inspekcji jednostek: automatyczne Zarządzanie polami z `createdBy` `createdDate` `lastModifiedBy` adnotacjami,, i `lastModifiedDate` .
* Dodaje `@GeneratedValue` obsługę adnotacji do automatycznego generowania identyfikatorów dla pól identyfikatorów `String` typu.
* Dodaje obsługę konfiguracji wielu baz danych dla kont jednego Azure Cosmos DB z wieloma bazami danych i wieloma kontami Azure Cosmos DB z wieloma bazami danych.
* Dodaje obsługę `@Version` adnotacji dla dowolnego pola ciągu.
* Aktualizuje typy zwracane przez interfejs API synchronizacji do `Iterable` typów zamiast `List` .
* Udostępnia na `CosmosClientBuilder` podstawie zestawu SDK Azure Cosmos dB jako źródła sprężyny do `@Configuration` klasy.
* Aktualizacje `CosmosConfig` zawierające metryki zapytań i implementację procesora diagnostyki odpowiedzi.
* Dodaje obsługę zwracania `Optional` typu danych dla kwerend pojedynczego wyniku.
#### <a name="renames"></a>Zmienia nazwę
* `CosmosDbFactory` do programu `CosmosFactory` .
* `CosmosDBConfig` do programu `CosmosConfig` .
* `CosmosDBAccessException` do programu `CosmosAccessException` .
* `Document` Adnotacja do `Container` adnotacji.
* `DocumentIndexingPolicy` Adnotacja do `CosmosIndexingPolicy` adnotacji.
* `DocumentQuery` do programu `CosmosQuery` .
* Flaga Application. Properties `populateQueryMetrics` `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Planowanie zadania rejestrowania diagnostyki w `Parallel` wątkach, aby uniknąć blokowania wielosieciowych wątków we/wy.
* Naprawia optymistyczne blokowanie operacji usuwania.
* Rozwiązuje problem z kwerendami ucieczki dla `IN` klauzuli.
* Rozwiązuje problem, zezwalając `long` na typ danych dla `@Id` .
* Rozwiązuje problem, umożliwiając `boolean` , `long` , `int` i `double` jako typy danych `@PartitionKey` adnotacji.
* Poprawki `IgnoreCase` i `AllIgnoreCase` słowa kluczowe dla ignorowania zapytań Case.
* Usuwa domyślną wartość jednostki żądania 4 000, gdy kontenery są tworzone automatycznie.

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej na temat [struktury sprężynowej](https://spring.io/projects/spring-framework).

Dowiedz się więcej na temat [rozruchu sprężynowego](https://spring.io/projects/spring-boot).

Dowiedz się więcej na temat [sprężyny danych](https://spring.io/projects/spring-data).