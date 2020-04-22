---
title: Śledzenie zależności w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Monitoruj wywołania zależności z lokalnej lub sieci Web aplikacji platformy Microsoft Azure za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1e30d8036c1fc624d39f027f38e314c6c57360f6
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731505"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Śledzenie zależności w usłudze Azure Application Insights 

*Zależność* jest zewnętrznym składnikiem, który jest wywoływany przez aplikację. Zazwyczaj jest to usługa wywoływana przy użyciu protokołu HTTP, bazy danych lub systemu plików. [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) mierzy czas trwania wywołań zależności, czy jego niepowodzenie lub nie, wraz z dodatkowymi informacjami, takimi jak nazwa zależności i tak dalej. Można zbadać wywołania zależności określonych i skorelować je z żądaniami i wyjątkami.

## <a name="automatically-tracked-dependencies"></a>Automatycznie śledzone zależności

Pakiety SDK usługi Application Insights dla `DependencyTrackingTelemetryModule` platformy .NET i .NET Core są dostarczane, z którym jest moduł telemetryczny, który automatycznie zbiera zależności. Ta kolekcja zależności jest włączona automatycznie dla [aplikacji ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) i [ASP.NET Core,](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) po skonfigurowaniu zgodnie z połączonymi oficjalnymi dokami. `DependencyTrackingTelemetryModule` jest wysyłany jako [ten](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) pakiet NuGet i jest wprowadzany automatycznie `Microsoft.ApplicationInsights.Web` podczas `Microsoft.ApplicationInsights.AspNetCore`korzystania z pakietów NuGet lub .

 `DependencyTrackingTelemetryModule`obecnie automatycznie śledzi następujące zależności:

|Zależności |Szczegóły|
|---------------|-------|
|Http/Https | Połączenia lokalne lub zdalne http/https |
|Wywołania WCF| Śledzone automatycznie tylko wtedy, gdy używane są powiązania oparte na technologii Http.|
|SQL | Połączenia wykonane `SqlClient`za pomocą . Zobacz [to](#advanced-sql-tracking-to-get-full-sql-query) dla przechwytywania kwerendy SQL.  |
|[Magazyn platformy Azure (obiekt blob, tabela, kolejka)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania wykonane za pomocą klienta usługi Azure Storage. |
|[SDK klienta usługi EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersja 1.1.0 i powyżej. |
|[SDK klienta usługi ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Wersja 3.0.0 i powyżej. |
|Azure Cosmos DB | Śledzone automatycznie tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Tryb TCP nie zostanie przechwycony przez usługa Application Insights. |

Jeśli brakuje zależności lub przy użyciu innego SDK upewnij się, że znajduje się na liście [automatycznie zebranych zależności.](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies) Jeśli zależność nie jest zbierane automatycznie, nadal można śledzić go ręcznie za pomocą [wywołania zależności ścieżki](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Konfigurowanie automatycznego śledzenia zależności w aplikacjach konsoli

Aby automatycznie śledzić zależności z aplikacji konsoli .NET, zainstaluj `Microsoft.ApplicationInsights.DependencyCollector` `DependencyTrackingTelemetryModule` pakiet Nuget i inicjuj w następujący sposób:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Dla aplikacji konsoli .NET Core TelemetryConfiguration.Active jest przestarzały. Zapoznaj się z wytycznymi [zawartymi](https://docs.microsoft.com/azure/azure-monitor/app/worker-service) w dokumentacji obsługi technicznej pracownika i [dokumentacji monitorowania ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

### <a name="how-automatic-dependency-monitoring-works"></a>Jak działa automatyczne monitorowanie zależności?

Zależności są zbierane automatycznie przy użyciu jednej z następujących technik:

* Korzystanie z instrumentacji kodu bajtów wokół wybranych metod. (InstrumentationEngine albo z StatusMonitor lub Rozszerzenie aplikacji Azure Web)
* Wywołania zwrotne w ramach eventsource
* Wywołania zwrotne usługi DiagnosticSource (w najnowszych podstawowych zestawy SDK .NET/.NET)

## <a name="manually-tracking-dependencies"></a>Ręczne śledzenie zależności

Poniżej przedstawiono kilka przykładów zależności, które nie są zbierane automatycznie, a zatem wymagają śledzenia ręcznego.

* Usługa Azure Cosmos DB jest śledzona automatycznie tylko wtedy, gdy jest używany [protokół HTTP/HTTPS.](../../cosmos-db/performance-tips.md#networking) Tryb TCP nie zostanie przechwycony przez usługa Application Insights.
* Redis

Dla tych zależności nie są zbierane automatycznie przez SDK, można śledzić je ręcznie przy użyciu [interfejsu API TrackDependency,](api-custom-events-metrics.md#trackdependency) który jest używany przez standardowe moduły automatycznej kolekcji.

Na przykład jeśli tworzysz kod z zestawu, który nie napisał samodzielnie, można czas wszystkie wywołania do niego, aby dowiedzieć się, jaki wkład ma do czasów odpowiedzi. Aby te dane były wyświetlane na wykresach zależności w `TrackDependency`usłudze Application Insights, wyślij je za pomocą programu .

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Alternatywnie, `TelemetryClient` zapewnia `StartOperation` metody `StopOperation` rozszerzenia i które mogą być używane do ręcznego śledzenia zależności, jak pokazano [tutaj](custom-operations-tracking.md#outgoing-dependencies-tracking)

Jeśli chcesz wyłączyć standardowy moduł śledzenia zależności, usuń odwołanie do DependencyTrackingTelemetryModule w [AplikacjiInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) dla aplikacji ASP.NET. Aby uzyskać ASP.NET aplikacje Core, postępuj zgodnie z instrukcjami [tutaj](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Śledzenie połączeń AJAX ze stron sieci Web

W przypadku stron sieci web pakiet SDK javascript aplikacji aplikacji Aplikacji JavaScript automatycznie zbiera wywołania AJAX jako zależności.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Zaawansowane śledzenie SQL w celu uzyskania pełnego zapytania SQL

W przypadku wywołań SQL nazwa serwera i bazy danych jest `DependencyTelemetry`zawsze zbierana i przechowywana jako nazwa zebranego . Istnieje dodatkowe pole o nazwie "dane", które może zawierać pełny tekst zapytania SQL.

W przypadku aplikacji ASP.NET Core nie jest wymagany żaden dodatkowy krok, aby uzyskać pełną kwerendę SQL.

W przypadku aplikacji ASP.NET pełne zapytanie SQL jest zbierane za pomocą instrumentacji kodu bajtów, która wymaga aparatu instrumentacji. Wymagane są dodatkowe kroki specyficzne dla platformy, jak opisano poniżej.

| Platforma | Kroki potrzebne do uzyskania pełnego zapytania SQL |
| --- | --- |
| Aplikacja internetowa platformy Azure |W panelu sterowania aplikacji sieci Web otwórz blok [Usługi Application Insights](../../azure-monitor/app/azure-web-apps.md) i włącz polecenia SQL w obszarze .NET |
| Serwer usług IIS (maszyna wirtualna platformy Azure, on-prem itd.) | Użyj modułu PowerShell Monitor stanu, aby [zainstalować aparat instrumentacji](../../azure-monitor/app/status-monitor-v2-api-reference.md) i ponownie uruchomić usługi IIS. |
| Azure Cloud Service | Dodawanie [zadania uruchamiania w celu zainstalowania monitora stanu](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Aplikacja powinna być dołączana do zestawu SDK applicationInsights w czasie kompilacji, instalując pakiety NuGet dla [aplikacji ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) lub [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Nieobsługiwane

W powyższych przypadkach prawidłowym sposobem sprawdzania poprawności, że aparat instrumentacji jest poprawnie zainstalowany, `DependencyTelemetry` jest sprawdzenie, czy wersja SDK zebranych jest "rddp". 'rdddsd' lub 'rddf' wskazuje, że zależności są zbierane za pośrednictwem wywołań zwrotnych DiagnosticSource lub EventSource, a zatem pełna kwerenda SQL nie zostanie przechwycona.

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności

* [Mapa aplikacji](app-map.md) wizualizuje zależności między aplikacją a sąsiednimi składnikami.
* [Diagnostyka transakcji](transaction-diagnostics.md) pokazuje ujednolicone, skorelowane dane serwera.
* [Karta Przeglądarki](javascript.md) pokazuje połączenia AJAX z przeglądarek użytkowników.
* Kliknij z powolnych lub nieudanych żądań, aby sprawdzić ich wywołania zależności.
* [Analytics](#logs-analytics) może służyć do kwerendy danych zależności.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>Diagnozowanie powolnych żądań

Każde zdarzenie żądania jest skojarzone z wywołania zależności, wyjątki i inne zdarzenia, które są śledzone podczas przetwarzania żądania przez aplikację. Więc jeśli niektóre żądania robią źle, można dowiedzieć się, czy to z powodu powolnych odpowiedzi z zależności.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie od żądań do zależności

Otwórz kartę **Wydajność** i przejdź do karty **Zależności** u góry obok operacji.

Kliknij **nazwę zależności** w obszarze ogólny. Po wybraniu zależności wykres rozkładu tej zależności czas trwania pojawi się po prawej stronie.

![Na karcie wydajność kliknij kartę Zależność u góry, a następnie nazwę zależności na wykresie](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknij niebieski przycisk **Przykłady** w prawym dolnym rogu, a następnie w próbce, aby wyświetlić szczegóły transakcji end-to-end.

![Kliknij przykład, aby wyświetlić szczegóły transakcji end-to-end](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profiluj swoją witrynę na żywo

Nie masz pojęcia, gdzie idzie czas? [Profiler usługi Application Insights](../../azure-monitor/app/profiler.md) śledzi wywołania HTTP w witrynie na żywo i pokazuje funkcje w kodzie, który trwał najdłużej.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Żądania nie powiodły się mogą być również skojarzone z nieudanymi wywołaniami zależności.

Możemy przejść do karty **Błędy** po lewej stronie, a następnie kliknij kartę **zależności** u góry.

![Kliknij wykres żądań nieudanych](./media/asp-net-dependencies/4-fail.png)

W tym miejscu będzie można zobaczyć liczbę zależności nie powiodło się. Aby uzyskać więcej szczegółów na temat wystąpienia nie powiodło się, próbując kliknąć nazwę zależności w dolnej tabeli. Możesz kliknąć niebieski przycisk **Zależności** w prawym dolnym rogu, aby uzyskać szczegółowe informacje o transakcji end-to-end.

## <a name="logs-analytics"></a>Dzienniki (Analytics)

Zależności można śledzić w [języku zapytania Kusto](/azure/kusto/query/). Oto kilka przykładów.

* Znajdź wszystkie nieudane wywołania zależności:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Znajdź połączenia AJAX:

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Znajdź wywołania zależności skojarzone z żądaniami:

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Znajdź wywołania AJAX skojarzone z widokami strony:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*W jaki sposób raport automatycznego modułu zbierającego zależności nie powiódł wywołań zależności?*

* Nieudane wywołania zależności będą miały pole "sukces" ustawione na False. `DependencyTrackingTelemetryModule`nie zgłasza `ExceptionTelemetry`. Pełny model danych dla zależności jest opisany [w tym miejscu](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>SDK typu open source
Podobnie jak każdy moduł SDK usługi Application Insights, moduł kolekcji zależności jest również open source. Przeczytaj i wnieś udział w kodzie lub zgłoś problemy [w oficjalnym repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Następne kroki

* [Wyjątki](../../azure-monitor/app/asp-net-exceptions.md)
* [Dane & użytkownika](../../azure-monitor/app/javascript.md)
* [Dostępność](../../azure-monitor/app/monitor-web-app-availability.md)
