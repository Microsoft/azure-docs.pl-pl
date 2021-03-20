---
title: Zarządzanie konfliktami między regionami w Azure Cosmos DB
description: Dowiedz się, jak zarządzać konfliktami w Azure Cosmos DB przez tworzenie zasad ostatniego rozwiązywania konfliktów — WINS lub niestandardowych
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f98c2201159350f5774f4d2b05102384f31f3af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339346"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami rozwiązywania konfliktów w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W przypadku zapisów w wielu regionach, gdy wielu klientów zapisuje do tego samego elementu, mogą wystąpić konflikty. W przypadku wystąpienia konfliktu można rozwiązać konflikt, używając różnych zasad rozwiązywania konfliktów. W tym artykule opisano sposób zarządzania zasadami rozwiązywania konfliktów.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Tworzenie zasad rozwiązywania konfliktów polegających na traktowaniu ostatniego zapisu jako prawidłowego

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą zasad rozwiązywania konfliktów polegających na traktowaniu ostatniego zapisu jako prawidłowego. Domyślną ścieżką dla ostatniego składnika zapisywania usługi WINS jest pole timestamp lub `_ts` Właściwość. W przypadku interfejsu API SQL może to również być ustawiona jako ścieżka zdefiniowana przez użytkownika z typem liczbowym. W konflikcie jest najwyższa wartość WINS. Jeśli ścieżka nie jest ustawiona lub jest nieprawidłowa, domyślnie jest to `_ts` . Konflikty rozwiązane z tymi zasadami nie są wyświetlane w kanale informacyjnym konfliktu. Te zasady mogą być używane przez wszystkie interfejsy API.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>ZESTAW SDK PLATFORMY .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javav4"></a> Zestaw SDK Java v4

# <a name="async"></a>[Asynchroniczne](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionLWWAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionLWWSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-lww-javav2"></a>Zestawy SDK Java v2

# <a name="async-java-v2-sdk"></a>[Asynchroniczne środowisko Java w wersji 2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizuj zestaw Java v2 SDK](#tab/sync)

[Synchronizuj zestaw Java v2 SDK](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>Zestaw SDK języka Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Utwórz niestandardowe zasady rozwiązywania konfliktów przy użyciu procedury składowanej

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą niestandardowych zasad rozwiązywania konfliktów z procedurą składowaną, aby rozwiązać konflikt. Te konflikty nie są widoczne w kanale informacyjnym konfliktów, chyba że w procedurze składowanej wystąpi błąd. Po utworzeniu zasad przy użyciu kontenera należy utworzyć procedurę składowaną. Przykładowy zestaw SDK platformy .NET zawiera przykład. Te zasady są obsługiwane tylko w przypadku interfejsu API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Przykładowa procedura składowana niestandardowego rozwiązywania konfliktów

Procedury składowane rozwiązywania konfliktów niestandardowych należy zaimplementować przy użyciu sygnatury funkcji pokazanej poniżej. Nazwa funkcji nie musi być zgodna z nazwą używaną podczas rejestrowania procedury składowanej w kontenerze, ale upraszcza nazywanie. Poniżej znajduje się opis parametrów, które muszą zostać zaimplementowane dla tej procedury składowanej.

- **incomingItem**: element wstawiany lub aktualizowany w zatwierdzeniu, który generuje konflikty. Ma wartość null w przypadku operacji usuwania.
- **existingItem**: aktualnie przydzielony element. Ta wartość jest inna niż null w aktualizacjach i wartości null dla operacji INSERT lub usunięć.
- **ischowania**: wartość logiczna wskazująca, czy incomingItem powoduje konflikt z wcześniej usuniętym elementem. W przypadku wartości true existingItem ma również wartość null.
- **conflictingItems**: tablica zatwierdzonej wersji wszystkich elementów w kontenerze, które powodują konflikt z INCOMINGITEM on ID lub innymi unikatowymi właściwościami indeksu.

> [!IMPORTANT]
> Podobnie jak w przypadku każdej procedury składowanej, niestandardowa procedura rozwiązywania konfliktów może uzyskać dostęp do dowolnych danych z tym samym kluczem partycji i można wykonać dowolną operację wstawiania, aktualizowania lub usuwania, aby rozwiązać konflikty.

Ta przykładowa procedura składowana rozwiązuje konflikty, wybierając najniższą wartość ze `/myCustomId` ścieżki.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>ZESTAW SDK PLATFORMY .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav4"></a> Zestaw SDK Java v4

# <a name="async"></a>[Asynchroniczne](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionSprocAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionSprocSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav2"></a>Zestawy SDK Java v2

# <a name="async-java-v2-sdk"></a>[Asynchroniczne środowisko Java w wersji 2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizuj zestaw Java v2 SDK](#tab/sync)

[Synchronizuj zestaw Java v2 SDK](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Zestaw SDK języka Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Po utworzeniu kontenera należy utworzyć procedurę składowaną `resolver`.

## <a name="create-a-custom-conflict-resolution-policy"></a>Tworzenie niestandardowych zasad rozwiązywania konfliktów

Poniższe przykłady pokazują, jak skonfigurować kontener za pomocą niestandardowych zasad rozwiązywania konfliktów. Te konflikty są widoczne w kanale informacyjnym konfliktów.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>ZESTAW SDK PLATFORMY .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-javav4"></a> Zestaw SDK Java v4

# <a name="async"></a>[Asynchroniczne](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionCustomAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionCustomSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-javav2"></a>Zestawy SDK Java v2

# <a name="async-java-v2-sdk"></a>[Asynchroniczne środowisko Java w wersji 2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Synchronizuj zestaw Java v2 SDK](#tab/sync)

[Synchronizuj zestaw Java v2 SDK](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>Zestaw SDK języka Python

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Odczytywanie z kanału informacyjnego konfliktów

Te przykłady pokazują, jak odczytywać z kanału informacyjnego konfliktów kontenera. Konflikty są wyświetlane w kanale informacyjnym powodującym konflikt tylko wtedy, gdy nie zostały rozpoznane automatycznie lub w przypadku użycia niestandardowych zasad konfliktu.

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>ZESTAW SDK PLATFORMY .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-v2-sdks"></a><a id="read-from-conflict-feed-javav2"></a>Zestawy SDK Java v2

# <a name="async-java-v2-sdk"></a>[Asynchroniczne środowisko Java w wersji 2 SDK](#tab/async)

[Async Java v2 SDK](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="sync-java-v2-sdk"></a>[Synchronizuj zestaw Java v2 SDK](#tab/sync)

[Synchronizuj zestaw Java v2 SDK](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-DocumentDB](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Następne kroki

Poznaj następujące pojęcia dotyczące usługi Azure Cosmos DB:

- [Dystrybucja globalna — szczegóły działania](global-dist-under-the-hood.md)
- [Jak skonfigurować wieloregionowe operacje zapisu w aplikacjach](how-to-multi-master.md)
- [Konfigurowanie klientów dla usługi wieloadresowości](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Dodawanie lub usuwanie regionów z konta usługi Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Jak configuremulti zapisy w aplikacjach](how-to-multi-master.md).
- [Partycjonowanie i dystrybucja danych](partitioning-overview.md)
- [Indeksowanie w usłudze Azure Cosmos DB](index-policy.md)
