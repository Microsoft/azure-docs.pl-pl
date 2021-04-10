---
title: Dane wiosenne Azure Cosmos DB v3 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o danych wiosennych Azure Cosmos DB v3 dla interfejsu API SQL, w tym daty wydania, daty wycofania i zmiany wprowadzone między poszczególnymi wersjami zestawu SDK Azure Cosmos DB asynchronicznej platformy Java.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 536c0ab78a4b7e08abb1532635aff722df9e15cd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563110"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Dane wiosenne Azure Cosmos DB v3 for Core (SQL) API: informacje o wersji i zasoby
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
> * [Łącznik platformy Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Dostawca zasobów REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Moduł wykonywania zbiorczego — .NET 2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Moduł wykonywania zbiorczego — Java](sql-api-sdk-bulk-executor-java.md)

Dane wiosenne Azure Cosmos DB wersja 3 dla rdzenia (SQL) umożliwiają deweloperom korzystanie z Azure Cosmos DB w aplikacjach wiosennych. Dane wiosenne Azure Cosmos DB uwidaczniają interfejs danych sprężyny do manipulowania bazami plików i kolekcjami, pracy z dokumentami i wystawiania zapytań. W tym samym artefaktie Maven są obsługiwane zarówno interfejsy API synchronizacji, jak i asynchroniczne (reaktywne). 

> [!IMPORTANT]
> Dane wiosenne Azure Cosmos DB są zależne od struktury danych wiosennych.
> 
> wersje Cosmos systemu Azure ze sprężyną danych z 3.0.0 do 3.4.0 obsługują dane wiosenne w wersjach 2,2 i 2,3.
> 
> Platforma Azure-Sprężyna danych — Cosmos wersje 3.5.0 i nowsze obsługują dane wiosenne w wersjach 2.4.3 i nowszych.
>

[Platforma wiosny](https://spring.io/projects/spring-framework) to model programowania i konfiguracji, który usprawnia Programowanie aplikacji Java. Sprężyna upraszcza "instalację wodociągową" aplikacji przy użyciu iniekcji zależności. Wielu programistów, takich jak wiosenne, sprawia, że tworzenie i testowanie aplikacji jest bardziej proste. [Rozruch sprężynowy](https://spring.io/projects/spring-boot) rozszerza ten sposób obsługi instalacji wodociągowej z oczami na rozwój aplikacji sieci Web i mikrousług. [Dane sprężynowe](https://spring.io/projects/spring-data) to model programowania i platforma służąca do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB z kontekstu sprężynowej lub sprężynowej aplikacji rozruchowej. 

W aplikacjach w [chmurze ze sprężyną na platformie Azure](https://azure.microsoft.com/services/spring-cloud/) można używać danych wiosennych Azure Cosmos DB.

> [!IMPORTANT]  
> Te informacje o wersji dotyczą wersji 3 danych wiosennych Azure Cosmos DB. [Informacje o wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Dane sprężynowe Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje na temat sprężyny danych na innych Azure Cosmos DB interfejsów API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Szybkie rozpoczynanie pracy

  Zacznij korzystać z danych wiosennych Azure Cosmos DB, postępując zgodnie z [przewodnikiem po rozpoczęciu uruchamiania sprężyny](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db). Podejście do rozruchu sprężynowego jest zalecanym sposobem rozpoczęcia korzystania z łącznika Azure Cosmos DB danych sprężyny.

  Alternatywnie możesz dodać zależność Azure Cosmos DB danych sprężyny do `pom.xml` pliku, jak pokazano poniżej:

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Link |
|---|---|
|**Zestaw SDK do pobrania**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**Dokumentacja interfejsu API** | [Dokumentacja interfejsu API języka Java](/java/api/com.azure.spring.data.cosmos) |
|**Współtworzenie zestawu SDK** | [Zestaw Azure SDK dla centralnego repozytorium Java w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Wprowadzenie** | [Szybki Start: Tworzenie aplikacji wiosennej Azure Cosmos DB danych w celu zarządzania Azure Cosmos DB danymi interfejsu API SQL](./create-sql-api-spring-data.md) <br> [Repozytorium GitHub z kodem szybkiego startu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Podstawowe przykłady kodu** | [Azure Cosmos DB: sprężynowe Azure Cosmos DB danych dla interfejsu API SQL](sql-api-spring-data-sdk-samples.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Porady dotyczące wydajności**| [Wskazówki dotyczące wydajności dla zestawu Java SDK v4 (mające zastosowanie do sprężyny danych)](performance-tips-java-sdk-v4-sql.md)| 
| **Rozwiązywanie problemów** | [Rozwiązywanie problemów z zestawem Java SDK v4 (dotyczy to sprężyny danych)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB warsztatów i laboratoriów** |[Strona główna Cosmos DB warsztatów](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Uwagi dodatkowe

* Dane wiosenne Azure Cosmos DB obsługują Java JDK 8 i Java JDK 11.
* Dane wiosenne 2,3 są obecnie obsługiwane, sprężynowe 2,4 dane nie są obecnie obsługiwane.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej na temat [struktury sprężynowej](https://spring.io/projects/spring-framework).

Dowiedz się więcej na temat [rozruchu sprężynowego](https://spring.io/projects/spring-boot).

Dowiedz się więcej na temat [sprężyny danych](https://spring.io/projects/spring-data).