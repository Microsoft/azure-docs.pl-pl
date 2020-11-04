---
title: Rejestrowanie i używanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika w zestawach SDK Azure Cosmos DB
description: Dowiedz się, jak rejestrować i wywoływać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika przy użyciu zestawów SDK usługi Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 022a45199cfc2d467b1d0d408e86cb5d621070d9
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339856"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Jak rejestrować procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika oraz jak ich używać w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Interfejs API SQL w usłudze Azure Cosmos DB obsługuje rejestrowanie i wywoływanie procedur składowanych, wyzwalaczy i funkcji zdefiniowanych przez użytkownika (UDF) napisanych w języku JavaScript. Aby zarejestrować i wywołać procedury składowane, można użyć zestawów [SDK programu SQL, .NET](sql-api-sdk-dotnet.md) [Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)lub [Python](sql-api-sdk-python.md) . Po zdefiniowaniu co najmniej jednej procedury składowanej, wyzwalaczy i funkcji zdefiniowanych przez użytkownika można ładować i przeglądać je w [Azure Portal](https://portal.azure.com/) przy użyciu Eksplorator danych.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>Jak uruchamiać procedury składowane

Procedury składowane pisze się przy użyciu języka JavaScript. Mogą one tworzyć, aktualizować, odczytywać i usuwać elementy oraz wysyłać o nie zapytania w kontenerze usługi Azure Cosmos. Aby uzyskać więcej informacji na temat pisania procedur składowanych w Azure Cosmos DB, zobacz [jak pisać procedury składowane w Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures) artykule.

Poniższe przykłady pokazują, jak zarejestrować i wywoływać procedurę składowaną przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z tematem [Tworzenie dokumentu](how-to-write-stored-procedures-triggers-udfs.md#create-an-item), ponieważ źródło tej procedury składowanej jest zapisane jako `spCreateToDoItem.js`.

> [!NOTE]
> W przypadku kontenerów podzielonych na partycje podczas wykonywania procedury składowanej w opcjach żądania należy podać wartość klucza partycji. Procedury składowane są zawsze ograniczone do klucza partycji. Elementy, które mają inną wartość klucza partycji, nie będą widoczne dla procedury składowanej. Ma to również zastosowanie do wyzwalaczy.

### <a name="stored-procedures---net-sdk-v2"></a>Procedury składowane — zestaw SDK dla platformy .NET V2

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu .NET SDK V2:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Poniższy kod przedstawia sposób wywoływania procedury składowanej przy użyciu zestawu .NET SDK V2:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>Procedury składowane — zestaw SDK dla platformy .NET v3

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu .NET SDK v3:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

Poniższy kod przedstawia sposób wywoływania procedury składowanej przy użyciu zestawu .NET SDK v3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>Procedury składowane — zestaw SDK platformy Java

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedury składowane — zestaw SDK języka JavaScript

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną przy użyciu zestawu SDK języka JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Poniższy kod pokazuje, jak wywołać procedurę składowaną przy użyciu zestawu SDK języka JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedury składowane — zestaw SDK języka Python

Poniższy przykład pokazuje, jak zarejestrować procedurę składowaną za pomocą zestawu SDK języka Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()

sproc = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
created_sproc = container.scripts.create_stored_procedure(body=sproc) 
```

Poniższy kod przedstawia sposób wywoływania procedury składowanej przy użyciu zestawu SDK języka Python:

```python
import uuid

new_id= str(uuid.uuid4())

# Creating a document for a container with "id" as a partition key.

new_item =   {
      "id": new_id, 
      "category":"Personal",
      "name":"Groceries",
      "description":"Pick up strawberries",
      "isComplete":False
   }
result = container.scripts.execute_stored_procedure(sproc=created_sproc,params=[[new_item]], partition_key=new_id) 
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Jak uruchamiać wyzwalacze wywoływane przed operacją

Poniższe przykłady pokazują, jak zarejestrować i wywoływać wyzwalacz uruchamiany przed operacją przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z [przykładem wyzwalacza wywoływanego przed operacją](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers), ponieważ źródło tego wyzwalacza jest zapisane jako `trgPreValidateToDoItemTimestamp.js`.

Podczas wykonywania wyzwalacze wywoływane przed operacją są przekazywane w obiekcie RequestOptions przez określenie parametru `PreTriggerInclude`, a następnie przekazanie nazwy wyzwalacza w obiekcie listy.

> [!NOTE]
> Mimo że nazwa wyzwalacza jest przekazywana jako lista, nadal można wykonywać tylko jeden wyzwalacz na operację.

### <a name="pre-triggers---net-sdk-v2"></a>Wyzwalacze wstępne — zestaw SDK dla platformy .NET V2

Poniższy kod przedstawia sposób rejestrowania wstępnego wyzwalacza przy użyciu zestawu .NET SDK V2:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Poniższy kod przedstawia sposób wywołania wstępnego wyzwalacza przy użyciu zestawu .NET SDK V2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Wyzwalacze wstępne — zestaw SDK dla platformy .NET v3

Poniższy kod przedstawia sposób rejestrowania wstępnego wyzwalacza przy użyciu zestawu .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

Poniższy kod przedstawia sposób wywołania wstępnego wyzwalacza przy użyciu zestawu .NET SDK v3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Wyzwalacze wykonywane przed operacją — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany przed operacją przy użyciu zestawu SDK języka Python:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Jak uruchamiać wyzwalacze wywoływane po operacji

Poniższe przykłady pokazują, jak zarejestrować wyzwalacz uruchamiany po operacji przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z [przykładem wyzwalacza wywoływanego po operacji](how-to-write-stored-procedures-triggers-udfs.md#post-triggers), ponieważ źródło tego wyzwalacza jest zapisane jako `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk-v2"></a>Wyzwalacze końcowe — zestaw SDK dla platformy .NET V2

Poniższy kod przedstawia sposób rejestrowania wyzwalacza końcowego przy użyciu zestawu .NET SDK V2:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Poniższy kod przedstawia sposób wywołania wyzwalacza końcowego przy użyciu zestawu .NET SDK V2:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Wyzwalacze końcowe — zestaw SDK dla platformy .NET v3

Poniższy kod przedstawia sposób rejestrowania wyzwalacza końcowego przy użyciu zestawu .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

Poniższy kod przedstawia sposób wywołania wyzwalacza końcowego przy użyciu zestawu .NET SDK v3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Wyzwalacze wykonywane po operacji — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPostValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPostValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

Poniższy kod pokazuje, jak wywołać wyzwalacz wykonywany po operacji przy użyciu zestawu SDK języka Python:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'post_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>Jak pracować z funkcjami zdefiniowanymi przez użytkownika

Poniższe przykłady pokazują, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawów SDK usługi Azure Cosmos DB. Zapoznaj się z tym [przykładem funkcji zdefiniowanej przez użytkownika](how-to-write-stored-procedures-triggers-udfs.md#udfs), ponieważ źródło tego wyzwalacza jest zapisane jako `udfTax.js`.

### <a name="user-defined-functions---net-sdk-v2"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK dla platformy .NET V2

Poniższy kod przedstawia sposób rejestrowania funkcji zdefiniowanej przez użytkownika przy użyciu zestawu .NET SDK V2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Poniższy kod przedstawia sposób wywoływania funkcji zdefiniowanej przez użytkownika przy użyciu zestawu .NET SDK V2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK dla platformy .NET v3

Poniższy kod przedstawia sposób rejestrowania funkcji zdefiniowanej przez użytkownika przy użyciu zestawu .NET SDK v3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

Poniższy kod przedstawia sposób wywoływania funkcji zdefiniowanej przez użytkownika przy użyciu zestawu .NET SDK v3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK platformy Java

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK platformy Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK języka JavaScript

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funkcje zdefiniowane przez użytkownika — zestaw SDK języka Python

Poniższy kod pokazuje, jak zarejestrować funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/udfTax.js') as file:
    file_contents = file.read()
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.scripts.create_user_defined_function(udf_definition)
```

Poniższy kod pokazuje, jak wywołać funkcję zdefiniowaną przez użytkownika przy użyciu zestawu SDK języka Python:

```python
results = list(container.query_items(
    'query': 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Następne kroki

Poznaj dodatkowe pojęcia i dowiedz się, jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB:

- [Praca z procedurami składowanymi, wyzwalaczami i funkcjami zdefiniowanymi przez użytkownika w usłudze Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Praca ze zintegrowanym interfejsem API zapytań języka JavaScript w usłudze Azure Cosmos DB](javascript-query-api.md)
- [Jak pisać procedury składowane, wyzwalacze i funkcje zdefiniowane przez użytkownika w usłudze Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Jak pisać procedury składowane i wyzwalacze przy użyciu interfejsu API zapytań języka JavaScript w usłudze Azure Cosmos DB](how-to-write-javascript-query-api.md)
