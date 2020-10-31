---
title: Migrowanie z biblioteki wykonawców zbiorczych do pomocy technicznej zbiorczej w Azure Cosmos DB .NET v3 SDK
description: Dowiedz się, jak przeprowadzić migrację aplikacji z korzystania z biblioteki wykonawców zbiorczych do pomocy technicznej zbiorczej w zestawie Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: f7f51f6944de48e58ff53e7685164df3a04afe56
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075589"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migrowanie z biblioteki wykonawców zbiorczych do pomocy technicznej zbiorczej w Azure Cosmos DB .NET v3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano czynności wymagane do przeprowadzenia migracji kodu istniejącej aplikacji korzystającej z [biblioteki programu do zbiorczego wykonawcy platformy .NET](bulk-executor-dot-net.md) do funkcji [obsługi zbiorczej](tutorial-sql-api-dotnet-bulk-import.md) w najnowszej wersji zestawu .NET SDK.

## <a name="enable-bulk-support"></a>Włącz obsługę zbiorczą

Włącz obsługę zbiorczą dla tego `CosmosClient` wystąpienia za pomocą konfiguracji [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Utwórz zadania dla każdej operacji

Obsługa Zbiorcza w zestawie SDK platformy .NET działa z wykorzystaniem [równoległych operacji biblioteki zadań](/dotnet/standard/parallel-programming/task-parallel-library-tpl) i grupowania, które są wykonywane współbieżnie. 

W zestawie SDK nie ma pojedynczej metody, która będzie przyjmować listę dokumentów lub operacji jako parametr wejściowy, ale zamiast tego należy utworzyć zadanie dla każdej operacji, która ma zostać wykonana zbiorczo, a następnie po prostu poczekaj na ich zakończenie.

Na przykład, jeśli początkowe dane wejściowe to lista elementów, w których każdy element ma następujący schemat:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Jeśli chcesz przeprowadzić import zbiorczy (podobnie jak w przypadku korzystania z programu BulkExecutor. BulkImportAsync), musisz mieć współbieżne wywołania do `CreateItemAsync` . Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Jeśli chcesz przeprowadzić *aktualizację* zbiorczą (podobną do korzystania z [BulkExecutor. BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), musisz mieć współbieżne wywołania `ReplaceItemAsync` metody po zaktualizowaniu wartości elementu. Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

A jeśli chcesz przeprowadzić *usuwanie* zbiorcze (podobnie jak w przypadku korzystania z programu [BulkExecutor. BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), musisz mieć współbieżne wywołania do `DeleteItemAsync` , z `id` kluczem partycji i dla każdego elementu. Przykład:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Stan wyniku zadania przechwytywania

W poprzednich przykładach kodu utworzyliśmy współbieżną listę zadań i nazywamy `CaptureOperationResponse` metodę dla każdego z tych zadań. Ta metoda jest rozszerzeniem, które pozwala nam zachować *podobny schemat odpowiedzi* jako BulkExecutor, przechwytując wszelkie błędy i śledząc [użycie jednostek żądań](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Gdzie `OperationResponse` jest zadeklarowany jako:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Wykonywanie operacji współbieżnie

Aby śledzić zakres całej listy zadań, używamy tej klasy pomocnika:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`Metoda będzie czekać do momentu zakończenia wszystkich operacji i będzie można jej użyć w następujący sposób:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Statystyka przechwytywania

Poprzedni kod czeka, aż wszystkie operacje zostaną zakończone i obliczy wymagane dane statystyczne. Te statystyki są podobne do tego, że [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)biblioteki modułu wykonującego zbiorczo.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse`Zawiera:

1. Łączny czas potrzebny do przetworzenia listy operacji za pomocą obsługi zbiorczej.
1. Liczba operacji zakończonych powodzeniem.
1. Całkowita liczba zużytych jednostek żądania.
1. Jeśli wystąpią błędy, zostanie wyświetlona lista spójnych kolekcji zawierających wyjątek i skojarzony element do celów rejestrowania i identyfikacji.

## <a name="retry-configuration"></a>Ponów konfigurację

Biblioteka wykonawców zbiorczych ma [wskazówki](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) , które wymieniono w celu ustawienia `MaxRetryWaitTimeInSeconds` i `MaxRetryAttemptsOnThrottledRequests` [RetryOptions](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) do `0` delegowania kontroli do biblioteki.

Aby uzyskać obsługę zbiorczą w zestawie SDK platformy .NET, nie ma żadnych ukrytych zachowań. Opcje ponowień można skonfigurować bezpośrednio za pomocą [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) i [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> W przypadkach, gdy jednostki żądania aprowizacji są znacznie mniejsze niż oczekiwane na podstawie ilości danych, warto rozważyć ustawienie wartości górnych. Operacja zbiorcza zajmie więcej czasu, ale ma wyższą szansę na całkowite sukcesy z powodu wyższych ponownych prób.

## <a name="performance-improvements"></a>Ulepszenia wydajności

Podobnie jak w przypadku innych operacji przy użyciu zestawu .NET SDK, korzystanie z interfejsów API usługi Stream skutkuje lepszą wydajnością i pozwala uniknąć niepotrzebnej serializacji. 

Używanie interfejsów API usługi Stream jest możliwe tylko wtedy, gdy charakter używanych danych pasuje do strumienia bajtów (na przykład strumieni plików). W takich przypadkach użycie `CreateItemStreamAsync` `ReplaceItemStreamAsync` metod,, lub `DeleteItemStreamAsync` i praca z `ResponseMessage` (zamiast `ItemResponse` ) zwiększa przepływność, która może zostać osiągnięta.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wersjach zestawu SDK platformy .NET, zobacz artykuł [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) .
* Pobierz kompletny [kod źródłowy migracji](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) z usługi GitHub.
* [Dodatkowe przykłady zbiorcze w witrynie GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)