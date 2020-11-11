---
title: Rozwiązywanie problemów z zapytaniami Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z zapytaniami w Azure Stream Analytics zadania.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: ef03560cff704255d2779a747d124e0b39a1c657
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491311"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Rozwiązywanie problemów z zapytaniami Azure Stream Analytics

W tym artykule opisano typowe problemy związane z tworzeniem zapytań Stream Analytics i sposobach ich rozwiązywania.

W tym artykule opisano typowe problemy związane z tworzeniem zapytań Azure Stream Analytics, sposobach rozwiązywania problemów z kwerendą i sposobami ich naprawy. Wiele kroków rozwiązywania problemów wymaga włączenia dzienników zasobów dla zadania Stream Analytics. Jeśli nie masz włączonych dzienników zasobów, zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Zapytanie nie produkuje oczekiwanych danych wyjściowych

1.  Sprawdzanie błędów przez testowanie lokalne:

    - Na Azure Portal na karcie **zapytanie** wybierz pozycję **test**. Aby [przetestować zapytanie,](stream-analytics-test-query.md)Użyj pobranych przykładowych danych. Przejrzyj błędy i spróbuj je poprawić.   
    - Możesz również [testować zapytanie lokalnie](stream-analytics-live-data-local-testing.md) przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio lub [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Debuguj zapytania krok po kroku lokalnie przy użyciu diagramu zadań](debug-locally-using-job-diagram-vs-code.md) w Azure Stream Analytics narzędzia dla Visual Studio Code. Diagram zadania pokazuje, w jaki sposób dane są przepływami ze źródeł danych wejściowych (centrum zdarzeń, IoT Hub itd.) przez wiele kroków zapytania, a wreszcie do ujścia danych wyjściowych. Każdy krok zapytania jest mapowany do tymczasowego zestawu wyników zdefiniowanego w skrypcie przy użyciu instrukcji WITH. Możesz wyświetlić dane, a także metryki w każdym pośrednim zestawie wyników, aby znaleźć źródło problemu.

    ![Wynik podglądu diagramu zadań](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Jeśli używasz [**sygnatur czasowych przez**](/stream-analytics-query/timestamp-by-azure-stream-analytics), sprawdź, czy zdarzenia mają sygnatury czasowe większe niż [godzina rozpoczęcia zadania](./stream-analytics-time-handling.md).

4.  Eliminowanie typowych pułapek, takich jak:
    - Klauzula [**WHERE**](/stream-analytics-query/where-azure-stream-analytics) w zapytaniu odfiltrowana wszystkie zdarzenia, uniemożliwiając wygenerowanie danych wyjściowych.
    - Funkcja [**Cast**](/stream-analytics-query/cast-azure-stream-analytics) kończy się niepowodzeniem, co powoduje niepowodzenie zadania. Aby uniknąć błędów rzutowania typu, należy zamiast tego użyć [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics) .
    - Gdy korzystasz z funkcji okna, poczekaj, aż cały czas trwania okna zobaczy dane wyjściowe zapytania.
    - Sygnatura czasowa dla zdarzeń poprzedza czas rozpoczęcia zadania i zdarzenia są porzucane.
    - Warunki [**sprzężenia**](/stream-analytics-query/join-azure-stream-analytics) są niezgodne. Jeśli nie ma żadnych dopasowań, nie będzie danych wyjściowych.

5.  Upewnij się, że zasady określania kolejności zdarzeń zostały skonfigurowane zgodnie z oczekiwaniami. Przejdź do pozycji **Ustawienia** i wybierz pozycję [**porządkowanie zdarzeń**](./stream-analytics-time-handling.md). Zasady *nie* są stosowane, gdy do testowania zapytania jest używany przycisk **Testuj** . Ten wynik jest jedną różnicą między testowaniem w przeglądarce a uruchomieniem zadania w środowisku produkcyjnym. 

6. Debuguj przy użyciu dzienników aktywności i zasobów:
    - Używaj [dzienników aktywności](../azure-resource-manager/management/view-activity-logs.md)i Filtruj, aby identyfikować i debugować błędy.
    - Użyj [dzienników zasobów zadań](stream-analytics-job-diagnostic-logs.md) , aby identyfikować i debugować błędy.

## <a name="resource-utilization-is-high"></a>Użycie zasobów jest wysokie

Upewnij się, że korzystasz z przetwarzanie równoległe w Azure Stream Analytics. Możesz dowiedzieć się, jak [skalować za pomocą zapytań przetwarzanie równoległe](stream-analytics-parallelization.md) Stream Analytics zadań przez skonfigurowanie partycji wejściowych i dostrajanie definicji zapytania analitycznego.

Jeśli wykorzystanie zasobów odbywa się w sposób ciągły przez 80%, wydłuża się opóźnienie i liczba zdarzeń zaległych, należy rozważyć zwiększenie jednostek przesyłania strumieniowego. Wysokie wykorzystanie wskazuje, że zadanie jest używane blisko maksymalnych przydziałów zasobów.

## <a name="debug-queries-progressively"></a>Debug queries progressively (Stopniowe debugowanie zapytań)

W czasie rzeczywistym, wiedząc, jakie dane wyglądają jak w środku zapytania, mogą być przydatne. Można to sprawdzić przy użyciu diagramu zadania w programie Visual Studio. Jeśli nie masz programu Visual Studio, możesz wykonać dodatkowe kroki w celu wygenerowania danych pośrednich.

Ponieważ dane wejściowe lub kroki zadania Azure Stream Analytics mogą być odczytywane wiele razy, można napisać dodatkowe instrukcje SELECT INTO. Wykonanie tej operacji spowoduje wyjście danych pośrednich do magazynu i pozwala sprawdzić poprawność danych, tak jak *zmienne czujki* , podczas debugowania programu.

Następujące przykładowe zapytanie w zadaniu Azure Stream Analytics ma jedno wejście strumienia, dwa dane wejściowe danych referencyjnych i dane wyjściowe do usługi Azure Table Storage. Zapytanie łączy dane z centrum zdarzeń i dwóch referencyjnych obiektów BLOB w celu uzyskania informacji o nazwie i kategorii:

![Przykład Stream Analytics wybierz do zapytania](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy pamiętać, że zadanie jest uruchomione, ale żadne zdarzenia nie są generowane w danych wyjściowych. Na kafelku **monitorowanie** , widocznym tutaj, można zobaczyć, że dane wejściowe są wytwarzane, ale nie wiesz, który krok **sprzężenia** spowodował porzucenie wszystkich zdarzeń.

![Kafelek monitorowanie Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

W takiej sytuacji można dodać kilka dodatkowych instrukcji SELECT INTO do "log" pośrednich wyników SPRZĘŻENIa i danych odczytywanych z danych wejściowych.

W tym przykładzie dodaliśmy dwa nowe "tymczasowe dane wyjściowe". Mogą to być dowolny ujścia, którego potrzebujesz. Oto przykład użycia usługi Azure Storage:

![Dodawanie dodatkowych instrukcji SELECT INTO do zapytania Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można napisać ponownie zapytanie w następujący sposób:

![Zapisano ponownie polecenie SELECT w Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie Rozpocznij zadanie i pozwól, aby było uruchamiane przez kilka minut. Następnie wykonaj zapytania dotyczące temp1 i temp2 z programem Visual Studio Cloud Explorer, aby utworzyć następujące tabele:

**tabela temp1** 
 ![ Zaznacz w tabeli temp1 Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabela temp2** 
 ![ Zaznacz w tabeli temp2 Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 mają dane, a kolumna Name jest wypełniana prawidłowo w temp2. Jednak ze względu na to, że w danych wyjściowych nadal nie ma danych, wystąpił problem:

![Zaznacz w tabeli output1 bez zapytania dotyczącego danych Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pobierając próbkowanie danych, można prawie upewnić się, że występuje problem z drugim SPRZĘŻENIem. Możesz pobrać dane referencyjne z obiektu BLOB i zajrzeć do:

![Zaznacz w tabeli ref Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tych danych referencyjnych różni się od formatu kolumny [from] w temp2. To dlatego, że dane nie dotarły do output1 zgodnie z oczekiwaniami.

Możesz naprawić format danych, przekazać go do referencyjnego obiektu BLOB, a następnie spróbować ponownie:

![Wybierz tabelę tymczasową Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tym razem dane w danych wyjściowych są formatowane i wypełniane zgodnie z oczekiwaniami.

![Wybierz do ostatecznej kwerendy Stream Analytics tabeli](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Uzyskaj pomoc

Aby uzyskać dalszą pomoc, Wypróbuj naszą [stronę pytań firmy&Microsoft dotyczącą Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
