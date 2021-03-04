---
title: Dodawanie formantów do mapy systemu Android | Mapy Microsoft Azure
description: Jak dodać kontrolkę powiększenia, kontrolkę gęstość, obrócić formant i selektor stylu do mapy w Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8224192ed0d13af2ff6ac60aac5aa928589ff01a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054887"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Dodawanie formantów do mapy (Android SDK)

W tym artykule opisano sposób dodawania formantów interfejsu użytkownika do mapy.

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

Kontrolka powiększenia dodaje przyciski umożliwiające powiększanie mapy w i na zewnątrz. Poniższy przykład kodu tworzy wystąpienie `ZoomControl` klasy i dodaje ją do mapy.

```java
map.controls.add(new ZoomControl());
```

Zrzut ekranu poniżej jest formantem powiększenia załadowanym na mapie.

![Kontrolka powiększenia dodana do mapy](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Dodaj kontrolkę gęstość

Kontrolka gęstość dodaje przyciski umożliwiające przechylenie skoku na mapę względem zakresu. Poniższy przykład kodu tworzy wystąpienie `PitchControl` klasy i dodaje ją do mapy.

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

Zrzut ekranu poniżej przedstawia kontrolkę gęstość załadowana na mapie.

![Kontrolka skoku dodana do mapy](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Dodaj kontrolkę kompas

Kontrolka kompas dodaje przycisk służący do obracania mapy. Poniższy przykład kodu tworzy wystąpienie `CompassControl` klasy i dodaje ją do mapy.

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

Zrzut ekranu poniżej znajduje się w kontrolce kompasu załadowanej na mapie.

![Kontrolka kompas dodana do mapy](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Dodaj kontrolę ruchu

Kontrolka ruchu dodaje przycisk służący do przełączania widoczności danych ruchu na mapie. Poniższy przykład kodu tworzy wystąpienie `TrafficControl` klasy i dodaje ją do mapy.

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

Zrzut ekranu poniżej dotyczy kontroli ruchu załadowanej na mapie.

![Kontrolka ruchu dodana do mapy](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi kontrolkami

Wiele kontrolek można umieścić w tablicy i dodać do mapy wszystkie jednocześnie i umieścić w tym samym obszarze mapy, aby uprościć tworzenie. Poniższe podejście dodaje standardowe kontrolki nawigacji do mapy.

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
