---
title: Dodaj warstwę wykresu słupkowego do Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać warstwy wykresu słupkowego w wizualizacji Microsoft Azure Maps dla Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 136676b46df6e32f98dca99fccba19d4aa369dbe
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896280"
---
# <a name="add-a-bar-chart-layer"></a>Dodawanie warstwy wykresu słupkowego

**Warstwa wykresu słupkowego** jest przydatna do pobierania danych do następnego wymiaru przez umożliwienie wizualizacji danych lokalizacji jako słupków 3W lub cylindrów na mapie. Podobnie jak w przypadku warstwy bąbelków, wykres słupkowy później może łatwo wizualizować dwie metryki w tym samym czasie, używając koloru i wysokości względnej. Aby słupki miały wysokość, należy dodać miarę do zasobnika **rozmiar** okienka **pola** . Jeśli nie podano miary, słupki bez wysokości są wyświetlane jako płaskie kwadraty lub okręgi w zależności od opcji **kształtu paska** .

> [!div class="mx-imgBorder"]
> ![Mapa wyświetlająca dane punktu przy użyciu warstwy wykresu słupkowego](media/power-bi-visual/bar-chart-layer-styled.png)

Użytkownicy mogą pochylać i obrócić mapę, aby wyświetlić dane z różnych perspektyw. Mapę można obchylić lub obchylać przy użyciu jednej z poniższych metod.

-   Włącz opcję **kontrolki nawigacji** w **ustawieniach mapy** okienka **Formatowanie** . Spowoduje to dodanie przycisku, aby pochylić mapę.
-   Naciśnij prawy przycisk myszy w dół i przeciągnij myszą w górę lub w dół.
-   Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i przeciągnij je w górę lub w dół.
-   Korzystając z mapy, przytrzymaj klawisz **SHIFT** , a następnie naciśnij klawisze strzałek w **górę** lub **w dół** .

Mapę można obrócić przy użyciu jednej z poniższych metod.

-   Włącz opcję **kontrolki nawigacji** w **ustawieniach mapy** okienka **Formatowanie** . Spowoduje to dodanie przycisku, aby obrócić mapę.
-   Naciśnij prawy przycisk myszy w dół i przeciągnij wskaźnik myszy w lewo lub w prawo.
-   Korzystając z ekranu dotykowego, dotknij mapy dwoma palcami i Obróć.
-   Przy użyciu mapy, przytrzymaj klawisz **SHIFT** , a następnie naciśnij klawisz strzałki w **lewo** lub w **prawo** .

Poniżej znajdują się wszystkie ustawienia w okienku **Format** , które są dostępne w sekcji **warstwa wykresu słupkowego** .

| Ustawienie              | Opis      |
|----------------------|------------------|
| Kształt paska            | Kształt paska 3W.<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Pole — słupki renderowane jako prostokątne pola.<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cylinder — słupki renderowane jako cylindry. |
| Height               | Wysokość każdego paska. Jeśli pole zostanie przesłane do zasobnika **rozmiar** okienka **pola** , słupki będą skalowane względem tej wartości wysokości. |
| Skaluj wysokość przy powiększeniu | Określa, czy wysokość słupków ma być skalowana względem poziomu powiększenia. |
| Width                | Szerokość każdego paska.  |
| Skaluj szerokość przy powiększeniu  | Określa, czy szerokość słupków ma być skalowana względem poziomu powiększenia.  |
| Kolor wypełnienia           | Kolor każdego paska. Ta opcja jest ukryta, gdy pole jest przesyłane do zasobnika **Legenda** okienka **pola** , a w okienku **Format** zostanie wyświetlona sekcja różne **kolory danych** . |
| Przejrzystość         | Przezroczystość każdego paska. |
| Minimalne powiększenie             | Dostępne są minimalne kafelki poziomów powiększenia. |
| Maks. powiększenie             | Dostępne są maksymalne kafelki poziomów powiększenia. |
| Pozycja warstwy       | Określa pozycję warstwy względem innych warstw mapy. |

> [!NOTE]
> Jeśli paski mają niewielką wartość szerokości, a **Szerokość skalowania przy powiększeniu** jest wyłączona, mogą one zniknąć przy pomniejszeniu ilości, ponieważ ich renderowane wartości byłyby mniejsze niż rozmiar piksela. Jednak po włączeniu opcji **skalowanie na poziomie powiększenia** dodatkowe obliczenia są wykonywane, gdy zmieni się poziom powiększenia, co może mieć wpływ na wydajność dużych zestawów danych.

## <a name="next-steps"></a>Następne kroki

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