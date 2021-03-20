---
title: Dodaj warstwę referencyjną do Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać warstwy referencyjnej w wizualizacji Microsoft Azure Maps dla Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86261947"
---
# <a name="add-a-reference-layer"></a>Dodawanie warstwy odwołania

Funkcja warstwa referencyjna umożliwia przekazanie dodatkowego przestrzennego zestawu danych do wizualizacji i nałożonej na mapę w celu zapewnienia kontekstu dodawania. Ten zestaw danych jest hostowany przez Power BI i musi być [plikiem GEOJSON](https://wikipedia.org/wiki/GeoJSON) z `.json` rozszerzeniem lub `.geojson` .

Aby dodać plik **GEOJSON** jako warstwę referencyjną, przejdź do okienka **Formatowanie** , rozwiń sekcję **warstwa referencyjna** i naciśnij przycisk **+ Dodaj plik lokalny** .

Po dodaniu pliku GEOJSON do warstwy referencyjnej, nazwa pliku pojawi się zamiast przycisku **+ Dodaj plik lokalny** z **symbolem X** obok niego. Naciśnij przycisk **X** , aby usunąć dane z wizualizacji, a następnie usuń plik GEOJSON z Power BI.

Następująca mapa jest wyświetlana [2016 spisu dla Kolorado](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson), Kolorowanie według populacji.

> [!div class="mx-imgBorder"]
> ![Mapa wyświetlająca Kolorado spisu na 2016, Kolorowanie według populacji jako warstwy odniesienia](media/power-bi-visual/reference-layer-CO-census-tract.png)

Poniżej znajdują się wszystkie ustawienia w okienku **Format** , które są dostępne w sekcji **warstwa referencyjna** .

| Ustawienie              | Opis   |
|----------------------|---------------|
| Dane referencyjne warstwy | Plik danych GEOJSON, który ma zostać przekazany do wizualizacji jako dodatkowa warstwa na mapie. Przycisk **+ Dodaj plik lokalny** otwiera okno dialogowe pliku, za pomocą którego użytkownik może wybrać plik GEOJSON, który ma `.json` rozszerzenie lub `.geojson` . |

> [!NOTE]
> W tej wersji zapoznawczej wizualizacji Azure Maps warstwa referencyjna będzie ładować tylko pierwsze funkcje kształtu 5 000 do mapy. Ten limit zostanie zwiększony w przyszłej aktualizacji.

## <a name="styling-data-in-a-reference-layer"></a>Style danych w warstwie referencyjnej

Właściwości można dodać do każdej funkcji w pliku GEOJSON, aby dostosować sposób jej stylu na mapie. Ta funkcja używa funkcji prostego warstwy danych w zestawie SDK sieci Web Azure Maps. Aby uzyskać więcej informacji, zobacz ten dokument w obszarze [obsługiwane właściwości stylu](spatial-io-add-simple-data-layer.md#default-supported-style-properties). Niestandardowe obrazy ikon nie są obsługiwane w wizualizacji Azure Mapsej jako środek zabezpieczeń.

Poniżej znajduje się przykład funkcji punktu GEOJSON, która ustawia kolor wyświetlania na czerwony.

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>Następne kroki

Dodaj więcej kontekstu do mapy:

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [Pokazywanie ruchu w czasie rzeczywistym](power-bi-visual-show-real-time-traffic.md)
