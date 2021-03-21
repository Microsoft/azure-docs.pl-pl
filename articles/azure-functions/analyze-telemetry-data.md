---
title: Analizuj dane telemetryczne Azure Functions w Application Insights
description: Dowiedz się, jak wyświetlać i wykonywać zapytania dotyczące Azure Functions danych telemetrycznych zebranych przez program i przechowywanych w usłudze Azure Application Insights.
ms.topic: how-to
ms.date: 10/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: d06fe64ddc0475b5ca7d9c16876c8dfc9acda544
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729372"
---
# <a name="analyze-azure-functions-telemetry-in-application-insights"></a>Analizuj dane telemetryczne Azure Functions w Application Insights 

Azure Functions integruje się z Application Insights, aby lepiej monitorować aplikacje funkcji. Application Insights zbiera dane telemetryczne wygenerowane przez aplikację funkcji, w tym informacje o zapisach aplikacji do dzienników. Integracja Application Insights jest zazwyczaj włączana podczas tworzenia aplikacji funkcji. Jeśli aplikacja funkcji nie ma zestawu kluczy instrumentacji, należy najpierw [włączyć integrację Application Insights](configure-monitoring.md#enable-application-insights-integration). 

Domyślnie dane zbierane z aplikacji funkcji są przechowywane w Application Insights. W [Azure Portal](https://portal.azure.com)Application Insights zawiera obszerny zestaw wizualizacji danych telemetrycznych. Możesz przejść do szczegółów dzienników błędów i zdarzeń zapytań i metryk. W tym artykule przedstawiono podstawowe przykłady sposobu wyświetlania zebranych danych i wykonywania względem nich zapytań. Aby dowiedzieć się więcej o eksplorowaniu danych aplikacji funkcji w Application Insights, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md). 

Aby dowiedzieć się więcej o przechowywaniu danych i potencjalnych kosztach magazynowania, zobacz Zbieranie i przechowywanie [danych oraz przechowywanie ich w Application Insights](../azure-monitor/app/data-retention-privacy.md).   

## <a name="viewing-telemetry-in-monitor-tab"></a>Wyświetlanie telemetrii na karcie Monitor

Z [włączoną integracją Application Insights](configure-monitoring.md#enable-application-insights-integration)można wyświetlić dane telemetryczne na karcie **monitorowanie** .

1. Na stronie aplikacja funkcji wybierz funkcję, która została uruchomiona co najmniej raz po skonfigurowaniu Application Insights. Następnie wybierz pozycję **monitor** w okienku po lewej stronie. Wybierz pozycję **Odśwież** okresowo, dopóki nie zostanie wyświetlona lista wywołań funkcji.

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Wyświetlenie listy w czasie, gdy klient telemetrii wysyła dane do serwera, może upłynąć do 5 minut. Opóźnienie nie ma zastosowania do [Live Metrics Stream](../azure-monitor/app/live-stream.md). Ta usługa nawiązuje połączenie z hostem funkcji podczas ładowania strony, dlatego dzienniki są przesyłane strumieniowo bezpośrednio do strony.

1. Aby wyświetlić dzienniki dla określonego wywołania funkcji, wybierz łącze kolumna **Data (UTC)** dla tego wywołania. Dane wyjściowe rejestrowania dla tego wywołania pojawiają się na nowej stronie.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

1. Wybierz pozycję **Uruchom w Application Insights** , aby wyświetlić Źródło zapytania pobierającego dane dziennika Azure monitor w usłudze Azure log. Jeśli używasz usługi Azure Log Analytics w ramach subskrypcji po raz pierwszy, zostanie wyświetlony monit o jej włączenie.

1. Po włączeniu Log Analytics zostanie wyświetlona następująca kwerenda. Można zobaczyć, że wyniki zapytania są ograniczone do 30 ostatnich dni ( `where timestamp > ago(30d)` ), a wyniki nie mogą zawierać więcej niż 20 wierszy ( `take 20` ). W przeciwieństwie do listy szczegóły wywołania funkcji jest używany w ciągu ostatnich 30 dni bez limitu.

   ![Lista wywołań analizy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Aby uzyskać więcej informacji, zobacz temat [wysyłanie danych telemetrycznych](#query-telemetry-data) w dalszej części tego artykułu.

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie danych telemetrycznych w Application Insights

Aby otworzyć Application Insights z aplikacji funkcji w [Azure Portal](https://portal.azure.com):

1. Przejdź do aplikacji funkcji w portalu.

1. Wybierz pozycję **Application Insights** w obszarze **Ustawienia** na lewej stronie. 

1. Jeśli używasz usługi Application Insights z subskrypcją, zobaczysz monit o jej włączenie. W tym celu wybierz pozycję **włącz Application Insights**, a następnie wybierz pozycję **Zastosuj** na następnej stronie.

![Otwórz Application Insights na stronie przeglądu aplikacji funkcji](media/functions-monitoring/ai-link.png)

Informacje o sposobach używania Application Insights można znaleźć w [dokumentacji Application Insights](/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w Application Insights. Jeśli znasz już Application Insights, możesz przejść bezpośrednio do [sekcji dotyczącej sposobu konfigurowania i dostosowywania danych telemetrycznych](configure-monitoring.md#configure-log-levels).

![Karta przegląd Application Insights](media/functions-monitoring/metrics-explorer.png)

Następujące obszary Application Insights mogą być pomocne podczas oceny zachowania, wydajności i błędów w funkcjach:

| Zbadaj | Opis |
| ---- | ----------- |
| **[Niepowodzenia](../azure-monitor/app/asp-net-exceptions.md)** |  Twórz wykresy i alerty na podstawie błędów funkcji i wyjątków serwera. **Nazwa operacji** to nazwa funkcji. Błędy w zależnościach nie są wyświetlane, chyba że zaimplementowano niestandardową telemetrię dla zależności. |
| **[Wydajność](../azure-monitor/app/performance-counters.md)** | Analizuj problemy z wydajnością, wyświetlając wykorzystanie zasobów i przepływność dla **wystąpień ról w chmurze**. Te dane dotyczące wydajności mogą być przydatne w scenariuszach debugowania, w których funkcje boggingją bazowe zasoby. |
| **[Metryki](../azure-monitor/essentials/metrics-charts.md)** | Tworzenie wykresów i alertów opartych na metrykach. Metryki obejmują liczbę wywołań funkcji, czas wykonywania i współczynnik sukcesu. |
| **[Metryki na żywo](../azure-monitor/app/live-stream.md)** | Wyświetlanie danych metryk w miarę ich tworzenia w czasie niemal rzeczywistym. |

## <a name="query-telemetry-data"></a>Zapytanie danych telemetrycznych

[Application Insights Analytics](../azure-monitor/logs/log-query-overview.md) zapewnia dostęp do wszystkich danych telemetrycznych w formie tabel w bazie danych. Analiza zawiera język zapytań służący do wyodrębniania, manipulowania i wizualizacji danych. 

Wybierz pozycję **dzienniki** , aby zbadać lub zbadać zarejestrowane zdarzenia.

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Oto przykład zapytania, który pokazuje rozkład żądań na proces roboczy w ciągu ostatnich 30 minut.

```kusto
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Dostępne tabele są wyświetlane na karcie **schemat** po lewej stronie. Dane generowane przez wywołania funkcji można znaleźć w następujących tabelach:

| Tabela | Opis |
| ----- | ----------- |
| **ścieżki** | Dzienniki utworzone przez środowisko uruchomieniowe, kontroler skalowania i ślady z kodu funkcji. |
| **żądania** | Jedno żądanie wywołania funkcji. |
| **wyłączenia** | Wszystkie wyjątki zgłoszone przez środowisko uruchomieniowe. |
| **customMetrics** | Liczba zakończonych powodzeniem i niepowodzeniem wywołań, współczynnik sukcesu i czas trwania. |
| **customEvents** | Zdarzenia śledzone przez środowisko uruchomieniowe, na przykład: żądania HTTP wyzwalające funkcję. |
| **Liczniki wydajności** | Informacje o wydajności serwerów, na których działają funkcje. |

Inne tabele są przeznaczone dla testów dostępności, a dane telemetryczne klienta i przeglądarki. Możesz zaimplementować niestandardową telemetrię, aby dodać do nich dane.

W każdej tabeli niektóre dane specyficzne dla funkcji znajdują się w `customDimensions` polu.  Na przykład następujące zapytanie pobiera wszystkie ślady mające poziom rejestrowania `Error` .

```kusto
traces 
| where customDimensions.LogLevel == "Error"
```

Środowisko uruchomieniowe udostępnia `customDimensions.LogLevel` `customDimensions.Category` pola i. W dziennikach można podać dodatkowe pola, które można napisać w kodzie funkcji. Aby zapoznać się z przykładem w języku C#, zobacz [Rejestrowanie strukturalne](functions-dotnet-class-library.md#structured-logging) w przewodniku dewelopera biblioteki klas .NET.

## <a name="query-scale-controller-logs"></a>Dzienniki kontrolera skali zapytań

_Ta funkcja jest w wersji zapoznawczej._

Po włączeniu zarówno [rejestrowania kontrolera skalowania](configure-monitoring.md#configure-scale-controller-logs) , jak i [integracji Application Insights](configure-monitoring.md#enable-application-insights-integration)można użyć przeszukiwania dzienników Application Insights, aby wykonać zapytanie dotyczące wyemitowanych dzienników kontrolera skalowania. Dzienniki kontrolera skalowania są zapisywane w `traces` kolekcji w kategorii **ScaleControllerLogs** .

Poniższe zapytanie może służyć do wyszukiwania wszystkich dzienników kontrolera skalowania dla bieżącej aplikacji funkcji w określonym przedziale czasu:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
```

Następujące zapytanie zostaje rozwinięte w poprzednim zapytaniu, aby pokazać, jak uzyskać tylko dzienniki wskazujące zmianę w skali:

```kusto
traces 
| extend CustomDimensions = todynamic(tostring(customDimensions))
| where CustomDimensions.Category == "ScaleControllerLogs"
| where message == "Instance count changed"
| extend Reason = CustomDimensions.Reason
| extend PreviousInstanceCount = CustomDimensions.PreviousInstanceCount
| extend NewInstanceCount = CustomDimensions.CurrentInstanceCount
```

## <a name="consumption-plan-specific-metrics"></a>Metryki specyficzne dla planu zużycia

W przypadku korzystania z [planu zużycia](consumption-plan.md) *koszt* wykonania pojedynczego wykonania funkcji jest mierzony w *GB-sekund*. Koszt wykonywania jest obliczany przez połączenie jego użycia pamięci z jego czasem wykonywania. Aby dowiedzieć się więcej, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md).

Następujące zapytania telemetryczne są specyficzne dla metryk, które wpływają na koszt działania funkcji w planie zużycia.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="azure-monitor-metrics"></a>Metryki Azure Monitor

Oprócz danych telemetrycznych zbieranych przez Application Insights można również uzyskać dane dotyczące sposobu działania aplikacji funkcji z [Azure monitor metryk](../azure-monitor/essentials/data-platform-metrics.md). Wraz z typowymi [metrykami dostępnymi dla App Service aplikacji](../app-service/web-sites-monitor.md#understand-metrics)istnieją dwie metryki specyficzne dla interesujących Cię funkcji:

| Metric | Opis |
| ---- | ---- |
| **FunctionExecutionCount** | Licznik wykonywania funkcji wskazuje liczbę wykonań aplikacji funkcji. Jest to skorelowane z liczbą przypadków uruchomienia funkcji w aplikacji. Ta Metryka nie jest obecnie obsługiwana w przypadku planów Premium i dedykowanych (App Service) działających w systemie Linux. |
| **FunctionExecutionUnits** | Jednostki wykonywania funkcji są kombinacją czasu wykonywania i użycia pamięci.  Dane pamięci nie są obecnie dostępne w Azure Monitor. Jeśli jednak chcesz zoptymalizować użycie pamięci przez aplikację, można użyć danych licznika wydajności zebranych przez Application Insights. Ta Metryka nie jest obecnie obsługiwana w przypadku planów Premium i dedykowanych (App Service) działających w systemie Linux.|

Aby dowiedzieć się więcej o obliczaniu kosztów planu zużycia przy użyciu danych Application Insights, zobacz [szacowanie kosztów planu zużycia](functions-consumption-costs.md). Aby dowiedzieć się więcej o korzystaniu z Eksploratora monitora do wyświetlania metryk, zobacz [wprowadzenie do usługi Azure Eksplorator metryk](../azure-monitor/essentials/metrics-getting-started.md).


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu Azure Functions:

+ [Monitorowanie usługi Azure Functions](functions-monitoring.md)
+ [Jak skonfigurować monitorowanie dla Azure Functions](configure-monitoring.md)