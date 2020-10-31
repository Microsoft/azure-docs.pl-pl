---
title: Dane lokalizacji geograficznej i GEOJSON w Azure Cosmos DB
description: Dowiedz się, jak tworzyć obiekty przestrzenne za pomocą Azure Cosmos DB i interfejsu API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 10882a0f4daf5c0d9aa1d4aeb53b2f14f9d1dea0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100834"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dane lokalizacji geograficznej i GEOJSON w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ten artykuł zawiera wprowadzenie do funkcji geoprzestrzennych w Azure Cosmos DB. Obecnie przechowywanie i dostęp do danych geoprzestrzennych jest obsługiwane tylko przez Azure Cosmos DB kont interfejsu API SQL. Po zapoznaniu się z naszą dokumentacją dotyczącą indeksowania geograficznego można odpowiedzieć na następujące pytania:

* Jak mogę przechowywać dane przestrzenne w Azure Cosmos DB?
* Jak można badać dane geograficzne w Azure Cosmos DB w SQL i LINQ?
* Jak mogę włączyć lub wyłączyć indeksowanie przestrzenne w Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Przypadki użycia danych przestrzennych

Dane geograficzne często obejmują zapytania bliskości, na przykład "Znajdź wszystkie sklepy kawowe w pobliżu mojej bieżącej lokalizacji". Typowe przypadki użycia to:

* Analiza geolokalizacyjna, prowadząca do określonych inicjatyw marketingowych.
* Personalizacja oparta na lokalizacjach dla wielu branż, takich jak handel detaliczny i opieka medyczna.
* Ulepszenie logistyczne dla optymalizacji transportowej.
* Analiza ryzyka, szczególnie w przypadku ubezpieczeń i firm finansowych.
* Świadomość sytuacji w przypadku alertów i powiadomień.

## <a name="introduction-to-spatial-data"></a>Wprowadzenie do danych przestrzennych

Dane przestrzenne opisują położenie i kształt obiektów w miejscu. W większości aplikacji odpowiadają one obiektom na dane ziemi i danych geoprzestrzennych. Dane przestrzenne mogą służyć do reprezentowania lokalizacji osoby, miejsca zainteresowania lub granicy miasta lub Lake.

Interfejs API SQL Azure Cosmos DB obsługuje dwa typy danych przestrzennych: typ danych **geometrii** i typ danych **Geografia** .

- Typ **geometrii** reprezentuje dane w układzie współrzędnych Euclidean (płaski)
- Typ **geografii** reprezentuje dane w układzie współrzędnych rundy-ziemi.

## <a name="supported-data-types"></a>Obsługiwane typy danych

Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań dotyczących danych punktów geoprzestrzennych, które są reprezentowane przy użyciu [specyfikacji GEOJSON](https://tools.ietf.org/html/rfc7946). Struktury danych GEOJSON są zawsze prawidłowymi obiektami JSON, dlatego mogą być przechowywane i wysyłane zapytania przy użyciu Azure Cosmos DB bez jakichkolwiek wyspecjalizowanych narzędzi lub bibliotek.

Azure Cosmos DB obsługuje następujące typy danych przestrzennych:

- Moment
- LineString
- Wielokąt
- MultiPolygon

### <a name="points"></a>Punkty

**Punkt** oznacza pojedynczą pozycję w miejscu. W danych geoprzestrzennych punkt reprezentuje dokładną lokalizację, która może być adresem ulicy sklepu spożywczego, kiosku, samochodów lub miast.  Punkt jest reprezentowany w GeoJSON (i Azure Cosmos DB) za pomocą pary współrzędnej lub długości i szerokości geograficznej.

Oto przykład JSON dla punktu:

**Punkty w Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Typy danych przestrzennych można osadzić w dokumencie Azure Cosmos DB, jak pokazano w tym przykładzie profilu użytkownika zawierającego dane lokalizacji:

**Użyj profilu z lokalizacją przechowywaną w Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punkty w układzie współrzędnych geometrii

W przypadku typu danych **geometria** Specyfikacja GEOJSON określa najpierw oś poziomą i drugą oś pionową.

### <a name="points-in-a-geography-coordinate-system"></a>Punkty w układzie współrzędnych geograficznych

W przypadku typu danych **Geografia** Specyfikacja GEOJSON określa długość i szerokość geograficzną. Podobnie jak w przypadku innych aplikacji do mapowania, Długość geograficzna i Szerokość geograficzna są kąty i reprezentowane w stopniu. Wartości długości geograficznej są mierzone od pierwszych Południek i są między-180 stopni i 180,0 stopni, a wartości szerokości geograficznej są mierzone od równika i są między-90,0 stopni i 90,0 stopni.

Azure Cosmos DB interpretuje współrzędne reprezentowane na system odniesienia WGS-84. Więcej szczegółowych informacji na temat współrzędnych systemów odniesienia znajduje się poniżej.

### <a name="linestrings"></a>LineStrings

**LineStrings** reprezentuje serię dwóch lub więcej punktów w miejscu i segmentów linii, które je łączą. W danych geoprzestrzennych LineStrings są często używane do reprezentowania autostrad lub rzek.

**LineStrings w GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Wielokąty

**Wielokąt** jest granicą połączonych punktów, które tworzą zamkniętą LineString. Wielokąty są często używane do reprezentowania naturalnych formatów, takich jak laki i jurysdykcje polityczne, takie jak miasta i Stany. Oto przykład wielokąta w Azure Cosmos DB:

**Wielokąty w GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Specyfikacja GEOJSON wymaga, aby w przypadku prawidłowych wielokątów należała była taka sama jak pierwsza, aby utworzyć kształt zamknięty.
>
> Punkty w obrębie wielokąta muszą być określone w kolejności licznika w prawo. Wielokąt określony w kolejności w prawo oznacza odwrotność regionu w nim.
>
>

### <a name="multipolygons"></a>Wielowielokąty

**MultiPolygon** jest tablicą z zero lub więcej wielokątów. **Wielowielokąty** nie mogą nakładać się na strony ani mieć żadnego wspólnego obszaru. Mogą one stykać się z jednym lub wieloma punktami.

**Wielowielokątne w GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Systemy odniesienia współrzędnych

Ze względu na to, że kształt ziemi jest nieregularny, współrzędne danych geoprzestrzennych są reprezentowane w wielu systemach odniesienia współrzędnych (KSR), z których każdy ma własne ramki referencyjne i jednostki miary. Na przykład "krajowa siatka Zjednoczonego Królestwa" to system odniesienia dla Wielkiej Brytanii, ale nie poza nim.

Najbardziej popularnym KSR w korzystaniu z dzisiaj jest świat Geodetic System  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Urządzenia GPS i wiele usług mapowania, w tym mapy Google i interfejsy API mapy Bing używają WGS-84. Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań dotyczących danych geoprzestrzennych geograficznych tylko przy użyciu WGS-84 KSR.

## <a name="creating-documents-with-spatial-data"></a>Tworzenie dokumentów z danymi przestrzennymi
Podczas tworzenia dokumentów zawierających wartości GEOJSON są one automatycznie indeksowane przy użyciu indeksu przestrzennego zgodnie z zasadami indeksowania kontenera. Jeśli pracujesz z zestawem SDK Azure Cosmos DB w języku, który jest typem dynamicznym, takim jak Python lub Node.js, musisz utworzyć prawidłowy plik GEOJSON.

**Utwórz dokument z danymi geograficznymi w Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Jeśli pracujesz z interfejsami API SQL, możesz użyć `Point` klas,, `LineString` `Polygon` , i `MultiPolygon` w `Microsoft.Azure.Cosmos.Spatial` przestrzeni nazw do osadzania informacji o lokalizacji w obiektach aplikacji. Te klasy pomagają uprościć serializacji i deserializacji danych przestrzennych w GeoJSON.

**Tworzenie dokumentu z danymi geograficznymi w programie .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Jeśli nie masz informacji o szerokości geograficznej i długości geograficznej, ale masz adresy fizyczne lub lokalizacje, takie jak miasto lub kraj/region, można wyszukiwać rzeczywiste współrzędne przy użyciu usługi geokodowania, takiej jak usługi w usłudze mapy Bing. Dowiedz się więcej o geokodowania map Bing [tutaj](/bingmaps/rest-services/).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zacząć korzystać z obsługi geograficznej w Azure Cosmos DB, możesz dalej:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o wysyłaniu [zapytań do danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-query.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-index.md)