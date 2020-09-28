---
title: Regionalne punkty końcowe dla bazy danych programu Azure Cosmos DB Graph
description: Dowiedz się, jak nawiązać połączenie z najbliższym punktem końcowym bazy danych grafu dla aplikacji
author: jasonwhowell
ms.author: jasonh
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3dd7df738f49b14a16be8a9741fb2c82ed3bf97c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409013"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionalne punkty końcowe dla konta programu Azure Cosmos DB Graph
Baza danych programu Azure Cosmos DB Graph jest [dystrybuowana globalnie](distribute-data-globally.md) , dzięki czemu aplikacje mogą używać wielu punktów końcowych odczytu. Aplikacje, które wymagają dostępu do zapisu w wielu lokalizacjach, powinny umożliwiać obsługę [wielu wzorców](how-to-multi-master.md) .

Powody, aby wybrać więcej niż jeden region:
1. **Skalowalność w poziomie** — w miarę wzrostu obciążenia aplikacji może być rozsądne kierowanie ruchu odczytu do różnych regionów świadczenia usługi Azure.
2. **Małe opóźnienia** — możesz zmniejszyć obciążenie opóźnienia sieci dla każdego przechodzenia przez kierowanie ruchu odczytu i zapisu do najbliższego regionu platformy Azure.

Wymaganie **zamieszkania danych** jest realizowane przez ustawienie zasad Azure Resource Manager na koncie Cosmos DB. Klient może ograniczyć regiony, w których Cosmos DB replikuje dane.

## <a name="traffic-routing"></a>Routing ruchu

Aparat bazy danych programu Cosmos DB Graph działa w wielu regionach, z których każdy zawiera wiele klastrów. Każdy klaster ma setki maszyn. Konto grafu Cosmos DB DNS CNAME *AccountName.Gremlin.Cosmos.Azure.com* jest rozpoznawane jako rekord DNS a klastra. Pojedynczy adres IP modułu równoważenia obciążenia ukrywa wewnętrzną topologię klastra.

Dla każdego regionu konta programu Cosmos DB Graph tworzony jest regionalny rekord CNAME systemu DNS. Formatem regionalnego punktu końcowego jest *AccountName-region.Gremlin.Cosmos.Azure.com*. Segment regionów regionalnego punktu końcowego jest uzyskiwany przez usunięcie wszystkich spacji z nazwy [regionu platformy Azure](https://azure.microsoft.com/global-infrastructure/regions) . Na przykład `"East US 2"` region `"contoso"` globalnego konta bazy danych będzie miał *contoso-eastus2.Gremlin.Cosmos.Azure.com* CNAME DNS

TinkerPop Gremlin Client został zaprojektowany do pracy z jednym serwerem. Aplikacja może używać globalnego rekordu CNAME DNS na potrzeby ruchu odczytu i zapisu. Aplikacje obsługujące regiony powinny używać regionalnego punktu końcowego do odczytu ruchu. Użyj regionalnego punktu końcowego do zapisu ruchu tylko wtedy, gdy określony region jest skonfigurowany do akceptowania zapisów. 

> [!NOTE]
> Aparat grafu Cosmos DB może akceptować operacje zapisu w regionie odczytu przez ruch przychodzący do regionu zapisu. Nie zaleca się wysyłania zapisów do regionu tylko do odczytu, ponieważ zwiększa to opóźnienie przechodzenia i podlega ograniczeniom w przyszłości.

Konto globalnego konta bazy danych CNAME zawsze wskazuje prawidłowy region zapisu. Podczas pracy w trybie failover po stronie serwera w regionie zapisu Cosmos DB aktualizuje globalne konto bazy danych CNAME do punktu w nowym regionie. Jeśli aplikacja nie może obsłużyć przekierowywania ruchu po przejściu w tryb failover, powinna używać globalnego konta bazy danych DNS CNAME.

> [!NOTE]
> Cosmos DB nie kieruje ruchu w oparciu o sąsiedztwo geograficzne obiektu wywołującego. Do każdej aplikacji można wybrać odpowiedni region zgodnie z unikatowymi potrzebami aplikacji.

## <a name="portal-endpoint-discovery"></a>Odnajdywanie punktów końcowych portalu

Najprostszym sposobem uzyskania listy regionów dla konta Azure Cosmos DB Graph jest blok przegląd w Azure Portal. Będzie ona działała w przypadku aplikacji, które często nie zmieniają regionów lub aby można było zaktualizować listę za pomocą konfiguracji aplikacji.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Pobieranie regionów konta Cosmos DB Graph z portalu":::

W poniższym przykładzie przedstawiono ogólne zasady dostępu do regionalnego punktu końcowego Gremlin. Aplikacja powinna rozważyć liczbę regionów, do których ma być wysyłany ruch, oraz liczbę odpowiednich klientów Gremlin do wystąpienia.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Odnajdywanie punktu końcowego zestawu SDK

Aplikacja może używać [zestawu SDK Azure Cosmos DB](sql-api-sdk-dotnet.md) , aby odnajdywać lokalizacje odczytu i zapisu dla konta grafu. Te lokalizacje można zmienić w dowolnym momencie za pomocą ręcznej zmiany konfiguracji po stronie serwera lub automatycznej pracy awaryjnej.

TinkerPop Gremlin SDK nie ma interfejsu API do odnajdywania regionów konta bazy danych programu Graph Cosmos DB. Aplikacje, które wymagają odnajdowania punktów końcowych środowiska uruchomieniowego, muszą obsługiwać 2 oddzielne zestawy SDK w przestrzeni procesu.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Następne kroki
* [Jak zarządzać kontrolą konta bazy danych](how-to-manage-database-account.md) w Azure Cosmos DB
* [Wysoka dostępność](high-availability.md) w Azure Cosmos DB
* [Dystrybucja globalna z Azure Cosmos DBą pod okapem](global-dist-under-the-hood.md)
* [Przykłady interfejsu wiersza polecenia platformy Azure](cli-samples.md) dla Azure Cosmos DB
