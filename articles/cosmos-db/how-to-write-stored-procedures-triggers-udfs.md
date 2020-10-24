---
title: Zapisuj procedury składowane, wyzwalacze i UDF w Azure Cosmos DB
description: Dowiedz się, jak definiować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 87d33308932ecd7c523c6fbe287648d4afc21ef0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92485804"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB

Usługa Azure Cosmos DB zapewnia zintegrowane z językiem, transakcyjne wykonywanie kodu JavaScript, które umożliwia pisanie **procedur składowanych**, **wyzwalaczy** i **funkcji zdefiniowanych przez użytkownika (UDF)**. Korzystając z interfejsu API SQL w usłudze Azure Cosmos DB, można definiować procedury składowane, wyzwalacze i funkcje UDF w języku JavaScript. Logikę można napisać w języku JavaScript, a następnie wykonać ją w aparacie bazy danych. Wyzwalacze, procedury składowane i funkcje UDF można tworzyć i wykonywać przy użyciu witryny [Azure Portal](https://portal.azure.com/), [zintegrowanego interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md) i [zestawów SDK klienta interfejsu API SQL usługi Cosmos DB](sql-api-dotnet-samples.md). 

Aby wywołać procedurę składowaną, wyzwalacz i funkcję zdefiniowaną przez użytkownika, należy je zarejestrować. Aby uzyskać więcej informacji, zobacz [Jak pracować z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> W przypadku kontenerów podzielonych na partycje podczas wykonywania procedury składowanej w opcjach żądania należy podać wartość klucza partycji. Procedury składowane są zawsze ograniczone do klucza partycji. Elementy, które mają inną wartość klucza partycji, nie będą widoczne dla procedury składowanej. Ma to również zastosowanie do wyzwalaczy.
> [!Tip]
> Program Cosmos obsługuje wdrażanie kontenerów za pomocą procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika. Aby uzyskać więcej informacji [, zobacz Tworzenie kontenera Azure Cosmos dB przy użyciu funkcji po stronie serwera.](./manage-with-templates.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Jak pisać procedury składowane

Procedury składowane pisze się przy użyciu języka JavaScript. Mogą one tworzyć, aktualizować, odczytywać i usuwać elementy w kontenerze usługi Azure Cosmos oraz wysyłać względem nich zapytania. Procedury składowane są rejestrowane w danej kolekcji i mogą operować na dowolnych dokumentach lub załącznikach znajdujących się w tej kolekcji.

Poniżej przedstawiono prostą procedurę składowaną, która zwraca odpowiedź „Hello World”.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Obiekt kontekstu zapewnia dostęp do wszystkich operacji, które mogą być wykonywane w usłudze Azure Cosmos DB, a także do obiektów żądań i odpowiedzi. W tym przypadku obiekt odpowiedzi jest używany w celu ustawienia wysłania treści odpowiedzi z powrotem do klienta.

Po napisaniu procedurę składowaną należy zarejestrować w kolekcji. Aby dowiedzieć się więcej, zobacz artykuł [Jak używać procedur składowanych w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures).

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Tworzenie elementu za pomocą procedury składowanej

Po utworzeniu elementu przy użyciu procedury składowanej, element zostanie wstawiony do kontenera usługi Azure Cosmos i zostanie zwrócony identyfikator nowo utworzonego elementu. Tworzenie elementu jest operacją asynchroniczną i zależy od funkcji wywołania zwrotnego języka JavaScript. Funkcja wywołania zwrotnego ma dwa parametry — jeden dla obiektu błędu na wypadek, gdyby operacja zakończyła się niepowodzeniem, a drugi dla wartości zwracanej, czyli w tym przypadku dla utworzonego obiektu. Wewnątrz wywołania zwrotnego można obsłużyć wyjątek lub zgłosić błąd. W przypadku, gdy wywołanie zwrotne nie zostanie podane i wystąpi błąd, środowisko uruchomieniowe usługi Azure Cosmos DB zgłosi błąd.

Procedura składowana obejmuje również parametr umożliwiający ustawienie opisu — jest to wartość logiczna. Jeśli dla tego parametru zostanie ustawiona wartość „true”, a opisu nie będzie, procedura składowana zgłosi wyjątek. W przeciwnym razie pozostała część procedury składowanej zostanie wykonana.

Następująca przykładowa procedura składowana pobiera tablicę nowych elementów Cosmos platformy Azure jako dane wejściowe, wstawia je do kontenera usługi Azure Cosmos i zwraca liczbę wstawionych elementów. W tym przykładzie wykorzystujemy próbkę ToDoList z [przewodnika Szybki start interfejsu API SQL platformy .NET](create-sql-api-dotnet.md)

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tablice jako parametry wejściowe dla procedur składowanych

Podczas definiowania procedury składowanej w witrynie Azure Portal parametry wejściowe są zawsze wysyłane do tej procedury składowanej jako ciąg. Nawet jeśli jako dane wejściowe przekażesz tablicę ciągów, tablica jest konwertowana na ciąg i wysyłana do procedury składowanej. Aby to obejść, można zdefiniować funkcję w swojej procedurze składowanej, aby przeanalizować ciąg jako tablicę. Poniższy kod pokazuje, jak można przeanalizować parametr wejściowy ciągu jako tablicę:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Transakcje w ramach procedur składowanych

Za pomocą procedury składowanej można zaimplementować transakcje na elementach w kontenerze. W poniższym przykładzie użyto transakcji w ramach aplikacji gry w ligę futbolową, aby wymieniać zawodników między dwoma drużynami w jednej operacji. Procedura składowana próbuje odczytać dwa elementy usługi Azure Cosmos, które odpowiadają identyfikatorom odtwarzacza przekazanym jako argument. Jeśli obydwaj zawodnicy zostaną odnalezieni, to procedura składowana aktualizuje te elementy, zamieniając ich drużyny. Jeśli po drodze zostaną napotkane jakiekolwiek błędy, procedura składowana zgłasza wyjątek języka JavaScript, który niejawnie przerywa transakcję.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Powiązane wykonywanie w ramach procedur składowanych

Poniżej przedstawiono przykład procedury składowanej, która zbiorczo importuje elementy do kontenera usługi Azure Cosmos. Procedura składowana obsługuje powiązane wykonywanie, sprawdzając wartość logiczną zwracaną z obiektu `createDocument`, a następnie używa liczby elementów wstawianych w każdym wywołaniu procedury składowanej do śledzenia i wznawiania postępu w partiach.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>Asynchroniczne oczekiwania z procedurami składowanymi

Poniżej znajduje się przykład procedury składowanej, która używa operacji Async-await z niesie obietnice zwiększenia przy użyciu funkcji pomocnika. Procedura składowana wykonuje zapytania dotyczące elementu i zastępuje go.

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Jak pisać wyzwalacze

Usługa Azure Cosmos DB obsługuje wyzwalacze wykonywane przed operacją (pre-trigger) i po operacji (post-trigger). Te pierwsze są wykonywane przed modyfikacją elementu bazy danych, a te drugie są wykonywane po modyfikacji elementu bazy danych.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Wyzwalacze wstępne

Poniższy przykład pokazuje, jak prewyzwalacz jest używany do sprawdzania poprawności właściwości elementu usługi Azure Cosmos, który jest tworzony. W tym przykładzie wykorzystujemy próbkę ToDoList z [przewodnika Szybki start interfejsu API SQL platformy .NET](create-sql-api-dotnet.md), aby dodać właściwość znacznika czasu do nowo dodanego elementu, jeśli jeszcze jej nie zawiera.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Wyzwalacze wykonywane przed operacją nie mogą mieć żadnych parametrów wejściowych. Obiekt żądania w wyzwalaczu służy do manipulowania komunikatem żądania skojarzonym z tą operacją. W poprzednim przykładzie przed utworzeniem elementu usługi Azure Cosmos jest uruchamiany przed wyzwalaczem, a treść komunikatu żądania zawiera element, który ma zostać utworzony w formacie JSON.

Podczas rejestrowania wyzwalaczy można określić operacje, z którymi można je uruchamiać. Ten wyzwalacz powinien zostać utworzony z wartością `TriggerOperation` operacji `TriggerOperation.Create`, co oznacza, że użycie tego wyzwalacza w operacji zamieniania, jak pokazano w poniższym kodzie, jest niedozwolone.

Aby zapoznać się z przykładami rejestrowania i wywoływania wyzwalacza wykonywanego przed operacją, zobacz artykuły na temat [wyzwalaczy wykonywanych przed operacją](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) i [wyzwalaczy wykonywanych po operacji](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

### <a name="post-triggers"></a><a id="post-triggers"></a>Wyzwalacze końcowe

W poniższym przykładzie pokazano wyzwalacz wykonywany po operacji. Ten wyzwalacz wysyła zapytanie o element metadanych i aktualizuje go za pomocą informacji o nowo utworzonym elemencie.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Jedną rzeczą, o której należy pamiętać, jest transakcyjne wykonywanie wyzwalaczy w usłudze Azure Cosmos DB. Wyzwalacz po uruchomieniu jest uruchamiany jako część tej samej transakcji dla samego elementu bazowego. Wyjątek podczas wykonywania po wyzwoleniu spowoduje niepowodzenie całej transakcji. Wszystkie przekazane elementy zostaną wycofane i zwrócono wyjątek.

Aby zapoznać się z przykładami rejestrowania i wywoływania wyzwalacza wykonywanego przed operacją, zobacz artykuły na temat [wyzwalaczy wykonywanych przed operacją](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) i [wyzwalaczy wykonywanych po operacji](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Jak pisać funkcje zdefiniowane przez użytkownika

Poniższy przykład pokazuje tworzenie funkcji zdefiniowanej przez użytkownika w celu obliczenia podatku dochodowego dla różnych przedziałów dochodu. Ta funkcja zdefiniowana przez użytkownika zostanie następnie użyta w zapytaniu. Na potrzeby tego przykładu załóżmy, że istnieje kontener o nazwie „Incomes” (Dochody) z następującymi właściwościami:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Poniższej przedstawiono definicję funkcji obliczającej podatek dochodowy dla różnych przedziałów dochodu:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Aby zapoznać się z przykładami rejestrowania i używania funkcji zdefiniowanej przez użytkownika, zobacz artykuł [Jak używać funkcji zdefiniowanych przez użytkownika w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs).

## <a name="logging"></a>Rejestrowanie 

Korzystając z procedury składowanej, wyzwalaczy lub funkcji zdefiniowanych przez użytkownika, można rejestrować kroki przy użyciu `console.log()` polecenia. To polecenie spowoduje skoncentrowanie ciągu do debugowania `EnableScriptLogging` , gdy ma wartość true, jak pokazano w następującym przykładzie:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Następne kroki

Poznaj dodatkowe pojęcia i dowiedz się, jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB:

* [Jak rejestrować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika oraz jak ich używać w usłudze Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Praca z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md)