---
title: Dodawanie warstwy kafelków do Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać warstwy kafelków w wizualizacji Microsoft Azure Maps dla Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261940"
---
# <a name="add-a-tile-layer"></a>Dodawanie warstwy kafelków

Funkcja warstwa kafelków, taka jak funkcja warstwy referencyjnej, umożliwia nakładanie się dodatkowych danych na mapę w celu zapewnienia większego kontekstu. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy bazowej Azure Maps. Jest to doskonały sposób, aby nałożyć duże lub złożone zestawy danych, takie jak dronom lub miliony wierszy danych.

> [!div class="mx-imgBorder"]
> ![Mapa wyświetlająca warstwę bąbelkową powyżej warstwy kafelków pokazująca bieżące dane pogodowe podczerwieni z Azure Maps](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane lub dynamicznie renderowane. Wstępnie renderowane obrazy są przechowywane jak każdy inny obraz na serwerze przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków. Dynamicznie renderowane obrazy używają usługi do ładowania obrazów blisko czasu rzeczywistego. Warstwy kafelków to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, dane wektorowe mogą być również renderowane jako warstwa kafelków.

Pole ograniczenia i zakres powiększania, w którym dostępna jest usługa kafelków, można przesłać jako ustawienia, aby ograniczyć miejsce, w którym są żądane kafelki. Jest to zarówno rozszerzenie wydajności dla usługi wizualizacji, jak i kafelka. Poniżej znajduje się przegląd wszystkich ustawień dostępnych w okienku **Format** , które są dostępne w sekcji **warstwa kafelków** .

| Ustawienie        | Opis   |
|----------------|---------------|
| Url            | Sformatowany adres URL wskazujący na usługę kafelków.  |
| Rozmiar kafelka      | Wartość całkowita określająca zarówno wymiary szerokości, jak i wysokości kafelków.   |
| Granica Północna    | Północne szerokości geograficznej pola ograniczenia, w którym są dostępne kafelki. |
| Granica Południowa    | Południowa szerokość pola ograniczenia, w którym są dostępne kafelki. |
| Granica Wschodnia     | Wschodnia długość pola ograniczenia, w którym są dostępne kafelki.  |
| Granica Zachodnia     | Długość geograficzna pola ograniczenia, w którym są dostępne kafelki.   |
| Przejrzystość   | Przezroczystość warstwy kafelków.   |
| Jest firmy TMS         | Usługa mapy kafelków — Specyfikacja, która odwraca oś współrzędnej Y warstwy kafelków. |
| Minimalne powiększenie       | Dostępne są minimalne kafelki poziomów powiększenia. |
| Maks. powiększenie       | Dostępne są maksymalne kafelki poziomów powiększenia.  |
| Pozycja warstwy | Określa pozycję warstwy względem innych warstw mapy. |

## <a name="tile-url-formatting"></a>Formatowanie adresu URL kafelka

Wizualizacja Azure Maps obsługuje trzy różne konwencje nazewnictwa usługi kafelków:

-   **X, Y, notacja powiększenia** -X to kolumna, Y jest pozycją w wierszu kafelka w siatce kafelków, a w notacji Powiększ wartość na podstawie poziomu powiększenia.
-   **Notacja Quadkey** — łączy x, y i Powiększ informacje w jedną wartość ciągu. Ta wartość ciągu jest unikatowym identyfikatorem dla pojedynczego kafelka.
-   **Pole ograniczenia** — Określ obraz w formacie współrzędnych pola ograniczenia: `{west},{south},{east},{north}` . Ten format jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

ADRES URL kafelka adres URL protokołu HTTPS do szablonu adresu URL kafelka, który używa następujących parametrów:

-   `{x}` -X pozycja kafelka. Również wymagają `{y}` i `{z}` .
-   `{y}` -Y pozycja kafelka. Również wymagają `{x}` i `{z}` .
-   `{z}` — Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}` .
-   `{quadkey}` -Identyfikator kafelka `quadkey` na podstawie konwencji nazewnictwa systemu kafelków mapy Bing.
-   `{bbox-epsg-3857}` -Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.

Poniżej znajduje się przykład sformatowanego adresu URL kafelka dla [usługi kafelków Pogoda](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) w Azure Maps. Zwróć uwagę, że `[subscription-key]` jest to symbol zastępczy klucza subskrypcji Azure Maps.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Aby uzyskać więcej informacji na Azure Maps rozdzielenie systemu, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Następne kroki

Dodaj więcej kontekstu do mapy:

> [!div class="nextstepaction"]
> [Pokazywanie ruchu w czasie rzeczywistym](power-bi-visual-show-real-time-traffic.md)
