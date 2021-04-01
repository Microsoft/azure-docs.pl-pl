---
title: Azure Cosmos DB powiązanie danych wyjściowych dla funkcji 2. x i wyższych
description: Dowiedz się, jak używać powiązania danych wyjściowych Azure Cosmos DB w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 779b66412319ec8422977a7e56570a4d16f89aa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98071548"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB powiązanie danych wyjściowych dla Azure Functions 2. x i wyższych

Powiązanie danych wyjściowych Azure Cosmos DB umożliwia pisanie nowego dokumentu w Azure Cosmos DB bazie danych przy użyciu interfejsu API SQL.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c)

Przykłady odnoszą się do `ToDoItem` typu prostego:

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

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>Wyzwalacz kolejki, napisz jeden dokument

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która dodaje dokument do bazy danych przy użyciu danych z usługi queue storage.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector

W poniższym przykładzie pokazano [funkcję języka C#](functions-dotnet-class-library.md) , która dodaje kolekcję dokumentów do bazy danych przy użyciu danych w formacie JSON komunikatu w kolejce.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz kolejki, napisz jeden dokument](#queue-trigger-write-one-doc-c-script)
* [Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Wyzwalacz kolejki, Zapisz dokumenty przy użyciu IAsyncCollector

Aby utworzyć wiele dokumentów, można powiązać z `ICollector<T>` lub `IAsyncCollector<T>` gdzie `T` jest jednym z obsługiwanych typów.

Ten przykład dotyczy `ToDoItem` typu prostego:

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

Oto function.jspliku:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

* [Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej](#queue-trigger-save-message-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej](#http-trigger-save-one-document-to-database-via-return-value-java)
* [Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>Wyzwalacz kolejki, Zapisz komunikat do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, która dodaje dokument do bazy danych z danymi z wiadomości w usłudze queue storage.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem wartości zwracanej

Poniższy przykład pokazuje funkcję języka Java, której podpis jest oznaczony ```@CosmosDBOutput``` i ma zwracaną wartość typu ```String``` . Dokument JSON zwrócony przez funkcję zostanie automatycznie zapisany w odpowiedniej kolekcji CosmosDB.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>Wyzwalacz HTTP, Zapisz jeden dokument do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje dokument do CosmosDB za pośrednictwem ```OutputBinding<T>``` parametru Output. W tym przykładzie ```outputItem``` parametr musi zawierać adnotację ```@CosmosDBOutput``` , a nie sygnaturę funkcji. Użycie ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania w celu napisania dokumentu w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>Wyzwalacz HTTP, zapisanie wielu dokumentów do bazy danych za pośrednictwem kodu Wyjściowegobinding

Poniższy przykład pokazuje funkcję języka Java, która zapisuje wiele dokumentów do CosmosDB za pośrednictwem ```OutputBinding<T>``` parametru Output. W tym przykładzie ```outputItem``` parametr zawiera adnotację ```@CosmosDBOutput``` , a nie sygnaturę funkcji. Parametr Output ```outputItem``` ma listę ```ToDoItem``` obiektów jako typ parametru szablonu. Użycie ```OutputBinding<T>``` umożliwia korzystanie z funkcji powiązania do zapisywania dokumentów w CosmosDB, a jednocześnie pozwala zwrócić inną wartość do obiektu wywołującego funkcji, takiego jak dokument JSON lub XML.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@CosmosDBOutput` adnotacji w parametrach, które będą zapisywane w Cosmos DB.  Typ parametru adnotacji powinien mieć wartość ```OutputBinding<T>``` , gdzie T jest natywnym typem Java lub Pojo.

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
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

W polu Bulk Wstaw najpierw obiekty, a następnie Uruchom funkcję stringify. Oto kod JavaScript:

```javascript
    module.exports = function (context) {
    
        context.bindings.employeeDocument = JSON.stringify([
        {
            "id": "John Henry-123456",
            "name": "John Henry",
            "employeeId": "123456",
            "address": "A town nearby"
        },
        {
            "id": "John Doe-123457",
            "name": "John Doe",
            "employeeId": "123457",
            "address": "A town far away"
        }]);
    
      context.done();
    };
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład pokazuje, jak napisać dane do Cosmos DB za pomocą powiązania danych wyjściowych. Powiązanie jest zadeklarowane w pliku konfiguracji funkcji (_functions.json_) i pobiera dane z kolejki i zapisuje je w Cosmos DB dokumencie.

```json
{ 
  "name": "EmployeeDocument",
  "type": "cosmosDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "direction": "out" 
} 
```

W pliku _run.ps1_ obiekt zwrócony z funkcji jest mapowany do `EmployeeDocument` obiektu, który jest utrwalony w bazie danych.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name EmployeeDocument -Value @{ 
    id = $QueueItem.name + '-' + $QueueItem.employeeId 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak napisać dokument do bazy danych usługi Azure CosmosDB jako dane wyjściowe funkcji.

Definicja powiązania jest definiowana w *function.jsna* miejscu, w którym ustawiono *Typ* `cosmosDB` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Aby zapisać dane w bazie danych, przekaż obiekt dokumentu do `set` metody parametru Database.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/dev/test/WebJobs.Extensions.CosmosDB.Tests) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyjście-konfiguracja](#configuration). Oto `CosmosDB` przykład atrybutu w sygnaturze metody:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput`Adnotacja jest dostępna do zapisywania danych do Cosmos DB. Adnotację można zastosować do funkcji lub do poszczególnych parametrów funkcji. W przypadku użycia na metodzie funkcji zwracana wartość funkcji jest zapisywana w Cosmos DB. Jeśli używasz adnotacji z parametrem, typ parametru musi być zadeklarowany jako, `OutputBinding<T>` gdzie `T` natywny typ Java lub Pojo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `CosmosDB` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź**     | n/d | Musi być ustawiony na `cosmosDB` .        |
|**wskazywa**     | n/d | Musi być ustawiony na `out` .         |
|**Nazwij**     | n/d | Nazwa parametru powiązania, który reprezentuje dokument w funkcji.  |
|**Bazy** | **DatabaseName**|Baza danych zawierająca kolekcję, w której jest tworzony dokument.     |
|**CollectionName** |**CollectionName**  | Nazwa kolekcji, w której jest tworzony dokument. |
|**Metodę createifnotexists**  |**Metodę createifnotexists**    | Wartość logiczna określająca, czy kolekcja jest tworzona, gdy nie istnieje. Wartość domyślna to *false* , ponieważ nowe kolekcje są tworzone z zarezerwowaną przepływność, która ma wpływ na koszty. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/cosmos-db/).  |
|**partitionKey**|**PartitionKey** |Gdy `CreateIfNotExists` ma wartość true, definiuje ścieżkę klucza partycji dla utworzonej kolekcji.|
|**collectionThroughput**|**CollectionThroughput**| Gdy `CreateIfNotExists` ma wartość true, definiuje [przepływność](../cosmos-db/set-throughput.md) utworzonej kolekcji.|
|**connectionStringSetting**    |**ConnectionStringSetting** |Nazwa ustawienia aplikacji zawierającego Azure Cosmos DB parametry połączenia.        |
|**preferredLocations**| **PreferredLocations**| Obowiązkowe Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Azure Cosmos DB. Wartości powinny być rozdzielane przecinkami. Na przykład "Wschodnie stany USA, Południowo-środkowe stany USA, Europa Północna". |
|**useMultipleWriteLocations**| **UseMultipleWriteLocations**| Obowiązkowe Po ustawieniu na `true` z `PreferredLocations` program może użyć [zapisu wieloregionowego](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) w usłudze Azure Cosmos DB. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

Domyślnie podczas zapisu do parametru danych wyjściowych w funkcji jest tworzony dokument w bazie danych. Ten dokument ma automatycznie wygenerowany identyfikator GUID jako identyfikator dokumentu. Możesz określić identyfikator dokumentu dla dokumentu wyjściowego, określając `id` Właściwość w obiekcie JSON przekazaną do parametru Output.

> [!Note]
> Po określeniu identyfikatora istniejącego dokumentu zostanie on zastąpiony nowym dokumentem wyjściowym.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Odwołanie |
|---|---|
| CosmosDB | [Kody błędów CosmosDB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x. Aby uzyskać więcej informacji na temat ustawień globalnej konfiguracji w wersji 2. x, zobacz [host.json Reference for Azure Functions wersja 2. x](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|Bramamode|Brama|Tryb połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB. Dostępne są opcje `Direct` i `Gateway`|
|Protokół|Schemat|Protokół połączenia używany przez funkcję podczas nawiązywania połączenia z usługą Azure Cosmos DB.  Przeczytaj [tutaj, aby uzyskać wyjaśnienie obu trybów](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n/d|Prefiks dzierżawy do użycia we wszystkich funkcjach w aplikacji.|

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję w przypadku utworzenia lub zmodyfikowania dokumentu Azure Cosmos DB (wyzwalacz)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Odczytaj dokument Azure Cosmos DB (powiązanie danych wejściowych)](./functions-bindings-cosmosdb-v2-input.md)
