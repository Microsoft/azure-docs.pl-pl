---
title: Wykonywanie zapytania SQL w Azure Cosmos DB
description: Dowiedz się, jak utworzyć zapytanie SQL i wykonać je w Azure Cosmos DB. W tym artykule opisano sposób tworzenia i wykonywania zapytań SQL przy użyciu interfejsu API REST, zestawu SDK platformy .NET, zestawu SDK języka JavaScript i różnych zestawów SDK.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74871265"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB wykonywania zapytania SQL

Dowolny język obsługujący żądania HTTP/HTTPS może wywoływać interfejs API REST Cosmos DB. Cosmos DB oferuje również biblioteki programistyczne dla języków programowania .NET, Node.js, JavaScript i Python. Interfejs API REST i biblioteki obsługują zapytania za pomocą języka SQL, a zestaw .NET SDK obsługuje również [zapytania LINQ](sql-query-linq-to-sql.md).

W poniższych przykładach pokazano, jak utworzyć zapytanie i przesłać je do konta bazy danych Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>Interfejs API REST

Usługa Cosmos DB oferuje otwarty model programowania RESTful używający protokołu HTTP. Model zasobów składa się z zestawu zasobów w ramach konta bazy danych, który jest objęty subskrypcją platformy Azure. Konto bazy danych składa się z zestawu *baz danych*, z których każdy może zawierać wiele *kontenerów*, które z kolei zawierają *elementy*, UDF i inne typy zasobów. Każdy zasób Cosmos DB jest adresowany przy użyciu logicznego i stabilnego identyfikatora URI. Zestaw zasobów nosi nazwę *źródła danych*. 

Podstawowy model interakcji z tymi zasobami odbywa się za pośrednictwem czasowników HTTP `GET` ,, `PUT` `POST` i `DELETE` , przy użyciu standardowych interpretacji. Użyj `POST` , aby utworzyć nowy zasób, wykonać procedurę składowaną lub wydać zapytanie Cosmos DB. Zapytania są zawsze operacjami tylko do odczytu bez efektów ubocznych.

W poniższych przykładach pokazano `POST` zapytanie interfejsu API SQL dotyczące przykładowych elementów. Zapytanie ma prosty filtr dla `name` Właściwości JSON. `x-ms-documentdb-isquery`I Content-Type: `application/query+json` Heads oznacza, że operacja jest kwerendą. Zamień na `mysqlapicosmosdb.documents.azure.com:443` Identyfikator URI konta Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Wyniki są następujące:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Następne, bardziej złożone zapytanie zwraca wiele wyników z sprzężenia:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Wyniki są następujące: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Jeśli wyniki zapytania nie mogą zmieścić się na jednej stronie, interfejs API REST zwraca token kontynuacji w `x-ms-continuation-token` nagłówku odpowiedzi. Klienci mogą podzielić na strony wyniki, dołączając nagłówek do kolejnych wyników. Możesz również kontrolować liczbę wyników na stronie za pomocą `x-ms-max-item-count` nagłówka Number.

Jeśli zapytanie zawiera funkcję agregującą, taką jak COUNT, Strona zapytania może zwrócić częściowo zagregowaną wartość tylko dla jednej strony wyników. Klienci muszą wykonać agregację drugiego poziomu dla tych wyników, aby wygenerować wyniki końcowe. Na przykład można obliczyć sumę względem liczb zwracanych na poszczególnych stronach w celu zwrócenia łącznej liczby.

Aby zarządzać zasadami spójności danych dla zapytań, użyj `x-ms-consistency-level` nagłówka jako we wszystkich żądaniach interfejsu API REST. Spójność sesji wymaga również ECHA najnowszego `x-ms-session-token` nagłówka pliku cookie w żądaniu zapytania. Zasady indeksowania kontenera uwzględnionego w zapytaniu mogą również wpływać na spójność wyników zapytania. W przypadku domyślnych ustawień zasad indeksowania dla kontenerów indeks jest zawsze aktualny z zawartością elementu, a wyniki zapytania są zgodne ze spójnością wybraną dla danych. Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB poziomów spójności] [poziom spójności].

Jeśli skonfigurowane zasady indeksowania w kontenerze nie mogą obsługiwać określonego zapytania, serwer Azure Cosmos DB zwraca 400 "złe żądanie". Ten komunikat o błędzie zwraca zapytania o ścieżki jawnie wykluczone z indeksowania. Możesz określić nagłówek, `x-ms-documentdb-query-enable-scan` Aby zezwolić na wykonywanie przez kwerendę skanowania, gdy indeks nie jest dostępny.

Aby uzyskać szczegółowe metryki wykonywania zapytania, można ustawić `x-ms-documentdb-populatequerymetrics` nagłówek na `true` . Aby uzyskać więcej informacji, zobacz [SQL query metrics for Azure Cosmos DB (Metryki zapytań języka SQL dla usługi Azure Cosmos DB)](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (ZESTAW SDK DLA PLATFORMY .NET)

Zestaw .NET SDK obsługuje wykonywanie zapytań LINQ i SQL. Poniższy przykład pokazuje, jak wykonać powyższe zapytanie filtru z platformą .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Poniższy przykład porównuje dwie właściwości dla równości w poszczególnych elementach i używa anonimowych projekcji.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

W następnym przykładzie pokazano sprzężenia wyrażone za pomocą LINQ `SelectMany` .

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Klient platformy .NET automatycznie wykonuje iterację na wszystkich stronach wyników zapytania w `foreach` blokach, jak pokazano w poprzednim przykładzie. Opcje zapytania wprowadzone w sekcji [interfejs API REST](#REST-API) są również dostępne w zestawie .NET SDK przy użyciu `FeedOptions` `FeedResponse` klas i w `CreateDocumentQuery` metodzie. Liczbę stron można kontrolować za pomocą `MaxItemCount` Ustawienia.

Można również jawnie kontrolować stronicowanie, tworząc `IDocumentQueryable` przy użyciu `IQueryable` obiektu, odczytując `ResponseContinuationToken` wartości i przekazując je ponownie jako `RequestContinuationToken` `FeedOptions` . Można ustawić, `EnableScanInQuery` Aby włączyć skanowanie, gdy zapytanie nie jest obsługiwane przez skonfigurowane zasady indeksowania. W przypadku kontenerów partycjonowanych można użyć `PartitionKey` programu, aby uruchomić zapytanie względem pojedynczej partycji, chociaż Azure Cosmos DB może automatycznie wyodrębnić ten element z tekstu zapytania. Za pomocą `EnableCrossPartitionQuery` programu można uruchamiać zapytania dotyczące wielu partycji.

Aby uzyskać więcej przykładów platformy .NET z zapytaniami, zobacz [przykłady Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) w witrynie GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>Interfejs API języka JavaScript po stronie serwera

Azure Cosmos DB zapewnia model programowania do [wykonywania logiki aplikacji opartej na języku JavaScript](stored-procedures-triggers-udfs.md) bezpośrednio w kontenerach za pomocą procedur składowanych i wyzwalaczy. Logika JavaScript zarejestrowana na poziomie kontenera może następnie wystawiać operacje bazy danych na elementach danego kontenera, opakowanych w transakcjach w środowisku otoczenia.

Poniższy przykład pokazuje, jak używać `queryDocuments` programu w interfejsie API serwera JavaScript do wykonywania zapytań w procedurach składowanych i wyzwalaczach:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure Cosmos DB poziomów spójności](consistency-levels.md)
