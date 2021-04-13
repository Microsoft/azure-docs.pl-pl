---
title: Dokumentacja usługi Spring Data Azure Cosmos DB 3 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej o interfejsie API Spring Data Azure Cosmos DB v3 for SQL, w tym o datach wydaniach, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK Java Azure Cosmos DB SQL Async.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 594d38425be0304a9f7737bdfba60b29187a2e2d
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363516"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Interfejs API spring Data Azure Cosmos DB v3 for Core (SQL): informacje o wersji i zasoby
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

Usługa Spring Data Azure Cosmos DB w wersji 3 dla języka Core (SQL) umożliwia deweloperom korzystanie Azure Cosmos DB w aplikacjach Spring. Interfejs Spring Data Azure Cosmos DB udostępnia interfejs Spring Data do manipulowania bazami danych i kolekcjami, pracy z dokumentami i wystawiania zapytań. Interfejsy API synchronizacji i asynchronicznej (reaktywnej) są obsługiwane w tym samym artefaktze maven. 

> [!IMPORTANT]
> Program Spring Data Azure Cosmos DB jest zależny od struktury Spring Data.
> 
> Wersja azure-spring-data-cosmos z wersji 3.0.0 do 3.4.0 obsługuje platformę Spring Data w wersjach 2.2 i 2.3.
> 
> Usługa azure-spring-data-cosmos w wersji 3.5.0 lub starszej obsługuje platformę Spring Data w wersji 2.4.3 lub starszej.
>

Spring [Framework to](https://spring.io/projects/spring-framework) model programowania i konfiguracji, który usprawnia tworzenie aplikacji w języku Java. Aplikacja Spring usprawnia "wychylianie" aplikacji przy użyciu wstrzykiwania zależności. Wielu deweloperów, takich jak Spring, sprawia, że tworzenie i testowanie aplikacji jest prostsze. [Spring Boot](https://spring.io/projects/spring-boot) rozszerza tę obsługę instalacji sieciowej, mając na celu opracowywanie aplikacji internetowej i mikrousług. [Spring Data](https://spring.io/projects/spring-data) to model programowania i framework do uzyskiwania dostępu do magazynów danych, takich jak Azure Cosmos DB, z kontekstu aplikacji Spring Spring Boot aplikacji. 

Możesz użyć aplikacji Spring Data Azure Cosmos DB w swoich [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) aplikacjach.

> [!IMPORTANT]  
> Te informacje o wersji są dla wersji 3 programu Spring Data Azure Cosmos DB. Informacje o wersji [dla wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Usługa Spring Data Azure Cosmos DB obsługuje tylko interfejs API SQL.
>
> Zapoznaj się z tymi artykułami, aby uzyskać informacje na temat danych Spring Data w innych Azure Cosmos DB API:
> * [Spring Data for Apache Cassandra z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Baza danych MongoDB spring data z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>Szybkie rozpoczynanie pracy

  Przygotuj się do pracy z usługą Spring Data Azure Cosmos DB, korzystając z [naszego Spring Boot Starter.](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) Podejście Spring Boot Starter jest zalecanym sposobem rozpoczęcia korzystania z łącznika Spring Data Azure Cosmos DB Connector.

  Alternatywnie możesz dodać zależność spring data Azure Cosmos DB do `pom.xml` pliku, jak pokazano poniżej:

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
|**Dokumentacja interfejsu API** | [Dokumentacja referencyjna interfejsu API języka Java](/java/api/com.azure.spring.data.cosmos) |
|**Współtwomentuj zestaw SDK** | [Repozytorium Azure SDK for Java Central w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**Wprowadzenie** | [Szybki start: tworzenie aplikacji Spring Data Azure Cosmos DB do zarządzania danymi interfejsu API SQL Azure Cosmos DB SQL](./create-sql-api-spring-data.md) <br> [Repozytorium GitHub z kodem szybkiego startu](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**Podstawowe przykłady kodu** | [Azure Cosmos DB: przykłady interfejsu API SQL Azure Cosmos DB Spring Data](sql-api-spring-data-sdk-samples.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **Porady dotyczące wydajności**| [Porady dotyczące wydajności dla zestawu Java SDK w wersji 4 (dotyczy spring data)](performance-tips-java-sdk-v4-sql.md)| 
| **Rozwiązywanie problemów** | [Rozwiązywanie problemów z zestawem Java SDK w wersji 4 (dotyczy rozwiązania Spring Data)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB warsztaty i laboratoria** |[Cosmos DB strony głównej warsztatów](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>Uwagi dodatkowe

* Środowisko Spring Data Azure Cosmos DB obsługuje java JDK 8 i Java JDK 11.
* Spring Data 2.3 jest obecnie obsługiwana, spring data 2.4 nie jest obecnie obsługiwana.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Dowiedz się więcej o [platformie Spring Framework.](https://spring.io/projects/spring-framework)

Dowiedz się więcej o [Spring Boot](https://spring.io/projects/spring-boot).

Dowiedz się więcej na [temat danych Spring Data.](https://spring.io/projects/spring-data)