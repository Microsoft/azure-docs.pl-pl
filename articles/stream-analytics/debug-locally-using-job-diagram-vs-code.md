---
title: Debuguj Azure Stream Analytics zapytań lokalnie przy użyciu diagramu zadań w Visual Studio Code
description: W tym artykule opisano sposób debugowania zapytań lokalnie przy użyciu diagramu zadań w rozszerzeniu Azure Stream Analytics Visual Studio Code.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f8885229c8ab6784c6c0bf45487812fcc43f9b22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903818"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Debuguj Azure Stream Analytics zapytań lokalnie przy użyciu diagramu zadań w Visual Studio Code

Zadania przesyłania strumieniowego, które nie są wynikiem lub nieoczekiwane wyniki, często potrzebują rozwiązywania problemów. Rozszerzenie Visual Studio Code dla Azure Stream Analytics integruje diagramy zadań, metryki, dzienniki diagnostyczne i wyniki pośrednie, aby pomóc w szybkim wyizolowaniu źródła problemu. Możesz użyć diagramu zadania podczas testowania zapytania lokalnie, aby zbadać pośredni zestaw wyników i metryki dla każdego kroku.

## <a name="debug-a-query-using-job-diagram"></a>Debugowanie zapytania przy użyciu diagramu zadań

Skrypt Azure Stream Analytics służy do przekształcania danych wejściowych w dane wyjściowe. Na diagramie zadań pokazano, jak przepływy danych ze źródeł wejściowych, takich jak centrum zdarzeń lub IoT Hub, przez wiele kroków zapytania do ujścia danych wyjściowych. Każdy krok zapytania jest mapowany do tymczasowego zestawu wyników zdefiniowanego w skrypcie przy użyciu `WITH` instrukcji. Można wyświetlić dane oraz metryki każdego kroku zapytania w każdym pośrednim zestawie wyników, aby znaleźć źródło problemu.

> [!NOTE]
> Ten diagram zadań przedstawia tylko dane i metryki na potrzeby lokalnego testowania w jednym węźle. Nie należy używać go do dostrajania wydajności i rozwiązywania problemów.

### <a name="start-local-testing"></a>Rozpocznij testowanie lokalne

Skorzystaj z tego [przewodnika Szybki Start](quick-create-visual-studio-code.md) , aby dowiedzieć się, jak utworzyć zadanie Stream Analytics przy użyciu Visual Studio Code lub [wyeksportować istniejące zadanie do projektu lokalnego](visual-studio-code-explore-jobs.md). Poświadczenia dla danych wejściowych i wyjściowych są wypełniane automatycznie dla eksportowanych zadań.

Jeśli chcesz przetestować zapytanie z lokalnymi danymi wejściowymi, postępuj zgodnie z tymi [instrukcjami](visual-studio-code-local-run.md). Jeśli chcesz przetestować przy użyciu danych wejściowych na żywo, [Skonfiguruj dane wejściowe](stream-analytics-add-inputs.md) przejdź do następnego kroku. 

Otwórz plik skryptu * \. asaql* i wybierz pozycję **Uruchom lokalnie**. Następnie wybierz pozycję **Użyj lokalnego wejścia** lub **Użyj danych wejściowych na żywo**. Diagram zadań pojawia się po prawej stronie okna.

### <a name="view-the-output-and-intermediate-result-set"></a>Wyświetlanie danych wyjściowych i pośrednich zestawów wyników  

1. Wszystkie dane wyjściowe zadania są wyświetlane w oknie wyników w prawym dolnym rogu okna Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Wyniki zadania wyjściowego](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Wybierz krok zapytania, aby przejść do skryptu. Nastąpi automatyczne przekierowanie do odpowiedniego skryptu w edytorze po lewej stronie. Wynik pośredni pojawia się w oknie wyników w prawym dolnym rogu okna Visual Studio Code.

   > [!div class="mx-imgBorder"]
   > ![Wynik podglądu diagramu zadań](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Wyświetlanie metryk

W tej sekcji przedstawiono metryki dostępne dla każdej części diagramu.

1. Wybierz kartę **metryki** obok karty **wynik** w prawym dolnym rogu okna Visual Studio Code.

2. Wybierz z listy rozwijanej **zadanie** . Możesz wybrać wolne miejsce w węźle grafu, aby przejść do metryk na poziomie zadania. Ten widok zawiera wszystkie metryki, które są aktualizowane co 10 sekund, gdy zadanie jest uruchomione. Możesz wybrać lub usunąć zaznaczenie metryk po prawej stronie, aby wyświetlić je na wykresach.

   > [!div class="mx-imgBorder"]
   > ![Metryki diagramu zadania](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Wybierz nazwę źródła danych wejściowych z listy rozwijanej, aby wyświetlić metryki wejściowe. Źródło danych wejściowych na poniższym zrzucie ekranu jest nazywane *cudzysłowami*. Aby uzyskać więcej informacji na temat metryk wejściowych, zobacz [Omówienie monitorowania zadania Stream Analytics i sposobu monitorowania zapytań](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Metryki wejściowe diagramu zadania](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Wybierz krok zapytania z diagramu zadania lub wybierz nazwę kroku z listy rozwijanej, aby wyświetlić metryki na poziomie kroku. Opóźnienie jest jedyną dostępną metryką kroku.

   > [!div class="mx-imgBorder"]
   > ![Metryki kroków](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Wybierz dane wyjściowe z diagramu lub z listy rozwijanej, aby wyświetlić metryki dotyczące danych wyjściowych. Aby uzyskać więcej informacji na temat metryk danych wyjściowych, zobacz [Omówienie monitorowania zadań Stream Analytics i sposobu monitorowania zapytań](stream-analytics-monitoring.md). Na żywo ujścia danych wyjściowych nie są obsługiwane.

   > [!div class="mx-imgBorder"]
   > ![Metryki wyjściowe](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Wyświetlanie dzienników diagnostycznych

Dzienniki diagnostyczne na poziomie zadania zawierają informacje diagnostyczne na potrzeby wejściowych źródeł danych i ujścia danych wyjściowych. Po wybraniu węzła wejściowego lub węzła wyjściowego są wyświetlane tylko odpowiednie dzienniki. W przypadku wybrania kroku zapytania nie są wyświetlane żadne dzienniki. Wszystkie dzienniki można znaleźć na poziomie zadania, a Dzienniki można filtrować według ważności i czasu.

   > [!div class="mx-imgBorder"]
   > ![Dzienniki diagnostyczne](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Wybierz wpis dziennika, aby wyświetlić cały komunikat.

   > [!div class="mx-imgBorder"]
   > ![Komunikat dotyczący dzienników diagnostycznych](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Inne funkcje diagramu zadań

W razie konieczności można wybrać opcję **Zatrzymaj** lub **Wstrzymaj** na pasku narzędzi. Gdy zadanie zostanie wstrzymane, możesz je wznowić z poziomu ostatnich danych wyjściowych.

> [!div class="mx-imgBorder"]
> ![Zatrzymywanie lub Wstrzymywanie zadania](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Wybierz opcję **Podsumowanie zadania** w prawym górnym rogu diagramu zadania, aby wyświetlić właściwości i konfiguracje dla zadania lokalnego.

> [!div class="mx-imgBorder"]
> ![Podsumowanie zadania lokalnego](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Ograniczenia

* W przypadku uruchamiania lokalnego nie są obsługiwane ujścia danych wyjściowych na żywo.

* Uruchamianie zadania lokalnie z funkcją języka JavaScript jest obsługiwane tylko w systemie operacyjnym Windows.

* Kod niestandardowy C# i funkcje Azure Machine Learning nie są obsługiwane. 

* Tylko opcje danych wejściowych w chmurze mają obsługę [zasad czasu](stream-analytics-out-of-order-and-late-events.md) , podczas gdy lokalne Opcje wejściowe nie są.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zadania Stream Analytics przy użyciu Visual Studio Code](quick-create-visual-studio-code.md)
* [Eksplorowanie Azure Stream Analytics z Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)
* [Przetestuj Azure Stream Analytics zadania lokalnie z danymi wejściowymi na żywo przy użyciu Visual Studio Code](visual-studio-code-local-run-live-input.md)
