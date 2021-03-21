---
title: Poznawanie zachowań klientów w usłudze Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący korzystania z usługi Application Insights, dzięki któremu dowiesz się, jak klienci używają aplikacji.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: 4f6eeb5265d99a17ed18ace91fa5f2d2f744dbb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100627556"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Korzystanie z usługi Azure Application Insights, aby dowiedzieć się, jak klienci używają aplikacji

Usługa Azure Application Insights zbiera informacje o użyciu, aby pomóc zrozumieć, jak użytkownicy korzystają z aplikacji.  Ten samouczek przeprowadzi Cię przez różne zasoby umożliwiające przeanalizowanie tych informacji.  Poznasz następujące czynności:

> [!div class="checklist"]
> * Analizowanie informacji o użytkownikach uzyskujących dostęp do aplikacji
> * Analizowanie sposobów używania aplikacji przez klientów przy użyciu informacji o sesji
> * Definiowanie lejków, które pozwalają porównać oczekiwaną aktywność użytkowników z ich rzeczywistą aktywnością 
> * Tworzenie skoroszytu w celu skonsolidowania wizualizacji i zapytań w jednym dokumencie
> * Grupowanie podobnych użytkownikom w celu wspólnego analizowania ich
> * Sprawdzanie, którzy użytkownicy wracają do aplikacji
> * Sprawdzanie, jak użytkownicy nawigują w aplikacji


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Pobierz i zainstaluj rozszerzenie [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../app/asp-net.md). 
- [Wyślij dane telemetryczne z aplikacji](../app/usage-overview.md#send-telemetry-from-your-app) w celu dodania niestandardowych zdarzeń/wyświetleń strony
- Wyślij [kontekst użytkownika](../app/usage-send-user-context.md), aby śledzić działania użytkownika w czasie i w pełni wykorzystać możliwości funkcji użycia.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .

## <a name="get-information-about-your-users"></a>Pobieranie informacji o użytkownikach
Panel **Użytkownicy** pozwala analizować na różne sposoby ważne informacje o użytkownikach. Można w nim znaleźć takie informacje jak lokalizacja, z której użytkownicy nawiązują połączenie, szczegóły klienta i obszary aplikacji, do których użytkownicy uzyskują dostęp. 

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.
2. Wybierz pozycję **Użytkownicy** z menu.
3. Widok domyślny przedstawia liczbę unikatowych użytkowników, którzy połączyli się z aplikacją w ciągu ostatnich 24 godzin.  Możesz zmienić przedział czasu i ustawić inne kryteria filtrowania tych informacji.

    ![Konstruktor zapytań](media/tutorial-users/QueryBuilder.png)

6. Kliknij listę rozwijaną **Podczas** i zmień przedział czasu na 7 dni.  Powoduje to zwiększenie ilości danych na różnych wykresach w panelu.

    ![Zmiana zakresu czasu](media/tutorial-users/TimeRange.png)

4. Kliknij listę rozwijaną **Podział według**, aby dodać do wykresu podział według właściwości użytkownika.  Wybierz pozycję **Kraj lub region**.  Wykres zawiera te same dane, ale umożliwia wyświetlenie podziału liczby użytkowników w poszczególnych krajach/regionach.

    ![Wykres dla kraju lub regionu](media/tutorial-users/CountryorRegion.png)

5. Umieść kursor na różnych paskach wykresu i Zauważ, że liczba dla każdego kraju/regionu odzwierciedla tylko przedział czasu reprezentowany przez ten pasek.
6. Spójrz na kolumnę **Szczegółowe informacje** po prawej stronie, która umożliwia analizowanie danych użytkowników.  Zawiera ona takie informacje jak liczba unikatowych sesji w danym okresie i rekordy ze wspólnymi właściwościami, które tworzą znaczącą część danych użytkowników. 

    ![Kolumna szczegółowych informacji](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Analizowanie sesji użytkowników
Panel **Sesje** jest podobny do panelu **Użytkownicy**.  Panel **Użytkownicy** pozwala poznać informacje o użytkownikach, którzy uzyskują dostęp do aplikacji, natomiast panel **Sesje** pomaga w zrozumieniu sposobu używania aplikacji przez użytkowników.  

1. Wybierz pozycję **Sesje** z menu.
2. Spójrz na wykres i zwróć uwagę, że są dostępne te same opcje filtrowania i dzielenia danych co w panelu **Użytkownicy**.

    ![Konstruktor zapytań sesji](media/tutorial-users/SessionsBuilder.png)

3. Okienko **Przykłady tych sesji** po prawej stronie zawiera listę sesji z dużą liczbą zdarzeń.  Są to sesje, które warto przeanalizować.

    ![Przykłady tych sesji](media/tutorial-users/SessionsSample.png)

4. Kliknij jedną z sesji, aby wyświetlić **oś czasu sesji**, która zawiera informacje o każdej akcji w ramach sesji.  Może to ułatwić znajdowanie takich informacji jak sesje z dużą liczbą wyjątków.

    ![Oś czasu sesji](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Grupowanie podobnych użytkowników
**Kohorta** to zbiór użytkowników pogrupowanych według podobnych właściwości.  Kohorty umożliwiają filtrowanie danych w innych panelach, co pozwala analizować poszczególne grupy użytkowników.  Można na przykład analizować tylko użytkowników, którzy ukończyli zakup.

1.  Wybierz pozycję **Kohorty** z menu.
2.  Kliknij przycisk **Nowa**, aby utworzyć nową kohortę.
3.  Wybierz listę rozwijaną **którzy korzystali** i wybierz akcję.  Zostaną uwzględnieni tylko użytkownicy, którzy wykonali tę akcję w przedziale czasu raportu.

    ![Kohorta, która wykonała określone akcje](media/tutorial-users/CohortsDropdown.png)

4.  Wybierz pozycję **Użytkownicy** z menu.
5.  Z listy rozwijanej **Pokaż** wybierz kohortę, którą właśnie utworzono.  Dane wykresu są ograniczone do tych użytkowników.

    ![Kohorta w narzędziu użytkowników](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Porównywanie oczekiwanej aktywności z rzeczywistą aktywnością
Powyższe panele dotyczą wykonanych działań użytkowników aplikacji, natomiast panel **Lejki** zawiera działania, które użytkownicy mają wykonać.  Lejek reprezentuje zestaw kroków w aplikacji i procent użytkowników, którzy przechodzą między krokami.  Można na przykład utworzyć lejek, który pozwala zmierzyć procent użytkowników łączących się z aplikacją i wyszukujących produkt.  Spowoduje to wyświetlenie procentu użytkowników, którzy dodali produkt do koszyka oraz procent użytkowników, którzy ukończyli zakup.

1. Wybierz pozycję **Lejki** z menu, a następnie kliknij pozycję **Nowy**. 

    ![Zrzut ekranu przedstawiający sposób tworzenia nowego lejka.](media/tutorial-users/funnelsnew.png)

2. Wpisz **nazwę lejka**.
3. Utwórz lejek z co najmniej dwoma krokami przez wybranie akcji dla każdego kroku.  Lista akcji została utworzona na podstawie danych użycia zebranych przez usługę Application Insights.

    ![Zrzut ekranu przedstawiający sposób tworzenia kroków w nowym lejku.](media/tutorial-users/funnelsedit.png)

4. Kliknij pozycję **Zapisz**, aby zapisać lejek, a następnie wyświetl jego wyniki.  W oknie po prawej stronie lejka są wyświetlane najczęściej występujące zdarzenia przed pierwszym działaniem i po ostatnim działaniu, aby lepiej zrozumieć tendencje użytkowników dotyczące określonej sekwencji.

    ![Zrzut ekranu przedstawiający wyniki zdarzenia nowo utworzonego lejka.](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Sprawdzanie, którzy klienci wracają
Panel **Przechowywanie** ułatwia sprawdzenie, którzy użytkownicy wrócą do aplikacji.  

1. Wybierz pozycję **Przechowywanie** z menu.
2. Domyślnie przeanalizowane informacje obejmują użytkowników, którzy wykonali dowolną akcję, a następnie wrócili do wykonywania dowolnych akcji.  Możesz zmienić ten filtr, aby uwzględnić dowolne dane, na przykład tylko użytkowników, którzy wrócili po ukończeniu zakupu.

    ![Zrzut ekranu przedstawiający sposób ustawienia filtru przechowywania.](media/tutorial-users/retentionquery.png)

3. Wracający użytkownicy spełniający kryteria są wyświetlani w formie grafiki i tabeli dla różnych czasów trwania.  Typowy wzorzec dotyczy stopniowego spadku liczby wracających użytkowników w czasie.  Nagły spadek między okresami może wymagać uwagi. 

    ![Zrzut ekranu przedstawiający wykres dla użytkowników, którzy są zgodni z kryteriami ustawionymi dla filtru przechowywania.](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analizowanie nawigacji użytkowników
**Przepływ użytkownika** wizualnie przedstawia, jak użytkownicy nawigują między stronami i funkcjami aplikacji.  Dzięki temu można na przykład dowiedzieć się, gdzie użytkownicy zwykle przechodzą z określonej strony, jak zazwyczaj zamykają aplikację oraz czy istnieją akcje powtarzane regularnie.

1.  Wybierz pozycję **Przepływy użytkowników** z menu.
2.  Kliknij pozycję **Nowy**, aby utworzyć nowy przepływ użytkownika, a następnie kliknij pozycję **Edytuj**, aby edytować jego szczegóły.
3.  Zwiększ **zakres czasu** do 7 dni, a następnie wybierz zdarzenie początkowe.  Przepływ będzie śledzić sesje użytkownika, które rozpoczynają się od tego zdarzenia.

    ![Zrzut ekranu przedstawiający sposób tworzenia nowego przepływu użytkownika.](media/tutorial-users/flowsedit.png)

4.  Zostanie wyświetlony przepływ użytkownika wraz ze ścieżkami użytkowników i liczbami ich sesji.  Niebieskie linie wskazują akcję, którą użytkownik wykonał po bieżącej akcji.  Czerwona linia wskazuje koniec sesji użytkownika.

    ![Zrzut ekranu przedstawiający wyświetlanie ścieżek użytkownika i liczby sesji dla przepływu użytkownika.](media/tutorial-users/flows.png)

5.  Aby usunąć zdarzenie z przepływu, kliknij pozycję **x** w rogu akcji, a następnie kliknij pozycję **Utwórz wykres**.  Wykres zostanie narysowany ponownie z uwzględnieniem wystąpień usuniętego zdarzenia.  Kliknij pozycję **Edytuj**. Spowoduje to dodanie zdarzenia do listy **Wykluczone zdarzenia**.

    ![Zrzut ekranu przedstawiający listę wykluczonych zdarzeń dla przepływu użytkownika.](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Konsolidowanie danych użycia
**Skoroszyty** łączą wizualizacje danych, zapytania analityczne i tekst w interakcyjnych dokumentach.  Umożliwiają one grupowanie typowych danych użycia, konsolidowanie informacji dotyczących określonego zdarzenia oraz raportowanie zespołowi użycia aplikacji.

1.  Wybierz pozycję **Skoroszyty** z menu.
2.  Kliknij pozycję **Nowy**, aby utworzyć nowy skoroszyt.
3.  Zostanie wyświetlone zapytanie zawierające wszystkie dane użycia z ostatniego dnia w formie wykresu słupkowego.  Możesz użyć tego zapytania, ręcznie je edytować lub kliknąć pozycję **Przykładowe zapytania**, aby wybrać inne przydatne zapytania.

    ![Zrzut ekranu przedstawiający listę przykładowych zapytań, których można użyć.](media/tutorial-users/samplequeries.png)

4.  Kliknij przycisk **Zakończono edytowanie**.
5.  Kliknij przycisk **Edytuj** w górnym okienku, aby edytować tekst u góry skoroszytu.  Jest on sformatowany za pomocą języka Markdown.

    ![Zrzut ekranu przedstawiający sposób edycji tekstu w górnej części skoroszytu.](media/tutorial-users/markdown.png)

6.  Kliknij pozycję **Dodaj użytkowników**, aby dodać wykres z informacjami o użytkowniku.  Jeśli chcesz, edytuj szczegóły wykresu, a następnie kliknij pozycję **Zakończono edytowanie**, aby zapisać wykres.


## <a name="next-steps"></a>Następne kroki
Wiesz już, jak analizować użytkowników, więc możesz przejść do następnego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe pulpity nawigacyjne, które łączą te informacje z innymi przydatnymi danymi dotyczącymi aplikacji.

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych pulpitów nawigacyjnych](./tutorial-app-dashboards.md)

