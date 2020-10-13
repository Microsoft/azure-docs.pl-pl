---
title: Wprowadzenie do Azure Maps Power BI wizualizacji | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak używać wizualizacji Microsoft Azure Maps Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: aecda5ed9b87ba2345a52923a574919d6a08594f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362675"
---
# <a name="getting-started-with-the-azure-maps-power-bi-visual"></a>Wprowadzenie do Azure Maps Power BI wizualizacji

<Token>**dotyczy:** ![ Zielony znacznik wyboru. ](media/power-bi-visual/yes.png) Usługa Power BI dla ***odbiorców*** ![ zielony znacznik wyboru. ](media/power-bi-visual/yes.png) Usługa Power BI dla projektantów & ![ zielony znacznik wyboru dla deweloperów. ](media/power-bi-visual/yes.png) Power BI Desktop ![ X wskazująca nie. ](media/power-bi-visual/no.png) Wymaga licencji Pro lub Premium </Token>

W tym artykule pokazano, jak używać wizualizacji Microsoft Azure Maps Power BI.

> [!NOTE]
> Tę wizualizację można utworzyć i wyświetlić zarówno w Power BI Desktop, jak i usługa Power BI. Kroki i ilustracje w tym artykule dotyczą programu Power BI Desktop.

Wizualizacja Azure Maps dla Power BI oferuje bogaty zestaw danych graficznych dla danych przestrzennych na mapie. Szacuje się, że ponad 80% danych gospodarczych ma kontekst lokalizacji. Wizualizacja Azure Maps może służyć do uzyskania wglądu w sposób odnoszący się do tego kontekstu lokalizacji i ma wpływ na dane biznesowe.

![Power BI pulpicie z wizualizacją Azure Maps wyświetlającą dane biznesowe](media/power-bi-visual/azure-maps-visual-hero.png)

## <a name="what-is-sent-to-azure"></a>Co jest wysyłane do platformy Azure?

Wizualizacja Azure Maps nawiązuje połączenie z usługą w chmurze hostowaną na platformie Azure w celu pobrania danych lokalizacji, takich jak obrazy mapy i współrzędne, które są używane do tworzenia wizualizacji mapy. 

-   Szczegóły dotyczące obszaru, w którym jest ukierunkowane mapowanie, są wysyłane do platformy Azure w celu pobrania obrazów potrzebnych do renderowania kanwy mapy (nazywanej również kafelkami mapy). 
-   Dane w zasobnikach lokalizacja, Szerokość geograficzna i długości geograficznej mogą być wysyłane do platformy Azure w celu pobrania współrzędnych mapy (proces nazywany geokodowaniem). 
-   Dane telemetryczne mogą być zbierane na kondycji wizualizacji (np. raporty o awarii), jeśli opcja telemetrii w Power BI jest włączona.

Poza opisanymi powyżej scenariuszami do serwerów Azure Maps nie są wysyłane żadne inne dane na mapie. Wszystkie operacje renderowania danych są wykonywane lokalnie w ramach klienta.

Może być konieczne zaktualizowanie zapory, aby zezwolić na dostęp do Azure Maps platformy przy użyciu poniższego adresu URL.

> `https://atlas.microsoft.com`

Aby dowiedzieć się więcej, informacje o ochronie prywatności i warunkach użytkowania związanych z wizualizacją Azure Maps można znaleźć w sekcji [Microsoft Azure informacje prawne](https://azure.microsoft.com/support/legal/).

## <a name="preview-behavior-and-requirements"></a>Działanie i wymagania wersji zapoznawczej

Istnieje kilka kwestii i wymagań dotyczących tej wersji zapoznawczej **Azure Maps**:

-   Wizualizacja **Azure Maps** jest w wersji zapoznawczej i musi być włączona w Power BI Desktop. Aby włączyć **Azure Maps**, wybierz **File** &gt; **Opcje plików i ustawienia** &gt; **Opcje** &gt; w **wersji zapoznawczej**, a następnie zaznacz pole wyboru **Wizualizacja Azure Maps** . Jeśli po wykonaniu tego działania Wizualizacja Azure Maps jest niedostępna, prawdopodobnie należy włączyć przełącznik administratora dzierżawy w portalu administracyjnym.
-   Zestaw danych musi zawierać pola zawierające informacje o **szerokości geograficznej** i **długości geograficznej** . Geokodowanie pól lokalizacji zostanie dodane w przyszłej aktualizacji.
-   Wbudowana kontrolka Legenda dla Power BI nie jest obecnie wyświetlana w tej wersji zapoznawczej. Zostanie dodana w przyszłej aktualizacji.

## <a name="use-the-azure-maps-visual"></a>Używanie wizualizacji Azure Maps

Po włączeniu wizualizacji **Azure Maps** wybierz ikonę **Azure Maps** z okienka **wizualizacje** .

![Przycisk Azure Maps wizualizacji](media/power-bi-visual/azure-maps-in-visualizations-pane.png)

Power BI tworzy pustą Azure Maps kanwę projektu wizualizacji. W wersji zapoznawczej zostanie wyświetlona dodatkowa zrzeczenie się.

![Power BI pulpitu z wizualizacją Azure Maps załadowana w stanie początkowym](media/power-bi-visual/visual-initial-load.png)

Wykonaj następujące kroki, aby załadować wizualizację Azure Maps:

1.  W okienku **pola** przeciągnij pola danych, które zawierają informacje o współrzędnej i szerokości geograficznej do zasobników **Latitude** i/lub **długości geograficznej** . Są to minimalne dane, które są konieczne do załadowania wizualizacji Azure Maps.
    
    > [!div class="mx-imgBorder"]
    > ![Wizualizacja Azure Maps punkty jako bąbelki na mapie po podanych polach szerokości geograficznej i długości geograficznej](media/power-bi-visual/bubble-layer.png)

2.  Aby kolorować dane na podstawie kategoryzacji, przeciągnij pole kategorii do zasobnika **Legenda** okienka **pola** . W tym przykładzie jest używana kolumna **AdminDistrict** (znana także jako Województwo).  
    
    > [!div class="mx-imgBorder"]
    > ![Wizualizacja Azure Maps Wyświetlanie punktów jako kolorowych bąbelków mapy po podanym polu legendy](media/power-bi-visual/bubble-layer-with-legend-color.png)

    > [!NOTE]
    > Wbudowana kontrolka Legenda dla Power BI nie jest obecnie wyświetlana w tej wersji zapoznawczej. Zostanie dodana w przyszłej aktualizacji.

3.  Aby skalować dane względnie, przeciągnij miarę do zasobnika **rozmiar** okienka **pola** . W tym przykładzie korzystamy z kolumny **Sales** .  
    
    > [!div class="mx-imgBorder"]
    > ![Wizualizacja Azure Maps Wyświetlanie punktów jako kolorowych i skalowanych bąbelków w polu mapa po danym rozmiarze.](media/power-bi-visual/bubble-layer-with-legend-color-and-size.png)

4.  Użyj opcji w okienku **Format** , aby dostosować sposób renderowania danych. Poniższy obraz jest tą samą mapą jak powyżej, ale z opcją wypełnienia warstwy bąbelków ustawioną na 50%, a opcja kontur o dużym kontraście jest włączona.  
    
    > [!div class="mx-imgBorder"]
    > ![Wizualizacja Azure Maps punkty jako bąbelki na mapie ze stylem niestandardowym](media/power-bi-visual/bubble-layer-styled.png)

## <a name="fields-pane-buckets"></a>Zasobniki okienka pola

Poniższe zasobniki danych są dostępne w okienku **pola** wizualizacji Azure Maps.

| Pole     | Opis  |
|-----------|--------------|
| Szerokość geograficzna  | Pole używane do określania wartości szerokości geograficznej punktów danych. Wartości szerokości geograficznej powinny należeć do zakresu od-90 do 90 w formacie dziesiętnym.  |
| Długość geograficzna | Pole używane do określania wartości długości geograficznej punktów danych. Wartości długości geograficznej muszą należeć do zakresu od-180 do 180 w formacie dziesiętnym.  |
| Legenda    | Pole używane do kategoryzowania danych i przypisywania unikatowego koloru punktów danych w każdej kategorii. Po wypełnieniu tego zasobnika w okienku **Format** zostanie wyświetlona sekcja **kolory danych** , która umożliwia dostosowanie kolorów. |
| Rozmiar      | Miara używana do względnego ustalania rozmiarów punktów danych na mapie.   |
| Etykietki narzędzi  | Dodatkowe pola danych, które są wyświetlane w etykietkach narzędzi, gdy kształty są przesuwane. |

## <a name="map-settings"></a>Ustawienia mapy

Sekcja **Ustawienia mapy** w okienku format zawiera opcje umożliwiające dostosowanie sposobu wyświetlania mapy i oddziałania aktualizacji.

| Ustawienie             | Opis  |
|---------------------|--------------|
| Dopasuj do autopowiększenia           | Automatycznie powiększa mapę do danych załadowanych za pomocą okienka **pola** wizualizacji. Zmiany danych zostaną zmienione na mapie. Gdy suwak jest **wyłączony** , w domyślnym widoku mapy są wyświetlane dodatkowe ustawienia widoku mapy. |
| Zawijanie świata          | Zezwala użytkownikowi na nieskończone przesunięcie mapy w poziomie. |
| Selektor stylu        | Dodaje do mapy przycisk, który umożliwia czytelnikom raportu zmianę stylu mapy. |
| Kontrolki nawigacji | Dodaje przyciski do mapy jako inną metodę, aby umożliwić czytelnikom raportu powiększanie, obracanie i zmianę szerokości mapy. Aby uzyskać więcej informacji, zapoznaj się z tym dokumentem na temat [nawigowania po mapie](map-accessibility.md#navigating-the-map) , aby uzyskać szczegółowe informacje na temat różnych sposobów nawigowania po mapie. |
| Styl mapy           | Styl mapy. Aby uzyskać więcej informacji, zobacz ten dokument, aby uzyskać więcej informacji na temat [obsługiwanych stylów mapy](supported-map-styles.md). |

### <a name="map-view-settings"></a>Ustawienia widoku mapy

Jeśli suwak **autopowiększenia** jest **wyłączony** , wyświetlane są następujące ustawienia i zezwala użytkownikowi na określanie informacji o widoku mapy domyślnej.

| Ustawienie          | Opis   |
|------------------|---------------|
| Zoom             | Domyślny poziom powiększenia mapy. Może być liczbą z przedziału od 0 do 22. |
| Wyśrodkuj Szerokość geograficzna  | Domyślna szerokość geograficzna na środku mapy. |
| Długość geograficzna środka | Domyślna długość geograficzna na środku mapy. |
| Nagłówek          | Domyślna Orientacja mapy (w stopniach), gdzie 0 to północ, 90 to wschód, 180 to południe, a 270 to zachód. Może to być dowolna liczba z zakresu od 0 do 360. |
| Tonu            | Domyślne pochylenie mapy (w stopniach) między 0 a 60, gdzie 0 to szybkość wyszukiwania w dół na mapie. |

## <a name="considerations-and-limitations"></a>Istotne zagadnienia i ograniczenia

Wizualizacja Azure Maps jest dostępna w następujących usługach i aplikacjach:

| Usługa/aplikacja                              | Dostępność |
|------------------------------------------|--------------|
| Power BI Desktop                         | Tak          |
| Usługa Power BI (app.powerbi.com)       | Tak          |
| Aplikacje mobilne Power BI             | Tak          |
| Usługa Power BI — publikowanie w Internecie                  | Nie           |
| Power BI Embedded                        | Nie           |
| Osadzanie usługi Power BI (PowerBI.com) | Tak          |

Obsługa dodatkowych usług Power BI/aplikacji zostanie dodana w przyszłych aktualizacjach.

**Gdzie jest Azure Maps dostępne?**

W tej chwili Azure Maps jest obecnie dostępna we wszystkich krajach i regionach, z wyjątkiem następujących:

- Chiny
- Korea Południowa

Aby uzyskać szczegółowe informacje o zapotrzebowaniu dla różnych Azure Maps usług, które obsługują tę wizualizację, zobacz dokument z [informacjami o zapotrzebowaniu geograficznym](geographic-coverage.md) .

**Które przeglądarki sieci Web są obsługiwane przez wizualizację Azure Maps?**

Zapoznaj się z tą dokumentacją, aby uzyskać informacje na temat [Azure Maps obsługiwanych przeglądarek sieci Web SDK](supported-browsers.md).

**Ile punktów danych można wizualizować?**

Ta Wizualizacja obsługuje do 30 000 punktów danych.

**Czy w tym elemencie wizualnym można używać adresów lub innych ciągów lokalizacji?**

Początkowa wersja zapoznawcza tego elementu wizualnego obsługuje tylko wartości szerokości i długości geograficznej w stopniach dziesiętnych. W przyszłej aktualizacji zostanie dodana obsługa adresów i innych ciągów lokalizacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wizualizacji Azure Maps Power BI:

> [!div class="nextstepaction"]
> [Informacje o warstwach w Azure Maps Power BI wizualizacji](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Zarządzanie wizualizacją Azure Maps w organizacji](power-bi-visual-manage-access.md)

Dostosuj wizualizację:

> [!div class="nextstepaction"]
> [Porady i wskazówki dotyczące formatowania kolorów w usłudze Power BI](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [Dostosowywanie tytułów, legend i tła wizualizacji](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)
