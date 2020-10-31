---
title: 'Samouczek Azure Cosmos DB: samouczek dotyczący dystrybucji globalnej dla interfejsu API SQL'
description: 'Samouczek: informacje o konfigurowaniu Azure Cosmos DB globalnej dystrybucji przy użyciu interfejsu API SQL z platformą .NET, Java i Python oraz różnymi innymi zestawami SDK'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: d953a9d8a62736c56328a8e66c00767927cf6e6a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074016"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Samouczek: Konfigurowanie globalnej dystrybucji Azure Cosmos DB przy użyciu interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule pokazano, jak za pomocą Azure Portal skonfigurować Azure Cosmos DB dystrybucję globalną, a następnie nawiązać połączenie przy użyciu interfejsu API SQL.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu [interfejsów API SQL](./introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API SQL

Aby można było korzystać z [dystrybucji globalnej](distribute-data-globally.md), w aplikacjach klienckich można określić uporządkowaną listę preferencji regionów, która będzie używana do wykonywania operacji na dokumentach. Na podstawie konfiguracji konta usługi Azure Cosmos DB, bieżącej dostępności regionalnej i określonej listy preferencji zestaw SDK SQL wybiera najbardziej optymalny punkt końcowy, w którym będą wykonywane operacje zapisu i odczytu.

Lista preferencji jest określana podczas inicjowania połączenia przy użyciu zestawów SDK SQL. Zestawy SDK akceptują opcjonalny parametr `PreferredLocations` , który jest uporządkowaną listą regionów świadczenia usługi Azure.

Zestaw SDK automatycznie wysyła wszystkie operacje zapisu do bieżącego regionu zapisu. Wszystkie operacje odczytu są wysyłane do pierwszego dostępnego regionu na liście preferowanych lokalizacji. Jeśli żądanie nie powiedzie się, klient przejdzie do następnego regionu listy.

Zestaw SDK podejmie próbę odczytu tylko z regionów określonych w preferowanych lokalizacjach. Jeśli na przykład konto usługi Azure Cosmos jest dostępne w czterech regionach, ale klient określi tylko dwa regiony do odczytu (bez zapisu) w ramach `PreferredLocations` , nie będą one obsługiwane w regionie odczytywania, który nie jest określony w `PreferredLocations` . Jeśli regiony odczytu określone na `PreferredLocations` liście nie są dostępne, odczyty z regionu zapisu będą obsługiwane.

Aplikacja może sprawdzić bieżący punkt końcowy zapisu i punkt końcowy odczytu wybrany przez zestaw SDK, sprawdzając dwie właściwości `WriteEndpoint` i `ReadEndpoint` dostępne w zestawie SDK w wersji 1,8 lub nowszej. Jeśli `PreferredLocations` Właściwość nie jest ustawiona, wszystkie żądania będą obsługiwane z bieżącego regionu zapisu.

Jeśli nie określisz preferowanych lokalizacji, ale użyto `setCurrentLocation` metody, zestaw SDK automatycznie wypełni preferowane lokalizacje na podstawie bieżącego regionu, w którym jest uruchomiony klient programu. Zestaw SDK porządkuje regiony na podstawie sąsiedztwa regionu w bieżącym regionie.

## <a name="net-sdk"></a>Zestaw SDK .NET

Zestawu SDK można używać bez konieczności wprowadzania jakichkolwiek zmian kodu. W takim przypadku zestaw SDK automatycznie kieruje operacje odczytu i zapisu do bieżącego regionu zapisu.

W wersji 1.8 (i nowszych) zestawu SDK .NET parametr ConnectionPolicy dla konstruktora DocumentClient ma właściwość o nazwie Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Jest to właściwość typu Collection `<string>`, która powinna zawierać listę nazw regionów. Wartości ciągu są formatowane według kolumny Nazwa regionu na stronie [regiony platformy Azure][regions] , bez spacji przed ani po pierwszym i ostatnim znakiem.

Bieżące punkty końcowe zapisu i odczytu są dostępne odpowiednio we właściwościach DocumentClient.WriteEndpoint i DocumentClient.ReadEndpoint.

> [!NOTE]
> Adresów URL punktów końcowych nie należy traktować jako długotrwałych stałych. Usługa może zaktualizować je w dowolnym momencie. Zestaw SDK obsługuje tę zmianę automatycznie.
>

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

Jeśli używasz zestawu SDK platformy .NET V2, użyj właściwości, `PreferredLocations` Aby ustawić preferowany region.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Alternatywnie można użyć `SetCurrentLocation` właściwości i zezwolić zestawowi SDK na wybór preferowanej lokalizacji na podstawie sąsiedztwa.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

Jeśli używasz zestawu SDK dla platformy .NET v3, użyj `ApplicationPreferredRegions` właściwości, aby ustawić preferowany region.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Alternatywnie można użyć `ApplicationRegion` właściwości i zezwolić zestawowi SDK na wybór preferowanej lokalizacji na podstawie sąsiedztwa.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Adresów URL punktów końcowych nie należy traktować jako długotrwałych stałych. Usługa może zaktualizować je w dowolnym momencie. Zestaw SDK obsługuje tę zmianę automatycznie.
>
>

Poniżej znajduje się przykładowy kod dla Node.js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Zestaw SDK dla języka Python

Poniższy kod przedstawia sposób ustawiania preferowanych lokalizacji za pomocą zestawu SDK języka Python:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Zestaw SDK Java v4

Poniższy kod przedstawia sposób ustawiania preferowanych lokalizacji za pomocą zestawu SDK języka Java:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

Po udostępnieniu konta bazy danych w wielu regionach klienci mogą wysyłać zapytania o swoją dostępność, wykonując żądanie GET dla tego identyfikatora URI `https://{databaseaccount}.documents.azure.com/`

Usługa zwraca listę regionów i odpowiadające im identyfikatory URI punktów końcowych usługi Azure Cosmos DB dla replik. W odpowiedzi jest wskazywany bieżący region zapisu. Klient może następnie wybrać odpowiedni punkt końcowy dla wszystkich kolejnych żądań interfejsu API REST w pokazany poniżej sposób.

Przykładowa odpowiedź

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Wszystkie żądania PUT, POST i DELETE muszą być przekazywane do wskazanego identyfikatora URI zapisu
* Wszystkie odbierane i inne żądania tylko do odczytu (na przykład zapytania) mogą przejść do dowolnego punktu końcowego wyboru klienta

Żądania zapisu w regionach tylko do odczytu kończą się niepowodzeniem i wyświetlany jest kod błędu HTTP 403 („Dostęp zabroniony”).

Jeśli region zapisu zmieni się po początkowej fazie odnajdywania klienta, kolejne zapisy w poprzednim regionie zapisu będą kończyć się niepowodzeniem z kodem błędu HTTP 403 ("zabronione"). Klient powinien wówczas ponownie uzyskać (żądanie GET) listę regionów, aby zaktualizować region zapisu.

To wszystko — na tym kończy się ten samouczek. Aby dowiedzieć się, jak zarządzać spójnością globalnie replikowanego konta, przeczytaj [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Natomiast aby uzyskać więcej informacji na temat sposobu działania globalnej replikacji w usłudze Azure Cosmos DB, zobacz [Dystrybuowanie danych globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsów API SQL

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak programować lokalnie przy użyciu lokalnego emulatora usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Programowanie lokalnie za pomocą emulatora](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/