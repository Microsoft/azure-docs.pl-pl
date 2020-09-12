---
title: Konfigurowanie i używanie linku Azure Synapse dla Azure Cosmos DB (wersja zapoznawcza)
description: Dowiedz się, jak włączyć link Synapse dla kont usługi Azure Cosmos, utworzyć kontener z włączonym magazynem analitycznym, połączyć bazę danych Azure Cosmos z obszarem roboczym Synapse i uruchomić zapytania.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.openlocfilehash: d77922d5957a0f6602e00621ebbabeaa3f3ac8f9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322470"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Konfigurowanie i używanie linku Azure Synapse dla Azure Cosmos DB (wersja zapoznawcza)

Link Synapse dla Azure Cosmos DB to natywna w pełni funkcjonalna funkcja przetwarzania danych transakcyjnych i analitycznych (HTAP) w chmurze, która umożliwia uruchamianie analizy niemal w czasie rzeczywistym w Azure Cosmos DB. Link Synapse tworzy ścisłą integrację między usługami Azure Cosmos DB i Azure Synapse Analytics.


> [!IMPORTANT]
> Aby użyć linku usługi Azure Synapse, upewnij się, że konto usługi Azure Cosmos jest obsługiwane & obszarze roboczym usługi Azure Synapse Analytics w jednym z obsługiwanych regionów. Link Synapse platformy Azure jest obecnie dostępny w następujących regionach świadczenia usługi Azure: zachodnie stany USA, Wschodnie stany USA, zachodni stany USA 2, Europa Północna, Europa Zachodnia, Południowe Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Australia Wschodnia, Wschodni wschód, Południowe Zjednoczone Królestwo.

Wykonaj następujące kroki, aby uruchomić zapytania analityczne za pomocą linku Synapse dla Azure Cosmos DB:

* [Włącz link Synapse dla kont usługi Azure Cosmos](#enable-synapse-link)
* [Tworzenie kontenera usługi Azure Cosmos z włączonym magazynem analitycznym](#create-analytical-ttl)
* [Łączenie bazy danych Azure Cosmos z obszarem roboczym Synapse](#connect-to-cosmos-database)
* [Zbadaj magazyn analityczny za pomocą Synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Włącz link usługi Azure Synapse dla kont usługi Azure Cosmos

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto platformy Azure](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Przejdź do konta usługi Azure Cosmos i Otwórz okienko **funkcje** .

1. Wybierz **łącze Synapse** z listy funkcji.

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Znajdź funkcję w wersji zapoznawczej linku Synapse":::

1. Następnie zostanie wyświetlony komunikat z prośbą o włączenie linku Synapse na Twoim koncie. Wybierz pozycję Włącz.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Włącz funkcję linku Synapse":::

1. Twoje konto ma teraz włączoną funkcję linku Synapse. Następnie zobacz jak utworzyć kontenery obsługujące magazyn analityczny, aby automatycznie rozpocząć replikację danych operacyjnych z magazynu transakcyjnego do magazynu analitycznego.

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

[Szablon Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) tworzy link Synapse z włączonym kontem usługi Azure Cosmos dla interfejsu API SQL. Ten szablon służy do tworzenia konta interfejsu API programu Core (SQL) w jednym regionie z kontenerem skonfigurowanym z włączoną funkcją analizy czasu wygaśnięcia (TTL) i opcją używania przepustowości ręcznej lub skalowania automatycznego. Aby wdrożyć ten szablon, kliknij pozycję **Wdróż na platformie Azure** na stronie Readme.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Tworzenie kontenera usługi Azure Cosmos z magazynem analitycznym

Możesz włączyć magazyn analityczny w kontenerze usługi Azure Cosmos podczas tworzenia kontenera. Można użyć Azure Portal lub skonfigurować `analyticalTTL` Właściwość podczas tworzenia kontenera przy użyciu zestawów sdk Azure Cosmos DB.

> [!NOTE]
> Obecnie można włączyć magazyn analityczny dla **nowych** kontenerów (zarówno w ramach nowych, jak i istniejących kont).

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) lub w [Eksploratorze usługi Azure Cosmos](https://cosmos.azure.com/).

1. Przejdź do konta usługi Azure Cosmos i Otwórz kartę **Eksplorator danych** .

1. Wybierz pozycję **nowy kontener** i wprowadź nazwę bazy danych, kontenera, klucza partycji i szczegółów przepływności. Włącz opcję **Magazyn analityczny** . Po włączeniu magazynu analitycznego tworzy on kontener z `AnalyicalTTL` właściwością ustawioną na wartość domyślną-1 (nieograniczone przechowywanie). Ten magazyn analityczny zachowuje wszystkie historyczne wersje rekordów.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Włącz magazyn analityczny dla kontenera usługi Azure Cosmos":::

1. Jeśli na tym koncie nie włączono wcześniej linku Synapse, zostanie wyświetlony monit z pytaniem, że jest to wymaganie wstępne do utworzenia kontenera z włączonym magazynem analitycznym. Jeśli zostanie wyświetlony monit, wybierz pozycję **Włącz łącze Synapse**.

1. Wybierz pozycję **OK**, aby utworzyć magazyn analityczny z włączoną obsługą usługi Azure Cosmos.

### <a name="net-sdk"></a>Zestaw SDK .NET

Poniższy kod tworzy kontener z magazynem analitycznym przy użyciu zestawu .NET SDK. Ustaw właściwość analityczny czas wygaśnięcia na wartość wymaganą. Listę dozwolonych wartości można znaleźć w artykule [analityczne obsługiwane wartości czasu wygaśnięcia (TTL)](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Zestaw SDK Java v4

Poniższy kod tworzy kontener z magazynem analitycznym przy użyciu zestawu SDK języka Java v4. Ustaw `AnalyticalStoreTimeToLiveInSeconds` Właściwość na wartość wymaganą:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Zestaw SDK języka Python v4

Wymagane są minimalne wersje języka Python 2,7 i Azure Cosmos DB zestawu SDK 4.1.0, a zestaw SDK jest zgodny tylko z interfejsem API SQL.

Pierwszym krokiem jest upewnienie się, że jest używana co najmniej wersja [Azure Cosmos DB 4.1.0 zestawu SDK języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
Następny krok polega na utworzeniu kontenera z magazynem analitycznym przy użyciu zestawu Azure Cosmos DB Python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Aktualizuj czas trwania magazynu analitycznego

Po włączeniu magazynu analitycznego z określoną wartością czasu wygaśnięcia (TTL) można później zaktualizować tę wartość do innej prawidłowej wartości. Można zaktualizować tę wartość przy użyciu witryny Azure Portal lub zestawów SDK. Aby uzyskać informacje o różnych opcjach analitycznych konfiguracji czasu wygaśnięcia (TTL), zobacz artykuł [analityczne obsługiwane wartości czasu wygaśnięcia (TTL)](analytical-store-introduction.md#analytical-ttl) .

#### <a name="azure-portal"></a>Azure Portal

Jeśli utworzono kontener z włączonym magazynem analitycznym za pomocą Azure Portal, zawiera on domyślny analityczny czas wygaśnięcia wynoszący-1. Aby zaktualizować tę wartość, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) lub w [Eksploratorze usługi Azure Cosmos](https://cosmos.azure.com/).

1. Przejdź do konta usługi Azure Cosmos i Otwórz kartę **Eksplorator danych** .

1. Wybierz istniejący kontener z włączonym magazynem analitycznym. Rozwiń je i zmodyfikuj następujące wartości:

  * Otwórz okno **Skalowanie i ustawienia**.
  * W obszarze **ustawienie** Znajdź, * * czas magazynu analitycznego na Live * *.
  * Wybierz pozycję **Włączony (brak wartości domyślnej)** lub wybierz pozycję **Włączony** i ustaw wartość TTL.
  * Kliknij przycisk **Zapisz**, aby zapisać zmiany.

#### <a name="net-sdk"></a>Zestaw SDK .NET

Poniższy kod przedstawia sposób aktualizowania czasu wygaśnięcia dla magazynu analitycznego przy użyciu zestawu .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Zestaw SDK Java v4

Poniższy kod przedstawia sposób aktualizowania czasu wygaśnięcia dla magazynu analitycznego za pomocą zestawu SDK języka Java v4:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Nawiązywanie połączenia z obszarem roboczym Synapse

Skorzystaj z instrukcji w temacie [Connect to Azure Synapse link](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) , aby uzyskać dostęp do bazy danych Azure Cosmos DB z usługi Azure Synapse Analytics Studio przy użyciu linku Azure Synapse.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a> Zapytanie przy użyciu Synapse Spark

Skorzystaj z instrukcji w temacie [zapytania Azure Cosmos DB ze sklepu analitycznego](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) , aby dowiedzieć się, jak wykonywać zapytania o Synapse Spark. W tym artykule przedstawiono kilka przykładów, na których można korzystać z magazynu analitycznego z gestów Synapse. Te gesty są widoczne po kliknięciu prawym przyciskiem myszy kontenera. Za pomocą gestów można szybko wygenerować kod i dostosować go do własnych potrzeb. Są one również idealne do odnajdywania danych za pomocą jednego kliknięcia.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Wprowadzenie do usługi Azure Synpase link — przykłady

Możesz znaleźć przykłady umożliwiające rozpoczęcie pracy z usługą Azure Synapse w witrynie [GitHub](https://aka.ms/cosmosdb-synapselink-samples). Są to kompleksowe rozwiązania ze scenariuszami IoT i handlu detalicznego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz następujące dokumenty:

* [Link Synapse platformy Azure dla Azure Cosmos DB.](synapse-link.md)

* [Omówienie magazynu analitycznego usługi Azure Cosmos DB.](analytical-store-introduction.md)

* [Często zadawane pytania dotyczące Azure Cosmos DB.](synapse-link-frequently-asked-questions.md)

* [Apache Spark w usłudze Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Obsługa środowiska wykonawczego bezserwerowego programu SQL Server w usłudze Azure Synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
