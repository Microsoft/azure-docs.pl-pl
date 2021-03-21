---
title: Azure Monitor wizualizacje kafelków skoroszytu
description: Dowiedz się więcej na temat wszystkich wizualizacji kafelków skoroszytu Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615940"
---
# <a name="tile-visualizations"></a>Wizualizacje kafelków

Kafelki są przydatnym sposobem prezentowania danych podsumowujących w skoroszytach. Na poniższej ilustracji przedstawiono typowy przypadek użycia kafelków z podsumowaniem na poziomie aplikacji na podstawie szczegółowej siatki.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Kafelki skoroszytu obsługują wyświetlanie tytułu, podtytuł, duże tekstu, ikon, gradientów opartych na metrykach, linii/słupków Spark, stopek itp.

## <a name="adding-a-tile"></a>Dodawanie kafelka

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie* , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Ustaw rozmiar na **pełny**.
6. Ustaw wizualizację na **kafelki**.
7. Wybierz przycisk **Ustawienia kafelka** , aby otworzyć okienko ustawienia.
    1. W polu *tytuł* Ustaw:
        * Użyj kolumny: `name` .
    2. W obszarze *po lewej stronie* Ustaw:
        * Użyj kolumny: `Requests` .
        * Renderowanie kolumn: `Big Number` .
        * Paleta kolorów: `Green to Red`
        * Wartość minimalna: `0` .
    3. Na *dole* Ustaw:
        * Użyj kolumny: `appName` .
8. Wybierz przycisk **Zapisz i Zamknij** w dolnej części okienka.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków z powyższymi ustawieniami zapytania i kafelka.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Kafelki w trybie odczytu:

[![Zrzut ekranu przedstawiający Widok podsumowania kafelka w trybie odczytu.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Linie Spark na kafelkach

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Dodano parametr zakresu czasu o nazwie `TimeRange` .
    1. Wybierz pozycję **Dodaj** , a następnie *Dodaj parametry*.
    2. W kontrolce parametr wybierz pozycję **Dodaj parametr**.
    3. Wprowadź `TimeRange` wartość w polu *Nazwa parametru* i wybierz pozycję `Time range picker` dla *typu parametru*.
    4. Wybierz pozycję **Zapisz** w górnej części okienka, a następnie wybierz pozycję **Zakończono edycję** w kontrolce parametrów.
3. Wybierz pozycję **Dodaj** , a następnie *Dodaj zapytanie* , aby dodać kontrolkę zapytanie dziennika poniżej kontrolki parametrów.
4. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
5. Użyj edytora zapytań, aby wprowadzić KQL do analizy.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Wybierz pozycję **Uruchom zapytanie**. (Pamiętaj, aby ustawić `TimeRange` wartość wybraną przed uruchomieniem zapytania).
7. Ustaw *wizualizację* na "kafelki".
8. Ustaw *rozmiar* na "pełny".
9. Wybierz pozycję **Ustawienia kafelka**.
    1. Na *kafelku* Ustaw:
        * Użyj kolumny: `name` .
    2. W obszarze *podkafelka* ustaw wartość:
        *  Użyj kolumny: `appNAme` .
    3. W obszarze *po lewej stronie* Ustaw:
        *  Użyj kolumny: `Requests` .
        * Renderowanie kolumn: `Big Number` .
        * Paleta kolorów: `Green to Red` .
        * Wartość minimalna: `0` .
    4. Na *dole* Ustaw:
        * Użyj kolumny: `Trend` .
        * Renderowanie kolumn: `Spark line` .
        * Paleta kolorów: `Green to Red` .
        * Wartość minimalna: `0` .
10. Wybierz pozycję **Zapisz i Zamknij** w dolnej części okienka.

![Zrzut ekranu przedstawiający wizualizację kafelka z linią Spark](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Rozmiary kafelków

Autor ma opcję ustawiania szerokości kafelka w ustawieniach kafelka.

* `fixed` wartooć

    Domyślnym zachowaniem kafelków jest taka sama stała szerokość, około 160 pikseli szerokości oraz odstęp wokół kafelków.

    ![Zrzut ekranu przedstawiający kafelki o stałej szerokości](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Każdy tytuł zmniejszy się lub zwiększy, aby dopasować jego zawartość, jednak kafelki są ograniczone do szerokości widoku kafelków (bez przewijania w poziomie).

    ![Zrzut ekranu przedstawiający kafelki z autoszerokością](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Każdy tytuł będzie zawsze zawierać pełną szerokość widoku kafelków, jeden tytuł w każdym wierszu.

     ![Zrzut ekranu przedstawiający kafelki o pełnym rozmiarze szerokości](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Następne kroki

* Kafelki obsługują również kompozytowe renderowanie słupków. Aby dowiedzieć się więcej, zobacz [dokumentację paska złożonego](workbooks-composite-bar.md).
* Aby dowiedzieć się więcej na temat parametrów czasu, takich jak `TimeRange` [Dokumentacja parametrów czasu skoroszytu](workbooks-time.md).