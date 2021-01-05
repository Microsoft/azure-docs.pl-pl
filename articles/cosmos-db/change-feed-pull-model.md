---
title: Model ściągania zestawienia zmian
description: Dowiedz się, jak używać modelu ściągania Azure Cosmos DB zmian, aby odczytać Źródło zmian i różnice między modelem ściągania i procesorem źródła zmian
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/04/2021
ms.reviewer: sngun
ms.openlocfilehash: e227e230c4de1234e068f72958367dc2ac709426
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881977"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Zmień model ściągania kanału informacyjnego w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Model ściągania źródła zmian umożliwia korzystanie ze źródła zmian Azure Cosmos DB w swoim własnym tempie. Podobnie jak w przypadku [procesora kanału informacyjnego zmian](change-feed-processor.md)można użyć modelu ściągania źródła zmian, aby zrównoleglanie przetwarzanie zmian wielu odbiorców z podajnikiem zmian.

> [!NOTE]
> Model ściągania źródła zmian jest obecnie w [wersji zapoznawczej tylko w Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.15.0-preview) . Wersja zapoznawcza nie jest jeszcze dostępna dla innych wersji zestawu SDK.

## <a name="comparing-with-change-feed-processor"></a>Porównywanie z procesorem źródła zmian

Wiele scenariuszy może przetwarzać Źródło zmian przy użyciu [procesora](change-feed-processor.md) lub modelu ściągania. Token kontynuacji modelu ściągania i kontener dzierżawy procesora źródła zmian są "zakładkami" dla ostatniego przetworzonego elementu (lub partii elementów) w strumieniu zmian.

Nie można jednak skonwertować tokenów kontynuacji na kontener dzierżawy (lub odwrotnie).

> [!NOTE]
> W większości przypadków, gdy konieczne jest odczytanie ze źródła zmian, najprostszą opcją jest użycie [procesora źródła zmian](change-feed-processor.md).

Należy rozważyć użycie modelu ściągania w następujących scenariuszach:

- Odczytaj zmiany z określonego klucza partycji
- Sterowanie tempem, w którym klient otrzymuje zmiany w celu przetworzenia
- Wykonaj jednorazowy odczyt istniejących danych ze źródła zmian (na przykład w celu przeprowadzenia migracji danych)

Oto kilka najważniejszych różnic między procesorem kanału informacyjnego zmiany i modelem ściągania:

|Cechy  | Procesor zestawienia zmian| Model ściągania |
| --- | --- | --- |
| Śledzenie bieżącego punktu w strumieniu zmian przetwarzania | Dzierżawa (przechowywana w kontenerze Azure Cosmos DB) | Token kontynuacji (przechowywany w pamięci lub ręcznie utrwalony) |
| Możliwość powtórzenia ostatnich zmian | Tak, przy użyciu modelu wypychania | Tak, z modelem ściągania|
| Sondowanie w poszukiwaniu przyszłych zmian | Automatycznie sprawdza zmiany na podstawie określonych przez użytkownika `WithPollInterval` | Ręcznie |
| Zachowanie w przypadku braku nowych zmian | Automatycznie czekaj `WithPollInterval` i ponownie sprawdzaj | Należy przechwycić wyjątek i ręcznie ponownie sprawdzić |
| Przetwarzanie zmian z całego kontenera | Tak, i automatycznie przeparalleluje wiele wątków/maszyn z tego samego kontenera| Tak i ręcznie paralleled using FeedTokens |
| Przetwarzaj zmiany tylko z jednego klucza partycji | Nieobsługiwane | Tak|
| Poziom pomocy technicznej | Ogólnie dostępne | Wersja zapoznawcza |

> [!NOTE]
> W przeciwieństwie do odczytu przy użyciu procesora zmian, należy jawnie obsługiwać przypadki, w których nie ma żadnych nowych zmian. 

## <a name="consuming-an-entire-containers-changes"></a>Zużywanie zmian całego kontenera

Można utworzyć, `FeedIterator` Aby przetworzyć kanał informacyjny zmiany przy użyciu modelu ściągania. Podczas pierwszego tworzenia należy `FeedIterator` określić wymaganą `ChangeFeedStartFrom` wartość, która składa się zarówno z pozycji początkowej, jak i w razie potrzeby `FeedRange` . `FeedRange`Jest zakresem wartości klucza partycji i określa elementy, które będą odczytywane ze źródła zmian przy użyciu tego konkretnego elementu `FeedIterator` .

Opcjonalnie możesz określić, `ChangeFeedRequestOptions` Aby ustawić wartość `PageSizeHint` . `PageSizeHint`Jest to maksymalna liczba elementów, które zostaną zwrócone na jednej stronie.

Znajduje się `FeedIterator` w dwóch wersjach. Oprócz przykładów poniżej, które zwracają obiekty jednostek, można również uzyskać odpowiedzi z pomocą `Stream` techniczną. Strumienie umożliwiają odczytywanie danych bez konieczności ich pierwszej deserializacji, co umożliwia zapisanie zasobów klientów.

Oto przykład do uzyskania `FeedIterator` , który zwraca obiekty Entity, w tym przypadku `User` obiekt:

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

Oto przykład do uzyskania `FeedIterator` , który zwraca `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

Jeśli nie podasz elementu `FeedRange` a `FeedIterator` , możesz przetwarzać cały kanał informacyjny zmiany kontenera we własnym tempie. Oto przykład, który rozpoczyna odczyt wszystkich zmian, rozpoczynając od bieżącego czasu:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Ze względu na to, że źródło zmian jest efektywnie nieograniczoną listą elementów obejmujących wszystkie przyszłe zapisy i aktualizacje, wartość `HasMoreResults` jest zawsze równa true. Gdy próbujesz odczytać Źródło zmian i nie ma żadnych nowych zmian, otrzymasz wyjątek. W powyższym przykładzie wyjątek jest obsługiwany przez oczekiwanie 5 sekund przed ponownym sprawdzeniem pod kątem zmian.

## <a name="consuming-a-partition-keys-changes"></a>Zużywanie zmian klucza partycji

W niektórych przypadkach można tylko przetwarzać zmiany określonego klucza partycji. Możesz uzyskać `FeedIterator` dla określonego klucza partycji i przetworzyć zmiany w taki sam sposób, jak w przypadku całego kontenera.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Korzystanie z FeedRange dla przetwarzanie równoległe

W [procesorze kanału informacyjnego zmiany](change-feed-processor.md)pracy jest automatycznie rozłożona na wielu użytkowników. W modelu ściągania źródła zmian można użyć, `FeedRange` Aby zrównoleglanie przetwarzanie źródła zmian. `FeedRange`Reprezentuje zakres wartości klucza partycji.

Oto przykład pokazujący, jak uzyskać listę zakresów dla kontenera:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Po uzyskaniu listy FeedRanges dla kontenera uzyskasz jedną `FeedRange` na [partycję fizyczną](partitioning-overview.md#physical-partitions).

Przy użyciu `FeedRange` , można następnie utworzyć, `FeedIterator` Aby zrównoleglanie przetwarzanie źródła zmian na wielu maszynach lub wątkach. W przeciwieństwie do poprzedniego przykładu, w którym pokazano, jak uzyskać `FeedIterator` dla całego kontenera lub pojedynczego klucza partycji, można użyć FeedRanges, aby uzyskać wiele FeedIterators, które mogą równolegle przetwarzać Źródło zmian.

W przypadku, gdy chcesz korzystać z FeedRanges, musisz mieć proces programu Orchestrator, który uzyskuje FeedRanges i dystrybuuje je do tych maszyn. Ta dystrybucja może być:

* Użycie `FeedRange.ToJsonString` i dystrybucja tej wartości ciągu. Odbiorcy mogą używać tej wartości z `FeedRange.FromJsonString`
* Jeśli dystrybucja jest w toku, przekazanie odwołania do `FeedRange` obiektu.

Oto przykład, który pokazuje, jak czytać z początku kanału informacyjnego zmiany kontenera przy użyciu dwóch hipotetycznych oddzielnych maszyn, które są odczytywane równolegle:

Maszyna 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

Maszyna 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }
}
```

## <a name="saving-continuation-tokens"></a>Zapisywanie tokenów kontynuacji

Możesz zapisać pozycję `FeedIterator` przez utworzenie tokenu kontynuacji. Token kontynuacji jest wartością ciągu, która zachowuje śledzenie ostatnio przetworzonych zmian FeedIterator. Pozwala to na `FeedIterator` wznowienie w tym punkcie później. Poniższy kod zostanie odczytany przez źródło zmian od momentu utworzenia kontenera. Gdy nie będą dostępne żadne dodatkowe zmiany, będzie ona utrzymywać token kontynuacji, aby można było później wznowić użycie źródła zmian.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        Thread.Sleep(5000);
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

Tak długo, jak kontener Cosmos nadal istnieje, token kontynuacji FeedIterator nigdy nie wygasa.

## <a name="next-steps"></a>Następne kroki

* [Przegląd źródła zmian](change-feed.md)
* [Używanie procesora źródła zmian](change-feed-processor.md)
* [Wyzwalanie funkcji usługi Azure Functions](change-feed-functions.md)