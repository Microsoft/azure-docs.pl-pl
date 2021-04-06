---
title: Debugowanie oparte na danych w Azure Stream Analytics
description: W tym artykule opisano sposób rozwiązywania problemów z zadaniem Azure Stream Analytics przy użyciu diagramu zadania i metryk w Azure Portal.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020404"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Data-driven debugging by using the job diagram (Debugowanie oparte na danych za pomocą diagramu zadań)

Diagram zadań w bloku **monitorowanie** w Azure Portal może ułatwić wizualizację potoku zadań. Pokazuje wejścia, wyjścia i kroki zapytania. Za pomocą diagramu zadań można sprawdzać metryki dla poszczególnych kroków, aby szybciej ustalić źródło problemu podczas rozwiązywania problemów.

## <a name="using-the-job-diagram"></a>Korzystanie z diagramu zadań

W Azure Portal, podczas Stream Analytics zadania, w obszarze **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **Diagram zadań**:

![Diagram zadań z metrykami — lokalizacja](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Wybierz każdy krok zapytania, aby wyświetlić odpowiadającą sekcję w okienku edycji zapytania. Wykres metryki dla kroku jest wyświetlany w dolnym okienku na stronie.

![Diagram zadań z metrykami — podstawowe zadanie](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Aby wyświetlić partycje danych wejściowych platformy Azure Event Hubs, wybierz pozycję **...** Zostanie wyświetlone menu kontekstowe. Można także zobaczyć dane wejściowe.

![Diagram zadań z metrykami — rozszerzanie partycji](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Aby wyświetlić wykres metryki tylko dla jednej partycji, wybierz węzeł partycji. Metryki są wyświetlane w dolnej części strony.

![Diagram zadań z metrykami — więcej metryk](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Aby wyświetlić wykres metryk dla fuzji, wybierz węzeł łączenie. Poniższy wykres pokazuje, że żadne zdarzenia nie zostały porzucone ani dostosowane.

![Diagram zadań z metrykami — siatka](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Aby wyświetlić szczegóły wartości metryki i godziny, wskaż wykres.

![Diagram zadań z metrykami — aktywowanie](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Rozwiązywanie problemów przy użyciu metryk

Metryka **QueryLastProcessedTime** wskazuje, kiedy określony krok otrzymał dane. Przeglądając topologię, możesz przejść do tyłu od procesora wyjściowego, aby zobaczyć, który krok nie otrzymuje danych. Jeśli krok nie pobiera danych, przejdź do kroku zapytania tuż przed nim. Sprawdź, czy poprzedni krok zapytania ma przedział czasu, a w przypadku przeprowadzenia przez niego wystarczającej ilości danych wyjściowych. (Należy pamiętać, że okna czasowe są przyłączone do godziny).
 
Jeśli poprzedni krok zapytania jest procesorem wejściowym, użyj metryk wejściowych, aby pomóc odpowiedzieć na następujące pytania. Mogą one pomóc w ustaleniu, czy zadanie pobiera dane ze źródeł danych wejściowych. Jeśli zapytanie jest podzielone na partycje, sprawdź każdą partycję.
 
### <a name="how-much-data-is-being-read"></a>Jak dużo danych jest odczytywanych?

*   **Metryka inputeventssourcestotal** to liczba odczytanych jednostek danych. Na przykład liczba obiektów BLOB.
*   **InputEventsTotal** to liczba odczytanych zdarzeń. Ta metryka jest dostępna dla każdej partycji.
*   **InputEventsInBytesTotal** to liczba odczytanych bajtów.
*   **InputEventsLastArrivalTimea** jest aktualizowana przy każdym zdarzeniu przydanym zdarzenia w kolejce.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Czy czas jest przenoszony do przodu? Jeśli są odczytywane rzeczywiste zdarzenia, wyróżnienie może nie być wystawiane.

*   Metryka **InputEventsLastPunctuationTime** wskazuje, kiedy wyróżnienie zostało wystawione, aby czas płynął do przodu. Jeśli interpunkcja nie zostanie wystawiona, przepływ danych może zostać zablokowany.
 
### <a name="are-there-any-errors-in-the-input"></a>Czy istnieją błędy w danych wejściowych?

*   **Metryka inputeventseventdatanulltotal** to liczba zdarzeń, które mają dane o wartości null.
*   **Metryka inputeventsserializererrorstotal** to liczba zdarzeń, które nie mogły zostać prawidłowo deserializowane.
*   **Metryka inputeventsdegradedtotal** to liczba zdarzeń, które były problemem innym niż deserializacja.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Czy zdarzenia są usuwane lub dostosowywane?

*   **InputEventsEarlyTotal** to liczba zdarzeń, które mają sygnaturę czasową aplikacji przed górnym limitem.
*   **InputEventsLateTotal** to liczba zdarzeń, które mają sygnaturę czasową aplikacji po górnym znaku wodnym.
*   **Metryka inputeventsdroppedbeforeapplicationstarttimetotal** to liczba zdarzeń porzuconych przed godziną rozpoczęcia zadania.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Czy w trakcie odczytywania danych?

*   **Zarejestrowane zdarzenia (łącznie)** wskazują, ile więcej komunikatów należy odczytać dla Event Hubs i danych wejściowych platformy Azure IoT Hub. Gdy ta liczba jest większa niż 0, oznacza to, że zadanie nie może przetworzyć danych tak szybko, jak to się dzieje. W takim przypadku może być konieczne zwiększenie liczby jednostek przesyłania strumieniowego i/lub upewnienie się, że zadanie może być równoległe. Więcej informacji na ten temat można znaleźć na  [stronie zapytania przetwarzanie równoległe](./stream-analytics-parallelization.md). 


## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dodatkową pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Stream Analytics](stream-analytics-introduction.md)
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Stream Analytics Dokumentacja języka zapytań](/stream-analytics-query/stream-analytics-query-language-reference)
* [Dokumentacja interfejsu API REST zarządzania Stream Analytics](/rest/api/streamanalytics/)