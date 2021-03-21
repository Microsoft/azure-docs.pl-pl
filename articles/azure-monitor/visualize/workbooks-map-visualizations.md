---
title: Azure Monitor wizualizacji mapy skoroszytu
description: Dowiedz się więcej na temat Azure Monitor wizualizacji mapy skoroszytu.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615990"
---
# <a name="map-visualization"></a>Wizualizacja mapy

Ułatwienia wizualizacji mapy w przypadku problemów z wskazywaniem numeru PIN w określonych regionach i przedstawiające zagregowane widoki wysokiego poziomu danych monitorowania przez zapewnienie możliwości agregowania wszystkich danych mapowanych na poszczególne lokalizacje/kraje/regiony.

Zrzut ekranu poniżej przedstawia łączną liczbę transakcji i opóźnień końcowych dla różnych kont magazynu. W tym miejscu rozmiar jest określany przez łączną liczbę transakcji i metryki kolorów poniżej mapy przedstawiają opóźnienia kompletne. Po pierwsze obserwacja liczba transakcji w regionie **zachodnie stany USA** jest niewielka w porównaniu do regionu **Wschodnie stany USA** , ale kompleksowe opóźnienie dla regionu **zachodnie stany USA** jest wyższe niż region **Wschodnie stany USA** . Zapewnia to wstępną analizę, którą amiss w regionie **zachodnie stany USA**.

![Zrzut ekranu mapy lokalizacji platformy Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Dodawanie mapy

Mapę można wizualizować, jeśli dane bazowe/metryki zawierają informacje o szerokości geograficznej, informacje o zasobach platformy Azure, informacje o lokalizacji lub kraju/regionie, nazwie lub kodzie kraju/regionu.

### <a name="using-azure-location"></a>Korzystanie z lokalizacji platformy Azure

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie*.
3. Zmień *Źródło danych* , aby `Azure Resource Graph` wybrać dowolną subskrypcję z kontem magazynu.
4. Wprowadź poniżej zapytanie dotyczące analizy oraz **zapytania select Run**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Ustaw *rozmiar* na `Large` .
6. Ustaw *wizualizację* na `Map` .
7. Wszystkie ustawienia zostaną automatycznie wypełnione. W przypadku ustawień niestandardowych wybierz przycisk **Ustawienia mapy** , aby otworzyć okienko ustawienia.
8. Poniżej znajduje się zrzut ekranu przedstawiający wizualizację mapy pokazującą konta magazynu dla wybranej subskrypcji.

![Zrzut ekranu mapy lokalizacji platformy Azure z powyższym zapytaniem](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Korzystanie z zasobów platformy Azure

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj metrykę*.
3. Użyj subskrypcji z kontami magazynu.
4. Zmień *Typ zasobu* na `storage account` i w obszarze *zasób* wybierz wiele kont magazynu.
5. Wybierz pozycję **Dodaj metrykę** i Dodaj metrykę transakcji.
    1. Przestrzeń nazw: `Account`
    2. Metryki `Transactions`
    3. Agregacji `Sum`
    
    ![Zrzut ekranu przedstawiający metrykę transakcji](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Wybierz pozycję **Dodaj metrykę** i Dodaj metrykę opóźnienia E2E.
    1. Przestrzeń nazw: `Account`
    1. Metryki `Success E2E Latency`
    1. Agregacji `Average`
    
    ![Zrzut ekranu przedstawiający metrykę opóźnienia na zakończenie od końca do końca](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Ustaw *rozmiar* na `Large` .
1. Ustaw rozmiar *wizualizacji* na `Map` .
1. W obszarze **Ustawienia mapy** ustaw następujące ustawienia:
    1. Informacje o lokalizacji przy użyciu: `Azure Resource`
    1. Pole zasobów platformy Azure: `Name`
    1. Rozmiar według: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Agregacja lokalizacji: `Sum of values`
    1. Typ kolorowania: `Heatmap`
    1. Kolor według: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregacja dla koloru: `Sum of values`
    1. Paleta kolorów: `Green to Red`
    1. Wartość minimalna: `0`
    1. Wartość metryki: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agreguj inne metryki według: `Sum of values`
    1. Wybierz **niestandardowe pole formatowania**
    1. Jednostka `Milliseconds`
    1. Stylów `Decimal`
    1. Maksymalna liczba znaków ułamkowych: `2`

### <a name="using-countryregion"></a>Przy użyciu kraju/regionu

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Wybierz pozycję **Dodaj*, a następnie pozycję *Dodaj zapytanie*.
3. Zmień *Źródło danych* na `Log` .
4. Wybierz pozycję *Typ zasobu* jako `Application Insights` , a następnie wybierz dowolny zasób Application Insights, który zawiera dane pageViews.
5. Użyj edytora zapytań, aby wprowadzić KQL do analizy, a następnie wybierz pozycję **Uruchom zapytanie**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Ustaw wartości w polu Rozmiar `Large` .
7. Ustaw wizualizację na `Map` .
8. Wszystkie ustawienia zostaną automatycznie wypełnione. W obszarze Ustawienia niestandardowe wybierz pozycję **Ustawienia mapy**.

### <a name="using-latitudelocation"></a>Korzystanie z szerokości/lokalizacji

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Wybierz pozycję **Dodaj*, a następnie pozycję *Dodaj zapytanie*.
3. Zmień *Źródło danych* na `JSON` .
1. Wprowadź dane JSON poniżej w edytorze zapytań i wybierz pozycję **Uruchom zapytanie**.
1. Ustaw wartości  w polu Rozmiar `Large` .
1. Ustaw *wizualizację* na `Map` .
1. W **ustawieniach mapy** w obszarze ustawienia metryki ustaw wartość *etykieta Metryka* na, `displayName` a następnie wybierz pozycję **Zapisz i Zamknij**.

Poniższa Wizualizacja mapy pokazuje użytkowników dla każdej lokalizacji szerokości i długości geograficznej z wybraną etykietą dla metryk.

![Zrzut ekranu przedstawiający wizualizację mapy pokazującą użytkowników dla każdej lokalizacji szerokości i długości geograficznej z wybraną etykietą metryk](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Ustawienia mapy

### <a name="layout-settings"></a>Ustawienia układu

| Ustawienie | Wyjaśnienie |
|:-------------|:-------------|
| `Location info using` | Wybierz sposób pobierania lokalizacji elementów wyświetlanych na mapie. <br> **Szerokość/Długość** geograficzna: zaznacz tę opcję, jeśli istnieją kolumny zawierające informacje o długości i szerokości geograficznej. Każdy wiersz z danymi długości i szerokości geograficznej będzie pokazywany jako odrębny element na mapie. <br> **Lokalizacja platformy Azure**: Wybierz tę opcję, jeśli istnieje kolumna z informacjami o lokalizacji platformy Azure (wschodnim, westeurope, centralindia itp.). Określ tę kolumnę, która będzie pobierać odpowiednie współrzędne geograficzne i Długość geograficzna dla każdej lokalizacji platformy Azure, grupować te same wiersze lokalizacji (na podstawie określonej agregacji), aby pokazać lokalizacje na mapie. <br> **Zasób platformy Azure**: Wybierz tę opcję, jeśli istnieje kolumna zawierająca informacje o zasobach platformy Azure (konto magazynu, konto cosmosdb itp.). Określ tę kolumnę, która będzie pobierać odpowiednie wartości szerokości i długości geograficznej dla każdego zasobu platformy Azure, grupuje te same lokalizacje (w oparciu o określoną agregację), aby pokazać lokalizacje na mapie. <br> **Kraj/region**: Wybierz tę opcję, jeśli istnieje kolumna, która zawiera informacje o nazwie kraju/regionu/kodu (usa, Stany Zjednoczone, w, Indie, CN, Chiny). Określ tę kolumnę, która pobierze odpowiednie współrzędne geograficzne i Długość geograficzna dla każdego kraju/regionu/kodu i pogrupuj wiersze w taki sam sposób, w jaki Country-Region kod/kraj-region, aby pokazać lokalizacje na mapie. Nazwa kraju i kod kraju nie będą zgrupowane razem jako pojedyncza jednostka na mapie.
| `Latitude/Longitude` | Te dwie opcje będą widoczne, jeśli wartość pola informacje o lokalizacji to: Szerokość/Długość geograficzna. Wybierz kolumnę, która ma szerokość geograficzną w polu Latitude i długość geograficzną w polu długości geograficznej. |
| `Azure location field` | Ta opcja będzie widoczna, jeśli wartość pola informacje o lokalizacji to: Lokalizacja platformy Azure. Wybierz kolumnę, w której są informacje o lokalizacji platformy Azure. |
| `Azure resource field` | Ta opcja będzie widoczna, jeśli wartość pola informacje o lokalizacji to: zasób platformy Azure. Wybierz kolumnę, która ma informacje o zasobach platformy Azure. |
| `Country/Region field` | Ta opcja będzie widoczna, jeśli wartość pola informacje o lokalizacji to: kraj lub region. Wybierz kolumnę, w której informacje o kraju/regionie. |
| `Size by` | Ta opcja określa rozmiar elementów wyświetlanych na mapie. Rozmiar zależy od wartości w kolumnie określonej przez użytkownika. Obecnie promień okręgu jest bezpośrednio proporcjonalny do pierwiastek kwadratowy wartości kolumny. Jeśli "Brak..." zaznaczone, wszystkie okręgi będą pokazywały domyślny rozmiar regionu.|
| `Aggregation for location` | To pole określa sposób agregowania **rozmiaru według** kolumn o tej samej lokalizacji platformy Azure/w regionie/kraju platformy Azure. |
| `Minimum region size` | To pole określa minimalny promień elementu wyświetlanego na mapie. Ta wartość jest używana w przypadku znaczącej różnicy między wartościami rozmiaru według kolumny, dlatego mniejsze elementy są trudno widoczne na mapie. |
| `Maximum region size` | To pole określa maksymalny promień elementu wyświetlanego na mapie. Ta wartość jest używana, gdy wartości rozmiaru według kolumny są niezwykle duże i obejmują ogromną powierzchnię mapy.|
| `Default region size` | To pole określa, co to jest domyślny promień elementu wyświetlanego na mapie. Domyślny promień jest używany, gdy rozmiar kolumny ma wartość "none"... lub wartość jest równa 0.|
| `Minimum value` | Wartość minimalna używana do obliczania rozmiaru regionu. Jeśli nie zostanie określony, wartość minimalna będzie najmniejsza wartość po agregacji. |
| `Maximum value` | Maksymalna wartość używana do obliczania rozmiaru regionu. Jeśli nie zostanie określony, wartość maksymalna będzie największą wartością po agregacji.|
| `Opacity of items on Map` | To pole określa, jak przezroczyste są elementy wyświetlane na mapie. Nieprzezroczystość 1 oznacza, że brak przejrzystości, w przypadku nieprzezroczystości równej 0 oznacza, że elementy nie będą widoczne na mapie. Jeśli na mapie znajduje się zbyt wiele elementów, nieprzezroczystość można ustawić na wartość niska, aby wszystkie nakładane elementy były widoczne.|

### <a name="color-settings"></a>Ustawienia koloru

| Typ kolorowania | Wyjaśnienie |
|:------------- |:-------------|
| `None` | Wszystkie węzły mają ten sam kolor. |
| `Thresholds` | W tym typie kolory komórek są ustawiane przez reguły progu (na przykład _procesor cpu > 90% => Red, 60% > procesora > 90% => Yellow, procesor < 60% => zielony_). <ul><li> **Kolor według**: wartość tej kolumny będzie używana przez wartości progowe/mapę cieplną Logic.</li> <li>**Agregacja dla koloru**: to pole określa sposób agregowania **koloru według** kolumn o tej samej lokalizacji platformy Azure/w regionie/kraju platformy Azure. </li> <ul> |
| `Heatmap` | W tym typie komórki są kolorowe w oparciu o paletę kolorów i pole kolor według. Będzie również mieć ten sam **kolor** i **agregację dla opcji kolorów** jako progi. |

### <a name="metric-settings"></a>Ustawienia metryki
| Ustawienie | Wyjaśnienie |
|:------------- |:-------------|
| `Metric Label` | Ta opcja będzie widoczna, jeśli wartość pola informacje o lokalizacji to: Szerokość/Długość geograficzna. Korzystając z tej funkcji, użytkownik może wybrać etykietę do pokazania dla metryk przedstawionych poniżej mapy. |
| `Metric Value` | To pole określa wartość metryki, która ma być wyświetlana poniżej mapy. |
| `Create 'Others' group after` | To pole określa limit przed utworzeniem grupy "inne". |
| `Aggregate 'Others' metrics by` | To pole określa agregację używaną dla grupy "others", jeśli jest wyświetlana. |
| `Custom formatting` | To pole służy do ustawiania jednostek, stylu i opcji formatowania dla wartości liczbowych. Jest to takie samo jak [niestandardowe formatowanie siatki](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak tworzyć [wizualizacje plastrów miodu w skoroszytach](../visualize/workbooks-honey-comb.md).