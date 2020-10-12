---
title: Azure Cosmos DB powiązania danych wejściowych dla funkcji 2. x i wyższych
description: Dowiedz się, jak używać powiązania danych wejściowych Azure Cosmos DB w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 42932d712d6c4a94cad28aec924b88fbc126662b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212794"
---
# <a name="azure-cosmos-db-input-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB powiązania danych wejściowych dla Azure Functions 2. x i wyższych

Powiązanie wejściowe usługi Azure Cosmos DB używa interfejsu API SQL, aby pobrać co najmniej jeden dokument usługi Azure Cosmos DB, a następnie przekazuje go do parametru wejściowego funkcji. Identyfikator dokumentu lub parametry zapytania można określić na podstawie wyzwalacza wywołującego funkcję.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-cosmosdb-v2.md).

> [!NOTE]
> Jeśli kolekcja jest [podzielona na partycje](../cosmos-db/partition-data.md#logical-partitions), operacje wyszukiwania muszą również określać wartość klucza partycji.
>

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Przykłady odnoszą się do `ToDoItem` typu prostego:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        [JsonProperty("id")]
        public string Id { get; set; }
        
        [JsonProperty("partitionKey")]
        public string PartitionKey { get; set; }
        
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON 

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera obiekt JSON. Wyzwalacz kolejki analizuje kod JSON w obiekcie typu `ToDoItemLookup` , który zawiera wartość identyfikatora i klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

>[!NOTE]
>W parametrze ciągu zapytania HTTP jest rozróżniana wielkość liter.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

W przykładzie pokazano, jak używać wyrażenia powiązania w `SqlQuery` parametrze. Dane trasy można przekazać do `SqlQuery` parametru, jak pokazano, ale obecnie [nie można przekazać wartości ciągu zapytania](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583).

> [!NOTE]
> Jeśli zachodzi potrzeba przeszukiwania tylko identyfikatora, zaleca się użycie wyszukiwania, podobnie jak w [poprzednich przykładach](#http-trigger-look-up-id-from-query-string-c), ponieważ będzie zużywał mniej [jednostek żądania](../cosmos-db/request-units.md). Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we `SqlQuery` właściwości atrybutu.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` wystąpienia dostarczonego przez powiązanie Azure Cosmos DB, aby odczytać listę dokumentów. `DocumentClient`Wystąpienie może być również używane na potrzeby operacji zapisu.

> [!NOTE]
> Aby ułatwić testowanie, można również użyć interfejsu [IDocumentClient](/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) .

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z ciągu](#queue-trigger-look-up-id-from-string-c-script)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-c-script)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-c-script)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Przykłady wyzwalacza HTTP odnoszą się do `ToDoItem` typu prostego:

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z ciągu

W poniższym przykładzie pokazano Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano Azure Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId` . Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we `SqlQuery` właściwości atrybutu.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` wystąpienia dostarczonego przez powiązanie Azure Cosmos DB, aby odczytać listę dokumentów. `DocumentClient`Wystąpienie może być również używane na potrzeby operacji zapisu.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ta sekcja zawiera następujące przykłady odczytywania pojedynczego dokumentu przez określenie wartości identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-javascript)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-javascript)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

W poniższym przykładzie pokazano Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano Azure Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId` . Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

Ta sekcja zawiera następujące przykłady odczytywania pojedynczego dokumentu przez określenie wartości identyfikatora z różnych źródeł:

* [Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON](#queue-trigger-look-up-id-from-json-python)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania](#http-trigger-look-up-id-from-query-string-python)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-python)
* [Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

W poniższym przykładzie pokazano Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja odczytuje pojedynczy dokument i aktualizuje wartość tekstową dokumentu.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje funkcję języka [Python](functions-reference-python.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "scriptFile": "__init__.py"
}
```

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje funkcję języka [Python](functions-reference-python.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora i wartości klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz kolejki, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

W poniższym przykładzie pokazano Azure Cosmos DB powiązania wejściowego w *function.js* pliku i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Funkcja pobiera wiele dokumentów określonych przez zapytanie SQL, przy użyciu wyzwalacza kolejki, aby dostosować parametry zapytania.

Wyzwalacz kolejki zawiera parametr `departmentId` . Komunikat w kolejce `{ "departmentId" : "Finance" }` zwróci wszystkie rekordy działu finansowego.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod języka Python:

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukiwanie identyfikatora na podstawie parametru ciągu zapytania](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania — parametr POJO](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy](#http-trigger-look-up-id-from-route-data-java)
* [Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [Wyzwalacz HTTP, pobieranie wielu dokumentów z danych trasy przy użyciu sqlQuery](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

Przykłady odnoszą się do `ToDoItem` typu prostego:

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora na podstawie parametru ciągu zapytania

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z określonej bazy danych i kolekcji w postaci ciągu.

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@CosmosDBInput` adnotacji w parametrach funkcji, których wartość pochodzi z Cosmos DB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania — parametr POJO

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji używane do pobierania dokumentu z określonej bazy danych i kolekcji. Dokument zostanie następnie przekonwertowany do ```ToDoItem``` wcześniej utworzonego wystąpienia Pojo i przeszedł jako argument do funkcji.

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które korzysta z parametru trasy, aby określić identyfikator i wartość klucza partycji do wyszukania. Ten identyfikator i wartość klucza partycji są używane do pobierania dokumentu z określonej bazy danych i kolekcji, zwracając ją jako element ```Optional<String>``` .

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje funkcję języka Java, która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa parametru trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania dokumentu z określonej bazy danych i kolekcji, co umożliwia przekonwertowanie zestawu wyników na typ ```ToDoItem[]``` , ponieważ w zależności od kryteriów zapytania można zwrócić wiele dokumentów.

> [!NOTE]
> Jeśli zachodzi potrzeba przeszukiwania tylko identyfikatora, zaleca się użycie wyszukiwania, podobnie jak w [poprzednich przykładach](#http-trigger-look-up-id-from-query-string---pojo-parameter-java), ponieważ będzie zużywał mniej [jednostek żądania](../cosmos-db/request-units.md). Operacje odczytu punktu (GET) są [bardziej wydajne](../cosmos-db/optimize-cost-queries.md) niż zapytania według identyfikatora.
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje funkcję języka Java, która pobiera wiele dokumentów. Funkcja jest wyzwalana przez żądanie HTTP, które używa parametru trasy ```desc``` do określenia ciągu do wyszukania w ```description``` polu. Termin wyszukiwania jest używany do pobierania kolekcji dokumentów z określonej bazy danych i kolekcji, a następnie konwersja zestawu wyników na typ ```ToDoItem[]``` i przekazanie go jako argumentu do funkcji.

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#configuration).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime)Użyj `@CosmosDBOutput` adnotacji w parametrach, które zapisują w Cosmos DB. Typ parametru adnotacji powinien mieć wartość `OutputBinding<T>` , gdzie `T` jest natywnym typem Java lub Pojo.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `CosmosDB` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź**     | nie dotyczy | Musi być ustawiony na `cosmosDB` .        |
|**wskazywa**     | nie dotyczy | Musi być ustawiony na `in` .         |
|**Nazwij**     | nie dotyczy | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** |**DatabaseName** |Baza danych zawierająca dokument.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji zawierającej dokument. |
|**id**    | **#C1** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązań](./functions-bindings-expressions-patterns.md). Nie ustawiaj jednocześnie `id` właściwości i **sqlQuery** . Jeśli nie ustawisz żadnej z nich, cała kolekcja zostanie pobrana. |
|**sqlQuery**  |**SqlQuery**  | Azure Cosmos DB zapytanie SQL używane do pobierania wielu dokumentów. Właściwość obsługuje powiązania środowiska uruchomieniowego, jak w tym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}` . Nie ustawiaj `id` właściwości i `sqlQuery` . Jeśli nie ustawisz żadnej z nich, cała kolekcja zostanie pobrana.|
|**connectionStringSetting**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego Azure Cosmos DB parametry połączenia. |
|**partitionKey**|**PartitionKey**|Określa wartość klucza partycji dla wyszukiwania. Może zawierać parametry powiązania. Jest to wymagane w przypadku wyszukiwań w kolekcjach [partycjonowanych](../cosmos-db/partition-data.md#logical-partitions) .|
|**preferredLocations**| **PreferredLocations**| Obowiązkowe Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Azure Cosmos DB. Wartości powinny być rozdzielane przecinkami. Na przykład "Wschodnie stany USA, Południowo-środkowe stany USA, Europa Północna". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aktualizacje nie są wykonywane automatycznie po zamknięciu funkcji. Zamiast tego należy użyć polecenia `context.bindings.<documentName>In` i, `context.bindings.<documentName>Out` Aby wprowadzić aktualizacje. Zapoznaj się z przykładem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Dane są udostępniane funkcji za pośrednictwem `DocumentList` parametru. Zmiany wprowadzone do dokumentu nie są automatycznie utrwalane.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime) [@CosmosDBInput](/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) adnotacja ujawnia Cosmos DB danych do funkcji. Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

---

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję w przypadku utworzenia lub zmodyfikowania dokumentu Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Zapisz zmiany w dokumencie Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)
