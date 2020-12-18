---
title: Wyświetlanie informacji o funkcji w usłudze mapy systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak wyświetlać informacje, gdy użytkownicy współpracują z funkcjami mapy. Użyj Android SDK Azure Maps, aby wyświetlić wyskakujące wiadomości i inne typy komunikatów.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679992"
---
# <a name="display-feature-information"></a>Wyświetlanie informacji o funkcjach

Dane przestrzenne są często reprezentowane przy użyciu punktów, linii i wielokątów. Z tymi danymi często są skojarzone informacje o metadanych. Na przykład punkt może przedstawiać lokalizację restauracji i metadane dotyczące tej restauracji może być nazwą, adresem i typem żywności, którą obsługuje. Te metadane można dodać jako właściwości GEOJSON `Feature` . Poniższy kod tworzy prostą funkcję punktu z `title` właściwością o wartości "Hello World!"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Zapoznaj się z dokumentacją [Tworzenie źródła danych](create-data-source-android-sdk.md) , aby poznać sposoby tworzenia i dodawania danych do mapy.

Gdy użytkownik współdziała z funkcją na mapie, zdarzenia mogą być używane do reagowania na te akcje. Typowym scenariuszem jest wyświetlenie komunikatu o właściwościach metadanych funkcji, z którą korzysta użytkownik. `OnFeatureClick`Zdarzenie jest głównym zdarzeniem używanym do wykrywania, kiedy użytkownik wykorzystał funkcję na mapie. Istnieje również `OnLongFeatureClick` zdarzenie. Podczas dodawania `OnFeatureClick` zdarzenia do mapy może być ograniczone do pojedynczej warstwy przez przekazanie identyfikatora warstwy w celu ograniczenia. Jeśli żaden identyfikator warstwy nie zostanie przekazana, naciśnięcie dowolnej funkcji na mapie, niezależnie od używanej warstwy, spowoduje uruchomienie tego zdarzenia. Poniższy kod tworzy warstwę symboli w celu renderowania danych punktu na mapie, a następnie dodaje `OnFeatureClick` zdarzenie i ogranicza go do tej warstwy symboli.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Wyświetl wyskakujący komunikat

Komunikat wyskakujący to jeden z najprostszych sposobów wyświetlania informacji dla użytkownika i jest dostępny we wszystkich wersjach systemu Android. Nie obsługuje żadnego typu danych wejściowych użytkownika i jest wyświetlana tylko przez krótki czas. Jeśli chcesz szybko poinformować użytkownika o tym, co Ci się podoba, komunikat wyskakujący może być dobrą opcją. Poniższy kod pokazuje, jak można użyć wyskakującego komunikatu ze `OnFeatureClick` zdarzeniem.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animacja wybieranej funkcji i wyświetlany wyskakujący komunikat](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Oprócz wyskakujących komunikatów istnieje wiele innych sposobów prezentowania właściwości metadanych funkcji, takich jak:

- Element [widget Snackbar](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` Podaj uproszczoną opinię na temat operacji. Pokazują one krótki komunikat w dolnej części ekranu na urządzeniu przenośnym i niższy na większych urządzeniach. `Snackbars` występuje nad wszystkimi innymi elementami na ekranie i tylko jeden może być wyświetlany w danym momencie.
- [Okna dialogowe](https://developer.android.com/guide/topics/ui/dialogs) — okno dialogowe to małe okno, które wyświetli komunikat z prośbą o podjęcie decyzji lub wprowadzeniem dodatkowych informacji. Okno dialogowe nie wypełnia ekranu i jest zwykle używane dla zdarzeń modalnych, które wymagają, aby użytkownicy musieli wykonać akcję, zanim będzie można kontynuować.
- Dodaj [fragment](https://developer.android.com/guide/components/fragments) do bieżącego działania.
- Przejdź do innego działania lub widoku.

## <a name="next-steps"></a>Następne kroki

Aby dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia mapy](android-map-events.md)

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
