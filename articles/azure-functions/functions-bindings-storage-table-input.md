---
title: Powiązania danych wejściowych usługi Azure Table Storage dla Azure Functions
description: Dowiedz się, jak używać powiązań danych wejściowych usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033782"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Powiązania danych wejściowych usługi Azure Table Storage dla Azure Functions

Użyj powiązania danych wejściowych usługi Azure Table Storage, aby odczytać tabelę na koncie usługi Azure Storage.

## <a name="example"></a>Przykład

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

### <a name="cloudtable"></a>CloudTable

`CloudTable` jest obsługiwane tylko w [funkcjach w wersji 2 i nowszych](functions-versions.md).

Użyj `CloudTable` parametru metody, aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Oto przykład funkcji, która bada Azure Functions tabeli dziennika:

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

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

`IQueryable` jest obsługiwane tylko w [środowisku uruchomieniowym funkcji V1](functions-versions.md).

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Kod skryptu C# dodaje odwołanie do zestawu SDK usługi Azure Storage, dzięki czemu typ jednostki może pochodzić od `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
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

Poniższy przykład używa filtru do wykonywania zapytań dotyczących osób o określonej nazwie w tabeli platformy Azure i ogranicza liczbę możliwych dopasowań do 10 wyników.

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższa funkcja używa wyzwalacza kolejki, aby odczytać pojedynczy wiersz tabeli jako dane wejściowe do funkcji.

W tym przykładzie Konfiguracja powiązania określa wartość jawną tabeli `partitionKey` i używa wyrażenia do przekazania do `rowKey` . `rowKey`Wyrażenie wskazuje, `{queueTrigger}` że klucz wiersza pochodzi z ciągu komunikatu kolejki.

Konfiguracja powiązania w _function.jsna_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Kod programu PowerShell w _run.ps1_:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

Poniższa funkcja używa wyzwalacza kolejki, aby odczytać pojedynczy wiersz tabeli jako dane wejściowe do funkcji.

W tym przykładzie Konfiguracja powiązania określa wartość jawną tabeli `partitionKey` i używa wyrażenia do przekazania do `rowKey` . `rowKey`Wyrażenie wskazuje, `{id}` że klucz wiersza pochodzi z ciągu komunikatu kolejki.

Konfiguracja powiązania w _function.jsw_ pliku:

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

Kod języka Python w pliku *\_ \_ init \_ \_ . PR* :

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

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

  Pełny przykład można znaleźć w sekcji przykład w języku C#.

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

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@TableInput` adnotacji w parametrach, których wartość pochodzi z magazynu tabel.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Table` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `table` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `in` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje tabelę lub jednostkę w kodzie funkcji. | 
|**tableName** | **TableName** | Nazwa tabeli.| 
|**partitionKey** | **PartitionKey** |Opcjonalny. Klucz partycji jednostki tabeli do odczytania. Zapoznaj się z sekcją [użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Opcjonalny. Klucz wiersza jednostki tabeli, który ma zostać odczytany. Zapoznaj się z sekcją [użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**czasochłonn** |**Czasochłonn** | Opcjonalny. Maksymalna liczba jednostek do odczytania w języku JavaScript. Zapoznaj się z sekcją [użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**filtru** |**Filtr** | Opcjonalny. Wyrażenie filtru OData dla danych wejściowych tabeli w języku JavaScript. Zapoznaj się z sekcją [użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Ustawienie to może być nazwą prefiksu "AzureWebJobs" lub nazwą parametrów połączenia. Na przykład, jeśli nazwa ustawienia to "AzureWebJobsMyStorage", w tym miejscu możesz określić "WebStorage". Środowisko uruchomieniowe funkcji automatycznie szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey` . Dostęp do danych tabeli przy użyciu parametru metody `T <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` jest zazwyczaj typem, który implementuje `ITableEntity` lub pochodzi od `TableEntity` . `filter`Właściwości i `take` nie są używane w tym scenariuszu.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu parametru metody `IQueryable<T> <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` musi być typem implementującym `ITableEntity` lub pochodnym elementu `TableEntity` . Możesz użyć `IQueryable` metod, aby wykonać wymagane filtrowanie. `partitionKey` `rowKey` `filter` `take` W tym scenariuszu nie są używane właściwości,, i.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwany w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

* **Odczytaj jeden wiersz w**

  Ustaw `partitionKey` i `rowKey` . Dostęp do danych tabeli przy użyciu parametru metody `T <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` jest zazwyczaj typem, który implementuje `ITableEntity` lub pochodzi od `TableEntity` . `filter`Właściwości i `take` nie są używane w tym scenariuszu.

* **Odczytaj jeden lub więcej wierszy**

  Dostęp do danych tabeli przy użyciu parametru metody `IQueryable<T> <paramName>` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. `T` musi być typem implementującym `ITableEntity` lub pochodnym elementu `TableEntity` . Możesz użyć `IQueryable` metod, aby wykonać wymagane filtrowanie. `partitionKey` `rowKey` `filter` `take` W tym scenariuszu nie są używane właściwości,, i.  

  > [!NOTE]
  > `IQueryable` nie jest obsługiwany w [środowisku uruchomieniowym funkcji v2](functions-versions.md). Alternatywą jest [użycie parametru metody w chmurze paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) , aby odczytać tabelę przy użyciu zestawu SDK usługi Azure Storage. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Atrybut [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) umożliwia dostęp do wiersza tabeli, który wyzwolił funkcję.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ustaw `filter` właściwości i `take` . Nie ustawiaj `partitionKey` ani `rowKey` . Uzyskaj dostęp do jednostki tabeli wejściowej (lub jednostek) za pomocą `context.bindings.<BINDING_NAME>` . Deserializowane obiekty mają `RowKey` właściwości i `PartitionKey` .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dane są przesyłane do parametru wejściowego określonego przez `name` klucz w *function.js* pliku. Określenie `partitionKey` i `rowKey` umożliwia filtrowanie do określonych rekordów. Zobacz [przykład programu PowerShell](#example) , aby uzyskać więcej szczegółów.

# <a name="python"></a>[Python](#tab/python)

Dane tabeli są przesyłane do funkcji jako ciąg JSON. Deserializowanie komunikatu przez wywołanie metody `json.loads` , jak pokazano w [przykładzie](#example)wejściowym.

---

## <a name="next-steps"></a>Następne kroki

* [Zapisywanie danych magazynu tabel z funkcji](./functions-bindings-storage-table-output.md)
