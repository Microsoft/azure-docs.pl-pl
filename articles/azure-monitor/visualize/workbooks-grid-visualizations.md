---
title: Azure Monitor wizualizacje siatki skoroszytu
description: Dowiedz się więcej o wszystkich wizualizacjach siatki Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615613"
---
# <a name="grid-visualizations"></a>Wizualizacje siatki

Siatki lub tabele są typowym sposobem prezentowania danych użytkownikom. Skoroszyty umożliwiają użytkownikom pojedyncze Style kolumn siatki, aby zapewnić bogaty interfejs użytkownika dla swoich raportów.

W poniższym przykładzie przedstawiono siatkę łączącą ikony, map cieplnych i paski Spark w celu prezentowania złożonych informacji. Skoroszyt zawiera również sortowanie, pole wyszukiwania i przycisk przechodzenie do analizy.

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Dodawanie siatki opartej na dzienniku

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy (na przykład maszyny wirtualne z pamięcią poniżej wartości progowej)
5. Ustaw wizualizację na **siatkę**
6. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, rozmiar, paleta kolorów i Legenda.

[![Zrzut ekranu przedstawiający kwerendę siatki opartą na dzienniku](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parametry wykresu dziennika

| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ zapytania do użycia. | Dziennik, wykres zasobów platformy Azure itd. |
| `Resource Type` | Typ zasobu, który ma być obiektem docelowym. | Application Insights, Log Analytics lub Azure — najpierw |
| `Resources` | Zestaw zasobów, z którego ma zostać pobrana wartość metryki. | MyApp1 |
| `Time Range` | Przedział czasu, aby wyświetlić wykres dziennika. | Ostatnia godzina, ostatnie 24 godziny itd. |
| `Visualization` | Wizualizacja do użycia. | Siatka |
| `Size` | Rozmiar w pionie formantu. | Małe, średnie, duże lub pełne |
| `Query` | Dowolne zapytanie KQL zwracające dane w formacie oczekiwanym przez wizualizację wykresu. | _żądania \| podsumowujące żądania = Count () według nazwy_ |

## <a name="simple-grid"></a>Siatka prosta

Skoroszyty mogą renderować wyniki KQL jako prostą tabelę. W poniższej siatce przedstawiono liczbę żądań i unikatowych użytkowników na żądania typu w aplikacji.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku w trybie edycji](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Style siatki

Gdy zwykła tabela zawiera dane, trudno odczytać i szczegółowe informacje nie zawsze są widoczne. Style siatki może pomóc ułatwić odczytywanie i interpretowanie danych.

Poniżej znajduje się ta sama siatka z poprzedniej sekcji w stylu map cieplnych.

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku z kolumnami w stylu jako map cieplnych](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Ta sama siatka jest taka sama jak w przypadku pasków: [ ![ zrzut ekranu przedstawiający siatkę opartą na dzienniku z kolumnami w stylu jako słupki](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Określanie stylu kolumny siatki

1. Wybierz przycisk **Ustawienia kolumny** na pasku narzędzi kontrolki zapytania.
2. W obszarze *Edytowanie ustawień kolumny* wybierz kolumnę do stylu.
3. Wybierz renderowanie kolumny (na przykład mapę cieplną, pasek, pasek poniżej itp.) i powiązane ustawienia, aby określić styl kolumny.

Poniżej znajduje się przykład, który styluje kolumnę *Request* jako pasek:

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku z kolumną Request jako pasek.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Renderowanie kolumn

| Renderowanie kolumn | Wyjaśnienie | Opcje dodatkowe |
|:------------- |:-------------|:-------------|
| `Automatic` | Wartość domyślna — używa najbardziej odpowiedniego modułu renderowania na podstawie typu kolumny.  |  |
| `Text` | Renderuje wartości kolumny jako tekst. | |
| `Right Aligned` | Podobny do tekstu, z wyjątkiem tego, że jest wyrównany do prawej. | |
| `Date/Time` | Renderuje odczytywany ciąg daty i godziny. | |
| `Heatmap` | Kolor komórek siatki na podstawie wartości komórki. | Paleta kolorów i minimalna/maksymalna wartość używana do skalowania. |
| `Bar` | Renderuje pasek obok komórki na podstawie wartości komórki. | Paleta kolorów i minimalna/maksymalna wartość używana do skalowania. |
| `Bar underneath` | Renderuje pasek w dolnej części komórki na podstawie wartości komórki. | Paleta kolorów i minimalna/maksymalna wartość używana do skalowania. |
| `Composite bar` | Renderuje pasek złożony przy użyciu określonych kolumn w tym wierszu. Aby uzyskać szczegółowe informacje, zapoznaj się z [paskiem złożonym](workbooks-composite-bar.md) . | Kolumny z odpowiednimi kolorami, aby renderować pasek i etykietę wyświetlaną w górnej części paska. |
| `Spark bars` | Renderuje pasek Spark w komórce na podstawie wartości tablicy dynamicznej w komórce. Na przykład kolumna trend ze zrzutu ekranu u góry. | Paleta kolorów i minimalna/maksymalna wartość używana do skalowania. |
| `Spark lines` | Renderuje linię Spark w komórce na podstawie wartości tablicy dynamicznej w komórce. | Paleta kolorów i minimalna/maksymalna wartość używana do skalowania. |
| `Icon` | Renderuje ikony na podstawie wartości tekstowych w komórce. Obsługiwane wartości to:,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` i `Blank` .|  |
| `Link` | Renderuje łącze, gdy kliknięto lub wykonuje konfigurowalną akcję. Użyj tego, jeśli chcesz, aby element był *tylko* łączem.  Każdy inny typ może być *również* linkiem przy użyciu `Make this item a link` Ustawienia. Aby uzyskać więcej informacji, zobacz [Akcje łącza](#link-actions) poniżej. |  |
| `Location` | Renderuje przyjazną nazwę regionu platformy Azure w oparciu o identyfikatory regionów. |  |
| `Resource type` | Renderuje przyjazny ciąg typu zasobu na podstawie identyfikatora typu zasobu  |  |
| `Resource` | Renderuje przyjazną nazwę zasobu i link na podstawie identyfikatora zasobu  | Opcja wyświetlania ikony typu zasobu  |
| `Resource group` | Renderuje przyjazną nazwę grupy zasobów i łącze na podstawie identyfikatora grupy zasobów. Jeśli wartość komórki nie jest grupą zasobów, zostanie przekonwertowana na jedną.  | Opcja wyświetlania ikony grupy zasobów  |
| `Subscription` | Renderuje przyjazną nazwę subskrypcji i link na podstawie identyfikatora subskrypcji.  Jeśli wartość komórki nie jest subskrypcją, zostanie przekonwertowana na jedną.  | Opcja wyświetlania ikony subskrypcji.  |
| `Hidden` | Ukrywa kolumnę w siatce. Przydatne, gdy zapytanie domyślne zwraca więcej kolumn, ale nie jest to wymagane. |  |

### <a name="link-actions"></a>Akcje linków

W przypadku `Link` wybrania modułu renderowania lub wybrania pola wyboru *Uczyń ten element łączem* , autor może skonfigurować akcję łącza, która będzie występować po zaznaczeniu komórki. Zwykle powoduje to przełączenie użytkownika do innego widoku z kontekstem wychodzącym z komórki lub otwarciem adresu URL.

### <a name="custom-formatting"></a>Formatowanie niestandardowe

Skoroszyty umożliwiają również użytkownikom ustawianie formatowania liczb wartości komórek. Można to zrobić, klikając pole wyboru *niestandardowe formatowanie* , jeśli jest dostępne.

| Opcja formatowania | Wyjaśnienie |
|:------------- |:-------------|
| `Units` | Jednostki dla kolumn — różne opcje dla wartości procentowej, liczniki, czas, bajt, liczba/czas, bajty/czas itd. Na przykład jednostką dla wartości 1234 można ustawić na milisekundy i jest ona renderowana jako 1,234 s. |
| `Style` | Format do renderowania jako-decimal, Currency, procent. |
| `Show group separator` | Pole wyboru, aby wyświetlić separatory grup. Renderuje 1234 jako 1 234 w Stanach Zjednoczonych. |
| `Minimum integer digits` | Minimalna liczba cyfr do użycia (wartość domyślna to 1). |
| `Minimum fractional digits` | Minimalna liczba cyfr ułamkowych do użycia (domyślnie 0).  |
| `Maximum fractional digits` | Maksymalna liczba cyfr ułamkowych do użycia. |
| `Minimum significant digits` | Minimalna liczba cyfr znaczących do użycia (wartość domyślna to 1). |
| `Maximum significant digits` | Maksymalna liczba cyfr znaczących do użycia. |
| `Custom text for missing values` | Gdy punkt danych nie ma wartości, Pokaż ten tekst niestandardowy zamiast pustego. |

### <a name="custom-date-formatting"></a>Niestandardowe formatowanie daty

Gdy autor określi, że kolumna jest ustawiona na moduł renderowania Data/godzina, autor może określić niestandardowe opcje formatowania daty przy użyciu pola wyboru *niestandardowe formatowanie daty* .

| Opcja formatowania | Wyjaśnienie |
|:------------- |:-------------|
| `Style` | Format służący do renderowania daty jako krótkiej, długiej, pełnych formatów lub czasu w formacie krótkim lub długim. |
| `Show time as` | Umożliwia autorowi podjęcie decyzji między pokazywaniem czasu lokalnego (domyślnego) lub czasu UTC. W zależności od wybranego stylu formatu daty informacje o strefie UTC/Time mogą nie być wyświetlane. |

## <a name="custom-column-width-setting"></a>Ustawienie szerokości kolumny niestandardowej

Autor może dostosować szerokość dowolnej kolumny w siatce przy użyciu pola *szerokość kolumny niestandardowej* w *ustawieniach kolumny*.

![Zrzut ekranu ustawień kolumny z polem niestandardowej szerokości kolumny podanym w czerwonym polu](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Jeśli pole pozostanie puste, Szerokość zostanie automatycznie określona na podstawie liczby znaków w kolumnie i liczby widocznych kolumn. Jednostką domyślną jest "ch" (znaki).

Wybranie przycisku niebieska **(Bieżąca szerokość)** w etykiecie spowoduje wypełnienie pola tekstowego z bieżącą szerokością wybranej kolumny. Jeśli wartość jest obecna w polu Szerokość niestandardowa bez jednostki miary, zostanie użyta domyślna.

Dostępne są następujące jednostki miary:

| Jednostka miary | Definicja           |
|:--------------------|:---------------------|
| ch                  | znaki (domyślne) |
| piks.                  | wielkość               |
| fr                  | jednostki ułamkowe     |
| %                   | percentage           |

Sprawdzanie poprawności danych wejściowych — Jeśli walidacja nie powiedzie się, zostanie wyświetlony komunikat z informacją o czerwono, ale użytkownik nadal może zastosować szerokość. Jeśli wartość jest obecna w danych wejściowych, zostanie ona przeanalizowana. Jeśli nie zostanie znaleziona prawidłowa jednostka miary, zostanie użyta wartość domyślna.

Nie ma minimalnej/maksymalnej szerokości, ponieważ jest to pozostało do uznania autora. Pole szerokości kolumny niestandardowej jest wyłączone dla ukrytych kolumn.

## <a name="examples"></a>Przykłady

### <a name="spark-lines-and-bar-underneath"></a>Linie i słupek platformy Spark poniżej

W poniższym przykładzie przedstawiono liczbę żądań i jej trend według nazwy żądania.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku z paskiem poniżej i linią Spark](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Mapę cieplną z udostępnionymi skalami i formatowaniem niestandardowym

W tym przykładzie przedstawiono różne metryki czasu trwania żądania i ich liczby. Moduł renderowania mapę cieplną korzysta z wartości minimalnych ustawionych w ustawieniach lub oblicza minimalną i maksymalną wartość kolumny, a następnie przypisuje kolor tła z wybranej palety dla komórki na podstawie wartości komórki względem minimalnej i maksymalnej wartości kolumny.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku z mapę cieplną o wspólnej skali między kolumnami](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

W powyższym przykładzie, współdzielona paleta (kolor zielony lub czerwony) i skala jest używana do kolorowania kolumn (średnia, mediana, P80, P95 i poziomie P99). Oddzielna paleta (niebieska) używana jako kolumna żądania.

#### <a name="shared-scale"></a>Skalowanie udostępnione

Aby uzyskać wspólną skalę:

1. Użyj wyrażeń regularnych, aby zaznaczyć więcej niż jedną kolumnę, do której ma zostać zastosowane ustawienie. Na przykład Ustaw nazwę kolumny na, aby `Mean|Median|p80|p95|p99` wybrać wszystkie.
2. Usuń ustawienia domyślne dla poszczególnych kolumn.

Spowoduje to zastosowanie nowego ustawienia wielokolumnowego w celu uwzględnienia wspólnej skali.

[![Zrzut ekranu przedstawiający ustawienie siatki oparte na dzienniku, aby uzyskać wspólną skalę w kolumnach](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Ikony do reprezentowania stanu

W poniższym przykładzie przedstawiono niestandardowy stan żądań na podstawie czasu trwania p95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Zrzut ekranu przedstawiający siatkę opartą na dzienniku z mapę cieplnąą ze wspólną skalą w kolumnach przy użyciu powyższego zapytania.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Obsługiwane nazwy ikon obejmują:,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` i `Blank` .

### <a name="using-thresholds-with-links"></a>Używanie progów z łączami

Poniższe instrukcje pokazują, jak używać progów z łączami do przypisywania ikon i otwierania różnych skoroszytów. Każde łącze w siatce spowoduje otwarcie innego szablonu skoroszytu dla tego zasobu Application Insights.

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję *Edytuj* element paska narzędzi.
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie*.
3. Zmień *Źródło danych* na "JSON" i *wizualizację* na "siatkę".
4. Wprowadź następujące zapytanie.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Uruchom zapytanie.
6. Wybierz pozycję **Ustawienia kolumny** , aby otworzyć ustawienia.
7. Wybierz pozycję "nazwa" z *kolumn*.
8. W obszarze *moduł renderowania kolumn* wybierz pozycję "progi".
9.  Wprowadź i wybierz następujące *Ustawienia progu*.
   
    | Operator | Wartość   | Ikony   |
    |----------|---------|---------|
    | ==       | warning | Ostrzeżenie |
    | ==       | error   | Niepowodzenie  |

    ![Zrzut ekranu karty Edytowanie ustawień kolumny z powyższymi ustawieniami.](./media/workbooks-grid-visualizations/column-settings.png)

    Pozostaw domyślny wiersz jako. Możesz wprowadzić dowolny tekst. Kolumna Text przyjmuje format ciągu jako dane wejściowe i wypełnia je wartością kolumny i jednostką, jeśli została określona. Na przykład jeśli ostrzeżenie jest wartością kolumny, tekst może być " {0} {1} link!", będzie wyświetlany jako "link ostrzegawczy".
10. Zaznacz pole *Utwórz ten element jako łącze* .
    1. W obszarze *Widok do otwarcia* wybierz pozycję "skoroszyt (szablon)".
    2. W obszarze *link wartość pochodzi z*, wybierz pozycję "Połącz".
    3. Zaznacz pole *blok Otwórz łącze w kontekście* .
    4. Wybierz następujące ustawienia w *ustawieniach linku skoroszytu*
        1. W obszarze *Identyfikator szablonu pochodzi z*, wybierz pozycję "kolumna".
        2. W obszarze *kolumna* wybierz pozycję "Połącz".

    ![Zrzut ekranu ustawień łącza z powyższymi ustawieniami.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Wybierz pozycję "Połącz" z *kolumn*. W obszarze Ustawienia obok pozycji *moduł renderowania kolumn* wybierz pozycję **(Ukryj kolumnę)**.
1. Aby zmienić nazwę wyświetlaną kolumny "name", wybierz kartę **etykiety** . W wierszu o nazwie "name" jako *Identyfikator kolumny* w kolumnie * etykieta kolumny wprowadź nazwę, która ma zostać wyświetlona.
2. Wybierz pozycję **Zastosuj**

![Zrzut ekranu wartości progowych w siatce z powyższymi ustawieniami](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Wartości procentowe jednostek ułamkowych

Jednostka ułamkowa (fr) to często używana dynamiczna jednostka miary w różnych typach siatek. W miarę zmiany rozmiaru okna/rozdzielczości, Szerokość fr również ulega zmianie.

Zrzut ekranu poniżej przedstawia tabelę zawierającą osiem kolumn, które są 1fr szerokością każdego i wszystkie równe szerokości. W miarę zmieniania rozmiaru okna szerokość każdej kolumny zmienia się proporcjonalnie.

[![Zrzut ekranu kolumn w siatce z wartością szerokości kolumny 1fr każdy](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Poniższy obraz pokazuje tę samą tabelę, z tą różnicą, że pierwsza kolumna jest ustawiona na 50% szerokości. Spowoduje to ustawienie wartości kolumny na połowę całkowitej szerokości siatki. Zmiany rozmiaru okna nadal zachowują szerokość 50%, chyba że rozmiar okna jest zbyt mały. Te kolumny dynamiczne mają minimalną szerokość na podstawie ich zawartości. Pozostała 50% siatki jest dzielona przez osiem całkowitej liczby jednostek ułamkowych. Poniższa kolumna "Kind" ma wartość 2fr, więc przyjmuje jedną czwartą pozostałego miejsca. Ponieważ inne kolumny są 1fr każdego z nich, każda z nich przyjmuje jedną ósmą z prawej połowy siatki.

[![Zrzut ekranu kolumn w siatce z 1 wartością szerokości kolumny wynoszącą 50% i resztą jako 1fr każdy](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Możliwe jest łączenie szerokości fr,%, px i ch i działa podobnie jak w poprzednich przykładach. Szerokości ustawiane przez jednostki statyczne (ch i px) to twarde stałe, które nie ulegną zmianie, nawet jeśli okno/rozwiązanie zostanie zmienione. Kolumny ustawione przez% będą przyjmować wartości procentowe na podstawie całkowitej szerokości siatki (mogą być niedokładne ze względu na wcześniej minimalne szerokości). Kolumny ustawione z fr będą po prostu podzielić pozostałe miejsce w siatce na podstawie liczby przydzielonych jednostek ułamkowych.

[![Zrzut ekranu kolumn w siatce z asortymentami użytymi dla różnych jednostek szerokości](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć [drzewo w skoroszytach](workbooks-tree-visualizations.md).
* Dowiedz się, jak tworzyć [Parametry tekstu skoroszytu](workbooks-text.md).