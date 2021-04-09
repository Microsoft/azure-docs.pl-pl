---
title: Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak używać usługi Change Streams n Azure Cosmos DB API MongoDB, aby uzyskać zmiany wprowadzone do danych.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 941fe8929b75fdebf187186ca7078b0ae1dd261c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658525"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Zmień strumienie w interfejsie API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Obsługa [kanałów zmian](change-feed.md) w interfejsie API Azure Cosmos DB dla MongoDB jest dostępna za pomocą interfejsu API zmiany strumieni. Za pomocą interfejsu API Zmień strumienie aplikacje mogą pobrać zmiany wprowadzone do kolekcji lub do elementów w jednym fragmentu. Później można wykonać dalsze czynności na podstawie wyników. Zmiany elementów w kolekcji są przechwytywane w kolejności ich modyfikacji, a porządek sortowania jest gwarantowany na klucz fragmentu.

> [!NOTE]
> Aby można było używać strumieni zmian, należy utworzyć interfejs API Azure Cosmos DB dla konta MongoDB z serwerem w wersji 3,6 lub nowszej. W przypadku uruchomienia przykładów zmiany strumienia dla starszej wersji może zostać wyświetlona *Nierozpoznana Nazwa etapu potoku: $changeStream* błędu.

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

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje, jak używać funkcji zmiany strumienia w języku Java, aby zapoznać się z pełnym przykładem w tym [repozytorium GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). Ten przykład pokazuje również, jak używać `resumeAfter` metody, aby wyszukać wszystkie zmiany od ostatniego odczytu. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

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
* `insert` `update` Typy operacji, i `replace` są obecnie obsługiwane. Jednak operacja usuwania lub inne zdarzenia nie są jeszcze obsługiwane.

Ze względu na te ograniczenia wymagane są opcje etap $match, etap $project i fullDocument, jak pokazano w poprzednich przykładach.

W przeciwieństwie do źródła zmian w interfejsie API SQL Azure Cosmos DB nie istnieje oddzielna [Biblioteka procesora kanału informacyjnego zmian](change-feed-processor.md) , która umożliwia korzystanie z strumienia zmian lub potrzeby kontenera dzierżawy. Obecnie nie są obsługiwane [wyzwalacze Azure Functions](change-feed-functions.md) do przetwarzania strumieni zmian.

## <a name="error-handling"></a>Obsługa błędów

Podczas używania strumieni zmian są obsługiwane następujące kody błędów i komunikaty:

* **Kod błędu HTTP 16500** — gdy strumień zmiany jest ograniczany, zwraca pustą stronę.

* **NamespaceNotFound (OperationType unvalidate)** — w przypadku uruchomienia strumienia zmian w kolekcji, która nie istnieje lub jeśli kolekcja została porzucona, `NamespaceNotFound` zwracany jest błąd. Ponieważ `operationType` Właściwość nie może zostać zwrócona w dokumencie wyjściowym, a nie w przypadku `operationType Invalidate` błędu, `NamespaceNotFound` zwracany jest błąd.

## <a name="next-steps"></a>Następne kroki

* [Użyj czasu wygaśnięcia, aby automatycznie wygasać dane w interfejsie API Azure Cosmos DB MongoDB](mongodb-time-to-live.md)
* [Indeksowanie w interfejsie API Azure Cosmos DB dla MongoDB](mongodb-indexing.md)
