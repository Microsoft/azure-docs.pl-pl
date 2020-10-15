---
title: Powiązania usługi Azure Table Storage dla Azure Functions
description: Dowiedz się, jak używać powiązań usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 70329d07a9966a5cbdafcd64000470c4edcbca9e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072054"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Powiązania usługi Azure Table Storage dla Azure Functions

W tym artykule opisano sposób pracy z powiązaniami w usłudze Azure Table Storage w programie Azure Functions. Azure Functions obsługuje powiązania danych wejściowych i wyjściowych dla usługi Azure Table Storage.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1. x

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2. x i nowsze

Powiązania magazynu tabel są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-WebJobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Dane wejściowe

Użyj powiązania danych wejściowych usługi Azure Table Storage, aby odczytać tabelę na koncie usługi Azure Storage.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Jedna jednostka

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która odczytuje pojedynczy wiersz tabeli. Dla każdej wiadomości wysyłanej do kolejki zostanie wyzwolona funkcja.

Wartość klucza wiersza "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która odczytuje wiele wierszy tabeli, `MyPoco` z których pochodzi Klasa `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` jest obsługiwane tylko w [środowisku uruchomieniowym funkcji V1](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody w celu odczytania tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która bada Azure Functions tabeli dziennika:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Aby uzyskać więcej informacji na temat korzystania z chmury, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

### <a name="one-entity"></a>Jedna jednostka

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w *function.jsw* kodzie skryptu pliku i [C#](functions-reference-csharp.md) , który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytywania pojedynczego wiersza tabeli. 

*function.jsw* pliku określa `partitionKey` i `rowKey` . `rowKey`Wartość "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w *function.jsw* kodzie skryptu pliku i [C#](functions-reference-csharp.md) , który używa powiązania. Funkcja odczytuje jednostki dla klucza partycji, który jest określony w komunikacie kolejki.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Kod skryptu C# dodaje odwołanie do zestawu SDK usługi Azure Storage, dzięki czemu typ jednostki może pochodzić od `TableEntity` :

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` nie jest obsługiwany w środowisku uruchomieniowym funkcji dla [wersji 2. x i nowszych)](functions-versions.md). Alternatywą jest użycie `CloudTable` parametru metody w celu odczytania tabeli przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która bada Azure Functions tabeli dziennika:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Aby uzyskać więcej informacji na temat korzystania z chmury, zobacz [Rozpoczynanie pracy z usługą Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md).

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wejściowych tabeli w *function.jsw* pliku i [kodzie JavaScript](functions-reference-node.md) , który używa powiązania. Funkcja używa wyzwalacza kolejki do odczytywania pojedynczego wiersza tabeli. 

*function.jsw* pliku określa `partitionKey` i `rowKey` . `rowKey`Wartość "{queueTrigger}" wskazuje, że klucz wiersza pochodzi z ciągu komunikatu kolejki.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#input---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Wiersz pojedynczej tabeli 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję wyzwalaną przez protokół HTTP, która zwraca listę obiektów osób znajdujących się w określonej partycji w magazynie tabel. W tym przykładzie klucz partycji jest wyodrębniany z trasy http, a TableName i Connection pochodzą z ustawień funkcji. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

Adnotacja TableInput może również wyodrębnić powiązania z treści JSON żądania, jak pokazano w poniższym przykładzie.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Poniższe przykłady używają filtru do wykonywania zapytań dotyczących osób mających określoną nazwę w tabeli platformy Azure i ograniczają liczbę możliwych dopasowań do 10 wyników.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Input — atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

 W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować powiązanie danych wejściowych tabeli:

* [Tabelaattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę tabeli, klucz partycji i klucz wiersza. Atrybutu można użyć dla `out` parametru lub wartości zwracanej funkcji, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Pełny przykład można znaleźć w temacie Input-C# example.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut może być stosowany na poziomie parametru, metody lub klasy. W poniższym przykładzie przedstawiono poziom klasy i poziom metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Konto magazynu do użycia jest określane w następującej kolejności:

* `Table` `Connection` Właściwość atrybutu.
* `StorageAccount`Atrybut zastosowany do tego samego parametru, który jest `Table` atrybutem.
* `StorageAccount`Atrybut zastosowany do funkcji.
* `StorageAccount`Atrybut zastosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@TableInput` adnotacji w parametrach, których wartość pochodzi z magazynu tabel.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

---

## <a name="input---configuration"></a>Dane wejściowe — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Table` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | nie dotyczy | Musi być ustawiony na `table` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**wskazywa** | nie dotyczy | Musi być ustawiony na `in` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | nie dotyczy | Nazwa zmiennej, która reprezentuje tabelę lub jednostkę w kodzie funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**partitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji jednostki tabeli do odczytania. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli, który ma zostać odczytany. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**take (pobierz)** |**Czasochłonn** | Opcjonalny. Maksymalna liczba jednostek do odczytania w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**filtru** |**Filtr** | Opcjonalny. Wyrażenie filtru OData dla danych wejściowych tabeli w języku JavaScript. Zapoznaj się z sekcją [użycie](#input---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Ustawienie to może być nazwą prefiksu "AzureWebJobs" lub nazwą parametrów połączenia. Na przykład, jeśli nazwa ustawienia to "AzureWebJobsMyStorage", w tym miejscu możesz określić "WebStorage". Środowisko uruchomieniowe funkcji automatycznie szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Dane wejściowe — użycie

# <a name="c"></a>[C#](#tab/csharp)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey` . Dostęp do danych tabeli przy użyciu parametru metody `T <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` jest zazwyczaj typem, który implementuje `ITableEntity` lub pochodzi od `TableEntity` . `filter`Właściwości i `take` nie są używane w tym scenariuszu.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu parametru metody `IQueryable<T> <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` musi być typem implementującym `ITableEntity` lub pochodnym elementu `TableEntity` . Możesz użyć `IQueryable` metod, aby wykonać wymagane filtrowanie. `partitionKey` `rowKey` `filter` `take` W tym scenariuszu nie są używane właściwości,, i.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwany w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey` . Dostęp do danych tabeli przy użyciu parametru metody `T <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` jest zazwyczaj typem, który implementuje `ITableEntity` lub pochodzi od `TableEntity` . `filter`Właściwości i `take` nie są używane w tym scenariuszu.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu parametru metody `IQueryable<T> <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` musi być typem implementującym `ITableEntity` lub pochodnym elementu `TableEntity` . Możesz użyć `IQueryable` metod, aby wykonać wymagane filtrowanie. `partitionKey` `rowKey` `filter` `take` W tym scenariuszu nie są używane właściwości,, i.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwany w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ustaw `filter` właściwości i `take` . Nie ustawiaj `partitionKey` ani `rowKey` . Uzyskaj dostęp do jednostki tabeli wejściowej (lub jednostek) za pomocą `context.bindings.<BINDING_NAME>` . Deserializowane obiekty mają `RowKey` właściwości i `PartitionKey` .

# <a name="python"></a>[Python](#tab/python)

Dane tabeli są przesyłane do funkcji jako ciąg JSON. Deserializowanie komunikatu przez wywołanie metody `json.loads` , jak pokazano w [przykładzie](#input)wejściowym.

# <a name="java"></a>[Java](#tab/java)

Atrybut [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) umożliwia dostęp do wiersza tabeli, który wyzwolił funkcję.

---

## <a name="output"></a>Dane wyjściowe

Za pomocą powiązania danych wyjściowych usługi Azure Table Storage można pisać jednostki w tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje aktualizowania istniejących jednostek. Użyj `TableOperation.Replace` operacji [z zestawu SDK usługi Azure Storage,](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) aby zaktualizować istniejącą jednostkę.

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która używa wyzwalacza http do pisania pojedynczego wiersza tabeli. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie danych wyjściowych tabeli w *function.jsw* kodzie skryptu pliku i [C#](functions-reference-csharp.md) , który używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje powiązanie danych wyjściowych tabeli w *function.jsw* pliku i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje wiele jednostek tabeli.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#output---configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak używać powiązania danych wyjściowych magazynu tabel. `table`Powiązanie jest konfigurowane w *function.jsna* przez przypisanie wartości do `name` , `tableName` , `partitionKey` i `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Następująca funkcja generuje unikatowy UUI dla `rowKey` wartości i utrzymuje komunikat w magazynie tabel.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która używa wyzwalacza HTTP do pisania pojedynczego wiersza tabeli.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Poniższy przykład pokazuje funkcję języka Java, która używa wyzwalacza HTTP do pisania wielu wierszy tabeli.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Dane wyjściowe — atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)należy użyć [tabeli](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę tabeli. Atrybutu można użyć dla `out` parametru lub wartości zwracanej funkcji, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Aby zapoznać się z kompletnym przykładem, zobacz [dane wyjściowe — przykład w języku C#](#output).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [atrybuty danych wejściowych](#input---attributes-and-annotations).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) w parametrach, aby zapisać wartości w magazynie tabel.

Zobacz [przykład, aby uzyskać więcej szczegółów](#output).

---

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Table` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | nie dotyczy | Musi być ustawiony na `table` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**wskazywa** | nie dotyczy | Musi być ustawiony na `out` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | nie dotyczy | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje tabelę lub jednostkę. Ustaw, aby `$return` odwoływać się do zwracanej wartości funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**partitionKey** |**PartitionKey** | Klucz partycji jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#output---usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "Moje magazyn". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

# <a name="c"></a>[C#](#tab/csharp)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru `ICollector<T> paramName` metody `IAsyncCollector<T> paramName` lub `T` zawierającego `PartitionKey` właściwości i `RowKey` . Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` .

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru `ICollector<T> paramName` metody `IAsyncCollector<T> paramName` lub `T` zawierającego `PartitionKey` właściwości i `RowKey` . Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` . `paramName`Wartość jest określona we `name` właściwości *function.json*.

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu metody `context.bindings.<name>` Where `<name>` jest wartością określoną we `name` właściwości *function.json*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu wiersza magazynu tabel z funkcji:

- **Wartość zwracana**: Ustaw `name` Właściwość w *function.jsna* wartość `$return` . W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: Przekaż**wartość do metody [Set](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [out](/python/api/azure-functions/azure.functions.out?view=azure-python) . Wartość przeniesiona do `set` elementu jest utrwalana jako komunikat centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania wiersza magazynu tabel z funkcji przy użyciu adnotacji [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: aby**jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , gdzie `T` zawiera `PartitionKey` `RowKey` właściwości i. Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Dokumentacja |
|---|---|
| Tabela | [Kody błędów tabeli](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Rozwiązywanie problemów](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
