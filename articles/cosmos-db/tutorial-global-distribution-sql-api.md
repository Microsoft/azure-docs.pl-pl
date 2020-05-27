---
title: 'Samouczek Azure Cosmos DB: samouczek dotyczący dystrybucji globalnej dla interfejsu API SQL'
description: 'Samouczek: informacje o konfigurowaniu Azure Cosmos DB globalnej dystrybucji przy użyciu interfejsu API SQL z platformą .NET, Java i Python oraz różnymi innymi zestawami SDK'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: ee648efde22e6bbef045b9d89b8d016e2e489f20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872967"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Samouczek: Konfigurowanie globalnej dystrybucji Azure Cosmos DB przy użyciu interfejsu API SQL

W tym artykule pokazano, jak za pomocą Azure Portal skonfigurować Azure Cosmos DB dystrybucję globalną, a następnie nawiązać połączenie przy użyciu interfejsu API SQL.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu [interfejsów API SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a>Nawiązywanie połączenia z preferowanym regionem przy użyciu interfejsu API SQL

Aby można było korzystać z [dystrybucji globalnej](distribute-data-globally.md), w aplikacjach klienckich można określić uporządkowaną listę preferencji regionów, która będzie używana do wykonywania operacji na dokumentach. Można to zrobić, ustawiając zasady połączenia. Na podstawie konfiguracji konta usługi Azure Cosmos DB, bieżącej dostępności regionalnej i określonej listy preferencji zestaw SDK SQL wybiera najbardziej optymalny punkt końcowy, w którym będą wykonywane operacje zapisu i odczytu.

Lista preferencji jest określana podczas inicjowania połączenia przy użyciu zestawów SDK SQL. Zestawy SDK akceptują opcjonalny parametr „PreferredLocations”, to znaczy uporządkowaną listę regionów świadczenia usługi Azure.

Zestaw SDK automatycznie wysyła wszystkie operacje zapisu do bieżącego regionu zapisu.

Wszystkie operacje odczytu są wysyłane do pierwszego dostępnego regionu na liście PreferredLocations. Jeśli żądanie kończy się niepowodzeniem, klient przechodzi do następnego regionu z listy itd.

Zestawy SDK podejmują próby odczytu tylko z regionów określonych na liście PreferredLocations. Na przykład jeśli konto bazy danych jest dostępne w czterech regionach, ale klient określa tylko dwa regiony odczytu (nie zapisu) na liście PreferredLocations, operacje odczytu z regionu odczytu, który nie jest określony na liście PreferredLocations, nie są obsługiwane. Jeśli regiony odczytu określone na liście PreferredLocations są niedostępne, operacje odczytu są obsługiwane przez region zapisu.

Aplikacja może zweryfikować bieżący punkt końcowy zapisu i punkt końcowy odczytu wybrany przez zestaw SDK, sprawdzając dwie właściwości, WriteEndpoint i ReadEndpoint, dostępne w zestawie SDK w wersji 1.8 i nowszych.

Jeśli właściwość PreferredLocations nie została określona, wszystkie żądania są obsługiwane z bieżącego regionu zapisu.

## <a name="net-sdk"></a>Zestaw SDK .NET
Zestawu SDK można używać bez konieczności wprowadzania jakichkolwiek zmian kodu. W takim przypadku zestaw SDK automatycznie kieruje operacje odczytu i zapisu do bieżącego regionu zapisu.

W wersji 1.8 (i nowszych) zestawu SDK .NET parametr ConnectionPolicy dla konstruktora DocumentClient ma właściwość o nazwie Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Jest to właściwość typu Collection `<string>`, która powinna zawierać listę nazw regionów. Wartości ciągu są sformatowane według kolumny Nazwa regionu na stronie [Regiony platformy Azure][regions], bez spacji przed pierwszym i po ostatnim znaku.

Bieżące punkty końcowe zapisu i odczytu są dostępne odpowiednio we właściwościach DocumentClient.WriteEndpoint i DocumentClient.ReadEndpoint.

> [!NOTE]
> Adresów URL punktów końcowych nie należy traktować jako długotrwałych stałych. Usługa może zaktualizować je w dowolnym momencie. Zestaw SDK obsługuje tę zmianę automatycznie.
>
>

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

## <a name="nodejsjavascript"></a>Node. js/JavaScript

> [!NOTE]
> Adresów URL punktów końcowych nie należy traktować jako długotrwałych stałych. Usługa może zaktualizować je w dowolnym momencie. Zestaw SDK obsługuje tę zmianę automatycznie.
>
>

Poniżej znajduje się przykładowy kod dla środowiska Node. js/JavaScript.

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

## <a name="java-v4-sdk"></a>Zestaw SDK Java v4

Poniższy kod przedstawia sposób ustawiania preferowanych lokalizacji za pomocą zestawu SDK języka Java:

### <a id="java4-preferred-locations"></a>
#### <a name="async"></a>[Asynchroniczne](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

#### <a name="sync"></a>[Synchronizuj](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST
Gdy konto bazy danych zostało udostępnione w wielu regionach, klienci mogą wysyłać zapytania o jego dostępność, wykonując żądanie GET względem poniższego identyfikatora URI.

    https://{databaseaccount}.documents.azure.com/

Usługa zwraca listę regionów i odpowiadające im identyfikatory URI punktów końcowych usługi Azure Cosmos DB dla replik. W odpowiedzi jest wskazywany bieżący region zapisu. Klient może następnie wybrać odpowiedni punkt końcowy dla wszystkich kolejnych żądań interfejsu API REST w pokazany poniżej sposób.

Przykładowa odpowiedź

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

