---
title: Powiązania Azure Cosmos DB dla funkcji 1. x
description: Dowiedz się, jak używać wyzwalaczy Azure Cosmos DB i powiązań w Azure Functions 1. x.
author: craigshoemaker
ms.author: cshoe
ms.topic: reference
ms.date: 11/21/2017
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 524df7805207ce517c7ae805fb17de1b041a2248
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002118"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Powiązania Azure Cosmos DB dla Azure Functions 1. x

> [!div class="op_single_selector" title1="Wybierz wersję środowiska uruchomieniowego Azure Functions, którego używasz: "]
> * [Wersja 1](functions-bindings-cosmosdb.md)
> * [Wersja 2](functions-bindings-cosmosdb-v2.md)

W tym artykule opisano sposób pracy z powiązaniami [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) w programie Azure Functions. Azure Functions obsługuje powiązania wyzwalacza, danych wejściowych i wyjściowych dla Azure Cosmos DB.

> [!NOTE]
> Ten artykuł dotyczy Azure Functions 1. x. Aby uzyskać informacje o sposobach używania tych powiązań w funkcjach 2. x i wyższych, zobacz [Azure Cosmos DB powiązania dla Azure Functions 2. x](functions-bindings-cosmosdb-v2.md).
>
>To powiązanie było pierwotnie nazwane DocumentDB. W funkcjach w wersji 1. x zmieniono nazwę wyzwalacza Cosmos DB; powiązanie danych wejściowych, powiązanie danych wyjściowych i pakiet NuGet zachowują nazwę DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Użycie powiązań usługi Azure Cosmos DB jest obsługiwane tylko w przypadku interfejsu API SQL. W przypadku wszystkich innych Azure Cosmos DB interfejsów API należy uzyskać dostęp do bazy danych z funkcji przy użyciu klienta statycznego dla interfejsu API, w tym [interfejsu api Azure Cosmos DB dla MongoDB](../cosmos-db/mongodb-introduction.md), [interfejs API Cassandra](../cosmos-db/cassandra-introduction.md), [interfejsu API Gremlin](../cosmos-db/graph-introduction.md)i [interfejs API tabel](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Azure Cosmos DB powiązania dla funkcji w wersji 1. x znajdują się w pakiecie NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB) w wersji 1. x. Kod źródłowy powiązań znajduje się w repozytorium [Azure-WebJobs-SDK-Extensions — rozszerzenia](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB) GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz Azure Cosmos DB używa [źródła zmian Azure Cosmos DB](../cosmos-db/change-feed.md) do nasłuchiwania operacji wstawiania i aktualizacji między partycjami. Kanał informacyjny zmiany publikuje wstawienia i aktualizacje, a nie usunięć.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Cosmos DB w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy Cosmos DB są modyfikowane.

Oto dane powiązania w *function.js* pliku:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Oto kod skryptu w języku C#:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Cosmos DB w *function.js* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy Cosmos DB są modyfikowane.

Oto dane powiązania w *function.js* pliku:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Oto kod JavaScript:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

---

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyzwalacz-konfiguracja](#trigger---configuration). Oto `CosmosDBTrigger` przykład atrybutu w sygnaturze metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza-C#](#trigger).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="trigger---configuration"></a>Wyzwalacz — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `CosmosDBTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `cosmosDBTrigger` . |
|**wskazywa** | n/d | Musi być ustawiony na `in` . Ten parametr jest ustawiany automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje listę dokumentów ze zmianami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia używane do nawiązywania połączenia z monitorowanym kontem Azure Cosmos DB. |
|**Bazy**|**DatabaseName**  | Nazwa bazy danych Azure Cosmos DB z monitorowaną kolekcją. |
|**CollectionName** |**CollectionName** | Nazwa monitorowanej kolekcji. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Obowiązkowe Nazwa ustawienia aplikacji zawierającego parametry połączenia do usługi, która zawiera kolekcję dzierżawy. Gdy nie jest ustawiona, `connectionStringSetting` wartość jest używana. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw muszą mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Obowiązkowe Nazwa bazy danych, która zawiera kolekcję służącą do przechowywania dzierżaw. Gdy nie jest ustawiona, `databaseName` zostanie użyta wartość ustawienia. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | Obowiązkowe Nazwa kolekcji używanej do przechowywania dzierżaw. Gdy nie jest ustawiona, wartość `leases` jest używana. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Obowiązkowe Po ustawieniu na wartość `true` Kolekcja dzierżawy jest tworzona automatycznie, gdy jeszcze nie istnieje. Wartość domyślna to `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Obowiązkowe Określa liczbę jednostek żądania, które mają zostać przypisane podczas tworzenia kolekcji dzierżaw. To ustawienie jest używane tylko wtedy `createLeaseCollectionIfNotExists` , gdy jest ustawiony na `true` . Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania przy użyciu portalu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Obowiązkowe Po ustawieniu dodaje prefiks do dzierżaw utworzonych w kolekcji dzierżawy dla tej funkcji, co pozwala na użycie dwóch oddzielnych Azure Functions do współużytkowania tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay**| Obowiązkowe Gdy ta wartość jest ustawiona, definiuje w milisekundach opóźnienie między sondowaniem partycji o nowe zmiany w kanale informacyjnym po opróżnieniu wszystkich bieżących zmian. Wartość domyślna to 5000 (5 sekund).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Obowiązkowe Gdy ta funkcja jest ustawiona, definiuje w milisekundach Interwał uruchamiania zadania w celu obliczenia, czy partycje są dystrybuowane równomiernie między znanymi wystąpieniami hostów. Wartość domyślna to 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Obowiązkowe Gdy ta wartość jest ustawiona, definiuje w milisekundach interwał, dla którego dzierżawa jest wykonywana w dzierżawie reprezentującej partycję. Jeśli dzierżawa nie zostanie odnowiona w tym interwale, spowoduje to jej wygaśnięcie, a własność partycji zostanie przeniesiona do innego wystąpienia. Wartość domyślna to 60000 (60 s).
|**leaseRenewInterval**| **LeaseRenewInterval**| Obowiązkowe Gdy ta wartość jest ustawiona, definiuje w milisekundach interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie przechowywanych w danym wystąpieniu. Wartość domyślna to 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| Obowiązkowe Gdy jest ustawiona, definiuje w milisekundach interwał między punktami kontrolnymi dzierżawy. Wartość domyślna to zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Obowiązkowe Po ustawieniu dostosowuje maksymalną ilość elementów odebranych na wywołanie funkcji.
|**startFromBeginning**| **StartFromBeginning**| Obowiązkowe Po ustawieniu nakazuje wyzwalaczowi rozpoczęcie odczytywania zmian od początku historii kolekcji zamiast bieżącego czasu. Działa to tylko po pierwszym uruchomieniu wyzwalacza, tak jak w kolejnych uruchomieniach, punkty kontrolne są już przechowywane. Ustawienie tej opcji na, `true` gdy istnieją już utworzone dzierżawy, nie ma żadnego wpływu.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz-użycie

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ w ramach partycji. Zarówno monitorowana kolekcja, jak i Kolekcja zawierająca dzierżawy muszą być dostępne, aby wyzwalacz działał.

>[!IMPORTANT]
> Jeśli wiele funkcji jest skonfigurowanych do korzystania z wyzwalacza Cosmos DB dla tej samej kolekcji, każda z tych funkcji powinna używać dedykowanej kolekcji dzierżaw lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie zostanie wyzwolona tylko jedna z funkcji. Informacje na temat prefiksu znajdują się w [sekcji Konfiguracja](#trigger---configuration).

Wyzwalacz nie wskazuje, czy dokument został zaktualizowany lub wstawiony, zawiera tylko dokument. Jeśli musisz obsługiwać aktualizacje i wstawiać je inaczej, możesz to zrobić, implementując pola sygnatury czasowej do wstawienia lub aktualizacji.

## <a name="input"></a>Dane wejściowe

Powiązanie wejściowe usługi Azure Cosmos DB używa interfejsu API SQL, aby pobrać co najmniej jeden dokument usługi Azure Cosmos DB, a następnie przekazuje go do parametru wejściowego funkcji. Identyfikator dokumentu lub parametry zapytania można określić na podstawie wyzwalacza wywołującego funkcję.

## <a name="input---example"></a>Dane wejściowe — przykład

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
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>Wyzwalacz kolejki, wyszukiwanie identyfikatora z poziomu JSON

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera obiekt JSON. Wyzwalacz kolejki analizuje kod JSON w obiekcie o nazwie `ToDoItemLookup` , który zawiera identyfikator do wyszukania. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z ciągu zapytania

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy przy użyciu sqlQuery

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu zapytania sqlQuery

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Zapytanie jest określone we `SqlQuery` właściwości atrybutu.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Pomiń przykładowe dane wejściowe](#input---attributes)

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Wyzwalacz HTTP, pobieranie wielu dokumentów przy użyciu DocumentClient (C#)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która pobiera listę dokumentów. Funkcja jest wyzwalana przez żądanie HTTP. Kod używa `DocumentClient` wystąpienia dostarczonego przez powiązanie Azure Cosmos DB, aby odczytać listę dokumentów. `DocumentClient`Wystąpienie może być również używane na potrzeby operacji zapisu.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
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
namespace CosmosDBSamplesV1
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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

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

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Oto kod skryptu w języku C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>Wyzwalacz HTTP, wyszukiwanie identyfikatora z danych trasy

Poniższy przykład pokazuje [funkcję skryptu języka C#](functions-reference-csharp.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa danych trasy do określenia identyfikatora do wyszukania. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
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
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
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
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
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

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ta sekcja zawiera następujące przykłady:

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
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

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

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

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
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
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

Poniższy przykład pokazuje [funkcję języka JavaScript](functions-reference-node.md) , która pobiera pojedynczy dokument. Funkcja jest wyzwalana przez żądanie HTTP, które używa ciągu zapytania do określenia wyszukiwanego identyfikatora. Ten identyfikator jest używany do pobierania `ToDoItem` dokumentu z określonej bazy danych i kolekcji.

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
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
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
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

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

---

## <a name="input---attributes"></a>Atrybuty wejściowe

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [następującą sekcję konfiguracyjną](#input---configuration).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="input---configuration"></a>Dane wejściowe — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `DocumentDB` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź**     | n/d | Musi być ustawiony na `documentdb` .        |
|**wskazywa**     | n/d | Musi być ustawiony na `in` .         |
|**Nazwij**     | n/d | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** |**DatabaseName** |Baza danych zawierająca dokument.        |
|**CollectionName** |**CollectionName** | Nazwa kolekcji zawierającej dokument. |
|**id**    | **#C1** | Identyfikator dokumentu do pobrania. Ta właściwość obsługuje [wyrażenia powiązań](./functions-bindings-expressions-patterns.md). Nie ustawiaj zarówno właściwości **ID** , jak i **sqlQuery** . Jeśli nie ustawisz żadnej z nich, cała kolekcja zostanie pobrana. |
|**sqlQuery**  |**SqlQuery**  | Azure Cosmos DB zapytanie SQL używane do pobierania wielu dokumentów. Właściwość obsługuje powiązania środowiska uruchomieniowego, jak w tym przykładzie: `SELECT * FROM c where c.departmentId = {departmentId}` . Nie ustawiaj zarówno właściwości **ID** , jak i **sqlQuery** . Jeśli nie ustawisz żadnej z nich, cała kolekcja zostanie pobrana.|
|**połączenia**     |**ConnectionStringSetting**|Nazwa ustawienia aplikacji zawierającego Azure Cosmos DB parametry połączenia.        |
|**partitionKey**|**PartitionKey**|Określa wartość klucza partycji dla wyszukiwania. Może zawierać parametry powiązania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

# <a name="c"></a>[C#](#tab/csharp)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Gdy funkcja zostanie zakończona pomyślnie, wszelkie zmiany wprowadzone w dokumencie wejściowym za pośrednictwem nazwanych parametrów wejściowych są utrwalane automatycznie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aktualizacje nie są wykonywane automatycznie po zamknięciu funkcji. Zamiast tego należy użyć polecenia `context.bindings.<documentName>In` i, `context.bindings.<documentName>Out` Aby wprowadzić aktualizacje. Zobacz [przykład danych wejściowych](#input).

---

## <a name="output"></a>Dane wyjściowe

Powiązanie danych wyjściowych Azure Cosmos DB umożliwia pisanie nowego dokumentu w Azure Cosmos DB bazie danych przy użyciu interfejsu API SQL.

## <a name="output---example"></a>Dane wyjściowe — przykład

# <a name="c"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, napisz jeden dokument
* Wyzwalacz kolejki, Zapisz dokumenty przy użyciu `IAsyncCollector`

Przykłady odnoszą się do `ToDoItem` typu prostego:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden dokument

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która dodaje dokument do bazy danych przy użyciu danych z usługi queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector

W poniższym przykładzie pokazano [funkcję języka C#](functions-dotnet-class-library.md) , która dodaje kolekcję dokumentów do bazy danych przy użyciu danych w formacie JSON komunikatu w kolejce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* Wyzwalacz kolejki, napisz jeden dokument
* Wyzwalacz kolejki, Zapisz dokumenty przy użyciu `IAsyncCollector`

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden dokument

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w *function.jsna* pliku i [funkcję skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych kolejki dla kolejki, która odbiera kod JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy Azure Cosmos DB dokumenty w następującym formacie dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać z `ICollector<T>` lub `IAsyncCollector<T>` gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy `ToDoItem` typu prostego:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Oto function.jspliku:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje Azure Cosmos DB powiązanie danych wyjściowych w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja używa powiązania danych wejściowych kolejki dla kolejki, która odbiera kod JSON w następującym formacie:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Funkcja tworzy Azure Cosmos DB dokumenty w następującym formacie dla każdego rekordu:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

---

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyjście-konfiguracja](#output---configuration). Oto `DocumentDB` przykład atrybutu w sygnaturze metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Aby uzyskać pełny przykład, zobacz [dane wyjściowe](#output).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

---

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `DocumentDB` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź**     | n/d | Musi być ustawiony na `documentdb` .        |
|**wskazywa**     | n/d | Musi być ustawiony na `out` .         |
|**Nazwij**     | n/d | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** | **DatabaseName**|Baza danych zawierająca kolekcję, w której jest tworzony dokument.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji, w której jest tworzony dokument. |
|**Metodę createifnotexists**  |**Metodę createifnotexists**    | Wartość logiczna określająca, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* , ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływność, która ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**połączenia**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego Azure Cosmos DB parametry połączenia.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

Domyślnie podczas zapisu do parametru danych wyjściowych w funkcji jest tworzony dokument w bazie danych. Ten dokument ma automatycznie wygenerowany identyfikator GUID jako identyfikator dokumentu. Możesz określić identyfikator dokumentu dla dokumentu wyjściowego, określając `id` Właściwość w obiekcie JSON przekazaną do parametru Output.

> [!Note]
> Po określeniu identyfikatora istniejącego dokumentu zostanie on zastąpiony nowym dokumentem wyjściowym.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Dokumentacja |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o liczbie bezserwerowych baz danych z Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
