---
title: Informacje o monitorowaniu zadań w Azure Stream Analytics
description: W tym artykule opisano sposób monitorowania Azure Stream Analytics zadań w Azure Portal.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: f8e0d49c2a35a1e97c79e6d4b8c867a4a8ab88b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006682"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Informacje o monitorowaniu zadań Stream Analytics i sposobach monitorowania zapytań

## <a name="introduction-the-monitor-page"></a>Wprowadzenie: Strona monitorowanie
Azure Portal prezentuje kluczowe metryki wydajności, których można użyć do monitorowania i rozwiązywania problemów z zapytaniami i wydajnością zadań. Aby wyświetlić te metryki, przejdź do Stream Analytics zadania, w którym widzisz metryki, i Wyświetl sekcję **monitorowanie** na stronie Przegląd.  

![Link monitorowania zadania Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Okno zostanie wyświetlone w sposób pokazany:

![Pulpit nawigacyjny monitorowania zadania Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metryki dostępne dla Stream Analytics
| Metric                 | Definicja                               |
| ---------------------- | ---------------------------------------- |
| Zaległe zdarzenia wejściowe       | Liczba zarejestrowanych zdarzeń wejściowych. Wartość różna od zera dla tej metryki oznacza, że zadanie nie jest w stanie zachować liczby zdarzeń przychodzących. Jeśli ta wartość jest powoli zwiększana lub konsekwentnie różna od zera, należy przeskalować zadanie. Więcej informacji można znaleźć w [opisie i dostosowaniu jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md). |
| Błędy konwersji danych | Liczba zdarzeń wyjściowych, których nie można przekonwertować na oczekiwany schemat wyjściowy. Zasady błędów można zmienić na "Drop", aby porzucić zdarzenia, które napotykają ten scenariusz. |
| Wczesne zdarzenia wejściowe       | Zdarzenia, których sygnatura czasowa aplikacji jest wcześniejsza niż ich czas przybycia o więcej niż 5 minut. |
| Nieudane żądania funkcji | Liczba wywołań funkcji zakończonych niepowodzeniem Azure Machine Learning (jeśli istnieją). |
| Zdarzenia funkcji        | Liczba zdarzeń wysyłanych do funkcji Azure Machine Learning (jeśli istnieje). |
| Żądania funkcji      | Liczba wywołań funkcji Azure Machine Learning (jeśli istnieje). |
| Błędy deserializacji danych wejściowych       | Liczba zdarzeń wejściowych, których nie można zdeserializować.  |
| Bajty zdarzeń wejściowych      | Ilość danych odebranych przez zadanie Stream Analytics w bajtach. Można go użyć do sprawdzenia, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Zdarzenia wejściowe           | Liczba rekordów deserializowanych ze zdarzeń wejściowych. Ta liczba nie obejmuje zdarzeń przychodzących, które powodują błędy deserializacji. Te same zdarzenia można pozyskać przez Stream Analytics wiele razy w scenariuszach takich jak odzyskiwanie wewnętrzne i samosprzężenia. W związku z tym zaleca się, aby nie oczekiwać zdarzeń wejściowych i metryk zdarzeń wyjściowych, które będą zgodne, jeśli zadanie ma proste zapytanie "pass through". |
| Odebrane źródła danych wejściowych       | Liczba komunikatów odebranych przez zadanie. W przypadku usługi Event Hub komunikat to pojedynczy EventData. W przypadku obiektu BLOB komunikat jest pojedynczym obiektem BLOB. Należy pamiętać, że źródła danych wejściowych są zliczane przed deserializacji. Jeśli występują błędy deserializacji, źródła danych wejściowych mogą być większe niż zdarzenia wejściowe. W przeciwnym razie może być mniejsze niż lub równe zdarzenia wejściowe, ponieważ każdy komunikat może zawierać wiele zdarzeń. |
| Opóźnione zdarzenia wejściowe      | Zdarzenia, które dotarły później niż skonfigurowane okno tolerancji opóźnionego przybycia. Dowiedz się więcej na temat [zagadnień dotyczących kolejności zdarzeń Azure Stream Analytics](./stream-analytics-time-handling.md) . |
| Zdarzenia poza kolejnością    | Liczba zdarzeń odebranych poza kolejnością, które zostały usunięte lub uzyskały skorygowaną sygnaturę czasową, na podstawie zasad określania kolejności zdarzeń. Może to mieć wpływ na konfigurację ustawienia okna tolerancja poza kolejnością. |
| Zdarzenia wyjściowe          | Ilość danych wysyłanych przez zadanie Stream Analytics do elementu docelowego danych wyjściowych, w liczbie zdarzeń. |
| Błędy środowiska uruchomieniowego         | Łączna liczba błędów związanych z przetwarzaniem zapytań (z wyjątkiem błędów znalezionych podczas pozyskiwania zdarzeń lub wyprowadzania wyników) |
| Użycie SU%       | Jeśli wykorzystanie zasobów odbywa się w sposób ciągły przez 80%, wydłuża się opóźnienie i liczba zdarzeń zaległych, należy rozważyć zwiększenie jednostek przesyłania strumieniowego. Wysokie wykorzystanie wskazuje, że zadanie jest używane blisko maksymalnych przydziałów zasobów. |
| Opóźnienie znaku wodnego       | Maksymalne opóźnienie dla wszystkich partycji wszystkich danych wyjściowych w zadaniu. |

Możesz użyć tych metryk do [monitorowania wydajności zadania Stream Analytics](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Dostosowywanie monitorowania w Azure Portal
Możesz dostosować typ wykresu, pokazywane metryki i zakres czasu w ustawieniach edycji wykresu. Aby uzyskać szczegółowe informacje, zobacz [How to Dostosowywanie monitorowania](../azure-monitor/platform/data-platform.md).

  ![Wykres Stream Analytics czasu monitora zapytania](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Najnowsze dane wyjściowe
Innym interesującym punktem danych do monitorowania zadania jest czas ostatniego wyjścia przedstawiony na stronie Przegląd.
Jest to czas aplikacji (tj. czas, który używa sygnatury czasowej z danych zdarzenia) najnowszych danych wyjściowych zadania.

## <a name="get-help"></a>Uzyskaj pomoc
Aby uzyskać dalszą pomoc, wypróbuj nasze [&stronie pytań na temat Azure Stream Analytics](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)
