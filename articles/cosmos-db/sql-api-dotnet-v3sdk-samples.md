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
ms.openlocfilehash: fe31e67168c4eaf6fe94c056a96fdf695ea94fe4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92279992"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Przykłady zestawu SDK platformy Azure Cosmos DB.NET v3 (Microsoft. Azure. Cosmos) dla interfejsu API SQL

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
| [Tworzenie bazy danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient. CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Odczytywanie bazy danych na podstawie identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database. ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet&preserve-view=true) |
| [Odczytywanie wszystkich baz danych dla konta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet&preserve-view=true) |
| [Usuwanie bazy danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database. DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet&preserve-view=true) |

## <a name="container-examples"></a>Przykłady dotyczące kontenerów

Metoda [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) przykładowego projektu *ContainerManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat kontenerów usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Working with Databases, Containers and items](account-databases-containers-items.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Tworzenie kontenera przy użyciu niestandardowych zasad indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database. CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true) |
| [Zmień skonfigurowaną wydajność kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container. ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) |
| [Pobieranie kontenera według identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container. ReadContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet&preserve-view=true) |
| [Odczytywanie wszystkich kontenerów w bazie danych](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database. GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Usuwanie kontenera](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container. DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet&preserve-view=true) |

## <a name="item-examples"></a>Przykłady dotyczące elementów

Metoda [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) przykładowego projektu *ItemManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej o elementach usługi Azure Cosmos przed uruchomieniem poniższych przykładów, zobacz [Working with Databases, Containers and items](account-databases-containers-items.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Utwórz element](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container. CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true) |
| [Odczytywanie elementów na podstawie Identyfikatora](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[wbudowane. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet&preserve-view=true) |
| [Zapytanie o elementy](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Zastąp element](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[wbudowane. ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet&preserve-view=true) |
| [Upsert element](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[wbudowane. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet&preserve-view=true) |
| [Usuwanie elementu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[wbudowane. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet&preserve-view=true) |
| [Zastąp element elementem Check Conditional ETag](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions. IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet&preserve-view=true) |

## <a name="indexing-examples"></a>Przykłady dotyczące indeksowania

Metoda [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) przykładowego projektu *IndexManagement* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat indeksowania w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [zasady indeksu](index-policy.md), [typy indeksów](index-types.md)i [ścieżki indeksów](index-paths.md). 

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wyklucz element z indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet&preserve-view=true) |
| [Użyj indeksowania z opóźnieniem](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy. Indexing](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet&preserve-view=true) |
| [Wyklucz określone ścieżki elementów z indeksu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet&preserve-view=true) |

## <a name="query-examples"></a>Przykłady zapytań

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) projektu przykładowe *zapytania* pokazuje, jak wykonać poniższe zadania przy użyciu gramatyki zapytań SQL, dostawcy LINQ z kwerendą i wyrażenia lambda. Aby dowiedzieć się więcej na temat odwołania do zapytań SQL w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [przykłady zapytań SQL dotyczących Azure Cosmos DB](how-to-sql-query.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Wykonywanie zapytań dotyczących elementów z pojedynczej partycji](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Wykonywanie zapytań dotyczących elementów z wielu partycji](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |
| [Zapytanie przy użyciu instrukcji języka SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[wbudowane. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet&preserve-view=true) |

## <a name="change-feed-examples"></a>Przykłady zestawienia zmian

Metoda [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) przykładowego projektu *ChangeFeed* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat źródła zmian w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [odczytywanie Azure Cosmos DB Zmienianie źródła danych](read-change-feed.md) i [Zmienianie procesora](change-feed-processor.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Podstawowe funkcje kanału informacyjnego zmiany](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Odczytaj Źródło zmian z określonego czasu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |
| [Odczytaj Źródło zmian od początku](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder. WithStartTime (DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet&preserve-view=true) |
| [Migrowanie z procesora zmian do źródła zmian w zestawie SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container. GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet&preserve-view=true) |

## <a name="server-side-programming-examples"></a>Przykłady programowania po stronie serwera

Metoda [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) przykładowego projektu *ServerSideScripts* pokazuje, jak wykonać poniższe zadania. Aby dowiedzieć się więcej na temat programowania po stronie serwera w Azure Cosmos DB przed uruchomieniem poniższych przykładów, zobacz [procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika](stored-procedures-triggers-udfs.md).

| Zadanie | Dokumentacja interfejsu API |
| --- | --- |
| [Tworzenie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts. CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Wykonywanie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
| [Usuwanie procedury składowanej](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts. DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet&preserve-view=true) |
