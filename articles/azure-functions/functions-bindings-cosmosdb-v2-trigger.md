---
title: Wyzwalacz Azure Cosmos DB dla funkcji 2. x i wyższych
description: Dowiedz się, jak używać wyzwalacza Azure Cosmos DB w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729219"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Wyzwalacz Azure Cosmos DB dla Azure Functions 2. x i wyższych

Wyzwalacz Azure Cosmos DB używa [źródła zmian Azure Cosmos DB](../cosmos-db/change-feed.md) do nasłuchiwania operacji wstawiania i aktualizacji między partycjami. Kanał informacyjny zmiany publikuje wstawienia i aktualizacje, a nie usunięć.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Cosmos DB w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika po dodaniu lub zmodyfikowaniu Cosmos DB rekordów.

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
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

Ta funkcja jest wywoływana, gdy w określonej bazie danych i kolekcji są wstawiane lub aktualizowane aktualizacje.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@CosmosDBTrigger` adnotacji w parametrach, których wartość pochodzi z Cosmos DB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Cosmos DB w *function.js* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika po dodaniu lub zmodyfikowaniu Cosmos DB rekordów.

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

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład pokazuje, jak uruchomić funkcję jako zmiany danych w Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

W pliku _run.ps1_ masz dostęp do dokumentu, który wyzwala funkcję za pośrednictwem `$Documents` parametru.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza Cosmos DB w *function.js* pliku oraz [funkcja języka Python](functions-reference-python.md) , która używa powiązania. Funkcja zapisuje komunikaty dziennika, gdy rekordy Cosmos DB są modyfikowane.

Oto dane powiązania w *function.js* pliku:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Oto kod języka Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę bazy danych i nazwę kolekcji. Aby uzyskać informacje o tych ustawieniach i innych właściwościach, które można skonfigurować, zobacz [wyzwalacz-konfiguracja](#configuration). Oto `CosmosDBTrigger` przykład atrybutu w sygnaturze metody:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Pełny przykład można znaleźć w temacie [Trigger](#example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime)Użyj `@CosmosDBInput` adnotacji w parametrach, które odczytują dane z Cosmos DB.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `CosmosDBTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `cosmosDBTrigger` . |
|**wskazywa** | n/d | Musi być ustawiony na `in` . Ten parametr jest ustawiany automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje listę dokumentów ze zmianami. |
|**connectionStringSetting**|**ConnectionStringSetting** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia używane do nawiązywania połączenia z monitorowanym kontem Azure Cosmos DB. |
|**Bazy**|**DatabaseName**  | Nazwa bazy danych Azure Cosmos DB z monitorowaną kolekcją. |
|**CollectionName** |**CollectionName** | Nazwa monitorowanej kolekcji. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Obowiązkowe Nazwa ustawienia aplikacji zawierającego parametry połączenia do konta Azure Cosmos DB, które przechowuje kolekcję dzierżawy. Gdy nie jest ustawiona, `connectionStringSetting` wartość jest używana. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. Parametry połączenia dla kolekcji dzierżaw muszą mieć uprawnienia do zapisu.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Obowiązkowe Nazwa bazy danych, która zawiera kolekcję służącą do przechowywania dzierżaw. Gdy nie jest ustawiona, `databaseName` zostanie użyta wartość ustawienia. Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w portalu. |
|**leaseCollectionName** | **LeaseCollectionName** | Obowiązkowe Nazwa kolekcji używanej do przechowywania dzierżaw. Gdy nie jest ustawiona, wartość `leases` jest używana. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Obowiązkowe Po ustawieniu na wartość `true` Kolekcja dzierżawy jest tworzona automatycznie, gdy jeszcze nie istnieje. Wartość domyślna to `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Obowiązkowe Określa liczbę jednostek żądania, które mają zostać przypisane podczas tworzenia kolekcji dzierżaw. To ustawienie jest używane tylko wtedy `createLeaseCollectionIfNotExists` , gdy jest ustawiony na `true` . Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania przy użyciu portalu.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Obowiązkowe Po ustawieniu wartość jest dodawana jako prefiks do dzierżaw utworzonych w kolekcji dzierżawy dla tej funkcji. Użycie prefiksu umożliwia dwóm osobom Azure Functions współużytkowanie tej samej kolekcji dzierżawy przy użyciu różnych prefiksów.
|**feedPollDelay**| **FeedPollDelay**| Obowiązkowe Czas (w milisekundach) oczekiwania między sondowaniem partycji o nowe zmiany w strumieniu, po opróżnieniu wszystkich bieżących zmian. Wartość domyślna to 5 000 milisekund lub 5 sekund.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Obowiązkowe Gdy ta funkcja jest ustawiona, definiuje w milisekundach Interwał uruchamiania zadania w celu obliczenia, czy partycje są dystrybuowane równomiernie między znanymi wystąpieniami hostów. Wartość domyślna to 13000 (13 sekund).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Obowiązkowe Gdy ta wartość jest ustawiona, definiuje w milisekundach interwał, dla którego dzierżawa jest wykonywana w dzierżawie reprezentującej partycję. Jeśli dzierżawa nie zostanie odnowiona w tym interwale, spowoduje to jej wygaśnięcie, a własność partycji zostanie przeniesiona do innego wystąpienia. Wartość domyślna to 60000 (60 s).
|**leaseRenewInterval**| **LeaseRenewInterval**| Obowiązkowe Gdy ta wartość jest ustawiona, definiuje w milisekundach interwał odnawiania dla wszystkich dzierżaw dla partycji aktualnie przechowywanych w danym wystąpieniu. Wartość domyślna to 17000 (17 sekund).
|**checkpointFrequency**| **CheckpointFrequency**| Obowiązkowe Gdy jest ustawiona, definiuje w milisekundach interwał między punktami kontrolnymi dzierżawy. Wartość domyślna to zawsze po każdym wywołaniu funkcji.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Obowiązkowe Po ustawieniu ta właściwość ustawia maksymalną liczbę elementów odebranych na wywołanie funkcji. Jeśli operacje w monitorowanej kolekcji są wykonywane za pomocą procedur składowanych, [zakres transakcji](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) jest zachowywany podczas odczytywania elementów ze źródła zmian. W związku z tym liczba odebranych elementów może być wyższa niż określona wartość, dzięki czemu elementy zmienione przez tę samą transakcję są zwracane jako część jednej niepodzielnej partii.
|**startFromBeginning**| **StartFromBeginning**| Obowiązkowe Ta opcja informuje wyzwalacz, aby odczytał zmiany od początku historii zmian kolekcji zamiast od bieżącego czasu. Odczyt od początku działa tylko podczas pierwszego uruchomienia wyzwalacza, jak w kolejnych uruchomieniach, punkty kontrolne są już przechowywane. Ustawienie tej opcji na, `true` gdy istnieją już utworzone dzierżawy, nie ma żadnego wpływu. |
|**preferredLocations**| **PreferredLocations**| Obowiązkowe Definiuje preferowane lokalizacje (regiony) dla kont bazy danych replikowanych geograficznie w usłudze Azure Cosmos DB. Wartości powinny być rozdzielane przecinkami. Na przykład "Wschodnie stany USA, Południowo-środkowe stany USA, Europa Północna". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

Wyzwalacz wymaga drugiej kolekcji, która używa do przechowywania _dzierżaw_ w ramach partycji. Zarówno monitorowana kolekcja, jak i Kolekcja zawierająca dzierżawy muszą być dostępne, aby wyzwalacz działał.

>[!IMPORTANT]
> Jeśli wiele funkcji jest skonfigurowanych do korzystania z wyzwalacza Cosmos DB dla tej samej kolekcji, każda z tych funkcji powinna używać dedykowanej kolekcji dzierżaw lub określić inną `LeaseCollectionPrefix` dla każdej funkcji. W przeciwnym razie zostanie wyzwolona tylko jedna z funkcji. Informacje na temat prefiksu znajdują się w [sekcji Konfiguracja](#configuration).

Wyzwalacz nie wskazuje, czy dokument został zaktualizowany lub wstawiony, zawiera tylko dokument. Jeśli musisz obsługiwać aktualizacje i wstawiać je inaczej, możesz to zrobić, implementując pola sygnatury czasowej do wstawienia lub aktualizacji.

## <a name="next-steps"></a>Następne kroki

- [Odczytaj dokument Azure Cosmos DB (powiązanie danych wejściowych)](./functions-bindings-cosmosdb-v2-input.md)
- [Zapisz zmiany w dokumencie Azure Cosmos DB (powiązanie danych wyjściowych)](./functions-bindings-cosmosdb-v2-output.md)
