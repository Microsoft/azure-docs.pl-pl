---
title: Zarządzanie zasadami indeksowania w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasadami indeksowania, dołączać lub wykluczać właściwość z indeksowania, jak definiować indeksowanie za pomocą różnych zestawów SDK Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/04/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 96ae4162c78f66b75d8c1ef2a8cec16995a5f016
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075708"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Zarządzanie zasadami indeksowania w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W Azure Cosmos DB dane są indeksowane zgodnie z [zasadami indeksowania](index-policy.md) , które są zdefiniowane dla każdego kontenera. Domyślne zasady indeksowania dla nowo utworzonych kontenerów wymuszają indeksy zakresu dla wszelkich ciągów i liczb. Te zasady można zastąpić własnymi niestandardowymi zasadami indeksowania.

> [!NOTE]
> Metoda aktualizacji zasad indeksowania opisana w tym artykule ma zastosowanie tylko do interfejsu API SQL (Core) Azure Cosmos DB. Dowiedz się więcej na temat indeksowania w [interfejsie API Azure Cosmos DB](mongodb-indexing.md) i [indeksowania pomocniczego w Azure Cosmos DB interfejs API Cassandra.](cassandra-secondary-index.md)

## <a name="indexing-policy-examples"></a>Przykłady zasad indeksowania

Poniżej przedstawiono kilka przykładów zasad indeksowania pokazywanych w [ich formacie JSON](index-policy.md#include-exclude-paths), które są widoczne na Azure Portal. Te same parametry można ustawić za pomocą interfejsu wiersza polecenia platformy Azure lub dowolnego zestawu SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Wycofaj zasady, aby selektywnie wykluczać niektóre ścieżki właściwości

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Te zasady indeksowania są równoważne z tymi, które są ustawiane ręcznie ```kind``` , ```dataType``` i ```precision``` do wartości domyślnych. Te właściwości nie są już wymagane do jawnego ustawiania i można je pominąć z poziomu zasad indeksowania w całości (jak pokazano w powyższym przykładzie).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Zasady zgody na wybiórcze uwzględnienie niektórych ścieżek właściwości

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Te zasady indeksowania są równoważne z tymi, które są ustawiane ręcznie ```kind``` , ```dataType``` i ```precision``` do wartości domyślnych. Te właściwości nie są już wymagane do jawnego ustawiania i można je pominąć z poziomu zasad indeksowania w całości (jak pokazano w powyższym przykładzie).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> Zwykle zaleca się użycie zasad indeksowania **rezygnacji** , aby Azure Cosmos DB aktywnie indeksować wszelkie nowe właściwości, które mogą zostać dodane do modelu.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Używanie indeksu przestrzennego tylko dla określonej ścieżki właściwości

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Przykłady złożonych zasad indeksowania

Oprócz dołączania lub wykluczania ścieżek dla poszczególnych właściwości można również określić indeks złożony. Jeśli chcesz wykonać zapytanie z `ORDER BY` klauzulą dla wielu właściwości, wymagany jest [indeks złożony](index-policy.md#composite-indexes) dla tych właściwości. Ponadto indeksy złożone będą mieć korzyść wydajności dla zapytań, które mają filtr i mają klauzulę ORDER BY dla różnych właściwości.

> [!NOTE]
> Ścieżki złożone są niejawne `/?` , ponieważ tylko wartość skalarna w tej ścieżce jest indeksowana. `/*`Symbol wieloznaczny nie jest obsługiwany w ścieżkach złożonych. Nie należy określać `/?` ani `/*` w ścieżce złożonej.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, opis wieku):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Powyższy indeks złożony w polu Nazwa i wiek są wymagane dla #1 zapytań i #2 zapytań:

#1 kwerendy:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

#2 kwerendy:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Ten złożony indeks będzie korzystać z #3 zapytań i #4 zapytań i optymalizowania filtrów:

#3 kwerendy:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 kwerendy:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, wiek ASC) i (nazwa ASC, opis wieku):

W ramach tych samych zasad indeksowania można zdefiniować wiele różnych indeksów złożonych.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Indeks złożony zdefiniowany dla (nazwa ASC, wiek ASC):

Określenie zamówienia jest opcjonalne. Jeśli nie zostanie określony, kolejność jest rosnąca.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Wykluczanie wszystkich ścieżek właściwości, ale utrzymywanie aktywności indeksowania

Tych zasad można używać w sytuacjach, gdy [Funkcja czasu wygaśnięcia (TTL)](time-to-live.md) jest aktywna, ale nie jest wymagany żaden dodatkowy indeks (aby użyć Azure Cosmos dB jako czystego magazynu klucz-wartość).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Brak indeksowania

Te zasady spowodują wyłączenie indeksowania. Jeśli `indexingMode` jest ustawiona na `none` , nie można ustawić czasu wygaśnięcia dla kontenera.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Aktualizowanie zasad indeksowania

W Azure Cosmos DB zasady indeksowania można aktualizować przy użyciu dowolnej z poniższych metod:

- z Azure Portal
- Korzystanie z interfejsu wiersza polecenia platformy Azure
- przy użyciu programu PowerShell
- Korzystanie z jednego z zestawów SDK

[Aktualizacja zasad indeksowania](index-policy.md#modifying-the-indexing-policy) wyzwala transformację indeksu. Postęp tego przekształcenia może być również śledzony z zestawów SDK.

> [!NOTE]
> Podczas aktualizowania zasad indeksowania zapisy do Azure Cosmos DB będą nieprzerwane. Dowiedz się więcej o [przekształceniach indeksowania](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Kontenery usługi Azure Cosmos przechowują swoje zasady indeksowania jako dokument JSON, który Azure Portal umożliwia bezpośrednie edytowanie.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Utwórz nowe konto usługi Azure Cosmos lub wybierz istniejące.

1. Otwórz okienko **Eksplorator danych** i wybierz kontener, w którym chcesz korzystać.

1. Kliknij pozycję **skaluj & ustawienia** .

1. Modyfikowanie dokumentu JSON zasad indeksowania (Zobacz przykłady [poniżej](#indexing-policy-examples))

1. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz** .

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Zarządzanie indeksowaniem przy użyciu witryny Azure Portal":::

## <a name="use-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Aby utworzyć kontener z niestandardowymi zasadami indeksowania, zobacz [Tworzenie kontenera z niestandardowymi zasadami indeksu przy użyciu interfejsu wiersza polecenia](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby utworzyć kontener z niestandardowymi zasadami indeksowania, zobacz [Tworzenie kontenera z niestandardowymi zasadami indeksu przy użyciu programu PowerShell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> Korzystanie z zestawu SDK platformy .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

`DocumentCollection`Obiekt z [zestawu .NET SDK V2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) uwidacznia `IndexingPolicy` Właściwość, która umożliwia zmianę `IndexingMode` i dodanie lub usunięcie `IncludedPaths` i `ExcludedPaths` .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Aby śledzić postęp transformacji indeksu, należy przekazać `RequestOptions` obiekt, który ustawia `PopulateQuotaInfo` Właściwość na `true` .

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

`ContainerProperties`Obiekt z [zestawu .NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (zobacz [ten przewodnik Szybki Start](create-sql-api-dotnet.md) dotyczący użycia) uwidacznia `IndexingPolicy` Właściwość, która umożliwia zmianę `IndexingMode` i dodanie lub usunięcie `IncludedPaths` i `ExcludedPaths` .

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Aby śledzić postęp transformacji indeksu, należy przekazać `RequestOptions` obiekt, który ustawia `PopulateQuotaInfo` Właściwość na `true` , a następnie pobrać wartość z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Podczas definiowania niestandardowych zasad indeksowania podczas tworzenia nowego kontenera, zestaw SDK V3's Fluent API umożliwia zapisanie tej definicji w zwięzłej i wydajnej sposób:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

`DocumentCollection`Obiekt z [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (zobacz [ten przewodnik Szybki Start](create-sql-api-java.md) dotyczący użycia) `getIndexingPolicy()` i `setIndexingPolicy()` metody. `IndexingPolicy`Obiekt, który manipuluje, umożliwia zmianę trybu indeksowania i Dodawanie lub usuwanie dołączonych i wykluczonych ścieżek.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Aby śledzić postęp transformacji indeksu w kontenerze, należy przekazać `RequestOptions` obiekt, który żąda informacji o przydziale, a następnie pobrać wartość z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK Node.js

`ContainerDefinition`Interfejs z [zestawu SDKNode.js](https://www.npmjs.com/package/@azure/cosmos) (zobacz [ten przewodnik Szybki Start](create-sql-api-nodejs.md) dotyczący użycia) ujawnia `indexingPolicy` Właściwość, która umożliwia zmianę `indexingMode` i dodanie lub usunięcie `includedPaths` i `excludedPaths` .

Pobierz szczegóły kontenera

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Ustawianie spójności trybu indeksowania

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Dodaj dołączoną ścieżkę obejmującą indeks przestrzenny

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Dodaj wykluczoną ścieżkę

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Aktualizowanie kontenera ze zmianami

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Aby śledzić postęp transformacji indeksu w kontenerze, należy przekazać `RequestOptions` obiekt, który ustawia `populateQuotaInfo` Właściwość na `true` , a następnie pobrać wartość z `x-ms-documentdb-collection-index-transformation-progress` nagłówka odpowiedzi.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Używanie zestawu Python SDK

# <a name="python-sdk-v3"></a>[Zestaw Python SDK v3](#tab/pythonv3)

W przypadku korzystania z [zestawu Python SDK v3](https://pypi.org/project/azure-cosmos/) (zobacz [ten przewodnik Szybki Start](create-sql-api-python.md) dotyczący użycia) Konfiguracja kontenera jest zarządzana jako słownik. Z tego słownika można uzyskać dostęp do zasad indeksowania i wszystkich jej atrybutów.

Pobierz szczegóły kontenera

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Ustawianie spójności trybu indeksowania

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definiowanie zasad indeksowania z dołączoną ścieżką i indeksem przestrzennym

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definiowanie zasad indeksowania przy użyciu ścieżki wykluczonej

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Dodawanie indeksu złożonego

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Aktualizowanie kontenera ze zmianami

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Zestaw Python SDK v4](#tab/pythonv4)

W przypadku korzystania z [zestawu SDK języka Python w wersji 4](https://pypi.org/project/azure-cosmos/), konfiguracja kontenera jest zarządzana jako słownik. Z tego słownika można uzyskać dostęp do zasad indeksowania i wszystkich jej atrybutów.

Pobierz szczegóły kontenera

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Ustawianie spójności trybu indeksowania

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definiowanie zasad indeksowania z dołączoną ścieżką i indeksem przestrzennym

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definiowanie zasad indeksowania przy użyciu ścieżki wykluczonej

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Dodawanie indeksu złożonego

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Aktualizowanie kontenera ze zmianami

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Pobieranie postępu transformacji indeksu z nagłówków odpowiedzi
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat indeksowania znajdziesz w następujących artykułach:

- [Omówienie indeksowania](index-overview.md)
- [Zasady indeksowania](index-policy.md)
