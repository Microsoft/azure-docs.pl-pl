---
title: Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak używać usługi Change Streams n Azure Cosmos DB API MongoDB, aby uzyskać zmiany wprowadzone do danych.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/04/2020
ms.author: srchi
ms.openlocfilehash: 4b159ef897185dc0c886b525e5fdf38cd919b8cc
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465715"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB

Obsługa [kanałów zmian](change-feed.md) w interfejsie API Azure Cosmos DB dla MongoDB jest dostępna za pomocą interfejsu API zmiany strumieni. Za pomocą interfejsu API Zmień strumienie aplikacje mogą pobrać zmiany wprowadzone do kolekcji lub do elementów w jednym fragmentu. Później można wykonać dalsze czynności na podstawie wyników. Zmiany elementów w kolekcji są przechwytywane w kolejności ich modyfikacji, a porządek sortowania jest gwarantowany na klucz fragmentu.

> [!NOTE]
> Aby można było używać strumieni zmian, należy utworzyć konto z wersją 3,6 interfejsu API Azure Cosmos DB dla MongoDB lub nowszej wersji. W przypadku uruchomienia przykładów zmiany strumienia dla starszej wersji może zostać wyświetlony `Unrecognized pipeline stage name: $changeStream` błąd.

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania ze strumieni zmian stosowane są następujące ograniczenia:

* `operationType`Właściwości i `updateDescription` nie są jeszcze obsługiwane w dokumencie wyjściowym.
* `insert` `update` Typy operacji, i `replace` są obecnie obsługiwane. 
* Operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje etap $match, etap $project i fullDocument, jak pokazano w poprzednich przykładach.

W przeciwieństwie do źródła zmian w interfejsie API SQL Azure Cosmos DB nie istnieje oddzielna [Biblioteka procesora kanału informacyjnego zmian](change-feed-processor.md) , która umożliwia korzystanie z strumienia zmian lub potrzeby kontenera dzierżawy. Obecnie nie są obsługiwane [wyzwalacze Azure Functions](change-feed-functions.md) do przetwarzania strumieni zmian.

## <a name="error-handling"></a>Obsługa błędów

Podczas używania strumieni zmian są obsługiwane następujące kody błędów i komunikaty:

* **Kod błędu HTTP 16500** — gdy strumień zmiany jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType unvalidate)** — w przypadku uruchomienia strumienia zmian w kolekcji, która nie istnieje lub jeśli kolekcja została porzucona, `NamespaceNotFound` zwracany jest błąd. Ponieważ `operationType` Właściwość nie może zostać zwrócona w dokumencie wyjściowym, a nie w przypadku `operationType Invalidate` błędu, `NamespaceNotFound` zwracany jest błąd.

## <a name="examples"></a>Przykłady

Poniższy przykład pokazuje, jak pobrać strumienie zmian dla wszystkich elementów w kolekcji. Ten przykład umożliwia utworzenie kursora do oglądania elementów, gdy są one wstawiane, aktualizowane lub zastępowane. `$match` `$project` `fullDocument` Do pobrania strumieni zmian są wymagane etapy, etapy i opcje. Obserwowanie operacji usuwania przy użyciu strumieni zmian nie jest obecnie obsługiwane. Obejście tego problemu pozwala na dodanie znacznika miękkiego do elementów, które są usuwane. Na przykład, można dodać atrybut w elemencie o nazwie "usunięte". Jeśli chcesz usunąć element, możesz ustawić wartość "usunięty" `true` i ustawić wartość czasu wygaśnięcia dla elementu. Ponieważ aktualizacja "usunięty" `true` jest aktualizacją, ta zmiana będzie widoczna w strumieniu zmiany.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```
# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Zmiany w ramach jednego fragmentuu

Poniższy przykład pokazuje, jak uzyskać zmiany w elementach w ramach jednego fragmentu. Ten przykład pobiera zmiany elementów, które mają klucz fragmentu równy "a", a wartość klucza fragmentu równą "1". Istnieje możliwość, że różni klienci czytają zmiany z różnych fragmentów równolegle.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania ze strumieni zmian stosowane są następujące ograniczenia:

* `operationType`Właściwości i `updateDescription` nie są jeszcze obsługiwane w dokumencie wyjściowym.
* `insert` `update` Typy operacji, i `replace` są obecnie obsługiwane. Operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje etap $match, etap $project i fullDocument, jak pokazano w poprzednich przykładach.

## <a name="error-handling"></a>Obsługa błędów

Podczas używania strumieni zmian są obsługiwane następujące kody błędów i komunikaty:

* **Kod błędu HTTP 429** — gdy strumień zmiany jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType unvalidate)** — w przypadku uruchomienia strumienia zmian w kolekcji, która nie istnieje lub jeśli kolekcja została porzucona, `NamespaceNotFound` zwracany jest błąd. Ponieważ `operationType` Właściwość nie może zostać zwrócona w dokumencie wyjściowym, a nie w przypadku `operationType Invalidate` błędu, `NamespaceNotFound` zwracany jest błąd.

## <a name="next-steps"></a>Następne kroki

* [Użyj czasu wygaśnięcia, aby automatycznie wygasać dane w interfejsie API Azure Cosmos DB MongoDB](mongodb-time-to-live.md)
* [Indeksowanie w interfejsie API Azure Cosmos DB dla MongoDB](mongodb-indexing.md)