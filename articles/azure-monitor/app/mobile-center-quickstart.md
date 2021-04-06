---
title: Monitoruj aplikacje mobilne przy użyciu Azure Monitor Application Insights
description: Zawiera instrukcje umożliwiające szybkie skonfigurowanie aplikacji mobilnej do monitorowania za pomocą Azure Monitor Application Insights i App Center
ms.subservice: application-insights
ms.topic: quickstart
author: lgayhardt
ms.author: lagayhar
ms.date: 06/26/2019
ms.reviewer: daviste
ms.custom: mvc
ms.openlocfilehash: 0c53569ea50d91bbd703197d1a4c3546e10e04ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100628859"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Rozpoczęcie analizowania aplikacji mobilnej za pomocą Centrum aplikacji i usługi Application Insights

Ten przewodnik Szybki start przeprowadzi Cię przez proces nawiązywania połączenia między wystąpieniem Centrum aplikacji dla Twojej aplikacji a usługą Application Insights. Korzystając z usługi Application Insights, możesz wykonywać zapytania, segmentować, filtrować i analizować dane telemetryczne za pomocą bardziej zaawansowanych narzędzi niż dostępne w usłudze [Analiza](/mobile-center/analytics/) Centrum aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Subskrypcja platformy Azure.
- Aplikacja dla platform iOS, Android, Xamarin lub React Native albo platformy uniwersalnej systemu Windows.
 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-up-with-app-center"></a>Zarejestruj się w usłudze App Center
Aby rozpocząć, Utwórz konto i [zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## <a name="onboard-to-app-center"></a>Dodawanie do Centrum aplikacji

Zanim użyjesz usługi Application Insights z aplikacją mobilną, musisz dodać aplikację do [Centrum aplikacji](/mobile-center/). Usługa Application Insights nie odbiera danych telemetrycznych bezpośrednio z aplikacji mobilnej. Zamiast tego aplikacja wysyła dane telemetryczne zdarzeń niestandardowych do Centrum aplikacji. Następnie Centrum aplikacji w miarę odbierania zdarzeń niestandardowych stale eksportuje ich kopie do usługi Application Insights. (Ta wartość nie dotyczy [zestawu SDK Application INSIGHTS js](https://github.com/Microsoft/ApplicationInsights-JS) ani [natywnej wtyczki reagującej](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-react-native) , w której dane telemetryczne są wysyłane bezpośrednio do Application Insights.)

Aby dodać aplikację, skorzystaj z opcji szybkiego startu Centrum aplikacji dla każdej platformy obsługiwanej przez Twoją aplikację. Utwórz oddzielne wystąpienie Centrum aplikacji dla każdej platformy:

* System [iOS](/mobile-center/sdk/getting-started/ios).
* System [Android](/mobile-center/sdk/getting-started/android).
* Platforma [Xamarin](/mobile-center/sdk/getting-started/xamarin).
* [Uniwersalne okna](/mobile-center/sdk/getting-started/uwp).
* [Reagowanie natywne](/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Śledzenie zdarzeń w aplikacji

Po dodaniu aplikacji do Centrum aplikacji musisz zmodyfikować ją w celu wysyłania danych telemetrycznych zdarzeń niestandardowych przy użyciu zestawu SDK Centrum aplikacji. Zdarzenia niestandardowe są jedynym typem danych telemetrycznych Centrum aplikacji, które są eksportowane do usługi Application Insights.

Aby wysyłać zdarzenia niestandardowe z aplikacji systemu iOS, użyj metody `trackEvent` lub `trackEvent:withProperties` z zestawu SDK Centrum aplikacji. [Dowiedz się więcej na temat śledzenia zdarzeń z aplikacji dla systemu iOS.](/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Aby wysyłać zdarzenia niestandardowe z aplikacji dla systemu Android, użyj metody `trackEvent` z zestawu SDK Centrum aplikacji. [Dowiedz się więcej na temat śledzenia zdarzeń z aplikacji dla systemu Android.](/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Aby wysyłać zdarzenia niestandardowe z innych platform aplikacji, użyj metody `trackEvent` z odpowiednich zestawów SDK Centrum aplikacji.

Aby upewnić się, że zdarzenia niestandardowe są otrzymywane, przejdź do karty **Zdarzenia** w sekcji **Analiza** w Centrum aplikacji. Od momentu wysłania zdarzeń z aplikacji do momentu ich wyświetlenia może upłynąć kilka minut.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Gdy aplikacja wysyła zdarzenia niestandardowe i są one odbierane przez Centrum aplikacji, musisz utworzyć zasób usługi Application Insights typu Centrum aplikacji w witrynie Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**  >  **Narzędzia deweloperskie**  >  **Application Insights**.

    > [!NOTE]
    > Jeśli tworzysz zasób Application Insights, możesz dowiedzieć się więcej, odwiedzając dokument [tworzenie Application Insights](../app/create-new-resource.md) .

    Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Jakaś globalnie unikatowa wartość, na przykład „moja_aplikacja_iOS” | Nazwa identyfikująca monitorowaną aplikację |
     | **Grupa zasobów**     | Nowa grupa zasobów lub istniejąca grupa zasobów z menu | Grupa zasobów, w której ma zostać utworzony nowy zasób usługi Application Insights |
   | **Lokalizacja** | Lokalizacja z menu | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

3. Kliknij pozycję **Utwórz**.

Jeśli aplikacja obsługuje wiele platform (iOS, Android itp.), najlepiej utworzyć oddzielne zasoby usługi Application Insights, po jednym dla każdej platformy.

## <a name="export-to-application-insights"></a>Eksportowanie do usługi Application Insights

W nowym zasobie Application Insights na stronie **Przegląd** . Skopiuj klucz Instrumentacji z zasobu.

W [App Center](https://appcenter.ms/) wystąpieniu aplikacji:

1. Na stronie **Ustawienia** kliknij pozycję **Eksportuj**.
2. Wybierz pozycję **Nowy eksport**, wybierz pozycję **Application Insights**, a następnie kliknij pozycję **Dostosuj**.
3. Wklej w oknie klucz instrumentacji usługi Application Insights.
4. Wyraź zgodę na zwiększenie użycia subskrypcji platformy Azure zawierającej zasób usługi Application Insights. Każdy zasób usługi Application Insights jest bezpłatny dla pierwszego 1 GB danych odebranych w miesiącu. [Dowiedz się więcej na temat cennika usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)

Pamiętaj, aby powtórzyć ten proces dla każdej platformy obsługiwanej przez aplikację.

Po skonfigurowaniu [eksportu](/mobile-center/analytics/export) każde zdarzenie niestandardowe odebrane przez Centrum aplikacji jest kopiowane do usługi Application Insights. Zanim zdarzenie dotrze do usługi Application Insights, może upłynąć kilka minut, dlatego jeśli nie jest ono wyświetlane natychmiast, zaczekaj chwilę przed dalszą diagnostyką.

Aby przy pierwszym połączeniu dostarczyć większą ilość danych, do usługi Application Insights automatycznie eksportowane są zdarzenia niestandardowe z Centrum aplikacji z ostatnich 48 godzin.

## <a name="start-monitoring-your-app"></a>Rozpoczęcie monitorowania aplikacji

Usługa Application Insights umożliwia wykonywanie zapytań, segmentowanie, filtrowanie i analizowanie danych telemetrycznych zdarzeń niestandardowych z aplikacji w sposób wykraczający poza możliwości narzędzi analitycznych dostępnych w Centrum aplikacji.

1. **Wykonaj zapytanie względem danych telemetrycznych zdarzeń niestandardowych.** Na stronie **przegląd** Application Insights wybierz pozycję **dzienniki (analiza)**.

   Zostanie otwarty portal dzienników Application Insights (analiza) skojarzony z zasobem Application Insights. Portal dzienniki (analiza) umożliwia bezpośrednie wysyłanie zapytań do danych przy użyciu języka zapytań Log Analytics, dzięki czemu możesz zadawać arbitralnie złożone pytania dotyczące aplikacji i jej użytkowników.
   
   Otwórz nową kartę w portalu dzienniki (analiza), a następnie wklej ją w poniższym zapytaniu. W jego wyniku zostanie zwrócona liczba unikatowych użytkowników, którzy wysłali poszczególne zdarzenia niestandardowe z aplikacji w ciągu ostatnich 24 godzin, posortowana według tych różnych liczb.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Portal dzienników (analiza)](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Wybierz zapytanie, klikając w dowolnym miejscu zapytania w edytorze tekstów.
   2. Następnie kliknij pozycję **Przejdź**, aby wykonać zapytanie. 

   Dowiedz się więcej o [portalu Analiza usługi Application Insights](../logs/log-query-overview.md) i [języku zapytań Log Analytics](/azure/data-explorer/kusto/query/).


2. **Przeprowadź segmentację i filtrowanie danych telemetrycznych zdarzeń niestandardowych.** Na stronie **Przegląd** usługi Application Insights wybierz w spisie treści pozycję **Użytkownicy**.

   ![Ikona narzędzia Użytkownicy](./media/mobile-center-quickstart/users-icon-001.png)

   Narzędzie Użytkownicy wyświetla liczbę użytkowników aplikacji, którzy kliknęli określone przyciski, odwiedzili określone ekrany lub wykonali jakąkolwiek inną akcję śledzoną jako zdarzenie za pomocą zestawu SDK Centrum aplikacji. Jeśli szukasz sposobu na segmentowanie i filtrowanie zdarzeń Centrum aplikacji, narzędzie Użytkownicy jest doskonałym wyborem.

   ![Narzędzie Użytkownicy](./media/mobile-center-quickstart/users-001.png) 

   Na przykład możesz podzielić użycie według lokalizacji geograficznej, wybierając pozycję **Kraj lub region** w menu rozwijanym **Podziel wg**.

3. **Przeprowadź analizę wzorców konwersji, przechowywania i nawigacji w aplikacji.** Na stronie **Przegląd** usługi Application Insights wybierz w spisie treści pozycję **Przepływy użytkownika**.

   ![Narzędzie Przepływy użytkownika](./media/mobile-center-quickstart/user-flows-001.png)

   Narzędzie Przepływy użytkownika wizualizuje zdarzenia, które zostały wysłane przez użytkowników po jakimś zdarzeniu początkowym. Przydaje się ono do uzyskania ogólnego obrazu sposobu, w jaki użytkownicy nawigują w aplikacjach. Pozwala również ujawnić miejsca, w których wielu użytkowników wychodzi z aplikacji lub wciąż powtarza te same akcje.

   Oprócz narzędzia Przepływy użytkownika usługa Application Insights zawiera także kilka innych narzędzi do analizy zachowania użytkowników, które pozwalają uzyskać odpowiedzi na konkretne pytania:

   * **Lejki** — służy do analizowania i monitorowania współczynników konwersji.
   * **Przechowywanie** — służy do analizowania skuteczności zatrzymywania użytkowników w aplikacji na przestrzeni czasu.
   * **Skoroszyty** — służy do łączenia wizualizacji i tekstu w raporcie, który można udostępniać.
   * **Kohorty** — służy do nadawania nazw i zapisywania określonych grup użytkowników lub zdarzeń, aby można było łatwo odwoływać się do nich z innych narzędzi analitycznych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz dalej używać usługi Application Insights z Centrum aplikacji, wyłącz eksportowanie w Centrum aplikacji i usuń zasób usługi Application Insights. Zapobiegnie to naliczaniu dalszych opłat za ten zasób przez usługę Application Insights.

Aby wyłączyć eksportowanie w Centrum aplikacji:

1. W Centrum aplikacji wybierz pozycję **Ustawienia**, a następnie **Eksportuj**.
2. Kliknij eksport usługi Application Insights, który chcesz usunąć, a następnie kliknij pozycję **Usuń eksport** u dołu i potwierdź.

Aby usunąć zasób usługi Application Insights:

1. W menu po lewej stronie portalu Azure kliknij pozycję **Grupy zasobów**, a następnie wybierz grupę zasobów, w której został utworzony zasób usługi Application Insights.
2. Otwórz zasób usługi Application Insights, który chcesz usunąć. Następnie kliknij pozycję **Usuń** w menu u góry zasobu i potwierdź. Spowoduje to trwałe usunięcie kopii danych, które zostały wyeksportowane do usługi Application Insights.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie używania aplikacji przez klientów](../app/usage-overview.md)