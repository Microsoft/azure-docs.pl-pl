---
title: Azure Cosmos DB Java SDK v4 dla informacji o wersji i zasobów interfejsu API SQL
description: Dowiedz się więcej na temat Azure Cosmos DB Java SDK v4 dla interfejsu API SQL i zestawu SDK, włącznie z datami wydania, datami wycofania i zmianami wprowadzonymi między każdą wersją Azure Cosmos DB asynchronicznej wersji zestawu Java SDK języka SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a70892a1900902cdf033434fe7edfed4c56f3b74
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097111"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: informacje o wersji i zasoby
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

Azure Cosmos DB Java SDK v4 for Core (SQL) łączy asynchroniczny interfejs API i interfejs API synchronizacji w jeden artefakt Maven. Zestaw SDK v4 oferuje ulepszoną wydajność, nowe funkcje interfejsu API i obsługę asynchroniczną na podstawie aktora projektu i [biblioteki](https://netty.io/)sieci. Użytkownicy mogą oczekiwać zwiększonej wydajności za pomocą Azure Cosmos DB Java SDK v4 a [Azure Cosmos DB ASYNC SDK w wersji 2](sql-api-sdk-async-java.md) i [Azure Cosmos DB synchronizacji Java SDK V2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Te informacje o wersji dotyczą tylko Azure Cosmos DB Java SDK v4. Jeśli obecnie używasz starszej wersji niż v4, zapoznaj się z przewodnikiem [Migrowanie do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , aby uzyskać pomoc w uaktualnianiu do wersji v4.
>
> Oto trzy kroki, aby szybko rozpocząć.
> 1. Zainstaluj [minimalny obsługiwany środowisko uruchomieniowe Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) aby można było użyć zestawu SDK.
> 2. Zapoznaj się z [przewodnikiem Szybki Start dla Azure Cosmos DB Java SDK v4](./create-sql-api-java.md) , który uzyskuje dostęp do artefaktu Maven i przeprowadzi Cię przez podstawowe żądania Azure Cosmos DB.
> 3. Przeczytaj Azure Cosmos DB [wskazówki dotyczące wydajności](performance-tips-java-sdk-v4-sql.md) i przewodniki dotyczące [rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md) z zestawem Java SDK v4, aby zoptymalizować zestaw SDK aplikacji.
>
> [Azure Cosmos DB warsztaty i laboratoria](https://aka.ms/cosmosworkshop) są kolejną doskonałym zasobem do uczenia się, jak używać Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Przydatna zawartość

| Zawartość | Łącze |
|---|---|
|**Zestaw SDK do pobrania**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentacja interfejsu API** | [Dokumentacja interfejsu API języka Java](/java/api/overview/azure/cosmosdb/client?preserve-view=true&view=azure-java-stable) |
|**Współtworzenie zestawu SDK** | [Zestaw Azure SDK dla centralnego repozytorium Java w serwisie GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Wprowadzenie** | [Szybki Start: Tworzenie aplikacji Java do zarządzania Azure Cosmos DB danych interfejsu API SQL](./create-sql-api-java.md) <br> [Repozytorium GitHub z kodem szybkiego startu](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Podstawowe przykłady kodu** | [Azure Cosmos DB: przykłady kodu Java dla interfejsu API SQL](sql-api-java-sdk-samples.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Aplikacja konsolowa ze źródłem zmian**| [Źródło zmian — przykład zestawu SDK dla języka Java w wersji 4](create-sql-api-java-changefeed.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Przykład aplikacji sieci Web**| [Tworzenie aplikacji sieci Web za pomocą zestawu Java SDK v4](sql-api-java-application.md) <br> [Repozytorium GitHub z przykładowym kodem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Porady dotyczące wydajności**| [Porady dotyczące wydajności dla zestawu Java SDK w wersji 4](performance-tips-java-sdk-v4-sql.md)| 
| **Rozwiązywanie problemów** | [Rozwiązywanie problemów z zestawem Java SDK w wersji 4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrowanie do wersji v4 ze starszego zestawu SDK** | [Migracja do zestawu SDK języka Java w wersji 4](migrate-java-v4-sdk.md) |
| **Minimalne obsługiwane środowisko uruchomieniowe**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 
| **Azure Cosmos DB warsztatów i laboratoriów** |[Strona główna Cosmos DB warsztatów](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Często zadawane pytania
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat usługi Cosmos DB, zobacz stronę usługi [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).