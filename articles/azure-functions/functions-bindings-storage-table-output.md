---
title: Powiązania wyjściowe usługi Azure Table Storage dla Azure Functions
description: Zapoznaj się z tematem jak używać powiązań wyjściowych usługi Azure Table Storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453006"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Powiązania wyjściowe usługi Azure Table Storage dla Azure Functions

Za pomocą powiązania danych wyjściowych usługi Azure Table Storage można pisać jednostki w tabeli na koncie usługi Azure Storage.

> [!NOTE]
> To powiązanie danych wyjściowych nie obsługuje aktualizowania istniejących jednostek. Użyj `TableOperation.Replace` operacji [z zestawu SDK usługi Azure Storage,](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) aby zaktualizować istniejącą jednostkę.

## <a name="example"></a>Przykład

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

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

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład pokazuje, jak napisać wiele jednostek do tabeli z funkcji.

Konfiguracja powiązania w _function.jsna_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod programu PowerShell w _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

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

Pełny przykład można znaleźć w sekcji [przykład w języku C#](#example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [atrybuty danych wejściowych](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) w parametrach, aby zapisać wartości w magazynie tabel.

Zobacz [przykład, aby uzyskać więcej szczegółów](#example).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Table` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `table` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `out` . Ta właściwość jest ustawiana automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje tabelę lub jednostkę. Ustaw, aby `$return` odwoływać się do zwracanej wartości funkcji.| 
|**tableName** |**TableName** | Nazwa tabeli.| 
|**partitionKey** |**PartitionKey** | Klucz partycji jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**rowKey** |**RowKey** | Klucz wiersza jednostki tabeli do zapisania. Zapoznaj się z [sekcją użycie](#usage) , aby uzyskać wskazówki dotyczące sposobu korzystania z tej właściwości.| 
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "Moje magazyn". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru `ICollector<T> paramName` metody `IAsyncCollector<T> paramName` lub `T` zawierającego `PartitionKey` właściwości i `RowKey` . Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` .

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Uzyskaj dostęp do jednostki tabeli wyjściowej przy użyciu parametru `ICollector<T> paramName` metody `IAsyncCollector<T> paramName` lub `T` zawierającego `PartitionKey` właściwości i `RowKey` . Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` . `paramName`Wartość jest określona we `name` właściwości *function.json*.

Alternatywnie można użyć `CloudTable` parametru metody do zapisu w tabeli przy użyciu zestawu Azure Storage SDK. Jeśli próbujesz powiązać z `CloudTable` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania wiersza magazynu tabel z funkcji przy użyciu adnotacji [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: aby** jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , gdzie `T` zawiera `PartitionKey` `RowKey` właściwości i. Te właściwości są często dołączane przez implementację `ITableEntity` lub dziedziczenie `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu metody `context.bindings.<name>` Where `<name>` jest wartością określoną we `name` właściwości *function.json*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby zapisać dane w tabeli, należy użyć `Push-OutputBinding` polecenia cmdlet, ustawić `-Name TableBinding` parametr i `-Value` parametr równy dane wiersza. Zobacz [przykład programu PowerShell](#example) , aby uzyskać więcej szczegółów.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu wiersza magazynu tabel z funkcji:

- **Wartość zwracana**: Ustaw `name` Właściwość w *function.jsna* wartość `$return` . W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako wiersz magazynu tabeli.

- Bezwzględnie **: Przekaż** wartość do metody [Set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) parametru zadeklarowanego jako typ [out](/python/api/azure-functions/azure.functions.out) . Wartość przeniesiona do `set` elementu jest utrwalana jako komunikat centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Odwołanie |
|---|---|
| Tabela | [Kody błędów tabeli](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Rozwiązywanie problemów](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)
