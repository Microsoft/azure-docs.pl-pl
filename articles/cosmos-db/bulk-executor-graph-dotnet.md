---
title: Korzystanie z biblioteki programu .NET do przetwarzania zbiorczego wykresu przy użyciu interfejsu API Azure Cosmos DB Gremlin
description: Dowiedz się, jak używać biblioteki wykonawców zbiorczych do masowego importowania danych grafu do Azure Cosmos DB kontenera interfejsu API Gremlin.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: jasonh
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d113189d1361122305f92bc86c46346e1e700f4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489374"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Korzystanie z biblioteki programu .NET Bulk wykonującej wykresy w celu wykonywania operacji zbiorczych w Azure Cosmos DB interfejsie API Gremlin

Ten samouczek zawiera instrukcje dotyczące importowania i aktualizowania obiektów grafu do kontenera interfejsu API usługi Azure Cosmos DB Gremlin przy użyciu biblioteki programu .NET CosmosDB BULK. Ten proces korzysta z klasy grafu w [bibliotece wykonawców zbiorczych](./bulk-executor-overview.md) w celu programistycznego tworzenia obiektów wierzchołków i brzegowych w taki sposób, aby wstawiać wiele z nich na żądanie sieciowe. To zachowanie można skonfigurować za pomocą biblioteki wykonawców zbiorczych w celu optymalnego wykorzystania zarówno zasobów bazy danych, jak i pamięci lokalnej.

W przeciwieństwie do wysyłania zapytań Gremlin do bazy danych, gdzie polecenie jest oceniane, a następnie wykonywane pojedynczo, korzystanie z biblioteki wykonawców zbiorczych będzie wymagało utworzenia i sprawdzenia poprawności obiektów lokalnie. Po utworzeniu obiektów ta biblioteka umożliwia sekwencyjne wysłanie obiektów grafów do usługi bazy danych. Dzięki tej metodzie pozyskiwanie danych może być nawet stukrotnie szybsze, dzięki czemu jest to idealna metoda do początkowej migracji danych lub okresowych operacji przenoszenia danych. Dowiedz się więcej, odwiedzając stronę GitHub [przykładowej aplikacji do przetwarzania zbiorczego programu Azure Cosmos DB Graph](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started).

## <a name="bulk-operations-with-graph-data"></a>Wykonywanie operacji zbiorczych z użyciem danych grafów

Biblioteka modułu wykonawczego [zbiorczego](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet) zawiera `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` przestrzeń nazw, która zapewnia funkcjonalność do tworzenia i importowania obiektów grafu. 

Następująca procedura przedstawia proces migracji danych w przypadku kontenera interfejsu API języka Gremlin:
1. Pobierz rekordy ze źródła danych.
2. Utwórz obiekty `GremlinVertex` i `GremlinEdge` z uzyskanych rekordów i dodaj je do struktury danych `IEnumerable`. W tej części aplikacji należy wdrożyć logikę wykrywania i dodawania relacji, w razie gdyby źródło danych nie było bazą danych grafów.
3. Użyj [metody Graph BulkImportAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?preserve-view=true&view=azure-dotnet), aby wstawić obiekty grafów do kolekcji.

Ten mechanizm zapewnia wyższą wydajność migracji danych w porównaniu z użyciem klienta języka Gremlin. To ulepszenie jest możliwe, ponieważ wstawianie danych przy użyciu języka Gremlin wymaga od aplikacji wysyłania zapytań pojedynczo, po czym każde z zapytań musi zostać zwalidowane, ocenione, a następnie wykonane w celu utworzenia danych. Biblioteka wykonawców zbiorczych będzie obsługiwać weryfikację w aplikacji i wysyłać wiele obiektów grafów jednocześnie dla każdego żądania sieci.

### <a name="creating-vertices-and-edges"></a>Tworzenie wierzchołków i krawędzi

Obiekt `GraphBulkExecutor` udostępnia metodę `BulkImportAsync`, która wymaga listy `IEnumerable` obiektów `GremlinVertex` lub `GremlinEdge` zdefiniowanych w przestrzeni nazw `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. W tym przykładzie rozdzielono krawędzie i wierzchołki na dwa zadania importu biblioteki BulkExecutor. Zapoznaj się z poniższym przykładem:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Aby uzyskać więcej informacji na temat parametrów biblioteki wykonawców zbiorczych, zapoznaj się z [tematem BulkImportData do Azure Cosmos DB](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account).

Ładunek musi zostać utworzony w obiektach `GremlinVertex` i `GremlinEdge`. Oto, w jaki sposób można utworzyć te obiekty:

**Wierzchołki**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Krawędzie**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Narzędzie wykonawczy Bulk nie sprawdza automatycznie istniejących wierzchołków przed dodaniem krawędzi. Przed uruchomieniem zadań BulkImport należy przeprowadzić odpowiednią walidację w aplikacji.

## <a name="sample-application"></a>Przykładowa aplikacja

### <a name="prerequisites"></a>Wymagania wstępne
* Program Visual Studio 2019 z obciążeniem programowania na platformie Azure. Możesz bezpłatnie zacząć korzystać z programu [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) .
* Subskrypcja platformy Azure. [Bezpłatne konto platformy Azure możesz utworzyć tutaj](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Możesz też utworzyć konto bazy danych Cosmos z [bezpłatnymi wersjami prób Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure.
* Baza danych interfejsu API języka Gremlin w usłudze Azure Cosmos DB z **nieograniczoną kolekcją**. W tym przewodniku pokazano, jak rozpocząć pracę z [interfejsem API języka Gremlin w usłudze Azure Cosmos DB na platformie .NET](./create-graph-dotnet.md).
* Usługa Git. Aby uzyskać więcej informacji, zapoznaj się ze [stroną plików do pobrania usługi Git](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji
W tym samouczku przejdziemy przez kroki umożliwiające rozpoczęcie pracy przy użyciu [przykładowego modułu wykonawca programu Azure Cosmos DB Graph](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started) obsługiwanego w witrynie GitHub. Ta aplikacja składa się z rozwiązania platformy .NET, które losowo generuje obiekty krawędzi i wierzchołków, a następnie wykonuje zbiorcze wstawienia do określonego konta bazy danych grafów. Aby uzyskać aplikację, uruchom poniższe polecenie `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

To repozytorium zawiera przykładową aplikację GraphBulkExecutor z następującymi plikami:

Plik|Opis
---|---
`App.config`|Tutaj są określane parametry aplikacji i bazy danych. Ten plik powinien być zmodyfikowany w pierwszej kolejności, aby nawiązać połączenie z docelową bazą danych i kolekcjami.
`Program.cs`| Ten plik zawiera logikę w tle tworzenia `DocumentClient` kolekcji, obsługi czyszczenia i wysyłania zbiorczych żądań modułu wykonawczego.
`Util.cs`| Ten plik zawiera klasę pomocnika, która z kolei zawiera logikę generowania danych testowych i sprawdzania, czy baza danych i kolekcje istnieją.

W pliku `App.config` następujące elementy to wartości konfiguracji, które można określić:

Ustawienie|Opis
---|---
`EndPointUrl`|Jest to **punkt końcowy zestawu SDK platformy .NET**, który można znaleźć w bloku Przegląd Twojego konta bazy danych interfejsu API języka Gremlin w usłudze Azure Cosmos DB. Ma następujący format: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Jest to klucz podstawowy lub pomocniczy wymieniony w obszarze konta usługi Azure Cosmos DB. Dowiedz się więcej na temat [zabezpieczania dostępu do danych usługi Azure Cosmos DB](./secure-access-to-data.md#primary-keys)
`DatabaseName`, `CollectionName`|Są to **nazwy docelowej bazy danych i kolekcji**. Jeśli dla ustawienia `ShouldCleanupOnStart` zostanie podana wartość `true`, te wartości wraz z ustawieniem `CollectionThroughput` zostaną użyte do usunięcia bazy danych i kolekcji, a następnie utworzenia nowych. Podobnie w przypadku podania dla ustawienia `ShouldCleanupOnFinish` wartości `true` zostaną one użyte do usunięcia bazy danych zaraz po zakończeniu pozyskiwania. Pamiętaj, że kolekcja docelowa musi być **nieograniczoną kolekcją**.
`CollectionThroughput`|Ta wartość jest używana do utworzenia nowej kolekcji, jeśli opcja `ShouldCleanupOnStart` jest ustawiona na wartość `true`.
`ShouldCleanupOnStart`|To ustawienie spowoduje usunięcie konta bazy danych i kolekcji przed uruchomieniem programu, a następnie utworzenie nowych przy użyciu wartości `DatabaseName`, `CollectionName` i `CollectionThroughput`.
`ShouldCleanupOnFinish`|To ustawienie spowoduje usunięcie konta bazy danych i kolekcji z określonymi wartościami `DatabaseName` i `CollectionName` po uruchomieniu programu.
`NumberOfDocumentsToImport`|To ustawienie określa liczbę testowych wierzchołków i krawędzi, które zostaną wygenerowane w tym przykładzie. Ta liczba będzie dotyczyć zarówno wierzchołków, jak i krawędzi.
`NumberOfBatches`|To ustawienie określa liczbę testowych wierzchołków i krawędzi, które zostaną wygenerowane w tym przykładzie. Ta liczba będzie dotyczyć zarówno wierzchołków, jak i krawędzi.
`CollectionPartitionKey`|To ustawienie zostanie użyte do utworzenia testowych wierzchołków i krawędzi, a ta właściwość będzie przypisana automatycznie. Zostanie również użyte podczas ponownego tworzenia bazy danych i kolekcji w przypadku ustawienia opcji `ShouldCleanupOnStart` na wartość `true`.

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

1. Dodaj szczegółowe parametry konfiguracji bazy danych w pliku `App.config`. Zostaną one użyte do utworzenia wystąpienia klasy DocumentClient. Jeśli baza danych i kontener nie zostały jeszcze utworzone, zostaną utworzone automatycznie.
2. Uruchom aplikację. Spowoduje to dwukrotne wywołanie polecenia `BulkImportAsync` — raz w celu zaimportowania wierzchołków i raz w celu zaimportowania krawędzi. Jeśli dowolny z obiektów spowoduje błąd podczas wstawiania, zostanie dodany do pliku `.\BadVertices.txt` lub `.\BadEdges.txt`.
3. Oceń wyniki, wysyłając zapytania do bazy danych grafów. Jeśli opcja `ShouldCleanupOnFinish` ma wartość „true”, baza danych zostanie automatycznie usunięta.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat szczegółów pakietu NuGet i informacji o wersji biblioteki programu .NET do zbiorczego modułu wykonawczego, zobacz [Szczegóły zestawu SDK modułu wykonawczy](sql-api-sdk-bulk-executor-dot-net.md). 
* Zapoznaj się z [poradami dotyczącymi wydajności](./bulk-executor-dot-net.md#performance-tips) , aby bardziej zoptymalizować użycie modułu wykonawczy BULK.
* Zapoznaj się z artykułem informacyjnym na temat narzędzia [BulkExecutor.Graph](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?preserve-view=true&view=azure-dotnet), aby uzyskać szczegółowe informacje na temat klas i metod zdefiniowanych w tej przestrzeni nazw.