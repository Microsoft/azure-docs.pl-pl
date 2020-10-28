---
title: Dodaj warstwę bąbelkową do Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać warstwy bąbelki w wizualizacji Microsoft Azure Maps dla Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896229"
---
# <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

**Warstwa bąbelków** renderuje dane lokalizacji jako skalowane kółka na mapie.

> [!div class="mx-imgBorder"]
> ![Mapa wyświetlająca dane punktu przy użyciu warstwy bąbelkowej](media/power-bi-visual/bubble-layer-with-legend-color.png)

Początkowo wszystkie bąbelki mają ten sam kolor wypełnienia. Jeśli pole zostanie przesłane do zasobnika **Legenda** okienka **pola** , bąbelki będą miały kolor na podstawie kategoryzacji. Obramowanie bąbelków jest białe, ale można je zmienić na nowy kolor lub przez włączenie opcji kontur dużego kontrastu. Opcja **konspektu o dużym kontraście** dynamicznie przypisuje kolor konturu o wysokim kontraście koloru wypełnienia. Pozwala to zagwarantować, że bąbelki są wyraźnie widoczne niezależnie od stylu mapy. Poniżej znajdują się podstawowe ustawienia w okienku **Format** , które są dostępne w sekcji **warstwy bąbelkowej** .

| Ustawienie               | Opis    |
|-----------------------|----------------|
| Rozmiar                  | Rozmiar każdego bąbelka. Ta opcja jest ukryta, gdy pole jest przesyłane do zasobnika **rozmiar** okienka **pola** . Dodatkowe opcje zostaną wyświetlone, jak opisano w temacie [skalowanie rozmiaru bąbelków](#bubble-size-scaling) w dalszej części tego artykułu. |
| Kolor wypełnienia            | Kolor każdego bąbelka. Ta opcja jest ukryta, gdy pole jest przesyłane do zasobnika **Legenda** okienka **pola** , a w okienku **Format** zostanie wyświetlona sekcja różne **kolory danych** . |
| Wypełnij przezroczystość     | Przezroczystość każdego bąbelka. |
| Kontur o dużym kontraście | Sprawia, że kolor konturu różni się od koloru wypełnienia dla lepszego ułatwienia dostępu przy użyciu wariantu koloru wypełnienia o wysokim kontraście. |
| Kolor konturu         | Kolor, który określa bąbelki. Ta opcja jest ukryta, gdy opcja **konturu o dużym kontraście** jest włączona. |
| Przezroczystość konturu  | Przezroczystość konspektu. |
| Szerokość konturu         | Szerokość konturu w pikselach. |
| Rozmazan                  | Ilość rozmycia zastosowana do konturu. Wartość 1 powoduje rozmycie bąbelków, tak aby tylko punkt środkowy nie miał przezroczystości. Wartość 0 powoduje zastosowanie dowolnego efektu rozmycia. |
| Wyrównanie skoku       | Określa, jak bąbelki wyglądają, gdy mapa jest poprzednia. <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Okienka ekranu — bąbelki pojawiają się na krawędzi mapy względem okienka ekranu. (wartość domyślna)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Bąbelki mapy są renderowane płaskie na powierzchni mapy. |
| Skalowanie powiększenia            | Ilość bąbelków powinna być skalowana względem poziomu powiększenia. Skalowanie powiększenia jednego oznacza brak skalowania. Duże wartości sprawiają, że bąbelki będą mniejsze, gdy są powiększone i większe, gdy są powiększone. Pozwala to zmniejszyć czytelność mapy w przypadku powiększania, ale zapewnia, że punkty są coraz bardziej zwiększane. Wartość 1 nie ma zastosowania w żadnym skalowaniu. |
| Minimalne powiększenie              | Dostępne są minimalne kafelki poziomów powiększenia. |
| Maks. powiększenie              | Dostępne są maksymalne kafelki poziomów powiększenia. |
| Pozycja warstwy        | Określa pozycję warstwy względem innych warstw mapy. |

## <a name="bubble-size-scaling"></a>Skalowanie rozmiaru bąbelków

Jeśli pole zostanie przesłane do zasobnika **rozmiar** okienka **pola** , bąbelki będą skalowane względem wartości miary poszczególnych punktów danych. Opcja **rozmiar** w sekcji **warstwy bąbelkowej** w okienku **Format** znika, gdy pole zostanie przesłane do zasobnika **rozmiaru** , ponieważ bąbelki będą skalowane do wartości minimalnej i maksymalnej. Poniższe opcje zostaną wyświetlone w sekcji **warstwa bąbelkowa** w okienku **Format** , gdy dla zasobnika o **rozmiarze** określono pole.

| Ustawienie             | Opis  |
|---------------------|--------------|
| Minimalny rozmiar            | Minimalny rozmiar bąbelków podczas skalowania danych.|
| Maksymalny rozmiar            | Maksymalny rozmiar bąbelka podczas skalowania danych.|
| Metoda skalowania rozmiaru | Algorytm skalowania używany do określania względnego rozmiaru bąbelka.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Liniowy zakres danych wejściowych jest liniowo mapowany na minimalny i maksymalny rozmiar. (wartość domyślna)<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Zakres rejestrowania danych wejściowych logarithmically mapowany na minimalny i maksymalny rozmiar.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier-Określ wartości x1, Y1, X2, Y2 dla krzywej Cubic-Bezier, aby utworzyć niestandardową metodę skalowania. |

Gdy ustawiona jest **Metoda skalowania rozmiaru** do **dziennika** , dostępne są następujące opcje.

| Ustawienie   | Opis      |
|-----------|------------------|
| Skalowanie dziennika | Skala logarytmiczna, która ma zostać zastosowana podczas obliczania rozmiaru bąbelków. |

Gdy **Metoda skalowania rozmiaru** jest ustawiona na wartość **Beziera** , w celu dostosowania krzywej skalowania zostaną udostępnione następujące opcje.

| Ustawienie | Opis                           |
|---------|---------------------------------------|
| X1      | X1 parametr zakrzywionej krzywej Beziera. |
| Y1      | Parametr X2 krzywej Beziera. |
| X2      | Parametr Y1 zakrzywionej krzywej Beziera. |
| Y2      | Parametr Y2 krzywej Beziera. |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) ma przydatne narzędzie do tworzenia parametrów dla krzywych Cubic-Bezier.

## <a name="next-steps"></a>Następne kroki

Zmień sposób wyświetlania danych na mapie:

> [!div class="nextstepaction"]
> [Dodawanie warstwy wykresu słupkowego](power-bi-visual-add-bar-chart-layer.md)

Dodaj więcej kontekstu do mapy:

> [!div class="nextstepaction"]
> [Dodawanie warstwy odwołania](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Pokazywanie ruchu w czasie rzeczywistym](power-bi-visual-show-real-time-traffic.md)

Dostosuj wizualizację:

> [!div class="nextstepaction"]
> [Porady i wskazówki dotyczące formatowania kolorów w usłudze Power BI](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Dostosowywanie tytułów, legend i tła wizualizacji](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)