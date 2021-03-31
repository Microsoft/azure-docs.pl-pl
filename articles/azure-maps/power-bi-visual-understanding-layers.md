---
title: Informacje o warstwach w Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje o różnych warstwach dostępnych w wizualizacji Microsoft Azure Maps dla Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261847"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>Informacje o warstwach w Azure Maps Power BI wizualizacji

Istnieją dwa typy warstw dostępne w wizualizacji Azure Maps. Pierwszy typ koncentruje się na renderowaniu danych, które są przesyłane do okienka **pola** wizualizacji i składają się z poniższych warstw, wywołując te warstwy renderowania danych.

:::row:::
    :::column span="":::
        **Warstwa bąbelkowa**

        Renderuje punkty jako skalowane okręgi na mapie.

        ![Warstwa bąbelków na mapie](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Warstwa wykresu słupkowego**

        Renderuje punkty jako słupki 3W na mapie.
        
        ![Warstwa wykresu słupkowego na mapie](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

Drugi typ warstwy łączy dodawanie zewnętrznych źródeł danych do mapowania, aby zapewnić więcej kontekstu i składa się z następujących warstw.

:::row:::
    :::column span="":::
        **Warstwa referencyjna**

        Nałóż przekazany plik GEOJSON na mapie.

        ![Warstwa referencyjna na mapie](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Warstwa kafelków**

        Nałóż warstwę kafelków niestandardowych na mapie.
        
        ![Warstwa kafelków na mapie](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **Warstwa ruchu**

        Nałóż informacje o ruchu w czasie rzeczywistym na mapie.
        
        ![Warstwa ruchu na mapie](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

Wszystkie warstwy renderowania danych, a także **warstwa kafelków**, mają opcje dla minimalnych i maksymalnych poziomów powiększenia, które są używane do określania zakresu poziomu powiększenia te warstwy powinny być wyświetlane w. Pozwala to na użycie jednego typu warstwy renderowania na jednym poziomie powiększenia i przejście do innej warstwy renderowania na innym poziomie powiększenia.

Te warstwy mają również opcję pozycjonowania względem innych warstw na mapie. Gdy używane są wiele warstw renderowania danych, kolejność, w jakiej są dodawane do mapy, określa kolejność warstw względnych, gdy mają tę samą wartość **pozycji warstwy** .

## <a name="general-layer-settings"></a>Ogólne ustawienia warstwy

Sekcja ogólna warstwa okienka **Format** to typowe ustawienia, które są stosowane do warstw, które są połączone z zestawem danych Power BI w okienku **pola** (warstwa bąbelków, wykres słupkowy).

| Ustawienie     | Opis   |
|-------------|---------------|
| Niezaznaczona przezroczystość | Przezroczystość kształtów, które nie są zaznaczone, gdy zaznaczony jest co najmniej jeden kształt.  |
| Pokaż zera              | Określa, czy na mapie ma być pokazywana liczba punktów mających wartość zero równą zeru. |
| Pokaż wartości ujemne          | Określa, czy należy wykreślić wartość bezwzględną wartości rozmiaru ujemnego.   |
| Minimalna wartość danych          | Minimalna wartość danych wejściowych do skalowania względem. Dobre dla wycinków odstających.  |
| Maksymalna wartość danych          | Maksymalna wartość danych wejściowych do skalowania względem. Dobre dla wycinków odstających.  |

## <a name="next-steps"></a>Następne kroki

Zmień sposób wyświetlania danych na mapie:

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wykresu słupkowego](power-bi-visual-add-bar-chart-layer.md)

Dodaj więcej kontekstu do mapy:

> [!div class="nextstepaction"]
> [Dodawanie warstwy odwołania](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Pokazywanie ruchu w czasie rzeczywistym](power-bi-visual-show-real-time-traffic.md)
