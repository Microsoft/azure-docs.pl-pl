---
title: Dane wiosenne Azure Cosmos DB v3 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat źródła danych Azure Cosmos DB v3 dla interfejsu API SQL, w tym dat wydania, dat wycofania i zmian wprowadzonych między każdą wersją Azure Cosmos DB asynchronicznej wersji zestawu Java SDK języka SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590722"
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

Dane sprężynowe Azure Cosmos DB v3 for Core (SQL) umożliwiają deweloperom korzystanie z Azure Cosmos DB w aplikacjach wiosennych. Dane wiosenne Azure Cosmos DB uwidaczniają interfejs danych sprężyny do manipulowania bazami plików i kolekcjami, pracy z dokumentami i wystawiania zapytań. W tym samym artefaktie Maven są obsługiwane zarówno interfejsy API synchronizacji, jak i asynchroniczne (reaktywne). 

Dane wiosenne Azure Cosmos DB są zależne od struktury danych wiosennych. Azure Cosmos DB zestawu SDK Teams Maven artefakty dla danych wiosennych v 2.2 i v 2.3.

[Platforma wiosny](https://spring.io/projects/spring-framework) to model programowania i konfiguracji, który upraszcza tworzenie aplikacji Java. Aby obsłużyć witrynę sieci Web organizacji, wiosną usprawnia "instalację wodociągową" aplikacji przy użyciu iniekcji zależności. Wielu programistów, takich jak sprężyna, ponieważ aplikacje do kompilowania i testowania staną się bardziej proste. [Rozruch sprężynowy](https://spring.io/projects/spring-boot) stanowi ten pomysł, aby obsłużyć instalację wodociągową z oczami na potrzeby tworzenia aplikacji sieci Web i mikrousług. [Dane sprężynowe](https://spring.io/projects/spring-data) to model programowania i platforma służąca do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu aplikacji sieci szkieletowej sprężyny lub sprężyny. 

W aplikacjach w [chmurze ze sprężyną na platformie Azure](https://azure.microsoft.com/services/spring-cloud/) można używać danych wiosennych Azure Cosmos DB.

> [!IMPORTANT]  
> Te informacje o wersji dotyczą 3 danych wiosennych Azure Cosmos DB. Informacje o wersji v2 można znaleźć [tutaj](sql-api-sdk-java-spring-v2.md). 
>
> Dane wiosenne Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Poniższe przewodniki obsługują dane wiosenne na innych Azure Cosmos DB interfejsach API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Zacznij tutaj

# <a name="explore"></a>[Eksplorowanie](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Przejdź do powyższej karty, aby uzyskać podstawowe dane dotyczące danych wiosennych Azure Cosmos DB.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurowanie zależności

Dostępne są dwie źródła danych Azure Cosmos DB v3 Maven.

Artefakt, który jest zależny od struktury danych wiosennych v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Artefakt, który jest zależny od struktury danych wiosennych v 2.3:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Połącz](#tab/connect)

### <a name="connect"></a>Połącz

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

### <a name="query"></a>Zapytanie

[Zapytanie](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Platforma danych wiosennych v 2.2 | Sprężyna Data Framework v 2.3 |
|---|---|
| **Pobieranie zestawu SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Współtworzenie zestawu SDK** | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Samouczek**| [Samouczek dotyczący sprężyny danych Azure Cosmos DB w witrynie GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Samouczek dotyczący sprężyny danych Azure Cosmos DB w witrynie GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>Historia wersji

### <a name="300-beta1-2020-08-17"></a>3.0.0 — beta. 1 (2020-08-17)
#### <a name="new-features"></a>Nowe funkcje
* Zaktualizowano identyfikator grupy do `com.azure` .
* Zaktualizowano identyfikator artefaktu do `azure-spring-data-2-3-cosmos` .
* Zaktualizowano zależność zestawu SDK platformy Azure-Cosmos do programu `4.3.2-beta.2` .
* Obsługa inspekcji jednostek — automatyczne Zarządzanie polami z adnotacjami createdBy, createdDate, lastModifiedBy i lastModifiedDate.
* `@GeneratedValue` Obsługa adnotacji dla automatycznego generowania identyfikatorów dla pól identyfikatorów `String` typu.
* Obsługa konfiguracji z wieloma bazami danych dla jednego konta Cosmos z wieloma bazami danych i wieloma kontami Cosmos z wieloma bazami danych.
* Obsługa `@Version` adnotacji w dowolnym polu ciągu.
* Zaktualizowano typy zwracanych interfejsów API synchronizacji do `Iterable` typów zamiast `List` .
* Uwidocznione `CosmosClientBuilder` z zestawu SDK Cosmos jako źródło sprężyny do `@Configuration` klasy.
* Zaktualizowano `CosmosConfig` w celu uwzględnienia metryk zapytania i implementacji procesora diagnostyki odpowiedzi.
* Obsługa zwracania `Optional` typu danych dla kwerend pojedynczego wyniku.
#### <a name="renames"></a>Zmienia nazwę
* `CosmosDbFactory` do programu `CosmosFactory` .
* `CosmosDBConfig` do programu `CosmosConfig` .
* `CosmosDBAccessException` do programu `CosmosAccessException` .
* `Document` Adnotacja do `Container` adnotacji.
* `DocumentIndexingPolicy` Adnotacja do `CosmosIndexingPolicy` adnotacji.
* `DocumentQuery` do programu `CosmosQuery` .
* Flaga Application. Properties `populateQueryMetrics` `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Poprawki klucza
* Planowanie zadań rejestrowania diagnostyki w `Parallel` wątkach, aby uniknąć blokowania wielosieciowych wątków we/wy.
* Naprawiono optymistyczne blokowanie operacji usuwania.
* Rozwiązano problem z kwerendami ucieczki dla `IN` klauzuli.
* Rozwiązano problem, zezwalając `long` na typ danych dla `@Id` .
* Rozwiązano problem, `boolean` zezwalając `long` `int` na to,,, `double` jako typy danych do `@PartitionKey` adnotacji.
* Stałe `IgnoreCase`  &  `AllIgnoreCase` słowa kluczowe dla ignorowania zapytań Case.
* Usunięto domyślną wartość jednostki żądania 4000 podczas automatycznego tworzenia kontenerów.

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

Aby dowiedzieć się więcej na temat struktury sprężynowej, zobacz [stronę główną projektu](https://spring.io/projects/spring-framework).

Aby dowiedzieć się więcej na temat rozruchu sprężynowego, zobacz [stronę główną projektu](https://spring.io/projects/spring-boot).

Aby dowiedzieć się więcej na temat wiosennych danych, zobacz [stronę główną projektu](https://spring.io/projects/spring-data).