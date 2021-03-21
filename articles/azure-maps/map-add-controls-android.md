---
title: Dodawanie formantów do mapy systemu Android | Mapy Microsoft Azure
description: Jak dodać kontrolkę powiększenia, kontrolkę gęstość, obrócić formant i selektor stylu do mapy w Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100223"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Dodawanie formantów do mapy (Android SDK)

W tym artykule opisano sposób dodawania formantów interfejsu użytkownika do mapy.

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

Kontrolka powiększenia dodaje przyciski umożliwiające powiększanie mapy w i na zewnątrz. Poniższy przykład kodu tworzy wystąpienie `ZoomControl` klasy i dodaje ją do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

Zrzut ekranu poniżej jest formantem powiększenia załadowanym na mapie.

![Kontrolka powiększenia dodana do mapy](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Dodaj kontrolkę gęstość

Kontrolka gęstość dodaje przyciski umożliwiające przechylenie skoku na mapę względem zakresu. Poniższy przykład kodu tworzy wystąpienie `PitchControl` klasy i dodaje ją do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

Zrzut ekranu poniżej przedstawia kontrolkę gęstość załadowana na mapie.

![Kontrolka skoku dodana do mapy](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Dodaj kontrolkę kompas

Kontrolka kompas dodaje przycisk służący do obracania mapy. Poniższy przykład kodu tworzy wystąpienie `CompassControl` klasy i dodaje ją do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

Zrzut ekranu poniżej znajduje się w kontrolce kompasu załadowanej na mapie.

![Kontrolka kompas dodana do mapy](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Dodaj kontrolę ruchu

Kontrolka ruchu dodaje przycisk służący do przełączania widoczności danych ruchu na mapie. Poniższy przykład kodu tworzy wystąpienie `TrafficControl` klasy i dodaje ją do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

Zrzut ekranu poniżej dotyczy kontroli ruchu załadowanej na mapie.

![Kontrolka ruchu dodana do mapy](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi kontrolkami

Wiele kontrolek można umieścić w tablicy i dodać do mapy wszystkie jednocześnie i umieścić w tym samym obszarze mapy, aby uprościć tworzenie. Poniższe podejście dodaje standardowe kontrolki nawigacji do mapy.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

Zrzut ekranu poniżej przedstawia wszystkie formanty załadowane na mapie. Należy zauważyć, że kolejność, w jakiej są dodawane do mapy, jest kolejnością ich wyświetlania.

![Wszystkie kontrolki dodane do mapy](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
