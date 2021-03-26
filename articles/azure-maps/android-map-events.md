---
title: Obsługa zdarzeń mapy w mapach systemu Android | Mapy Microsoft Azure
description: Informacje o zdarzeniach, które są wywoływane, gdy użytkownicy pracują z usługą Maps. Wyświetl listę wszystkich obsługiwanych zdarzeń mapy. Zobacz, jak używać Android SDK Azure Maps do obsługi zdarzeń.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ebe61e5956dc0f35794211a336eb7d884ee18d76
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608905"
---
# <a name="interact-with-the-map-android-sdk"></a>Korzystanie z mapy (Android SDK)

W tym artykule pokazano, jak używać Menedżera zdarzeń map.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

Mapa zarządza wszystkimi zdarzeniami za pomocą jego `events` właściwości. W poniższej tabeli wymieniono wszystkie obsługiwane zdarzenia mapy.

| Zdarzenie                  | Format programu obsługi zdarzeń | Opis |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Uruchamiany po ostatniej klatce renderowanej przed przejściem do stanu "bezczynności":<ul><li>Brak przejść do aparatu.</li><li>Wszystkie aktualnie żądane kafelki zostały załadowane.</li><li>Wszystkie Animacje zanikania/przejścia zostały ukończone.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod. |
| `OnCameraMoveCanceled` | `()`                 | Uruchamiany, gdy żądanie przeniesienia do aparatu zostało anulowane. |
| `OnCameraMoveStarted`  | `(int reason)`       | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod. `reason`Argument odbiornika zdarzeń zwraca liczbę całkowitą, która zawiera szczegółowe informacje o sposobie zainicjowania przenoszenia aparatu. Na poniższej liście przedstawiono możliwe przyczyny:<ul><li>1: gest</li><li>2: animacja dla deweloperów</li><li>3: Animacja interfejsu API</li></ul>   |
| `OnClick`              | `(double lat, double lon): boolean` | Uruchamiany, gdy mapa zostanie naciśnięty i zwolniony w tym samym punkcie na mapie. Ta procedura obsługi zdarzeń zwraca wartość logiczną wskazującą, czy zdarzenie powinno być używane lub przenoszone do innych detektorów zdarzeń. |
| `OnFeatureClick`       | `(List<Feature>): boolean`    | Uruchamiany, gdy mapa zostanie naciśnięty i zwolniony w tym samym punkcie w funkcji. Ta procedura obsługi zdarzeń zwraca wartość logiczną wskazującą, czy zdarzenie powinno być używane lub przenoszone do innych detektorów zdarzeń. |
| `OnLayerAdded` | `(Layer layer)` | Uruchamiany, gdy warstwa zostanie dodana do mapy. |
| `OnLayerRemoved` | `(Layer layer)` | Uruchamiany, gdy warstwa zostanie usunięta z mapy. |
| `OnLoaded` | `()` | Uruchamiany natychmiast po pobraniu wszystkich niezbędnych zasobów i wykonaniu pierwszego wizualnego renderowania mapy. |
| `OnLongClick`          | `(double lat, double lon): boolean` | Uruchamiany, gdy mapa zostanie naciśnięty, zatrzymywana przez chwilę, a następnie wydano w tym samym punkcie na mapie. Ta procedura obsługi zdarzeń zwraca wartość logiczną wskazującą, czy zdarzenie powinno być używane lub przenoszone do innych detektorów zdarzeń. |
| `OnLongFeatureClick `  | `(List<Feature>): boolean`    | Uruchamiany, gdy mapa zostanie naciśnięty, zatrzymywana przez chwilę, a następnie wydaną w tym samym punkcie w funkcji. Ta procedura obsługi zdarzeń zwraca wartość logiczną wskazującą, czy zdarzenie powinno być używane lub przenoszone do innych detektorów zdarzeń. |
| `OnReady`              | `(AzureMap map)`     | Uruchamiany po pierwszym załadowaniu mapy lub zmianie orientacji aplikacji oraz załadowaniu minimalnych wymaganych zasobów mapy, a mapa jest gotowa do programistycznego działania z. |
| `OnSourceAdded` | `(Source source)` | Uruchamiany po `DataSource` `VectorTileSource` dodaniu lub do mapy. |
| `OnSourceRemoved` | `(Source source)` | Uruchamiany po `DataSource` `VectorTileSource` usunięciu lub z mapy. |
| `OnStyleChange` | `()` | Uruchamiany, gdy styl mapy zostanie załadowany lub zmieniony. |

Poniższy kod pokazuje, jak dodać `OnClick` `OnFeatureClick` zdarzenia, i `OnCameraMove` do mapy.

::: zone pivot="programming-language-java-android"

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.events.add(OnClick { lat: Double, lon: Double -> 
    //Map clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnFeatureClick { features: List<Feature?>? -> 
    //Feature clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return false
})

map.events.add(OnCameraMove {
    //Map camera moved.
})
```

::: zone-end

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [nawigowania](how-to-use-android-map-control-library.md#navigating-the-map) po zasobie, jak korzystać z zdarzeń mapy i wyzwalacza.

## <a name="scope-feature-events-to-layer"></a>Zakresy zdarzeń funkcji do warstwy

Podczas dodawania `OnFeatureClick` zdarzeń lub `OnLongFeatureClick` do mapy, wystąpienie warstwy lub identyfikator warstwy można przesłać jako drugi parametr. Gdy przejdziesz do warstwy, zdarzenie będzie wyzwalane tylko wtedy, gdy zdarzenie wystąpi w tej warstwie. Zdarzenia objęte zakresem warstw są obsługiwane przez warstwę symboli, bąbelków, linii i wielokątów.

::: zone pivot="programming-language-java-android"

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.

    //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
    return true;
}, layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source.
val source = DataSource()
map.sources.add(source)

//Add data to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a layer and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnFeatureClick { features: List<Feature?>? -> 
        //One or more features clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add(
    OnLongFeatureClick { features: List<Feature?>? -> 
         //One or more features long clicked.

        //Return true indicating if event should be consumed and not passed further to other listeners registered afterwards, false otherwise.
        return false
    },
    layer
)
```

::: zone-end

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Nawigowanie po mapie](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
