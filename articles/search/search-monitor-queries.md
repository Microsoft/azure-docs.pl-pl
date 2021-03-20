---
title: Monitoruj zapytania
titleSuffix: Azure Cognitive Search
description: Monitoruj metryki zapytań pod kątem wydajności i przepływności. Zbieraj i analizuj dane wejściowe ciągu zapytania w dziennikach zasobów.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592381"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Monitorowanie żądań zapytań w usłudze Azure Wyszukiwanie poznawcze

W tym artykule wyjaśniono, jak zmierzyć wydajność zapytań i wolumin przy użyciu metryk i rejestrowania zasobów. Wyjaśniono również, jak zbierać terminy wejściowe używane w zapytaniach — informacje niezbędne do oceny narzędzia i skuteczności korpus wyszukiwania.

Dane historyczne, które są strumieniowo przechowywane przez 30 dni. Aby zapewnić dłuższe przechowywanie lub raportować dane operacyjne i ciągi zapytań, należy włączyć [ustawienie diagnostyczne](search-monitor-logs.md) określające opcję magazynu dla utrwalania zarejestrowanych zdarzeń i metryk.

Warunki maksymalizowające integralność pomiarów danych obejmują:

+ Użyj usługi do rozliczenia (usługi utworzonej w warstwie Podstawowa lub standardowa). Bezpłatna usługa jest udostępniana przez wielu subskrybentów, co wprowadza pewną ilość lotności jako ładującą zmiany.

+ Użyj pojedynczej repliki i partycji, jeśli to możliwe, do utworzenia środowiska zawartego i izolowanego. W przypadku korzystania z wielu replik metryki zapytań są uśredniane w wielu węzłach, co może obniżyć precyzję wyników. Podobnie, wiele partycji oznacza, że dane są podzielone, z możliwością, że niektóre partycje mogą mieć różne dane, jeśli również trwa indeksowanie. W przypadku dostrajania wydajności zapytań pojedynczy węzeł i partycja zapewnia bardziej stabilne środowisko do testowania.

> [!Tip]
> Dzięki dodatkowemu kodowi po stronie klienta i Application Insights można również przechwycić dane przeglądowe, aby uzyskać dokładniejszy wgląd w to, co jest przydatne dla użytkowników aplikacji. Aby uzyskać więcej informacji, zobacz [Analiza ruchu wyszukiwania](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Wolumin zapytania (zapytań)

Wolumin jest mierzony jako **zapytania wyszukiwania na sekundę** (zapytań), wbudowana Metryka, która może być raportowana jako średnia, liczba, minimum lub maksymalna liczba zapytań wykonywanych w oknie jednominutowym. Interwały jednej minuty (TimeGrain = "PT1M") dla metryk są stałe w systemie.

Często zapytania są wykonywane w milisekundach, dlatego w metrykach są wyświetlane tylko zapytania, które mierzą jako sekundy.

| Typ agregacji | Opis |
|------------------|-------------|
| Średnia | Średnia liczba sekund w ciągu minuty, w trakcie której wystąpiło wykonywanie zapytania.|
| Liczba | Liczba metryk emitowanych do dziennika w ramach interwału jednej minuty. |
| Maksimum | Najwyższa liczba zapytań wyszukiwania na sekundę zarejestrowanych w ciągu minuty. |
| Minimum | Najmniejsza liczba zapytań wyszukiwania na sekundę zarejestrowanych w ciągu minuty.  |
| Sum | Suma wszystkich zapytań wykonanych w ciągu minuty.  |

Na przykład w ciągu jednej minuty może istnieć wzorzec podobny do tego: jedna sekunda wysokiego obciążenia, która jest maksymalną wartością dla SearchQueriesPerSecond, a następnie 58 sekund średniego obciążenia, a wreszcie jedna sekunda z tylko jednym zapytaniem, co jest minimalne.

Inny przykład: Jeśli węzeł emituje metryki 100, gdzie wartość każdej metryki to 40, a następnie "Count" to 100, "Sum" jest 4000, "Average" to 40, a "Max" to 40.

## <a name="query-performance"></a>Wydajność zapytań

Wydajność zapytań w całej usłudze jest mierzona jako opóźnienie wyszukiwania (czas trwania zapytania) i ograniczone zapytania, które zostały porzucone w wyniku rywalizacji o zasoby.

### <a name="search-latency"></a>Opóźnienie wyszukiwania

| Typ agregacji | Opóźnienie | 
|------------------|---------|
| Średnia | Średni czas trwania zapytania w milisekundach. | 
| Liczba | Liczba metryk emitowanych do dziennika w ramach interwału jednej minuty. |
| Maksimum | Najdłuższy uruchomiony zapytanie w przykładzie. | 
| Minimum | Najkrótsze uruchomione zapytanie w przykładzie.  | 
| Łącznie | Łączny czas wykonywania wszystkich zapytań w przykładzie, wykonywanych w interwale (jedna minuta).  |

Rozważmy następujący przykład metryk **opóźnienia wyszukiwania** : 86 zapytania były próbkowane z średnim czasem trwania 23,26 milisekund. Co najmniej 0 wskazuje, że niektóre zapytania zostały usunięte. Długotrwałe wykonanie zapytania trwało 1000 milisekund. Łączny czas wykonywania wynosił 2 sekundy.

![Agregacje opóźnienia](./media/search-monitor-usage/metrics-latency.png "Agregacje opóźnienia")

### <a name="throttled-queries"></a>Ograniczone zapytania

Zapytania z ograniczeniami odwołują się do zapytań, które są opuszczane zamiast procesu. W większości przypadków ograniczenie jest normalną częścią działania usługi.  Niekoniecznie oznacza to, że wystąpił problem.

Ograniczanie przepływności występuje, gdy liczba aktualnie przetworzonych żądań przekroczy dostępne zasoby. Jeśli replika jest wykorzystana z rotacji lub podczas indeksowania, może zostać wyświetlony wzrost liczby żądań z ograniczeniami. Oba żądania zapytań i indeksowania są obsługiwane przez ten sam zestaw zasobów.

Usługa określa, czy żądania mają być porzucane na podstawie zużycia zasobów. Wartości procentowe zasobów zużywanych między pamięcią, PROCESORAmi i operacjami we/wy dysku są uśredniane w danym okresie czasu. Jeśli wartość procentowa przekroczy wartość progową, wszystkie żądania kierowane do indeksu są ograniczane do momentu zmniejszenia liczby żądań. 

W zależności od klienta żądanie ograniczone może być wskazane w następujący sposób:

+ Usługa zwraca błąd "wysyłasz zbyt wiele żądań. Spróbuj ponownie później”. 
+ Usługa zwraca kod błędu 503 wskazujący, że usługa jest obecnie niedostępna. 
+ W przypadku korzystania z portalu (na przykład Eksploratora wyszukiwania) zapytanie jest usuwane w trybie dyskretnym i konieczne będzie ponowne kliknięcie przycisku Wyszukaj.

Aby potwierdzić ograniczone zapytania, użyj metryki **zapytań wyszukiwania z ograniczeniami** . Metryki można eksplorować w portalu lub utworzyć metrykę alertów zgodnie z opisem w tym artykule. W przypadku zapytań, które zostały porzucone w interwale próbkowania, użyj *sum* , aby uzyskać procent zapytań, które nie zostały wykonane.

| Typ agregacji | Ograniczanie przepływności |
|------------------|-----------|
| Średnia | Procent zapytań porzuconych w interwale. |
| Liczba | Liczba metryk emitowanych do dziennika w ramach interwału jednej minuty. |
| Maksimum | Procent zapytań porzuconych w interwale.|
| Minimum | Procent zapytań porzuconych w interwale. |
| Łącznie | Procent zapytań porzuconych w interwale. |

W przypadku **kwerend wyszukiwania z ograniczeniami wartość procentowa**, minimum, maksimum, średnia i suma — wszystkie mają tę samą wartość: procent zapytań wyszukiwania, które zostały ograniczone, od łącznej liczby zapytań wyszukiwania w ciągu jednej minuty.

Na poniższym zrzucie ekranu pierwszy numer jest liczbą (lub liczbą metryk wysyłanych do dziennika). Dodatkowe agregacje, które pojawiają się u góry lub po umieszczeniu wskaźnika na metryce, obejmują średnią, maksymalną i łącznie. W tym przykładzie żadne żądania nie zostały porzucone.

![Agregacje ograniczenia](./media/search-monitor-usage/metrics-throttle.png "Agregacje ograniczenia")

## <a name="explore-metrics-in-the-portal"></a>Eksplorowanie metryk w portalu

Aby uzyskać szybki przegląd z bieżącymi numerami, na karcie **monitorowanie** na stronie Przegląd usługi są wyświetlane trzy metryki (**opóźnienie wyszukiwania**, **zapytania wyszukiwania na sekundę (za jednostkę wyszukiwania)**, **procent kwerend wyszukiwania ograniczonego**, wyrażone w godzinach, dni i tygodnie, z możliwością zmiany typu agregacji.

W celu uzyskania bardziej szczegółowej eksploracji Otwórz Eksploratora metryk z menu **monitorowanie** , tak aby można było warstwować, powiększać i wizualizować dane w celu eksplorowania trendów i anomalii. Dowiedz się więcej o Eksploratorze metryk, wykonując ten [samouczek na temat tworzenia wykresu metryk](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. W sekcji monitorowanie wybierz pozycję **metryki** , aby otworzyć Eksploratora metryk z zakresem ustawionym na usługę wyszukiwania.

1. W obszarze Metryka wybierz jedną z listy rozwijanej i przejrzyj listę dostępnych agregacji dla preferowanego typu. Agregacja definiuje sposób próbkowania zebranych wartości w każdym przedziale czasu.

   ![Eksplorator metryk dla metryki zapytań](./media/search-monitor-usage/metrics-explorer-qps.png "Eksplorator metryk dla metryki zapytań")

1. W prawym górnym rogu ustaw interwał czasu.

1. Wybierz wizualizację. Wartość domyślna to wykres liniowy.

1. Dodatkowe agregacje warstwowe, wybierając opcję **Dodaj metrykę** i wybierając różne agregacje.

1. Powiększ obszar na wykresie liniowym. Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij do drugiej strony obszaru i zwolnij przycisk. Wykres powiększy się dla tego zakresu czasu.

## <a name="identify-strings-used-in-queries"></a>Identyfikowanie ciągów używanych w zapytaniach

Po włączeniu rejestrowania zasobów system przechwytuje żądania zapytań w tabeli **AzureDiagnostics** . Jako warunek wstępny musisz mieć już włączone [Rejestrowanie zasobów](search-monitor-logs.md), określając obszar roboczy usługi log Analytics lub inną opcję magazynu.

1. W sekcji monitorowanie wybierz pozycję **dzienniki** , aby otworzyć puste okno zapytania w log Analytics.

1. Uruchom następujące wyrażenie, aby przeszukać zapytanie. Wyszukaj operacje, zwracając zestaw wyników tabelarycznych składający się z nazwy operacji, ciągu zapytania, indeksu kwerendy oraz liczby znalezionych dokumentów. Ostatnie dwie instrukcje wykluczają ciągi zapytania składające się z pustego lub nieokreślonego wyszukiwania, nad przykładowym indeksem, co spowoduje zmniejszenie szumu w wynikach.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Opcjonalnie Ustaw filtr kolumn na *Query_s* , aby przeszukać określoną składnię lub ciąg. Na przykład, można odfiltrować *wartość równą* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Zarejestrowane ciągi zapytań](./media/search-monitor-usage/log-query-strings.png "Zarejestrowane ciągi zapytań")

Chociaż ta technika działa w przypadku badania ad hoc, Tworzenie raportu umożliwia konsolidowanie i prezentowanie ciągów zapytań w układzie bardziej sprzyjających analizie.

## <a name="identify-long-running-queries"></a>Zidentyfikuj długotrwałe zapytania

Dodaj kolumnę czas trwania, aby uzyskać numery dla wszystkich zapytań, a nie tylko te, które są pobierane jako metryki. Sortowanie tych danych pokazuje, które zapytania trwają najdłużej.

1. W sekcji monitorowanie wybierz pozycję **dzienniki** , aby wykonać zapytanie o informacje dziennika.

1. Uruchom następujące zapytanie, aby zwrócić zapytania, posortowane według czasu trwania w milisekundach. Najdłuższe uruchomione zapytania znajdują się u góry.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sortuj zapytania według czasu trwania](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sortuj zapytania według czasu trwania")

## <a name="create-a-metric-alert"></a>Tworzenie alertu metryki

Alert dotyczący metryki ustala próg, w którym otrzymasz powiadomienie lub wyzwolisz akcję naprawczą, która została zdefiniowana z góry. 

W przypadku usługi wyszukiwania często można utworzyć alert metryki dla opóźnień wyszukiwania i zapytań z ograniczeniami. Jeśli wiesz, kiedy zapytania są porzucane, możesz wyszukać środki zaradcze, które zmniejszają obciążenie lub zwiększają pojemność. Jeśli na przykład zapytania z ograniczeniami są zwiększane podczas indeksowania, można je odłożyć do momentu działania zapytania.

Podczas wypychania limitów określonej konfiguracji partycji repliki można także ułatwić Konfigurowanie alertów dla progów woluminów zapytań (zapytań).

1. W sekcji monitorowanie wybierz pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**. Upewnij się, że usługa wyszukiwania została wybrana jako zasób.

1. W obszarze warunek kliknij pozycję **Dodaj**.

1. Konfiguruj logikę sygnału. W polu Typ sygnału wybierz pozycję **metryki** , a następnie wybierz sygnał.

1. Po wybraniu sygnału można użyć wykresu do wizualizacji danych historycznych w celu uzyskania świadomej decyzji dotyczącej sposobu działania konfiguracji warunków.

1. Następnie przewiń w dół do pozycji logika alertów. W celu sprawdzenia koncepcji można określić sztucznie niską wartość do celów testowych.

   ![Logika alertu](./media/search-monitor-usage/alert-logic-qps.png "Logika alertu")

1. Następnie określ lub Utwórz grupę akcji. Jest to odpowiedź do wywołania po spełnieniu progu. Może to być Powiadomienie wypychane lub automatyczna odpowiedź.

1. Na końcu Określ szczegóły alertu. Nazwa i opis alertu, przypisywanie wartości ważności i określanie, czy reguła ma zostać utworzona w stanie włączony lub wyłączony.

   ![Szczegóły alertu](./media/search-monitor-usage/alert-details.png "Szczegóły alertu")

Jeśli podano powiadomienie e-mail, otrzymasz wiadomość e-mail od "Microsoft Azure" z wierszem tematu "Azure: aktywowana ważność: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, zapoznaj się z tematem monitorowanie usługi wyszukiwania, aby dowiedzieć się więcej na temat pełnego zakresu możliwości nadzoru.

> [!div class="nextstepaction"]
> [Monitoruj operacje i działania na platformie Azure Wyszukiwanie poznawcze](search-monitor-usage.md)