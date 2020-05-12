---
title: Model ściągania zestawienia zmian
description: Dowiedz się, jak używać modelu ściągania Azure Cosmos DB zmian, aby odczytać Źródło zmian i różnice między modelem ściągania i procesorem źródła zmian
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116717"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Zmień model ściągania kanału informacyjnego w Azure Cosmos DB

Model ściągania źródła zmian umożliwia korzystanie ze źródła zmian Azure Cosmos DB w swoim własnym tempie. Podobnie jak w przypadku [procesora kanału informacyjnego zmian](change-feed-processor.md)można użyć modelu ściągania źródła zmian, aby zrównoleglanie przetwarzanie zmian wielu odbiorców z podajnikiem zmian.

> [!NOTE]
> Model ściągania źródła zmian jest obecnie w [wersji zapoznawczej tylko w Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview) . Wersja zapoznawcza nie jest jeszcze dostępna dla innych wersji zestawu SDK.

## <a name="consuming-an-entire-containers-changes"></a>Zużywanie zmian całego kontenera

Można utworzyć, `FeedIterator` Aby przetworzyć kanał informacyjny zmiany przy użyciu modelu ściągania. Podczas pierwszego tworzenia, można `FeedIterator` określić opcjonalny `StartTime` w obrębie `ChangeFeedRequestOptions` . Gdy nie zostanie określony, `StartTime` będzie to bieżąca godzina.

Znajduje się `FeedIterator` w dwóch wersjach. Oprócz przykładów poniżej, które zwracają obiekty jednostek, można również uzyskać odpowiedzi z pomocą `Stream` techniczną. Strumienie umożliwiają odczytywanie danych bez konieczności ich pierwszej deserializacji, co umożliwia zapisanie zasobów klientów.

Oto przykład do uzyskania `FeedIterator` , który zwraca obiekty Entity, w tym przypadku `User` obiekt:

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

Oto przykład do uzyskania `FeedIterator` , który zwraca `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

Korzystając z programu `FeedIterator` , można łatwo przetwarzać cały kanał informacyjny zmiany kontenera we własnym tempie. Przykład:

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>Zużywanie zmian klucza partycji

W niektórych przypadkach można tylko przetwarzać zmiany określonego klucza partycji. Możesz uzyskać `FeedIterator` dla określonego klucza partycji i przetworzyć zmiany w taki sam sposób, jak w przypadku całego kontenera:

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Korzystanie z FeedRange dla przetwarzanie równoległe

W [procesorze kanału informacyjnego zmiany](change-feed-processor.md)pracy jest automatycznie rozłożona na wielu użytkowników. W modelu ściągania źródła zmian można użyć, `FeedRange` Aby zrównoleglanie przetwarzanie źródła zmian. `FeedRange`Reprezentuje zakres wartości klucza partycji.

Oto przykład pokazujący, jak uzyskać listę zakresów dla kontenera:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Po uzyskaniu listy FeedRanges dla kontenera uzyskasz jedną `FeedRange` na [partycję fizyczną](partition-data.md#physical-partitions).

Przy użyciu `FeedRange` , można następnie utworzyć, `FeedIterator` Aby zrównoleglanie przetwarzanie źródła zmian na wielu maszynach lub wątkach. W przeciwieństwie do poprzedniego przykładu, w którym pokazano, jak uzyskać pojedynczy `FeedIterator` dla całego kontenera, można użyć, `FeedRange` Aby uzyskać wiele FeedIterators, które mogą jednocześnie przetwarzać Źródło zmian.

W przypadku, gdy chcesz korzystać z FeedRanges, musisz mieć proces programu Orchestrator, który uzyskuje FeedRanges i dystrybuuje je do tych maszyn. Ta dystrybucja może być:

* Użycie `FeedRange.ToJsonString` i dystrybucja tej wartości ciągu. Odbiorcy mogą używać tej wartości z`FeedRange.FromJsonString`
* Jeśli dystrybucja jest w toku, przekazanie odwołania do `FeedRange` obiektu.

Oto przykład, który pokazuje, jak czytać z początku kanału informacyjnego zmiany kontenera przy użyciu dwóch hipotetycznych oddzielnych maszyn, które są odczytywane równolegle:

Maszyna 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

Maszyna 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>Zapisywanie tokenów kontynuacji

Możesz zapisać pozycję `FeedIterator` przez utworzenie tokenu kontynuacji. Token kontynuacji jest wartością ciągu, która zachowuje śledzenie ostatnio przetworzonych zmian FeedIterator. Pozwala to na `FeedIterator` wznowienie w tym punkcie później. Poniższy kod zostanie odczytany przez źródło zmian od momentu utworzenia kontenera. Gdy nie będą dostępne żadne dodatkowe zmiany, będzie ona utrzymywać token kontynuacji, aby można było później wznowić użycie źródła zmian.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>Porównywanie z procesorem źródła zmian

Wiele scenariuszy może przetwarzać Źródło zmian przy użyciu [procesora](change-feed-processor.md) lub modelu ściągania. Token kontynuacji modelu ściągania i kontener dzierżawy procesora źródła zmian są "zakładkami" dla ostatniego przetworzonego elementu (lub partii elementów) w strumieniu zmian.
Nie można jednak skonwertować tokenów kontynuacji na kontener dzierżawy (lub odwrotnie).

Należy rozważyć użycie modelu ściągania w następujących scenariuszach:

- Chcesz wykonać jednorazowy odczyt istniejących danych ze źródła zmian.
- Chcesz tylko odczytywać zmiany z określonego klucza partycji
- Nie potrzebujesz modelu wypychania i chcesz użyć kanału informacyjnego zmiany we własnym tempie

Oto kilka najważniejszych różnic między procesorem kanału informacyjnego zmiany i modelem ściągania:

|  | Procesor zestawienia zmian| Model ściągania |
| --- | --- | --- |
| Śledzenie bieżącego punktu w strumieniu zmian przetwarzania | Dzierżawa (przechowywana w kontenerze Azure Cosmos DB) | Token kontynuacji (przechowywany w pamięci lub ręcznie utrwalony) |
| Możliwość powtórzenia ostatnich zmian | Tak, przy użyciu modelu wypychania | Tak, z modelem ściągania|
| Sondowanie w poszukiwaniu przyszłych zmian | Automatycznie sprawdza zmiany na podstawie określonych przez użytkownika`WithPollInterval` | Ręcznie |
| Przetwarzanie zmian z całego kontenera | Tak, i automatycznie przeparalleluje wiele wątków/maszyn z tego samego kontenera| Tak i ręcznie paralleled using FeedTokens |
| Przetwarzaj zmiany tylko z jednego klucza partycji | Nieobsługiwane | Yes|
| Poziom pomocy technicznej | Ogólnie dostępne | Wersja zapoznawcza |

## <a name="next-steps"></a>Następne kroki

* [Przegląd źródła zmian](change-feed.md)
* [Używanie procesora źródła zmian](change-feed-processor.md)
* [Wyzwalanie funkcji usługi Azure Functions](change-feed-functions.md)