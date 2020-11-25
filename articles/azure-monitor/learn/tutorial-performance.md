---
title: Diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights | Microsoft Docs
description: Samouczek omawiający znajdowanie i diagnozowanie problemów z wydajnością aplikacji za pomocą usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: df7cfff7d5bf1b89f88105f79c072d1d7e731b31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005490"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Znajdowanie i diagnozowanie problemów z wydajnością za pomocą usługi Azure Application Insights

Usługa Azure Application Insights gromadzi dane telemetryczne z Twojej aplikacji, aby pomóc w analizie jej działania i wydajności.  Tymi informacjami można się posłużyć przy identyfikowaniu stwierdzonych problemów albo identyfikowaniu usprawnień do aplikacji, które miałyby największy wpływ na użytkowników.  Ten samouczek przedstawia proces analizowania wydajności składników serwera aplikacji i perspektywy klienta.  Omawiane kwestie:

> [!div class="checklist"]
> * Identyfikowanie wydajności operacji po stronie serwera
> * Analizowanie operacji serwera w celu określenia głównej przyczyny niskiej wydajności
> * Identyfikowanie najwolniejszych operacji po stronie klienta
> * Analizowanie szczegółów wyświetleń stron przy użyciu języka zapytań


## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi obciążeniami:
    - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
    - Tworzenie aplikacji na platformie Azure
- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../app/asp-net.md).
- [Włącz profiler usługi Application Insights](../app/profiler.md#installation) dla swojej aplikacji.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .

## <a name="identify-slow-server-operations"></a>Identyfikowanie wolnych operacji serwera
Usługa Application Insights zbiera informacje o wydajności różnych operacji w aplikacji. Identyfikując operacje o najdłuższym czasie trwania, możesz zdiagnozować potencjalne problemy albo najlepiej ukierunkować trwające prace programistyczne w celu podniesienia ogólnej wydajności aplikacji.

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  
1. Aby otworzyć panel **Wydajność**, wybierz pozycję **Wydajność** z menu **Zbadaj** albo kliknij wykres **Czas odpowiedzi serwera**.

    ![Wydajność](media/tutorial-performance/1-overview.png)

2. Panel **Wydajność** przedstawia wydajność i średni czas trwania każdej operacji dla aplikacji.  Te informacje mogą posłużyć do zidentyfikowania tych operacji, które mają największy wpływ na użytkowników. W tym przykładzie dobrymi kandydatami do przyjrzenia się bliżej są operacje **GET Customers/Details** i **GET Home/Index** z powodu ich względnie długiego czasu trwania i względnie dużej liczby wywołań.  Inne operacje mogą mieć dłuższy czas trwania, ale są rzadko wywoływane, więc efekt ich poprawienia będzie niewielki.  

    ![Panel serwera wydajności](media/tutorial-performance/2-server-operations.png)

3. Wykres przedstawia obecnie średni czas trwania wybranych operacji w czasie. Możesz przełączyć się na 95. percentyl, aby znaleźć problemy z wydajnością. Dodaj operacje, które Cię interesują, przypinając je do wykresu.  To pokazuje, że istnieją wzrosty wartości warte zbadania.  Wyizoluj je jeszcze bardziej, zmniejszając przedział czasu wykresu.

    ![Przypinanie wartości](media/tutorial-performance/3-server-operations-95th.png)

4.  Panel wydajności po prawej stronie pokazuje rozkład czasów trwania różnych żądań dla wybranej operacji.  Zmniejsz okno, aby rozpocząć w okolicy 95. percentyla. Z karty ze szczegółowymi informacjami „3 najważniejsze zależności” można szybko odczytać, że zależności zewnętrzne najprawdopodobniej wpływają na spowolnienie transakcji.  Kliknij przycisk z liczbą próbek, aby wyświetlić listę próbek. Następnie możesz wybrać dowolną próbkę, aby wyświetlić szczegóły transakcji.

5.  Na pierwszy rzut oka zobaczysz, że wywołanie tabeli Fabrikamaccount Azure Table najbardziej wpływa na całkowity czas trwania transakcji. Zobaczysz też, że wyjątek spowodował jego niepowodzenia. Możesz kliknąć dowolną pozycję na liście, aby wyświetlić jej szczegóły po prawej stronie. [Dowiedz się więcej o środowisku diagnostyki transakcji](../app/transaction-diagnostics.md)

    ![Szczegóły operacji na końcu](media/tutorial-performance/4-end-to-end.png)
    

6.  [**Profiler**](../app/profiler-overview.md) pomaga lepiej korzystać z diagnostyki na poziomie kodu, pokazując rzeczywisty kod, który został uruchomiony dla operacji, oraz czas wymagany dla każdego kroku. Niektóre operacje mogą nie mieć śladu, ponieważ profiler jest uruchamiany okresowo.  Z upływem czasu coraz więcej operacji powinno mieć ślady.  Aby uruchomić profiler dla operacji, kliknij pozycję **Ślady narzędzia Profiler**.
5.  Ślad pokazuje indywidualne zdarzenia dla każdej operacji, więc można zdiagnozować główną przyczynę obecnego czasu trwania całej operacji.  Kliknij jeden z przykładów u góry, które mają najdłuższy czas trwania.
6.  Kliknij pozycję **ścieżka gorąca** , aby wyróżnić konkretną ścieżkę zdarzeń, które najbardziej przyczyniają się do łącznego czasu trwania operacji.  W tym przykładzie widać, że najwolniejsze wywołanie pochodzi z metody *FabrikamFiberAzureStorage.GetStorageTableData*. Częścią zabierającą najwięcej czasu jest metoda *CloudTable.CreateIfNotExist*. Jeśli ten wiersz kodu jest wywoływany po każdym wywołaniu funkcji, niepotrzebnie używane będą wywołanie sieciowe i zasób procesora CPU. Najlepszym sposobem poprawienia kodu jest umieszczenie tego wiersza w jakiejś metodzie startowej, która jest wykonywana tylko raz.

    ![Szczegóły profilera](media/tutorial-performance/5-hot-path.png)

7.  Obszar **Porada dotycząca wydajności** u góry ekranu potwierdza ocenę, że nadmierny czas trwania wynika z oczekiwania.  Kliknij link **oczekiwanie**, aby otworzyć dokumentację omawiającą interpretowanie różnych typów zdarzeń.

    ![Porada dotycząca wydajności](media/tutorial-performance/6-perf-tip.png)

8.   W celu dalszej analizy można kliknąć pozycję **Pobierz ślad** , aby pobrać śledzenie. Te dane można wyświetlić przy użyciu [Narzędzia PerfView](https://github.com/Microsoft/perfview#perfview-overview).

## <a name="use-logs-data-for-server"></a>Użyj danych dzienników dla serwera
 Dzienniki udostępniają bogaty język zapytań, który pozwala analizować wszystkie dane zbierane przez Application Insights. Możesz jej używać do wykonywania głębokiej analizy danych żądań i wydajności.

1. Wróć do panelu Szczegóły operacji, a następnie kliknij pozycję ![ dzienniki](media/tutorial-performance/app-viewinlogs-icon.png)**Widok ikon w dziennikach (analiza)**

2. Dzienniki są otwierane za pomocą zapytania dla każdego z widoków w panelu.  Zapytania te można uruchomić w proponowanej formie lub dostosować do własnych wymagań.  Pierwsze zapytanie pokazuje czas trwania operacji w miarę upływu czasu.

    ![zapytanie dzienników](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identyfikowanie wolnych operacji klienta
Oprócz identyfikowania procesów serwera do zoptymalizowania, usługa Application Insights może analizować perspektywę przeglądarek klienta.  To może pomóc zidentyfikować potencjalne ulepszenia do składników klienta, a nawet zidentyfikować problemy z różnymi przeglądarkami i lokalizacjami.

1. Wybierz pozycję **przeglądarka** w obszarze **Zbadaj** , a następnie kliknij pozycję **wydajność przeglądarki** lub wybierz pozycję **wydajność** w obszarze **Zbadaj** , a następnie przejdź do karty **przeglądarki** , klikając przycisk przełączania serwer/przeglądarka w prawym górnym rogu, aby otworzyć podsumowanie wydajności przeglądarki. Ma ono postać wizualnego podsumowania różnych danych telemetrycznych dla Twojej aplikacji z perspektywy przeglądarki.

    ![Podsumowanie informacji o przeglądarce](media/tutorial-performance/8-browser.png)

2. Wybierz jedną z nazw operacji, a następnie kliknij przycisk niebieskich przykładów w prawym dolnym rogu i wybierz operację. Spowoduje to wyświetlenie szczegółowych informacji o transakcjach i po prawej stronie można wyświetlić **Właściwości widoku strony**. Pozwala to wyświetlić szczegóły klienta żądającego strony, w tym typ przeglądarki i jej lokalizacji. Te informacje mogą pomóc w określeniu, czy występują problemy z wydajnością powiązane z konkretnymi typami klientów.

    ![Wyświetlenie strony](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Użyj danych dzienników dla klienta
Podobnie jak w przypadku danych zbieranych pod kątem wydajności serwera, Application Insights wszystkie dane klienta są dostępne do głębokiej analizy przy użyciu dzienników.

1. Wróć do podsumowania przeglądarki i kliknij kolejno pozycje ![ dzienniki ](media/tutorial-performance/app-viewinlogs-icon.png) **Widok ikon w dziennikach (analiza)**

2. Dzienniki są otwierane za pomocą zapytania dla każdego z widoków w panelu. Pierwsze zapytanie pokazuje czas trwania dla różnych wyświetleń stron w miarę upływu czasu.

    ![Zapytanie dzienników](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak identyfikować wyjątki czasu wykonywania, przejdź do następnego samouczka, aby dowiedzieć się, jak tworzyć alerty w odpowiedzi na błędy.

> [!div class="nextstepaction"]
> [Alerty dotyczące kondycji aplikacji](./tutorial-alert.md)

