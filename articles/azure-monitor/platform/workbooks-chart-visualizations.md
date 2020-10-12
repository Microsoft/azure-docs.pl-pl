---
title: Azure Monitor wizualizacje wykresów skoroszytu
description: Dowiedz się więcej na temat wszystkich wizualizacji wykresu Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006384"
---
# <a name="chart-visualizations"></a>Wizualizacje wykresów

Skoroszyty umożliwiają prezentowanie danych monitorowania jako wykresów. Obsługiwane typy wykresów obejmują wiersz, słupek, pasek kategorii, obszar, wykresy punktowe, wykres kołowy i czas. Autorzy mogą zdecydować się na dostosowanie wysokości, szerokości, palety kolorów, legendy, tytułów, wiadomości bez danych itp. wykresu i dostosowanie typów osi i kolorów serii przy użyciu ustawień wykresu.

Skoroszyty obsługują wykresy zarówno dla dzienników, jak i źródeł danych metryk.

## <a name="log-charts"></a>Wykresy dzienników

Dzienniki Azure Monitor podają właścicielom zasobów szczegółowe informacje o pracy aplikacji i infrastruktury. W przeciwieństwie do metryk, informacje o dziennikach nie są zbierane domyślnie i wymagają niektórych rodzajów kolekcji. Jednak gdy obecne dzienniki zawierają wiele informacji o stanie zasobów i danych przydatnych do celów diagnostycznych. Skoroszyty umożliwiają prezentowanie danych dzienników jako wizualizacji na potrzeby analizy użytkownika.

### <a name="adding-a-log-chart"></a>Dodawanie wykresu dziennika

W poniższym przykładzie przedstawiono trend żądań do aplikacji w ciągu ostatnich dni.

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić [KQL](/azure/kusto/query/) do analizy (na przykład trend żądań).
5. Ustaw wizualizację na jeden z: **obszar**, **pasek**, **pasek (kategorii)**, **linia**, **Wykres kołowy**, **punktowy**lub **czas**.
6. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, Wizualizacja, rozmiar, paleta kolorów i Legenda.

[![Zrzut ekranu przedstawiający wykres dziennika w trybie edycji](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parametry wykresu dziennika

| Parametr | Objaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ zapytania do użycia. | Dziennik, wykres zasobów platformy Azure itd. |
| `Resource Type` | Typ zasobu, który ma być obiektem docelowym. | Application Insights, Log Analytics lub Azure — najpierw |
| `Resources` | Zestaw zasobów, z którego ma zostać pobrana wartość metryki. | MyApp1 |
| `Time Range` | Przedział czasu, aby wyświetlić wykres dziennika. | Ostatnia godzina, ostatnie 24 godziny itd. |
| `Visualization` | Wizualizacja do użycia. | Obszar, słupek, linia, wykres kołowy, punktowy, czas, pasek kategorii |
| `Size` | Rozmiar w pionie formantu. | Małe, średnie, duże lub pełne |
| `Color palette` | Paleta kolorów, która ma być używana na wykresie. Zignorowano w trybie z obsługą metryki lub z segmentacją. | Niebieski, zielony, czerwony itd. |
| `Legend` | Funkcja agregacji, która ma być używana dla legendy. | Suma lub średnia wartości lub maks., minimalna, pierwsza, Ostatnia wartość |
| `Query` | Dowolne zapytanie KQL zwracające dane w formacie oczekiwanym przez wizualizację wykresu. | _żąda \| żądań wykonania serii = Count () domyślnie = 0 w sygnaturze czasowej z temu (1D) do Now () Step 1 godz_ |

### <a name="time-series-charts"></a>Wykresy szeregów czasowych

Wykresy szeregów czasowych, takie jak obszar, słupek, linia, punktowy i czas, można łatwo utworzyć przy użyciu kontrolki zapytania w skoroszytach. Klucz ma informacje o czasie i metryce w zestawie wyników.

#### <a name="simple-time-series"></a>Prosta seria czasowa

Poniższe zapytanie zwraca tabelę zawierającą dwie kolumny: *sygnatura czasowa* i *żądania*. Kontrolka zapytania używa *sygnatury czasowej* dla osi X i *żądań* dla osi Y.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Zrzut ekranu przedstawiający prosty wykres liniowy z serii czasu.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Szeregi czasowe z wieloma metrykami

Poniższe zapytanie zwraca tabelę z trzema kolumnami: *timestamp*, *Requests*i *users*. Kontrolka zapytania używa *sygnatury czasowej* dla osi X i *żąda*  &  *użytkowników* jako oddzielnej serii na osi Y.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Zrzut ekranu przedstawiający serię czasową z wieloma wykresami liniowymi dziennika metryk.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Przesegmentowanie szeregów czasowych

Poniższe zapytanie zwraca tabelę z trzema kolumnami: *timestamp*, *Requests*i *RequestName* , gdzie *RequestName* jest kolumną kategorii z nazwami żądań. Kontrolka zapytania w tym miejscu używa *sygnatury czasowej* dla osi X i dodaje serię na wartość *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Zrzut ekranu przedstawiający wykres liniowy z segmentacją z serii czasowych.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Podsumuj i Utwórz serię

Przykłady w poprzedniej sekcji używają `summarize` operatora, ponieważ jest to łatwiejsze do zrozumienia. Podsumowując, ma jednak duże ograniczenie, ponieważ pomija wiersz wyników, jeśli w zasobniku nie ma żadnych elementów. Może to skutkować przesunięciem okna czasu wykresu w zależności od tego, czy puste zasobniki znajdują się na wierzchu lub Backside zakresu czasu.

Zwykle lepszym rozwiązaniem jest użycie `make-series` operatora w celu utworzenia danych szeregów czasowych, które zawierają opcję dostarczania wartości domyślnych dla pustych zasobników.

Poniższe zapytanie używa `make-series` operatora.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Poniższe zapytanie pokazuje podobny wykres z `summarize` operatorem

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Mimo że podstawowy zestaw wyników jest inny. Dla wszystkich użytkowników należy ustawić wizualizację na obszar, linię, pasek lub czas, a skoroszyty zajmieją resztę.

[![Zrzut ekranu przedstawiający wykres liniowy dziennika wykonany z zapytania z serii](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Kategorii wykres słupkowy lub histogram

Wykresy kategorii umożliwiają użytkownikom reprezentowanie wymiaru lub kolumny na osi X wykresu, co jest szczególnie przydatne w histogramach. W poniższym przykładzie pokazano rozkład żądań według ich kodu wynikowego.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Zapytanie zwraca dwie kolumny: *żąda* metryki i kategorii *wynik* . Każda wartość w kolumnie *wynik* będzie mieć własny pasek na wykresie o wysokości proporcjonalnej do *metryki żądań*.

[![Zrzut ekranu przedstawiający wykres słupkowy kategorii dla żądań według kodu wyniku](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Wykresy kołowe

Wykresy kołowe umożliwiają wizualizację wartości liczbowej. Poniższy przykład pokazuje odsetek żądań według ich kodu wynikowego.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Zapytanie zwraca dwie kolumny: *żąda* metryki i kategorii *wynik* . Każda wartość w kolumnie *wynik* będzie mieć własny wycink w kole o rozmiarze proporcjonalnym do metryki *żądań* .

[![Zrzut ekranu przedstawiający wykres kołowy z wycinkami reprezentującymi kod wyniku](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Wykresy metryk

Większość zasobów platformy Azure emituje dane metryk o stanie i kondycji (na przykład użycie procesora CPU, dostępność magazynu, liczba transakcji bazy danych, Nieudane żądania aplikacji itp.). Skoroszyty umożliwiają wizualizację tych danych jako wykresy szeregów czasowych.

### <a name="adding-a-metric-chart"></a>Dodawanie wykresu metryki

W poniższym przykładzie zostanie wyświetlona liczba transakcji na koncie magazynu w ciągu poprzedniej godziny. Dzięki temu właściciel magazynu może zobaczyć trend transakcji i wyszukać anomalie w zachowaniu.

1. Przełącz skoroszyt do trybu edycji, wybierając pozycję **Edytuj** element paska narzędzi.
2. Za pomocą linku **Dodaj metrykę** Dodaj do skoroszytu formant metryki.
3. Wybierz typ zasobu (na przykład konto magazynu), zasoby docelowe, przestrzeń nazw i nazwę metryki, a następnie użyj agregacji.
4. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, podział przez, Wizualizacja, rozmiar i paleta kolorów.

[![Zrzut ekranu przedstawiający wykres metryki w trybie edycji](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parametry wykresu metryki

| Parametr | Objaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ zasobu, który ma być obiektem docelowym. | Magazyn lub maszyna wirtualna. |
| `Resources` | Zestaw zasobów, z którego ma zostać pobrana wartość metryki. | MyStorage1 |
| `Namespace` | Przestrzeń nazw z metryką. | Magazyn > obiektów BLOB |
| `Metric` | Metryka do wizualizacji. | > transakcje magazynu > obiektów BLOB |
| `Aggregation` | Funkcja agregacji, która ma zostać zastosowana do metryki. | Suma, liczność, średnia itp. |
| `Time Range` | Przedział czasu, w którym ma zostać wyświetlona Metryka. | Ostatnia godzina, ostatnie 24 godziny itd. |
| `Visualization` | Wizualizacja do użycia. | Obszar, słupek, linia, punktowy, Siatka |
| `Split By` | Opcjonalnie Podziel metrykę na wymiarze. | Transakcje według typu geograficznego |
| `Size` | Rozmiar w pionie formantu. | Małe, średnie lub duże |
| `Color palette` | Paleta kolorów, która ma być używana na wykresie. Ignorowany, jeśli `Split by` parametr jest używany. | Niebieski, zielony, czerwony itd. |

### <a name="examples"></a>Przykłady

Transakcje podzielone według nazwy interfejsu API jako wykres liniowy:

[![Zrzut ekranu przedstawiający wykres liniowy dla transakcji magazynu podzielony według nazwy interfejsu API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transakcje podzielone według typu odpowiedzi jako dużego wykresu słupkowego:

[![Zrzut ekranu przedstawiający wykres słupkowy z dużą metryką dla transakcji magazynu podzielony według typu odpowiedzi](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Średnie opóźnienie jako wykres punktowy:

[![Zrzut ekranu przedstawiający wykres punktowy z metryką na potrzeby opóźnienia magazynu](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Ustawienia wykresu

Autorzy mogą używać ustawień wykresu, aby dostosowywać pola, które są używane na osiach wykresu, jednostkach osi, formatowaniu niestandardowym, zakresach, zachowań grupowania, legendach i kolorach serii.

### <a name="the-settings-tab"></a>Karta Ustawienia

Kontrolki karta Ustawienia:

- Ustawienia osi, w tym pola, niestandardowe formatowanie umożliwiające użytkownikom ustawianie formatowania liczb na wartości osi i zakresy niestandardowe.
- Ustawienia grupowania, w tym pola, limity przed utworzeniem grupy "inne".
- Ustawienia legendy, w tym wyświetlanie metryk (nazwa serii, kolory i liczby) u dołu i/lub legendę (nazwy serii i kolory).

![Zrzut ekranu przedstawiający ustawienia wykresu.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Formatowanie niestandardowe

Opcje formatowania liczb obejmują:

| Opcja formatowania             | Objaśnienie                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Jednostki dla kolumn — różne opcje dla wartości procentowej, liczniki, czas, bajt, liczba/czas, bajty/czas itd. Na przykład jednostką dla wartości 1234 można ustawić na milisekundy i jest ona renderowana jako liczbę 1,234 s.                                  |
| `Style`                      | Format do renderowania jako-decimal, Currency, procent.                                               |
| `Show group separator`       | Pole wyboru, aby wyświetlić separatory grup. Renderuje 1234 jako 1 234 w Stanach Zjednoczonych.                                    |
| `Minimum integer digits`     | Minimalna liczba cyfr do użycia (wartość domyślna to 1).                                                   |
| `Minimum fractional digits`  | Minimalna liczba cyfr ułamkowych do użycia (domyślnie 0).                                                |
| `Maximum fractional digits`  | Maksymalna liczba cyfr ułamkowych do użycia.                                                            |
| `Minimum significant digits` | Minimalna liczba cyfr znaczących do użycia (wartość domyślna to 1).                                               |
| `Maximum significant digits` | Maksymalna liczba cyfr znaczących do użycia.                                                           |

![Zrzut ekranu ustawień osi X.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Karta serie

Karta Ustawienia serii umożliwia dostosowanie etykiet i kolorów widocznych dla serii na wykresie.

- To `Series name` pole jest używane w celu dopasowania do serii danych, a jeśli jest to zgodne, zostanie wyświetlona etykieta wyświetlania i kolor.
- `Comment`Pole jest przydatne dla autorów szablonów, ponieważ ten komentarz może być używany przez translatory do lokalizowania etykiet wyświetlania.

![Zrzut ekranu ustawień serii.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak utworzyć [kafelek w skoroszytach](workbooks-tile-visualizations.md).
- Dowiedz się, jak tworzyć [interaktywne skoroszyty](workbooks-interactive.md).