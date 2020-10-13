---
title: Format danych GEOJSON dla geofencingu | Mapy Microsoft Azure
description: Dowiedz się więcej o danych Azure Maps geoogrodzenia. Zapoznaj się z tematem jak używać interfejsów API GET geofencingu i po pobraniu pozycji współrzędne względem geofencingu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 27a2fee04afc559a8564aea5e112de07e9c0dcf6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037409"
---
# <a name="geofencing-geojson-data"></a>Geogeofencingu dane GEOJSON

Azure Maps [Get subogrodzenia](/rest/api/maps/spatial/getgeofence) i [przedogrodzeni](/rest/api/maps/spatial/postgeofence) interfejsy API umożliwiają pobranie bliskości współrzędnej względem podanego geoogrodzenia lub zestawu ograniczników. W tym artykule szczegółowo opisano sposób przygotowania danych geoogrodzenia, które mogą być używane w Azure Maps GET i POST API.

Dane dla geofencingu lub zestawu geoogrodzenia są reprezentowane przez `Feature` obiekt i `FeatureCollection` obiekt w `GeoJSON` formacie, który jest zdefiniowany w [rfc7946](https://tools.ietf.org/html/rfc7946). Oprócz tego:

* Typ obiektu GEOJSON może być `Feature` obiektem lub `FeatureCollection` obiektem.
* Obiekt geometry może być typu `Point` , `MultiPoint` ,,, `LineString` , `MultiLineString` `Polygon` `MultiPolygon` , i `GeometryCollection` .
* Wszystkie właściwości funkcji powinny zawierać element `geometryId` , który jest używany do identyfikowania geoogrodzenia.
* Funkcja z `Point` , `MultiPoint` , `LineString` , `MultiLineString` musi zawierać `radius` właściwości. `radius` wartość jest mierzona w licznikach, a `radius` wartości z zakresu od 1 do 10000.
* Funkcja with `polygon` i `multipolygon` Typ geometry nie mają właściwości RADIUS.
* `validityTime` jest opcjonalną właściwością, która umożliwia użytkownikowi ustawianie czasu wygaśnięcia i okresu ważności danych geoogrodzenia. Jeśli nie zostanie określony, dane nigdy nie wygasną i są zawsze poprawne.
* `expiredTime`Jest to data i godzina wygaśnięcia danych dotyczących geofencingu. Jeśli wartość `userTime` w żądaniu jest późniejsza niż ta wartość, odpowiednie dane geoogrodzenia są uznawane za wygasłe i nie są wysyłane do zapytania. W związku z tym, geometryId danych geoogrodzenia zostanie uwzględniony w `expiredGeofenceGeometryId` tablicy w odpowiedzi geołożonej.
* `validityPeriod`Jest to lista okresów czasu ważności geoogrodzenia. Jeśli wartość `userTime` w żądaniu przypada poza okresem ważności, odpowiednie dane geoogrodzenia są uznawane za nieprawidłowe i nie będą wysyłane do nich zapytania. GeometryId tych danych geograficznych jest dołączany do `invalidPeriodGeofenceGeometryId` tablicy w odpowiedzi geoogrodzenia. W poniższej tabeli przedstawiono właściwości elementu validityPeriod.

| Nazwa | Typ | Wymagane  | Opis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime (data/godzina)  | true | Data i godzina rozpoczęcia okresu ważności. |
| endTime   | Datetime (data/godzina)  | true |  Data i godzina zakończenia okresu ważności. |
| Nr wystąpienia | ciąg | fałsz |   Typ cyklu okresu. Wartością może być `Daily` , `Weekly` , `Monthly` , lub `Yearly` . Wartość domyślna to `Daily`.|
| businessDayOnly | Boolean | fałsz |  Wskazuje, czy dane są prawidłowe tylko w dniach roboczych. Wartość domyślna to `false`.|


* Wszystkie wartości współrzędnych są reprezentowane jako [Długość geograficzna, Szerokość geograficzna] zdefiniowana w `WGS84` .
* Dla każdej funkcji, która zawiera `MultiPoint` , `MultiLineString` , `MultiPolygon` lub `GeometryCollection` , właściwości są stosowane do wszystkich elementów. na przykład: wszystkie punkty w programie `MultiPoint` będą używały tego samego promienia do utworzenia wieloogrodzenia okręgu.
* W scenariuszu w kółku punktowym geometria okręgu może być reprezentowana przy użyciu `Point` obiektu geometrii z właściwościami opracowanymi w ramach [rozszerzania GEOJSON geometrie](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Poniżej znajduje się Przykładowa treść żądania dla geoogrodzenia reprezentowana jako geometryczna geometria okręgu `GeoJSON` przy użyciu punktu centralnego i promienia. Prawidłowy okres danych geoogrodzenia zaczyna się od 2018-10-22, 9:00 do 17:00, powtarzany każdego dnia z wyjątkiem weekendu. `expiredTime` wskazuje, że te dane geoogrodzenia będą uznawane za wygasłe, jeśli `userTime` żądanie jest późniejsze niż `2019-01-01` .  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
