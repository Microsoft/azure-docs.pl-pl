---
title: Azure Cosmos DB i zasoby dotyczące wersji zestawu Java SDK 4 dla interfejsu API SQL
description: Azure Cosmos DB Dowiedz się wszystkiego o zestawie JAVA SDK w wersji 4 dla interfejsu API SQL i zestawu SDK języka Java, w tym o datach wydania, datach wycofania i zmianach wprowadzonych między poszczególnymi wersjami zestawu SDK Java programu Azure Cosmos DB SQL Async.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: d31f3d1c510ffe6c3f0a739a4e41313c8c6e7cf0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364825"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB sdk Java w wersji 4 dla podstawowego interfejsu API (SQL): informacje o wersji i zasoby
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

Zestaw SDK Azure Cosmos DB Java w wersji 4 dla bazy danych Core (SQL) łączy asynchroniczny interfejs API i interfejs API synchronizacji w jeden artefakt Maven. Zestaw SDK w wersji 4 oferuje lepszą wydajność, nowe funkcje interfejsu API i obsługę asynchroniczną w oparciu o bibliotekę Project Reactor i [bibliotekę Netty.](https://netty.io/) Użytkownicy mogą oczekiwać lepszej wydajności dzięki Azure Cosmos DB Java SDK w wersji 4 w porównaniu z zestawem JAVA SDK w wersji [2](sql-api-sdk-async-java.md) Azure Cosmos DB Async i zestawem [AZURE COSMOS DB Sync Java SDK w wersji 2.](sql-api-sdk-java.md)

> [!IMPORTANT]  
> Te informacje o wersji są tylko Azure Cosmos DB Java SDK w wersji 4. Jeśli obecnie używasz wersji starszej niż 4, zapoznaj się z przewodnikiem Migrowanie do zestawu JAVA SDK w wersji [4](migrate-java-v4-sdk.md) Azure Cosmos DB, aby uzyskać pomoc w uaktualnianiu do wersji 4.
>
> Oto trzy kroki, aby szybko rozpocząć!
> 1. Zainstaluj minimalne [obsługiwane środowisko uruchomieniowe Java (JDK 8),](/java/azure/jdk/) aby można było używać zestawu SDK.
> 2. Aby uzyskać dostęp do artefaktu Ma Azure Cosmos DB ven i przejść przez podstawowe żądania, Azure Cosmos DB Java SDK w wersji [4.](./create-sql-api-java.md)
> 3. Przeczytaj wskazówki Azure Cosmos DB dotyczące wydajności zestawu [](troubleshoot-java-sdk-v4-sql.md) Java SDK w [wersji](performance-tips-java-sdk-v4-sql.md) 4 i przewodniki dotyczące rozwiązywania problemów, aby zoptymalizować zestaw SDK dla aplikacji.
>
> Warsztaty [Azure Cosmos DB i laboratoria](https://aka.ms/cosmosworkshop) to kolejny doskonały zasób do nauki korzystania z zestawu SDK Azure Cosmos DB Java w wersji 4!
>

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Link |
|---|---|
|**Zestaw SDK do pobrania**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentacja interfejsu API** | [Dokumentacja referencyjna interfejsu API języka Java](/java/api/overview/azure/cosmosdb/client) |
|**Współtwomentuj zestaw SDK** | [Repozytorium Azure SDK for Java Central w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Wprowadzenie** | [Szybki start: tworzenie aplikacji Java do zarządzania danymi interfejsu API SQL Azure Cosmos DB SQL](./create-sql-api-java.md) <br> [Repozytorium GitHub z kodem Szybkiego startu](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Podstawowe przykłady kodu** | [Azure Cosmos DB: przykłady kodu Java dla interfejsu API SQL](sql-api-java-sdk-samples.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Aplikacja konsolowa ze zmianą źródła danych**| [Zestawienia zmian — przykład zestawu Java SDK w wersji 4](create-sql-api-java-changefeed.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Przykład aplikacji internetowej**| [Tworzenie aplikacji internetowej przy użyciu zestawu Java SDK w wersji 4](sql-api-java-application.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Porady dotyczące wydajności**| [Porady dotyczące wydajności dla zestawu Java SDK w wersji 4](performance-tips-java-sdk-v4-sql.md)| 
| **Rozwiązywanie problemów** | [Rozwiązywanie problemów z zestawem Java SDK w wersji 4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrowanie do wersji 4 ze starszego zestawu SDK** | [Migracja do zestawu SDK języka Java w wersji 4](migrate-java-v4-sdk.md) |
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB warsztaty i laboratoria** |[Cosmos DB strony głównej warsztatów](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).