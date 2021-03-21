---
title: 'Azure Cosmos DB: przykłady platformy .NET (Microsoft. Azure. Cosmos) dla interfejsu API SQL'
description: Przykłady zestawu SDK języka C# .NET V3 w witrynie GitHub dla typowych zadań przy użyciu interfejsu API Azure Cosmos DB SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 30ccd5783b3fa9524072235a58b3c8708962ef1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434019"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Przykłady zestawu SDK platformy Azure Cosmos DB.NET v3 (Microsoft. Azure. Cosmos) dla interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Przykłady zestawu SDK dla platformy .NET V2](sql-api-dotnet-samples.md)
> * [Przykłady zestawu SDK dla platformy .NET v3](sql-api-dotnet-v3sdk-samples.md)
> * [Przykłady zestawu SDK dla języka Java v4](sql-api-java-sdk-samples.md)
> * [Przykłady zestawu SDK dla danych wiosennych v3](sql-api-spring-data-sdk-samples.md)
> * [Przykłady dla platformy Node.js](sql-api-nodejs-samples.md)
> * [Przykłady języka Python](sql-api-python-samples.md)
> * [Galeria przykładów kodu dla platformy Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

Repozytorium GitHub [Azure-Cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) obejmuje najnowsze przykładowe rozwiązania platformy .NET do wykonywania CRUD i innych typowych operacji na Azure Cosmos DB zasobach. Jeśli znasz poprzednią wersję zestawu .NET SDK, możesz użyć jej do zbierania terminów i dokumentu. Ponieważ Azure Cosmos DB obsługuje wiele modeli interfejsów API, wersja 3,0 zestawu .NET SDK używa ogólnych warunków "Container" i "Item". Kontener może być kolekcją, wykresem lub tabelą. Element może być dokumentem, krawędzią/wierzchołkiem lub wierszem i stanowi zawartość znajdująca się w kontenerze. Ten artykuł zawiera:

* Linki do zadań w poszczególnych plikach projektów przykładowych w języku C#.
* Linki do powiązanej dokumentacji interfejsu API.

## <a name="prerequisites"></a>Wymagania wstępne

Program Visual Studio 2019 z zainstalowanym przepływem pracy projektowania platformy Azure

- Możesz pobrać **bezpłatnie** [program Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)i korzystać z niego. Podczas instalacji programu Visual Studio upewnij się, że jest włączona opcja **Programowanie na platformie Azure**.

   [Pakiet NuGet Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Subskrypcja platformy Azure lub bezpłatne Cosmos DB konto wersji próbnej

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Możesz [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Twoja subskrypcja programu Visual Studio daje środki na korzystanie z płatnych usług platformy Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Przykłady są samodzielne i konfigurowane i oczyszczane po sobie. Każde wystąpienie wystawia subskrypcję za godzinę użycia w warstwie wydajności Twojego kontenera.
> 

## <a name="database-examples"></a>Przykłady dotyczące baz danych

Metoda [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) przykładowego projektu *DatabaseManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o bazach danych usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Working with Databases, Containers and items](account-databases-containers-items.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie bazy danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database. ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync) |
| [Odczytywanie wszystkich baz danych dla konta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database. DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) |

## <a name="container-examples"></a>Przykłady dotyczące kontenerów

Metoda [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) przykładowego projektu *ContainerManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kontenerów usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Working with Databases, Containers and items](account-databases-containers-items.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Tworzenie kontenera przy użyciu niestandardowych zasad indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Zmień skonfigurowaną wydajność kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) |
| [Pobieranie kontenera według identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container. ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync) |
| [Odczytywanie wszystkich kontenerów w bazie danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database. GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator) |
| [Usuwanie kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container. DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync) |

## <a name="item-examples"></a>Przykłady dotyczące elementów

Metoda [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) przykładowego projektu *ItemManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o elementach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Working with Databases, Containers and items](account-databases-containers-items.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie elementu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container. CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) |
| [Odczytywanie elementów na podstawie Identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[wbudowane. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) |
| [Zapytanie o elementy](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Zastąp element](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[wbudowane. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync) |
| [Upsert element](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[wbudowane. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) |
| [Usuwanie elementu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[wbudowane. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync) |
| [Zastąp element elementem Check Conditional ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions. IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania

Metoda [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) przykładowego projektu *IndexManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksu](index-policy.md), [typy indeksów](index-overview.md#index-types)i [ścieżki indeksów](index-policy.md#include-exclude-paths). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wyklucz element z indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective) |
| [Użyj indeksowania z opóźnieniem](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy. Indexing](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode) |
| [Wyklucz określone ścieżki elementów z indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths) |

## <a name="query-examples"></a>Przykłady zapytań

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) projektu przykładowe *zapytania* pokazuje, jak wykonać poniższe zadania przy użyciu gramatyki zapytań SQL, dostawcy LINQ z kwerendą i wyrażenia lambda. Aby dowiedzieć się więcej na temat odwołania do zapytań SQL w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL dotyczących Azure Cosmos DB](./sql-query-getting-started.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytań dotyczących elementów z pojedynczej partycji](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Wykonywanie zapytań dotyczących elementów z wielu partycji](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Zapytanie przy użyciu instrukcji języka SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |

## <a name="change-feed-examples"></a>Przykłady zestawienia zmian

Metoda [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) przykładowego projektu *ChangeFeed* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat źródła zmian w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [odczytywanie Azure Cosmos DB Zmienianie źródła danych](read-change-feed.md) i [Zmienianie procesora](change-feed-processor.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Podstawowe funkcje kanału informacyjnego zmiany](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Odczytaj Źródło zmian z określonego czasu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Odczytaj Źródło zmian od początku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder. WithStartTime (DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime) |
| [Migrowanie z procesora zmian do źródła zmian w zestawie SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) przykładowego projektu *ServerSideScripts* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts. CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync) |
| [Wykonywanie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync) |
| [Usuwanie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts. DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync) |
