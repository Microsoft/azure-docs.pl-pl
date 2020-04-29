---
title: Format danych GEOJSON dla geofencingu | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak przygotować dane z geoogrodzenia, które mogą być używane w Microsoft Azure Maps Pobierz i umieść interfejs API z geoogrodzeniem.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335617"
---
# <a name="geofencing-geojson-data"></a>Geogeofencingu dane GEOJSON

Azure Maps [Get subogrodzenia](/rest/api/maps/spatial/getgeofence) i [przedogrodzeni](/rest/api/maps/spatial/postgeofence) interfejsy API umożliwiają pobranie bliskości współrzędnej względem podanego geoogrodzenia lub zestawu ograniczników. W tym artykule szczegółowo opisano sposób przygotowania danych geoogrodzenia, które mogą być używane w Azure Maps GET i POST API.

Dane dla geofencingu lub zestawu geoogrodzenia `Feature` są reprezentowane przez obiekt i `FeatureCollection` obiekt w `GeoJSON` formacie, który jest zdefiniowany w [rfc7946](https://tools.ietf.org/html/rfc7946). Oprócz tego:

* Typ obiektu GEOJSON może być `Feature` obiektem lub `FeatureCollection` obiektem.
* `Point`Obiekt geometry może być typu, `MultiPoint`, `LineString`, `MultiLineString` `Polygon`,, `MultiPolygon`, i. `GeometryCollection`
* Wszystkie właściwości funkcji powinny zawierać element `geometryId`, który jest używany do identyfikowania geoogrodzenia.
* Funkcja z `Point`, `MultiPoint`, `LineString`, `MultiLineString` musi zawierać `radius` właściwości. `radius`wartość jest mierzona w licznikach, `radius` a wartości z zakresu od 1 do 10000.
* Funkcja with `polygon` i `multipolygon` typ geometry nie mają właściwości RADIUS.
* `validityTime`jest opcjonalną właściwością, która umożliwia użytkownikowi ustawianie czasu wygaśnięcia i okresu ważności danych geoogrodzenia. Jeśli nie zostanie określony, dane nigdy nie wygasną i są zawsze poprawne.
* `expiredTime` Jest to data i godzina wygaśnięcia danych dotyczących geofencingu. Jeśli wartość `userTime` w żądaniu jest późniejsza niż ta wartość, odpowiednie dane geoogrodzenia są uznawane za wygasłe i nie są wysyłane do zapytania. W związku z tym, geometryId danych geoogrodzenia zostanie uwzględniony w `expiredGeofenceGeometryId` tablicy w odpowiedzi geołożonej.
* `validityPeriod` Jest to lista okresów czasu ważności geoogrodzenia. Jeśli wartość `userTime` w żądaniu przypada poza okresem ważności, odpowiednie dane geoogrodzenia są uznawane za nieprawidłowe i nie będą wysyłane do nich zapytania. GeometryId tych danych geograficznych jest dołączany `invalidPeriodGeofenceGeometryId` do tablicy w odpowiedzi geoogrodzenia. W poniższej tabeli przedstawiono właściwości elementu validityPeriod.

| Nazwa | Typ | Wymagany  | Opis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime (data/godzina)  | true | Data i godzina rozpoczęcia okresu ważności. |
| endTime   | Datetime (data/godzina)  | true |  Data i godzina zakończenia okresu ważności. |
| Nr wystąpienia | ciąg | fałsz |   Typ cyklu okresu. `Daily`Wartością może być `Weekly`,, `Monthly`, lub. `Yearly` Wartość domyślna to `Daily`.|
| businessDayOnly | Boolean | fałsz |  Wskazuje, czy dane są prawidłowe tylko w dniach roboczych. Wartość domyślna to `false`.|


* Wszystkie wartości współrzędnych są reprezentowane jako [Długość geograficzna, `WGS84`Szerokość geograficzna] zdefiniowana w.
* Dla każdej funkcji, która zawiera `MultiPoint`, `MultiLineString`, `MultiPolygon` lub `GeometryCollection`, właściwości są stosowane do wszystkich elementów. na przykład: wszystkie punkty w programie `MultiPoint` będą używały tego samego promienia do utworzenia wieloogrodzenia okręgu.
* W scenariuszu w kółku punktowym geometria okręgu może być `Point` reprezentowana przy użyciu obiektu geometrii z właściwościami opracowanymi w ramach [rozszerzania GEOJSON geometrie](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Poniżej znajduje się Przykładowa treść żądania dla geoogrodzenia reprezentowana jako geometryczna geometria okręgu `GeoJSON` przy użyciu punktu centralnego i promienia. Prawidłowy okres danych geoogrodzenia zaczyna się od 2018-10-22, 9:00 do 17:00, powtarzany każdego dnia z wyjątkiem weekendu. `expiredTime`wskazuje, że te dane geoogrodzenia będą uznawane `userTime` za wygasłe, jeśli żądanie `2019-01-01`jest późniejsze niż.  

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
