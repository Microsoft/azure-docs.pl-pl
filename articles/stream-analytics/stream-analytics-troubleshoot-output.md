---
title: Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wyjściowymi w Azure Stream Analytics zadaniach.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133234"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Rozwiązywanie problemów dotyczących danych wyjściowych Azure Stream Analytics

W tym artykule opisano typowe problemy związane z Azure Stream Analytics połączeniami wyjściowymi, sposoby rozwiązywania problemów z danymi wyjściowymi oraz sposoby rozwiązania problemów. Wiele kroków rozwiązywania problemów wymaga włączenia zasobów i innych dzienników diagnostycznych dla zadania Stream Analytics. Jeśli nie masz włączonych dzienników zasobów, zobacz [Rozwiązywanie problemów Azure Stream Analytics przy użyciu dzienników zasobów](stream-analytics-job-diagnostic-logs.md).

## <a name="output-not-produced-by-job"></a>Dane wyjściowe nie zostały utworzone przez zadanie

1.  Sprawdź łączność z danymi wyjściowymi przy użyciu przycisku **Testuj połączenie** dla każdego elementu wyjściowego.

2.  Sprawdź [**metryki monitorowania**](stream-analytics-monitoring.md) na karcie **monitorowanie** . Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.
   * Jeśli zdarzenia wejściowe są większe niż 0, zadanie może odczytywać dane wejściowe. Jeśli zdarzenia wejściowe nie są większe niż 0, wystąpi problem z danymi wejściowymi zadania. Zobacz [Rozwiązywanie problemów z połączeniami](stream-analytics-troubleshoot-input.md) , aby dowiedzieć się, jak rozwiązywać problemy z połączeniem wejściowym.
   * Jeśli błędy konwersji danych są większe niż 0 i wspinanie się, zobacz [Azure Stream Analytics błędy danych](data-errors.md) , aby uzyskać szczegółowe informacje na temat błędów konwersji danych.
   * Jeśli błędy środowiska uruchomieniowego są większe niż 0, zadanie może odbierać dane, ale generuje błędy podczas przetwarzania zapytania. Aby znaleźć błędy, przejdź do [dziennika inspekcji](../azure-resource-manager/management/view-activity-logs.md) i odfiltruj stan *niepowodzenia* .
   * Jeśli wartość InputEvents jest większa niż 0 i OutputEvents równa 0, jeden z następujących warunków jest spełniony:
      * W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
      * Zdarzenia lub pola mogą być źle sformułowane, co powoduje wyjście z zera po przetworzeniu zapytania.
      * Zadanie nie może wypchnąć danych do ujścia wyjściowego w celu uzyskania łączności lub przyczyny uwierzytelnienia.

   We wszystkich wyżej wymienionych przypadkach błędów komunikaty dzienników operacji wyjaśniają dodatkowe szczegóły (w tym informacje o tym, co się dzieje), z wyjątkiem przypadków, gdy logika zapytań odfiltrowana wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, błędy są agregowane co 10 minut.

## <a name="job-output-is-delayed"></a>Job output is delayed (Dane wyjściowe zadania są opóźnione)

### <a name="first-output-is-delayed"></a>Pierwsze wyjście jest opóźnione

Po uruchomieniu zadania usługi Stream Analytics odczytywane są zdarzenia wejściowe, ale w pewnych okolicznościach generowanie danych wyjściowych może być opóźnione.

Duże wartości czasu w elementach zapytań czasowych mogą przyczynić się do opóźnienia danych wyjściowych. Aby wygenerować poprawne dane wyjściowe w dużych oknach czasu, zadanie przesyłania strumieniowego uruchamia się, odczytując dane z ostatniego możliwego czasu (do siedmiu dni temu) w celu wypełnienia przedziału czasu. W tym czasie nie są generowane żadne dane wyjściowe, dopóki nie zostanie ukończony odczyt oczekujących zdarzeń wejściowych. Ten problem może być narażony, gdy system uaktualnia zadania przesyłania strumieniowego, w tym celu należy ponownie uruchomić zadanie. Takie uaktualnienia są zwykle wykonywane co kilka miesięcy.

W związku z tym należy używać uznania podczas projektowania zapytania Stream Analytics. Jeśli używasz dużego przedziału czasu (więcej niż kilka godzin, do siedmiu dni) dla elementów czasowych w składni zapytania zadania, może to opóźnić na pierwsze wyjście, gdy zadanie zostanie uruchomione lub uruchomione ponownie.  

Jedno ograniczenie dla tego rodzaju pierwszego opóźnienia danych wyjściowych polega na użyciu technik przetwarzanie równoległe kwerend (partycjonowania danych) lub dodaniu większej liczby jednostek przesyłania strumieniowego w celu zwiększenia przepływności do momentu wychwycenia zadania.  Aby uzyskać więcej informacji, zobacz [uwagi dotyczące tworzenia zadań Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)

Czynniki te wpływają na oś czasu pierwszego generowanego wyjścia:

1. Korzystanie z agregacji w oknie (Grupuj według wirowania, przeskoku i ruchomych okien)
   - W przypadku agregacji okna wirowania lub przeskoku wyniki są generowane na końcu przedziału czasu okna.
   - W przypadku przesuwanego okna wyniki są generowane, gdy zdarzenie przejdzie lub wyjdzie z przesuwanego okna.
   - Jeśli planujesz używać dużego okna (> 1 godziny), najlepiej wybrać przeskoku lub ruchome okno, aby wyświetlić dane wyjściowe częściej.

2. Korzystanie z sprzężeń czasowych (SPRZĘŻENIe z DATEDIFF)
   - Dopasowania są generowane zaraz po nadejściu obu stron dopasowanego zdarzenia.
   - Dane, które nie posiadają dopasowania (lewe SPRZĘŻENIe zewnętrzne), są generowane na końcu okna DATEDIFF w odniesieniu do każdego zdarzenia po lewej stronie.

3. Korzystanie z funkcji analitycznych CZASowych (isfirst, LAST i LAG z LIMITem czasu trwania)
   - W przypadku funkcji analitycznych dane wyjściowe są generowane dla każdego zdarzenia, nie ma opóźnienia.

### <a name="output-falls-behind"></a>Dane wyjściowe wykraczają poza

W trakcie normalnego działania zadania, jeśli okaże się, że dane wyjściowe zadania są opóźnione (dłuższe i dłuższe opóźnienia), można wskazać główne przyczyny, badając te czynniki:
- Czy obiekt sink podrzędny jest ograniczany
- Czy źródło nadrzędnego jest ograniczone
- Czy logika przetwarzania w zapytaniu jest intensywnie COMPUTE

Aby wyświetlić te szczegóły, w Azure Portal wybierz zadanie przesyłania strumieniowego i wybierz **Diagram zadań**. Dla każdego elementu wejściowego istnieje Metryka zdarzenia zaległości dla partycji. Jeśli Metryka zdarzenia zaległości ciągle rośnie, jest wskaźnikiem, że zasoby systemowe są ograniczone. Może to być spowodowane ograniczaniem przepływów danych wyjściowych lub dużym procesorem CPU. Aby uzyskać więcej informacji na temat korzystania z diagramu zadań, zobacz [debugowanie oparte na danych przy użyciu diagramu zadań](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Ostrzeżenie o naruszeniu klucza z danymi wyjściowymi Azure SQL Database

Podczas konfigurowania usługi Azure SQL Database jako dane wyjściowe do zadania Stream Analytics zbiorczo wstawia rekordy do tabeli docelowej. Ogólnie rzecz biorąc, usługa Azure Stream Analytics gwarantuje [co najmniej raz dostarczenie](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) do ujścia danych wyjściowych. jeden z nich może być w dalszym ciągu zapewniać [jednokrotne dostarczenie]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) do danych wyjściowych SQL, gdy tabela SQL ma zdefiniowane unikatowe ograniczenie.

Po skonfigurowaniu unikatowych ograniczeń klucza w tabeli SQL, gdy do tabeli SQL są wstawiane zduplikowane rekordy, Azure Stream Analytics usuwa zduplikowany rekord. Dzieli dane na partie i cyklicznie wstawiając partie do momentu znalezienia pojedynczego duplikatu rekordu. Jeśli zadanie przesyłania strumieniowego ma znaczną liczbę zduplikowanych wierszy, ten proces dzielenia i wstawiania musi ignorować duplikaty po jednym, który jest mniej wydajny i czasochłonny. Jeśli w dzienniku aktywności w ciągu ostatniej godziny widzisz wiele komunikatów ostrzegawczych naruszenia klucza, prawdopodobnie dane wyjściowe SQL spowalniają całe zadanie.

Aby rozwiązać ten problem, należy [skonfigurować indeks]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) , który powoduje naruszenie klucza przez włączenie opcji IGNORE_DUP_KEY. Włączenie tej opcji pozwala na ignorowanie zduplikowanych wartości przez SQL podczas operacji wstawiania zbiorczego, a usługa SQL Azure po prostu generuje komunikat ostrzegawczy zamiast błędu. Azure Stream Analytics nie generują już błędów naruszenia klucza podstawowego.

Podczas konfigurowania IGNORE_DUP_KEY dla kilku typów indeksów należy zwrócić uwagę na następujące uwagi:

* Nie można ustawić IGNORE_DUP_KEY dla klucza podstawowego lub ograniczenia UNIQUE, które używa instrukcji ALTER INDEX, należy porzucić i ponownie utworzyć indeks.  
* Możesz ustawić opcję IGNORE_DUP_KEY przy użyciu polecenia ALTER INDEX dla unikatowego indeksu, który różni się od klucza podstawowego/UNIKATOWEgo ograniczenia i utworzonego przy użyciu polecenia Utwórz indeks lub definicję indeksu.  

* IGNORE_DUP_KEY nie dotyczy indeksów magazynu kolumn, ponieważ nie można wymusić unikatowości takich indeksów.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Nazwy kolumn są mniejsze niż w przypadku Azure Stream Analytics
Przy użyciu oryginalnego poziomu zgodności (1,0) Azure Stream Analytics używany do zmiany nazw kolumn na małe litery. Takie zachowanie zostało rozwiązane na późniejszych poziomach zgodności. Aby zachować sprawność, firma Microsoft zaleca klientom przejście do poziomu zgodności 1,1 i nowszych. Więcej informacji na temat poziomu zgodności można znaleźć w [Azure Stream Analytics zadaniach](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
