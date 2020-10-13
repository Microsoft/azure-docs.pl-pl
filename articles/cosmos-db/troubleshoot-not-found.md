---
title: Rozwiązywanie problemów z nieznalezionymi wyjątkami Azure Cosmos DB
description: Dowiedz się, jak diagnozować i rozwiązywać problemy z nieznalezionymi wyjątkami.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f32a37d5d08e8b20e59455393c70e4e4d288eb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802400"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>Diagnozowanie i rozwiązywanie problemów z nieznalezionymi wyjątkami Azure Cosmos DB
Kod stanu HTTP 404 oznacza, że zasób już nie istnieje.

## <a name="expected-behavior"></a>Oczekiwane zachowanie
Istnieje wiele prawidłowych scenariuszy, w których aplikacja oczekuje kodu 404 i poprawnie obsługuje scenariusz.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Zwrócony wyjątek nie został znaleziony dla elementu, który powinien istnieć lub istnieje
Oto możliwe przyczyny, dla których kod stanu 404 ma być zwracany, jeśli element powinien istnieć lub już istnieje.

### <a name="race-condition"></a>Sytuacja wyścigu
Istnieje wiele wystąpień klienta SDK i odczytywanie zaszło przed zapisem.

#### <a name="solution"></a>Rozwiązanie:
1. Domyślna spójność konta Azure Cosmos DB jest spójna z sesją. Gdy element zostanie utworzony lub zaktualizowany, odpowiedź zwróci token sesji, który można przekazywać między wystąpieniami zestawu SDK w celu zagwarantowania, że żądanie odczytu odczytuje z repliki z tą zmianą.
1. Zmień [poziom spójności](consistency-levels-choosing.md) na [silniejszy poziom](consistency-levels-tradeoffs.md).

### <a name="invalid-partition-key-and-id-combination"></a>Nieprawidłowa kombinacja klucza partycji i identyfikatora
Kombinacja klucza partycji i identyfikatora jest nieprawidłowa.

#### <a name="solution"></a>Rozwiązanie:
Popraw logikę aplikacji powodującą niepoprawną kombinację. 

### <a name="invalid-character-in-an-item-id"></a>Nieprawidłowy znak w IDENTYFIKATORze elementu
Element zostanie wstawiony do Azure Cosmos DB z [nieprawidłowym znakiem](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks) w identyfikatorze elementu.

#### <a name="solution"></a>Rozwiązanie:
Zmień identyfikator na inną wartość, która nie zawiera znaków specjalnych. Jeśli zmiana identyfikatora nie jest opcją, można zakodować w formacie base64 identyfikator do ucieczki znaków specjalnych.

Elementy znajdujące się już w kontenerze dla identyfikatora mogą zostać zastąpione przez użycie wartości RID zamiast odwołań opartych na nazwach.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Czas do przeczyszczenia na żywo
Element miał ustawioną właściwość [Time to Live (TTL)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) . Element został przeczyszczony, ponieważ właściwość TTL wygasła.

#### <a name="solution"></a>Rozwiązanie:
Zmień właściwość TTL, aby uniemożliwić przeczyszczenie elementu.

### <a name="lazy-indexing"></a>Indeksowanie z opóźnieniem
[Indeksowanie z opóźnieniem](index-policy.md#indexing-mode) nie zostało przechwycone.

#### <a name="solution"></a>Rozwiązanie:
Poczekaj na przechwycenie lub zmianę zasad indeksowania przez indeksowanie.

### <a name="parent-resource-deleted"></a>Usunięto zasób nadrzędny
Baza danych lub kontener, w którym znajduje się element, został usunięty.

#### <a name="solution"></a>Rozwiązanie:
1. [Przywróć](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) zasób nadrzędny lub ponownie utwórz zasoby.
1. Utwórz nowy zasób, aby zastąpić usunięty zasób.

### <a name="7-containercollection-names-are-case-sensitive"></a>7. nazwy kontenerów/kolekcji są rozróżniane wielkości liter
Nazwy kontenerów/kolekcji to Case-sesnsitive w Cosmos DB.

#### <a name="solution"></a>Rozwiązanie:
Upewnij się, że używasz dokładnej nazwy podczas nawiązywania połączenia z Cosmos DB.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).