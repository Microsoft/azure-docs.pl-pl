---
title: Rozwiązywanie problemów dotyczących wyjątku nie znaleziono Azure Cosmos DB
description: Jak zdiagnozować i naprawić wyjątek nie znaleziono
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294623"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Nie znaleziono Azure Cosmos DB diagnozowania i rozwiązywania problemów
Kod stanu HTTP 404 oznacza, że zasób już nie istnieje.

## <a name="expected-behavior"></a>Oczekiwane zachowanie
Istnieje wiele prawidłowych scenariuszy, w których aplikacja oczekuje na 404, a prawidłowo obsługuje scenariusz.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Nie znaleziono dla elementu, który powinien istnieć lub istnieje
Poniżej przedstawiono możliwą przyczynę zwrócenia kodu stanu 404, jeśli element powinien lub kończy działanie.

### <a name="1-race-condition"></a>1. warunek wyścigu
Istnieje wiele wystąpień klienta SDK i odczytywanie zaszło przed zapisem.

#### <a name="solution"></a>Rozwiązanie:
1. Domyślna spójność konta Cosmos DB jest spójna z sesją. Gdy element zostanie utworzony lub zaktualizowany, odpowiedź zwróci token sesji, który może zostać przesłany między wystąpieniami zestawu SDK w celu zagwarantowania, że żądanie odczytu jest odczytywane z repliki z tą zmianą.
2. Zmiana [poziomu spójności](consistency-levels-choosing.md) na [silniejszy](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. Nieprawidłowa kombinacja klucza partycji i identyfikatora.
Kombinacja klucza partycji i identyfikatora jest nieprawidłowa.

#### <a name="solution"></a>Rozwiązanie:
Popraw logikę aplikacji powodującą niepoprawną kombinację. 

### <a name="3-invalid-character-in-item-id"></a>3. nieprawidłowy znak w IDENTYFIKATORze elementu
Element zostanie wstawiony do Cosmos DB z [nieprawidłowym znakiem](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) w identyfikatorze elementu.

#### <a name="solution"></a>Rozwiązanie:
Zaleca się, aby użytkownicy mogli zmienić identyfikator na inną wartość, która nie zawiera znaków specjalnych. Jeśli zmiana identyfikatora nie jest opcją, można zakodować w formacie base64 identyfikator w celu ucieczki znaków specjalnych.

Elementy, które zostały już wstawione do kontenera, można zastąpić IDENTYFIKATORem przy użyciu wartości RID zamiast odwołań opartych na nazwach.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. przeczyszczenie czasu wygaśnięcia (TTL)
Element miał ustawioną właściwość [Time to Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . Element został przeczyszczony, ponieważ czas wygaśnięcia upłynął.

#### <a name="solution"></a>Rozwiązanie:
Zmień wartość czasu wygaśnięcia, aby uniemożliwić przeczyszczenie elementu.

### <a name="5-lazy-indexing"></a>5. indeksowanie z opóźnieniem
[Indeksowanie z opóźnieniem](index-policy.md#indexing-mode) nie zostało przechwycone.

#### <a name="solution"></a>Rozwiązanie:
Poczekaj na przechwycenie lub zmianę zasad indeksowania przez indeksowanie

### <a name="6-parent-resource-deleted"></a>6. Usunięto zasób nadrzędny
Baza danych i/lub kontener, w którym znajduje się element, zostały usunięte.

#### <a name="solution"></a>Rozwiązanie:
1. [Przywróć](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) zasób nadrzędny lub ponownie utwórz zasoby.
2. Utwórz nowy zasób, aby zastąpić usunięty zasób

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)