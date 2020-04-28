---
title: Liczniki zdarzeń w Application Insights | Microsoft Docs
description: Monitoruj system i niestandardową platformę .NET/.NET Core EventCounters w Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: e1037766587f58a30c20f614726e1241c16e5a16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187099"
---
# <a name="eventcounters-introduction"></a>Wprowadzenie do EventCounters

`EventCounter`jest mechanizmem .NET/.NET Core do publikowania i korzystania z liczników lub statystyk. [Ten](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) dokument zawiera omówienie i przykłady `EventCounters` sposobu ich publikowania i korzystania z nich. EventCounters są obsługiwane na wszystkich platformach systemu operacyjnego — Windows, Linux i macOS. Można go traktować jako odpowiednik na wielu platformach dla [liczniki wydajności](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter) , który jest obsługiwany tylko w systemach Windows.

Użytkownicy mogą publikować dowolne niestandardowe `EventCounters` , aby spełniały ich potrzeby, ale środowisko uruchomieniowe programu .net Core 3,0 domyślnie publikuje zestaw tych liczników. W tym dokumencie przedstawiono kroki wymagane do zbierania i wyświetlania `EventCounters` (zdefiniowane przez system lub zdefiniowane przez użytkownika) w usłudze Azure Application Insights.

## <a name="using-application-insights-to-collect-eventcounters"></a>Używanie Application Insights do zbierania EventCounters

Application Insights obsługuje zbieranie `EventCounters` ze swoimi `EventCounterCollectionModule`składnikami, który jest częścią nowo wydanego pakietu NuGet [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule`jest automatycznie włączana podczas korzystania z [AspNetCore](asp-net-core.md) lub [WorkerService](worker-service.md). `EventCounterCollectionModule`zbiera liczniki z niekonfigurowalną częstotliwością zbierania wynoszącą 60 sekund. Nie są wymagane żadne specjalne uprawnienia do zbierania EventCounters.

## <a name="default-counters-collected"></a>Zebrane liczniki domyślne

W przypadku aplikacji uruchamianych na platformie .NET Core 3,0 następujące liczniki są zbierane automatycznie przez zestaw SDK. Nazwa liczników będzie mieć postać "Kategoria | Licznik ".

|Kategoria | Licznik|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Liczniki kategorii Microsoft. AspNetCore. hosting są dodawane tylko w aplikacjach ASP.NET Core.

## <a name="customizing-counters-to-be-collected"></a>Dostosowywanie liczników do zebrania

Poniższy przykład pokazuje, jak dodać lub usunąć liczniki. To dostosowanie można wykonać w `ConfigureServices` metodzie aplikacji Application Insights po włączeniu zbierania danych telemetrycznych przy użyciu funkcji `AddApplicationInsightsTelemetry()` lub `AddApplicationInsightsWorkerService()`. Poniżej znajduje się przykładowy kod z aplikacji ASP.NET Core. W przypadku innych typów aplikacji zapoznaj się z [tym](worker-service.md#configuring-or-removing-default-telemetrymodules) dokumentem.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Liczniki zdarzeń w Eksploratorze metryk

Aby wyświetlić metryki EventCounter w [Eksploratorze metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), wybierz pozycję Application Insights Resource i wybrane metryki oparte na dzienniku jako przestrzeń nazw metryki. Następnie metryki EventCounter są wyświetlane w obszarze Kategoria niestandardowa.

> [!div class="mx-imgBorder"]
> ![Liczniki zdarzeń zgłoszone w Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Liczniki zdarzeń w analizie

Raporty licznika zdarzeń można także wyszukiwać i wyświetlać w [analizie](../../azure-monitor/app/analytics.md)w tabeli **customMetrics** .

Na przykład uruchom następujące zapytanie, aby zobaczyć, jakie liczniki są zbierane i dostępne dla kwerendy:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Liczniki zdarzeń zgłoszone w Application Insights](./media/event-counters/analytics-event-counters.png)

Aby uzyskać wykres określonego licznika (na przykład: `ThreadPool Completed Work Item Count`) w ciągu ostatniego okresu, uruchom następujące zapytanie.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Czat jednego licznika w Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Podobnie jak w przypadku **customMetrics** innych telemetrii, `cloud_RoleInstance` customMetrics ma także kolumnę, która wskazuje tożsamość wystąpienia serwera hosta, na którym działa aplikacja. Powyższe zapytanie pokazuje wartość licznika na wystąpienie i może służyć do porównywania wydajności różnych wystąpień serwera.

## <a name="alerts"></a>Alerty
Podobnie jak w przypadku innych metryk, można [ustawić alert](../../azure-monitor/app/alerts.md) ostrzegający o tym, czy licznik zdarzeń wykracza poza określony limit. Otwórz okienko alerty, a następnie kliknij pozycję Dodaj alert.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Czy mogę zobaczyć EventCounters w metrykach na żywo?

Metryki na żywo nie wyświetlają EventCounters z dzisiaj. Użyj Eksploratora metryk lub analizy, aby wyświetlić dane telemetryczne.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>Które platformy mogą zobaczyć domyślną listę liczników programu .NET Core 3,0?

EventCounter nie wymaga żadnych specjalnych uprawnień i jest obsługiwana we wszystkich platformach .NET Core 3,0. Obejmuje to następujące działania:

* **System operacyjny**: Windows, Linux lub macOS.
* **Metoda hostingu**: w procesie lub w trakcie procesu.
* **Metoda wdrażania**: zależna od platformy lub samodzielna.
* **Serwer sieci Web**: IIS (Internet Information Server) lub Kestrel.
* **Platforma hostingu**: funkcja Web Apps Azure App Service, Azure VM, Docker, Azure Kubernetes Service (AKS) i tak dalej.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Włączono Application Insights z portalu aplikacji sieci Web platformy Azure. Ale nie widzę EventCounters.?

 [Rozszerzenie Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) dla ASP.NET Core nie obsługuje jeszcze tej funkcji. Ten dokument zostanie zaktualizowany, gdy ta funkcja jest obsługiwana.

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md)
