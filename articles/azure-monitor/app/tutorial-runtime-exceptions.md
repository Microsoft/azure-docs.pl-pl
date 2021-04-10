---
title: Diagnozowanie wyjątków czasu wykonywania za pomocą usługi Azure Application Insights | Microsoft Docs
description: Samouczek omawiający znajdowanie i diagnozowanie wyjątków czasu wykonywania w aplikacji za pomocą usługi Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 1d8dfb3eaf3ea1ab901df4bef62210d28e02c3a4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383155"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Znajdowanie i diagnozowanie wyjątków czasu wykonywania za pomocą usługi Azure Application Insights

Usługa Azure Application Insights gromadzi dane telemetryczne z Twojej aplikacji, aby identyfikować i diagnozować wyjątki czasu wykonywania.  W tym samouczku przedstawiono ten proces dla Twojej aplikacji.  Omawiane kwestie:

> [!div class="checklist"]
> * Modyfikowanie projektu w celu włączenia śledzenia wyjątków
> * Identyfikowanie wyjątków dla różnych składników aplikacji
> * Wyświetlanie szczegółów wyjątku
> * Pobieranie migawki wyjątku do programu Visual Studio w celu debugowania
> * Analizowanie szczegółów nieudanych żądań przy użyciu języka zapytań
> * Tworzenie nowego elementu roboczego w celu poprawienia błędnego kodu


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Pobierz i zainstaluj rozszerzenie [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger).
- Włącz rozszerzenie [Visual Studio Snapshot Debugger](../app/snapshot-debugger.md).
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../app/asp-net.md). 
- Samouczek śledzi identyfikowanie wyjątków w Twojej aplikacji, dlatego zmodyfikuj kod w środowisku deweloperskim lub testowym, aby wygenerować wyjątek. 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .


## <a name="analyze-failures"></a>Analizowanie błędów
Usługa Application Insights zbiera wszelkie błędy z Twojej aplikacji i pozwala wyświetlać ich częstotliwość dla różnych operacji, aby pomóc w skoncentrowaniu wysiłków na tych z nich, które niosą za sobą największe skutki.  Następnie można przechodzić do szczegółów tych błędów, aby odkryć ich główną przyczynę.   

1. Wybierz pozycję **Application Insights**, a następnie swoją subskrypcję.  
2. Aby otworzyć panel **Błędy**, wybierz pozycję **Błędy** z menu **Zbadaj** albo kliknij wykres **Żądania zakończone niepowodzeniem**.

    ![Żądania zakończone niepowodzeniem](media/tutorial-runtime-exceptions/failed-requests.png)

3. Panel **Żądania zakończone niepowodzeniem** pokazuje liczbę żądań zakończonych niepowodzeniem oraz liczbę użytkowników, których to dotyczy, dla każdej operacji aplikacji.  Sortując te informacje według użytkowników, można znaleźć te błędy, które mają największy wpływ na użytkowników.  W tym przykładzie dobrymi kandydatami do przyjrzenia się bliżej są operacje **GET Employees/Create** i **GET Customers/Details** z powodu ich dużej liczby błędów i dotkniętych użytkowników.  Po wybraniu operacji na prawym panelu pokazywane są dalsze informacje na temat tej operacji.

    ![Panel Żądania zakończone niepowodzeniem](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Zmniejsz przedział czasu, aby przybliżyć okres, w którym współczynnik błędów wykazuje największy wzrost.

    ![Okno Żądania zakończone niepowodzeniem](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. Zapoznaj się z powiązanymi przykładami, klikając przycisk z liczbą odfiltrowanych wyników. „Sugerowane” przykłady zawierają powiązane dane telemetryczne wszystkich składników, nawet jeśli w którymś z nich zastosowano próbkowanie. Kliknij wynik wyszukiwania, aby wyświetlić szczegóły niepowodzenia.

    ![Przykłady żądań zakończonych niepowodzeniem](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. Szczegóły żądań zakończonych niepowodzeniem zawierają wykres Gantta, który informuje, że podczas tej transakcji wystąpiły dwa niepowodzenia zależności, co odpowiadało ponad 50% całkowitego czasu transakcji. To środowisko zawiera wszystkie dane telemetryczne wszystkich składników rozproszonej aplikacji, które są powiązane z tym identyfikatorem operacji. [Dowiedz się więcej na temat nowego środowiska](../app/transaction-diagnostics.md). Możesz wybrać dowolny element, aby wyświetlić jego szczegóły po prawej stronie. 

    ![Szczegóły żądania zakończonego niepowodzeniem](media/tutorial-runtime-exceptions/failed-request-details.png)

7. W szczegółach operacji widać również wyjątek FormatException, który wydaje się być przyczyną błędu.  Zobaczysz, że przyczyną jest nieprawidłowy kod pocztowy. Możesz otworzyć migawkę debugowania, aby wyświetlić informacje dotyczące debugowania na poziomie kodu w programie Visual Studio.

    ![Szczegóły wyjątku](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Identyfikowanie błędnego kodu
Rozszerzenie Snapshot Debugger zbiera migawki najczęściej występujących wyjątków w Twojej aplikacji, aby pomóc Ci w diagnozowaniu głównej przyczyny problemu w środowisku produkcyjnym.  Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Później istnieje możliwość debugowania kodu źródłowego przez pobranie migawki i otwarcie jej w programie Visual Studio 2019 Enterprise.

1. We właściwościach wyjątku kliknij pozycję **Otwórz migawkę debugowania**.
2. Zostanie otwarty panel **Migawka debugowania** ze stosem wywołań dla żądania.  Kliknij dowolną metodę, aby wyświetlić wartości wszystkich zmiennych lokalnych w momencie żądania.  Zaczynając od pierwszej metody w tym przykładzie, widać zmienne lokalne bez wartości.

    ![Migawka debugowania](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. Pierwsze wywołanie z prawidłowymi wartościami to **ValidZipCode** i widać, że w kodzie pocztowym podano litery, których nie można przekształcić na liczbę całkowitą.  Wygląda to na błąd w kodzie, który trzeba poprawić.

    ![Zrzut ekranu pokazujący błąd w kodzie, który należy poprawić.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Następnie można pobrać tę migawkę do programu Visual Studio, w którym można znaleźć faktyczny kod do poprawienia. Aby to zrobić, kliknij pozycję **Pobierz migawkę**.
5. Migawka zostanie załadowana do programu Visual Studio.
6. Teraz możesz uruchomić sesję debugowania w programie Visual Studio Enterprise, która szybko zidentyfikuje wiersz kodu będący przyczyną wyjątku.

    ![Wyjątek w kodzie](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Korzystanie z danych analizy
Wszystkie dane zbierane przez usługę Application Insights są przechowywane w usłudze Azure Log Analytics, która oferuje rozbudowany język zapytań umożliwiający analizowanie danych na różne sposoby.  Możemy użyć tych danych do przeanalizowania żądań, które spowodowały badany wyjątek. 

1. Kliknij informacje CodeLens nad kodem, aby wyświetlić dane telemetryczne dostarczane przez usługę Application Insights.

    ![Kod](media/tutorial-runtime-exceptions/codelens.png)

1. Kliknij pozycję **Analiza wpływu**, aby otworzyć okno Application Insights — analiza.  Jest ono wypełnione kilkoma zapytaniami udostępniającymi szczegóły żądań zakończonych niepowodzeniem, takie jak użytkownicy, przeglądarki i regiony dotknięte niepowodzeniem.<br><br>![Zrzut ekranu przedstawia okno Application Insights, które zawiera kilka zapytań.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Dodawanie elementu roboczego
Jeśli usługa Application Insights zostanie połączona z systemem śledzenia, takim jak Azure DevOps lub GitHub, element roboczy można utworzyć bezpośrednio z usługi Application Insights.

1. Wróć do panelu **Właściwości wyjątku** w usłudze Application Insights.
2. Kliknij pozycję **Nowy element roboczy**.
3. Zostanie otwarty panel **Nowy element roboczy** z już wypełnionymi informacjami na temat wyjątku.  Przed jego zapisaniem możesz dodać dowolne informacje dodatkowe.

    ![Nowy element roboczy](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak identyfikować wyjątki czasu wykonywania, przejdź do następnego samouczka, aby dowiedzieć się, jak identyfikować i diagnozować problemy z wydajnością.

> [!div class="nextstepaction"]
> [Identyfikowanie problemów z wydajnością](./tutorial-performance.md)

