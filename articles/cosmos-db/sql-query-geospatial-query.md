---
title: Wykonywanie zapytania o dane geograficzne za pomocą Azure Cosmos DB
description: Wykonywanie zapytania o dane przestrzenne za pomocą Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336133"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Wykonywanie zapytania o dane geograficzne za pomocą Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano sposób wykonywania zapytań dotyczących danych geoprzestrzennych w Azure Cosmos DB przy użyciu języka SQL i LINQ. Obecnie przechowywanie i dostęp do danych geoprzestrzennych jest obsługiwane tylko przez Azure Cosmos DB kont interfejsu API SQL. Azure Cosmos DB obsługuje następujące funkcje wbudowane Open Geospatial Consortium (OGC) na potrzeby zapytań geograficznych. Aby uzyskać więcej informacji na temat pełnego zestawu wbudowanych funkcji w języku SQL, zobacz temat [funkcje systemowe zapytań w Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funkcje wbudowane w przestrzennym języku SQL

Poniżej znajduje się lista funkcji systemu geoprzestrzennych, które są przydatne do wykonywania zapytań w Azure Cosmos DB:

|**Użycie**|**Opis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Zwraca wyrażenie warunkowe wskazujące, czy pierwszy obiekt GeoJSON (Point, Polygon lub LineString) znajduje się w drugim obiekcie GeoJSON (Point, Polygon lub LineString).|
|ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne)| Zwraca wyrażenie warunkowe wskazujące, czy dwa określone obiekty GeoJSON (Point, Polygon lub LineString) przecinają się.|
|ST_ISVALID| Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.|
| ST_ISVALIDDETAILED| Zwraca wartość JSON, która zawiera wartość logiczną, jeśli określone wyrażenie GEOJSON, wielokąt lub LineString jest prawidłowe. Jeśli jest nieprawidłowa, zwraca przyczynę jako wartość ciągu.|

Funkcje przestrzenne mogą być używane do wykonywania zapytań dotyczących odległości względem danych przestrzennych. Na przykład Oto zapytanie, które zwraca wszystkie dokumenty rodziny, które znajdują się w zakresie 30 km od określonej lokalizacji za pomocą `ST_DISTANCE` wbudowanej funkcji.

**Zapytanie**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Jeśli w zasadach indeksowania dołączysz indeksowanie przestrzenne, "zapytania o odległości" będą efektywnie obsługiwane przez indeks. Aby uzyskać więcej informacji na temat indeksowania przestrzennego, zobacz [indeksowanie geoprzestrzenne](sql-query-geospatial-index.md). Jeśli nie masz indeksu przestrzennego dla określonych ścieżek, zapytanie przeprowadzi skanowanie kontenera.

`ST_WITHIN` może służyć do sprawdzenia, czy punkt leży w obrębie wielokąta. Często wielokąty są używane do reprezentowania granic, takich jak kody pocztowe, granice stanu lub naturalne format. W przypadku dołączenia indeksowania przestrzennego do zasad indeksowania, zapytania "w ramach" będą efektywnie obsługiwane przez indeks.

Argumenty wielokąta w elemencie `ST_WITHIN` mogą zawierać tylko jeden pierścień, czyli, wielokąty nie mogą zawierać otworów.

**Zapytanie**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Wyniki**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Podobnie jak niezgodne typy działają w kwerendzie Azure Cosmos DB, jeśli wartość lokalizacji określona w obu argumentach jest źle sformułowana lub nieprawidłowa, zostanie ona oceniona jako **niezdefiniowana** i oceniony dokument, który ma zostać pominięty z wyników zapytania. Jeśli zapytanie nie zwraca wyników, uruchom polecenie, `ST_ISVALIDDETAILED` Aby debugować dlaczego typ przestrzenny jest nieprawidłowy.
>
>

Azure Cosmos DB obsługuje również wykonywanie zapytań odwrotnych, oznacza to, że można indeksować wielokąty lub linie w Azure Cosmos DB, a następnie wykonywać zapytania dotyczące obszarów zawierających określony punkt. Ten wzorzec jest często używany w logistyki do identyfikowania, na przykład, gdy wózek przechodzi lub opuszcza wybrany obszar.

**Zapytanie**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Wyniki**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` i `ST_ISVALIDDETAILED` można go użyć do sprawdzenia, czy obiekt przestrzenny jest prawidłowy. Na przykład następujące zapytanie sprawdza poprawność punktu z wartością szerokości geograficznej, która jest poza zakresem (-132,8). `ST_ISVALID` zwraca tylko wartość logiczną, a `ST_ISVALIDDETAILED` zwraca wartość logiczną i ciąg zawierający powód, dla którego jest uznawany za nieprawidłowy.

**Zapytanie**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Wyniki**

```json
    [{
      "$1": false
    }]
```

Te funkcje mogą być również używane do walidacji wielokątów. Przykładowo używamy `ST_ISVALIDDETAILED` do sprawdzania poprawności wielokąta, który nie jest zamknięty.

**Zapytanie**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Wyniki**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Zapytania LINQ w zestawie SDK platformy .NET

Zestaw SDK programu SQL .NET jest również dostawcą metod zastępczych `Distance()` i `Within()` do użycia w wyrażeniach LINQ. Dostawca programu SQL LINQ tłumaczy to wywołanie metody na równoważne wywołania funkcji wbudowanych języka SQL (odpowiednio ST_DISTANCE i ST_WITHIN).

Oto przykład zapytania LINQ, które znajduje wszystkie dokumenty w kontenerze usługi Azure Cosmos, którego `location` wartość znajduje się w promieniu 30 km od określonego punktu przy użyciu LINQ.

**Zapytanie LINQ dla odległości**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Podobnie, Oto zapytanie do znajdowania wszystkich dokumentów, które `location` znajdują się w określonym polu/wielokąta.

**Zapytanie LINQ dla w ramach**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zacząć korzystać z obsługi geograficznej w Azure Cosmos DB, możesz dalej:

* Dowiedz się więcej o [Azure Cosmos DB Query](sql-query-getting-started.md)
* Dowiedz się więcej o [danych lokalizacji geograficznej i GEOJSON w Azure Cosmos DB](sql-query-geospatial-intro.md)
* Dowiedz się więcej o [indeksowaniu danych przestrzennych za pomocą Azure Cosmos DB](sql-query-geospatial-index.md)
