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
ms.openlocfilehash: f0cb3d5f9184bacef42a0258add6dd2461a71dd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326664"
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

# <a name="explore"></a>[Przeglądać](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Te karty zawierają podstawowe dane Azure Cosmos DB próbki.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Konfigurowanie zależności

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Połącz](#tab/connect)

### <a name="connect"></a>Connect

Określ konto Azure Cosmos DB i szczegóły kontenera. Dane sprężynowe Azure Cosmos DB automatycznie tworzą klienta i łączą się z kontenerem.

[Application. Properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
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

---

## <a name="resources"></a>Zasoby

* **Współtworzenie zestawu SDK**: [dane wiosenne Azure Cosmos DB repozytorium w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Samouczek**: [Azure Cosmos DB samouczek dotyczący sprężyny danych w witrynie GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Najczęściej zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej na temat [struktury sprężynowej](https://spring.io/projects/spring-framework).

Dowiedz się więcej na temat [rozruchu sprężynowego](https://spring.io/projects/spring-boot).

Dowiedz się więcej na temat [sprężyny danych](https://spring.io/projects/spring-data).