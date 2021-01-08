---
title: Debuguj Azure Stream Analytics zapytań lokalnie przy użyciu diagramu zadań w programie Visual Studio
description: W tym artykule opisano sposób debugowania zapytań lokalnie przy użyciu diagramu zadań w Azure Stream Analytics Narzędzia dla programu Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019452"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Debuguj Azure Stream Analytics zapytań lokalnie przy użyciu diagramu zadań w programie Visual Studio

Zadania, które nie są wynikiem lub nieoczekiwane wyniki, są typowymi scenariuszami rozwiązywania problemów dotyczących zapytań strumieniowych. Możesz użyć diagramu zadania podczas testowania zapytania lokalnie w programie Visual Studio, aby zbadać pośredni zestaw wyników i metryki dla każdego kroku. Diagramy zadań mogą pomóc w szybkim wyizolowaniu źródła problemu w przypadku rozwiązywania problemów.

## <a name="debug-a-query-using-job-diagram"></a>Debugowanie zapytania przy użyciu diagramu zadań

Skrypt Azure Stream Analytics służy do przekształcania danych wejściowych w dane wyjściowe. Diagram zadania pokazuje, w jaki sposób dane są przepływami ze źródeł danych wejściowych (centrum zdarzeń, IoT Hub itd.) przez wiele kroków zapytania, a wreszcie do ujścia danych wyjściowych. Każdy krok zapytania jest mapowany do tymczasowego zestawu wyników zdefiniowanego w skrypcie przy użyciu `WITH` instrukcji. Można wyświetlić dane oraz metryki każdego kroku zapytania w każdym pośrednim zestawie wyników, aby znaleźć źródło problemu.

> [!NOTE]
> Ten diagram zadań przedstawia tylko dane i metryki na potrzeby lokalnego testowania w jednym węźle. Nie należy używać go do dostrajania wydajności i rozwiązywania problemów.

### <a name="start-local-testing"></a>Rozpocznij testowanie lokalne

Skorzystaj z tego [przewodnika Szybki Start](stream-analytics-quick-create-vs.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu programu Visual Studio lub [wyeksportować istniejące zadanie do projektu lokalnego](stream-analytics-vs-tools.md#export-jobs-to-a-project). Jeśli chcesz przetestować zapytanie z lokalnymi danymi wejściowymi, postępuj zgodnie z tymi [instrukcjami](stream-analytics-live-data-local-testing.md). Jeśli chcesz przetestować przy użyciu danych wejściowych na żywo, przejdź do następnego kroku.

> [!NOTE]
> Jeśli eksportujesz zadanie do projektu lokalnego i chcesz przeprowadzić test w odniesieniu do strumienia danych wejściowych na żywo, musisz ponownie określić poświadczenia dla wszystkich danych wejściowych.  

Wybierz źródło danych wejściowych i wyjściowych z edytora skryptów i wybierz polecenie **Uruchom lokalnie**. Diagram zadań pojawia się po prawej stronie.

### <a name="view-the-intermediate-result-set"></a>Wyświetl pośredni zestaw wyników  

1. Wybierz krok zapytania, aby przejść do skryptu. Nastąpi automatyczne przekierowanie do odpowiedniego skryptu w edytorze po lewej stronie.

   ![Skrypt nawigowania po diagramie zadań](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Wybierz krok zapytania i wybierz pozycję **Podgląd** w oknie dialogowym zdjęte. Zestaw wyników jest pokazywany na karcie w dolnym oknie wynik.

   ![Wynik podglądu diagramu zadań](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Wyświetl metryki kroków

W tej sekcji przedstawiono metryki dostępne dla każdej części diagramu.

#### <a name="input-sources-live-stream"></a>Źródła danych wejściowych (strumień na żywo)

![Diagram zadań — dynamiczne źródła danych wejściowych](./media/debug-locally-using-job-diagram/live-input.png)

|Metryka|Opis|
|-|-|
|**TaxiRide**| Nazwa danych wejściowych.|
|**Centrum zdarzeń** | Typ źródła danych wejściowych.|
|**Zdarzenia**|Liczba odczytanych zdarzeń.|
|**Zaległe źródła zdarzeń**|Ile więcej komunikatów należy odczytać dla Event Hubs i IoT Hub danych wejściowych.|
|**Zdarzenia w bajtach**|Liczba odczytanych bajtów.|
| **Zdarzenia zdegradowane**|Liczba zdarzeń, dla których wystąpił problem inny niż z deserializacji.|
|**Wczesne zdarzenia**| Liczba zdarzeń, które mają sygnaturę czasową aplikacji przed górnym limitem.|
|**Zdarzenia opóźnione**| Liczba zdarzeń, które mają sygnaturę czasową aplikacji po górnym znaku wodnym.|
|**Źródła zdarzeń**| Liczba odczytanych jednostek danych. Na przykład liczba obiektów BLOB.|

#### <a name="input-sources-local-input"></a>Źródła danych wejściowych (dane wejściowe lokalnego)

![Lokalne źródła danych wejściowych diagramu zadania](./media/debug-locally-using-job-diagram/local-input.png)

|Metryka|Opis|
|-|-|
|**TaxiRide**| Nazwa danych wejściowych.|
|**Liczba wierszy**| Liczba wierszy wygenerowanych z kroku.|
|**Rozmiar danych**| Rozmiar danych wygenerowanych z tego kroku.|
|**Lokalne dane wejściowe**| Użyj danych lokalnych jako danych wejściowych.|

#### <a name="query-steps"></a>Kroki zapytania

![Krok zapytania diagramu zadania](./media/debug-locally-using-job-diagram/query-step.png)

|Metryka|Opis|
|-|-|
|**TripData**|Nazwa tymczasowego zestawu wyników.|
|**Liczba wierszy**| Liczba wierszy wygenerowanych z kroku.|
|**Rozmiar danych**| Rozmiar danych wygenerowanych z tego kroku.|
  
#### <a name="output-sinks-live-output"></a>Ujścia danych wyjściowych (dane wyjściowe na żywo)

![Diagram zadań, który pokazuje lokalne ujścia danych wyjściowych.](./media/debug-locally-using-job-diagram/live-output.png)

|Metryka|Opis|
|-|-|
|**regionaggEH**|Nazwa danych wyjściowych.|
|**Zdarzenia**|Liczba danych wyjściowych zdarzeń do ujścia.|

#### <a name="output-sinks-local-output"></a>Ujścia danych wyjściowych (dane wyjściowe lokalne)

![Lokalne ujścia danych wyjściowych diagramu zadań](./media/debug-locally-using-job-diagram/local-output.png)

|Metryka|Opis|
|-|-|
|**regionaggEH**|Nazwa danych wyjściowych.|
|**Dane wyjściowe lokalnego**| Wynikowe dane wyjściowe do pliku lokalnego.|
|**Liczba wierszy**| Liczba wierszy, które są wyprowadzane do pliku lokalnego.|
|**Rozmiar danych**| Rozmiar danych wyjściowych do pliku lokalnego.|

### <a name="close-job-diagram"></a>Zamknij diagram zadań

Jeśli diagram zadań nie jest już potrzebny, wybierz pozycję **Zamknij** w prawym górnym rogu. Po zamknięciu okna diagramu należy ponownie rozpocząć testowanie lokalne, aby je zobaczyć.

### <a name="view-job-level-metrics-and-stop-running"></a>Wyświetl metryki na poziomie zadania i Zatrzymaj działanie

Inne metryki na poziomie zadania są wyświetlane w konsoli podręcznej. Naciśnij **klawisze CTRL + C** w konsoli programu, jeśli chcesz zatrzymać zadanie.

![Zadanie zatrzymania diagramu zadania](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Ograniczenia

* Power BI i Azure Data Lake Storage Gen1 ujścia danych wyjściowych nie są obsługiwane ze względu na ograniczenia modelu uwierzytelniania.

* Tylko opcje danych wejściowych w chmurze mają obsługę [zasad czasu](./stream-analytics-time-handling.md) , podczas gdy lokalne Opcje wejściowe nie są obsługiwane.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu programu Visual Studio](stream-analytics-quick-create-vs.md)
* [Use Visual Studio to view Azure Stream Analytics jobs (Wyświetlanie zadań usługi Azure Stream Analytics za pomocą programu Visual Studio)](stream-analytics-vs-tools.md)
* [Lokalne testowanie danych na żywo przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio (wersja zapoznawcza)](stream-analytics-live-data-local-testing.md)