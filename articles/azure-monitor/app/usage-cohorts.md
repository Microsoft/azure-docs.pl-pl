---
title: Kohorty użycia platformy Azure Application Insights | Microsoft Docs
description: Analizowanie różnych zestawów lub użytkowników, sesji, zdarzeń lub operacji, które mają coś wspólnego
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c35356951560b11a605334aba4c26dbc38086de
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583554"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorty

Kohorta to zbiór użytkowników, sesji, zdarzeń lub operacji, które mają coś wspólnego. W usłudze Azure Application Insights kohorty są definiowane przez zapytanie analityczne. W przypadkach, gdy konieczne jest wielokrotne analizowanie określonego zestawu użytkowników lub zdarzeń, kohorty może zapewnić większą elastyczność, aby dokładnie określić odpowiedni zestaw.

![Okienko kohorty](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorty a filtry podstawowe

Kohorty są używane w sposób podobny do filtrów. Definicje kohorty są tworzone na podstawie niestandardowych zapytań analitycznych, dzięki czemu są znacznie bardziej dostosowywalne i złożone. W przeciwieństwie do filtrów, można zapisać kohorty, tak aby inni członkowie zespołu mogli je ponownie wykorzystać.

Możesz zdefiniować kohorta użytkowników, którzy mają wszystkie próby nowej funkcji w aplikacji. Tę kohortaę można zapisać w zasobie Application Insights. W przyszłości można łatwo analizować tę zapisaną grupę określonych użytkowników.

> [!NOTE]
> Po ich utworzeniu kohorty są dostępne w narzędziach użytkownicy, sesje, zdarzenia i Przepływy użytkownika.

## <a name="example-engaged-users"></a>Przykład: zazaangażowani użytkownicy

Twój zespół definiuje użytkownika zaangażowanego jako każdego, kto korzysta z aplikacji pięć lub więcej razy w danym miesiącu. W tej sekcji definiujesz kohorta tych użytkowników.

1. Otwórz narzędzie kohorty.

2. Wybierz kartę **Galeria szablonów** . Zostanie wyświetlona kolekcja szablonów dla różnych kohorty.

3. Wybierz pozycję **zaangażowani Użytkownicy — według dni**.

    Dla tego kohorta istnieją trzy parametry:
    * **Działania**, w których można wybrać zdarzenia i wyświetlenia stron jako "użycie".
    * **Okres**, definicja miesiąca.
    * **UsedAtLeastCustom**, liczba przypadków, w których użytkownicy będą musieli użyć elementu w okresie do zliczenia jako zaangażowany.

4. Zmień wartość **UsedAtLeastCustom** na **5 dni** i pozostaw domyślny **okres** 28 dni.

    ![Zazaangażowani użytkownicy](./media/usage-cohorts/003.png)

    Teraz ten kohorta reprezentuje wszystkie identyfikatory użytkowników wysłane z dowolnym zdarzeniem niestandardowym lub wyświetleniem strony 5 osobnych dni w ciągu ostatnich 28.

5. Wybierz pozycję **Zapisz**.

   > [!TIP]
   > Nadaj nazwę kohorta, na przykład "Użytkownicy zaangażowani" (5 dni). Zapisz ją w obszarze "Moje raporty" lub "udostępnione raporty", w zależności od tego, czy chcesz, aby kohorta te osoby, które mają dostęp do tego zasobu Application Insights.

6. Wybierz pozycję **z powrotem do galerii**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Co można zrobić za pomocą tego kohorta?

Otwórz narzędzie użytkownicy. W polu listy rozwijanej **Pokaż** wybierz kohorta utworzony w obszarze **Użytkownicy, do których należysz**.

Teraz narzędzie użytkownicy jest filtrowane do kohorta użytkowników:

![Okienko użytkownicy przefiltrowane do określonego kohorta](./media/usage-cohorts/004.png)

Oto kilka istotnych kwestii, które należy zauważyć:

* Nie można utworzyć tego zestawu za poorednictwem zwykłych filtrów. Logika daty jest bardziej zaawansowana.
* Można dodatkowo filtrować ten kohorta za pomocą zwykłych filtrów w narzędziu użytkownicy. Mimo że kohorta jest zdefiniowany w 28-dniowym systemie Windows, można nadal dostosować zakres czasu w narzędziu użytkownicy do 30, 60 lub 90 dni.

Te filtry obsługują bardziej zaawansowane pytania, których nie można wyrazić za pomocą konstruktora zapytań. Przykładem są _osoby, które były zaangażowane w ciągu ostatnich 28 dni. Jak działają te same osoby w ciągu ostatnich 60 dni?_

## <a name="example-events-cohort"></a>Przykład: zdarzenia kohorta

Możesz również kohorty zdarzenia. W tej sekcji definiujesz kohorta zdarzeń i wyświetleń stron. Następnie zobaczysz, jak korzystać z nich z innych narzędzi. Ten kohorta może definiować zestaw zdarzeń, które zespół traktuje _aktywne użycie_ lub zestaw związany z określoną nową funkcją.

1. Otwórz narzędzie kohorty.

2. Wybierz kartę **Galeria szablonów** . Zostanie wyświetlona kolekcja szablonów dla różnych kohorty.

3. Wybierz **Selektor zdarzeń**.

    ![Zrzut ekranu przedstawiający selektor zdarzeń](./media/usage-cohorts/006.png)

4. W polu listy rozwijanej **działania** wybierz zdarzenia, które mają znajdować się w kohorta.

5. Zapisz kohorta i nadaj mu nazwę.

## <a name="example-active-users-where-you-modify-a-query"></a>Przykład: aktywni użytkownicy, którzy modyfikują zapytanie

Poprzednie dwie kohorty zostały zdefiniowane przy użyciu pól rozwijanych. Ale można także definiować kohorty za pomocą zapytań analitycznych w celu uzyskania całkowitej elastyczności. Aby dowiedzieć się, jak utworzyć kohorta użytkowników ze Zjednoczonego Królestwa.

![Animowany obraz idący poprzez użycie narzędzia kohorty](./media/usage-cohorts/cohorts0001.gif)

1. Otwórz narzędzie kohorty, wybierz kartę **Galeria szablonów** , a następnie wybierz pozycję **puste użytkownicy kohorta**.

    ![Kohorta pustej użytkowników](./media/usage-cohorts/001.png)

    Istnieją trzy sekcje:
   * Sekcja tekstowa promocji, w której można opisać kohorta bardziej szczegółowo dla innych członków zespołu.

   * Sekcja parametrów, w której można tworzyć własne parametry, takie jak **działania** i inne pola rozwijane z poprzednich dwóch przykładów.

   * Sekcja zapytania, w której definiujesz kohorta przy użyciu zapytania analizy.

     W sekcji zapytania [napiszesz zapytanie analityczne](/azure/kusto/query). Zapytanie wybiera określony zestaw wierszy opisujących kohorta, które chcesz zdefiniować. Narzędzie kohorty następnie niejawnie dodaje "| Podsumuj według klauzuli user_Id "do zapytania. Te dane są wyświetlane poniżej zapytania w tabeli, dzięki czemu można upewnić się, że zapytanie zwraca wyniki.

     > [!NOTE]
     > Jeśli nie widzisz zapytania, spróbuj zmienić rozmiar sekcji, aby uczynić ją wyższą i ujawnić zapytanie. Animowany plik GIF na początku tej sekcji ilustruje zachowanie zmiany rozmiarów.

2. Skopiuj i wklej następujący tekst do edytora zapytań:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Wybierz pozycję **Uruchom zapytanie**. Jeśli nie widzisz identyfikatorów użytkowników w tabeli, przejdź do kraju/regionu, w którym Twoja aplikacja ma użytkowników.

4. Zapisz i nazwij kohorta.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

_Kohorta użytkowników z określonego kraju/regionu. Gdy porównuję ten kohorta w narzędziu użytkownicy, aby po prostu ustawić filtr w tym kraju/regionie, widzę różne wyniki. Zalet?_

Kohorty i filtry są różne. Załóżmy, że masz kohorta użytkowników ze Zjednoczonego Królestwa (zdefiniowane jak w poprzednim przykładzie), a następnie porównasz jej wyniki w celu ustawienia filtru "kraj lub region = Zjednoczone Królestwo".

* Wersja kohorta pokazuje wszystkie zdarzenia użytkowników, którzy otrzymali jedno lub więcej wydarzeń z Zjednoczonego Królestwa w bieżącym przedziale czasu. Jeśli podzieli według kraju lub regionu, najkorzystniej widzisz wiele krajów i regionów.
* Wersja filtrów zawiera tylko zdarzenia ze Zjednoczonego Królestwa. Ale jeśli podzielę według kraju lub regionu, zobaczysz tylko Zjednoczone Królestwo.

## <a name="learn-more"></a>Więcej tutaj

* [Język zapytań usługi Analytics](../logs/log-analytics-tutorial.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
* [Przepływy użytkowników](usage-flows.md)
* [Przegląd użycia](usage-overview.md)