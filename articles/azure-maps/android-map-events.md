---
title: Obsługa zdarzeń mapy w mapach systemu Android | Mapy Microsoft Azure
description: Informacje o zdarzeniach, które są wywoływane, gdy użytkownicy pracują z usługą Maps. Wyświetl listę wszystkich obsługiwanych zdarzeń mapy. Zobacz, jak używać Android SDK Azure Maps do obsługi zdarzeń.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681665"
---
# <a name="interact-with-the-map-android-sdk"></a>Korzystanie z mapy (Android SDK)

W tym artykule pokazano, jak używać Menedżera zdarzeń map.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

Mapa zarządza wszystkimi zdarzeniami za pomocą jego `events` właściwości. W poniższej tabeli wymieniono wszystkie obsługiwane zdarzenia mapy.

| Wydarzenie                  | Format programu obsługi zdarzeń | Opis |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Uruchamiany po ostatniej klatce renderowanej przed przejściem do stanu "bezczynności":<ul><li>Brak przejść do aparatu.</li><li>Wszystkie aktualnie żądane kafelki zostały załadowane.</li><li>Wszystkie Animacje zanikania/przejścia zostały ukończone.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod. |
| `OnCameraMoveCanceled` | `()`                 | Uruchamiany, gdy żądanie przeniesienia do aparatu zostało anulowane. |
| `OnCameraMoveStarted`  | `(int reason)`       | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod. `reason`Argument odbiornika zdarzeń zwraca liczbę całkowitą, która zawiera szczegółowe informacje o sposobie zainicjowania przenoszenia aparatu. Poniżej znajduje się lista możliwych przyczyn:<ul><li>1: gest</li><li>2: animacja dla deweloperów</li><li>3: Animacja interfejsu API</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Uruchamiany, gdy mapa zostanie naciśnięty i zwolniony w tym samym punkcie na mapie. |
| `OnFeatureClick`       | `(List<Feature>)`    | Uruchamiany, gdy mapa zostanie naciśnięty i zwolniony w tym samym punkcie w funkcji.  |
| `OnLongClick`          | `(double lat, double lon)` | Uruchamiany, gdy mapa zostanie naciśnięty, zatrzymywana przez chwilę, a następnie wydano w tym samym punkcie na mapie. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Uruchamiany, gdy mapa zostanie naciśnięty, zatrzymywana przez chwilę, a następnie wydaną w tym samym punkcie w funkcji. |
| `OnReady`              | `(AzureMap map)`     | Uruchamiany po pierwszym załadowaniu mapy lub zmianie orientacji aplikacji oraz załadowaniu minimalnych wymaganych zasobów mapy, a mapa jest gotowa do programistycznego działania z. |

Poniższy kod pokazuje, jak dodać `OnClick` `OnFeatureClick` zdarzenia, i `OnCameraMove` do mapy.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [nawigowania](how-to-use-android-map-control-library.md#navigating-the-map) po zasobie, jak korzystać z zdarzeń mapy i wyzwalacza.

## <a name="scope-feature-events-to-layer"></a>Zakresy zdarzeń funkcji do warstwy

Podczas dodawania `OnFeatureClick` zdarzeń lub `OnLongFeatureClick` do mapy, identyfikator warstwy można przesłać jako drugi parametr. Po przekazaniu identyfikatora warstwy zdarzenie będzie wyzwalane tylko wtedy, gdy zdarzenie wystąpi w tej warstwie. Zdarzenia należące do zakresu warstw są obsługiwane przez warstwę symboli, bąbelków, linii i wielokątów.

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
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

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
