---
title: Indeksowanie danych geoprzestrzennych przy użyciu Azure Cosmos DB
description: Indeksuj dane przestrzenne za pomocą Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: 546b664c74980b3522fefed82c00eec414641eaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326630"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indeksowanie danych geoprzestrzennych przy użyciu Azure Cosmos DB

Zaprojektowano aparat bazy danych Azure Cosmos DB, aby niezależny od schemat i zapewnić obsługę pierwszej klasy w formacie JSON. Aparat zoptymalizowanej bazy danych z możliwością zapisu Azure Cosmos DB natywnie rozumie dane przestrzenne reprezentowane w standardzie GEOJSON.

W Nutshell geometria jest rzutowana od współrzędnych Geodetic do płaszczyzny 2D, a następnie przedzielona stopniowo na komórki przy użyciu **QuadTree**. Te komórki są mapowane na 1D w oparciu o lokalizację komórki w **Hilbertej krzywej wypełniania**, która zachowuje miejscowość punktów. Ponadto, gdy dane lokalizacji są indeksowane, przechodzi przez proces znany jako **mozaikowania**, czyli wszystkie komórki, które przecinają lokalizację, są identyfikowane i przechowywane jako klucze w indeksie Azure Cosmos DB. W czasie zapytania, argumenty, takie jak punkty i wielokąty, są również tessellated do wyodrębnienia odpowiednich zakresów identyfikatorów komórek, a następnie używane do pobierania danych z indeksu.

W przypadku określenia zasad indeksowania, które zawierają indeks przestrzenny dla/* (wszystkie ścieżki), wszystkie dane znajdujące się w kontenerze są indeksowane pod kątem wydajnych zapytań przestrzennych.

> [!NOTE]
> Azure Cosmos DB obsługuje indeksowanie punktów, LineStrings, wielokątów i wielowielokątnych
>
>

## <a name="modifying-geospatial-data-type"></a>Modyfikowanie typu danych geoprzestrzennych

W kontenerze **Konfiguracja geoprzestrzenna** określa sposób indeksowania danych przestrzennych. Określ jedną **konfigurację geograficzną** dla kontenera: Geografia lub geometria.

Można przełączać między rodzajami przestrzennymi **geograficznymi** i **geometrycznymi** w Azure Portal. Ważne jest, aby przed przełączeniem na typ przestrzenny geometrii utworzyć [prawidłowe zasady indeksowania geometrii przestrzennej z ograniczonym polem](#geometry-data-indexing-examples) .

Poniżej przedstawiono sposób ustawiania **konfiguracji geoprzestrzennej** w **Eksplorator danych** w Azure Portal:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Ustawianie konfiguracji geoprzestrzennej":::

Możesz również zmodyfikować `geospatialConfig` w zestawie SDK platformy .NET, aby dostosować **konfigurację geoprzestrzenną**:

Jeśli nie zostanie określony, `geospatialConfig` domyślnie będzie to typ danych Geografia. Po zmodyfikowaniu `geospatialConfig` wszystkie istniejące dane geograficzne w kontenerze zostaną ponownie indeksowane.

Oto przykład modyfikacji typu danych geoprzestrzennych do `geometry` przez ustawienie `geospatialConfig` właściwości i dodanie elementu **boundingBox**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Przykłady indeksowania danych geograficznych

Poniższy fragment kodu JSON przedstawia zasady indeksowania z włączonym indeksem przestrzennym dla typu danych **Geografia** . Jest ona prawidłowa dla danych przestrzennych z typem danych Geografia i będzie indeksować dowolny punkt GEOJSON, Wielokąt, MultiPolygon lub LineString znaleziony w dokumentach dla zapytań przestrzennych. W przypadku modyfikowania zasad indeksowania przy użyciu Azure Portal można określić następujące dane JSON dla zasad indeksowania, aby włączyć indeksowanie przestrzenne w kontenerze:

**Plik JSON zasad indeksowania kontenera z indeksowaniem przestrzennym geografii**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Jeśli wartość GEOJSON lokalizacji w dokumencie jest źle sformułowana lub nieprawidłowa, nie zostanie ona zaindeksowana na potrzeby zapytań przestrzennych. Możesz sprawdzić poprawność wartości lokalizacji przy użyciu ST_ISVALID i ST_ISVALIDDETAILED.

[Zasady indeksowania](how-to-manage-indexing-policy.md) można także modyfikować za pomocą interfejsu wiersza polecenia platformy Azure, programu PowerShell lub dowolnego zestawu SDK.

## <a name="geometry-data-indexing-examples"></a>Przykłady indeksowania danych geometrycznych

W przypadku typu danych **geometria** , podobnego do typu danych Geografia, należy określić odpowiednie ścieżki i typy do indeksowania. Ponadto należy również określić `boundingBox` w ramach zasad indeksowania, aby wskazać żądany obszar do indeksowania dla tej konkretnej ścieżki. Każda ścieżka geoprzestrzenna wymaga własnych `boundingBox` .

Pole ograniczenia składa się z następujących właściwości:

- **xmin**: minimalna indeksowana Współrzędna x
- **ymin**: minimalna indeksowana Współrzędna y
- **XMAX**: Maksymalna indeksowana Współrzędna x
- **ymax**: Maksymalna indeksowana Współrzędna y

Pole ograniczenia jest wymagane, ponieważ dane geometryczne zajmuje płaszczyznę, która może być nieskończona. Indeksy przestrzenne, jednak wymagają skończonego miejsca. Dla typu danych **Geografia** Ziemia jest granicą i nie trzeba ustawiać pola ograniczenia.

Utwórz pole ograniczenia, które zawiera wszystkie (lub najwięcej) danych. Tylko operacje obliczane na obiektach, które znajdują się w całości wewnątrz pola ograniczenia, będą mogły korzystać z indeksu przestrzennego. Nadanie pola powiązanego większym niż potrzeba będzie negatywnie wpływać na wydajność zapytań.

Oto przykładowe zasady indeksowania, które indeksuje dane **geometrii** z **geospatialConfig** ustawioną na `geometry` :

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
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Powyższe zasady indeksowania mają **boundingBox** (-10, 10) dla współrzędnych x i (-20, 20) dla współrzędnych y. Kontener z powyższymi zasadami indeksowania będzie indeksować wszystkie punkty, wielokąty, wielowielokątne i LineStrings, które znajdują się w całości w tym regionie.

> [!NOTE]
> Próba dodania zasad indeksowania z **boundingBox** do kontenera z `geography` typem danych zakończy się niepowodzeniem. **geospatialConfig** `geometry` Przed dodaniem **boundingBox**należy zmodyfikować geospatialConfig kontenera. Można dodać dane i zmodyfikować pozostałe zasady indeksowania (takie jak ścieżki i typy) przed lub po wybraniu typu danych geoprzestrzennych dla kontenera.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zacząć korzystać z obsługi geograficznej w Azure Cosmos DB, możesz dalej:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o wysyłaniu [zapytań do danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [danych lokalizacji geograficznej i GEOJSON w Azure Cosmos DB](sql-query-geospatial-intro.md)
