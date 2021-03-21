---
title: Liczniki wydajności w Application Insights | Microsoft Docs
description: Monitoruj system i niestandardowe liczniki wydajności .NET w Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 423e17ef2b44286c28b464836075284929d8644c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031365"
---
# <a name="system-performance-counters-in-application-insights"></a>Liczniki wydajności systemu w Application Insights

W systemie Windows dostępne są różne [liczniki wydajności](/windows/desktop/perfctrs/about-performance-counters), takie jak zajętości procesora CPU, pamięci, dysku i użycia sieci. Można także definiować własne liczniki wydajności. Zbieranie liczników wydajności jest obsługiwane, gdy aplikacja działa w ramach usług IIS na hoście lokalnym lub na maszynie wirtualnej, do której masz dostęp administracyjny. Chociaż aplikacje działające jako Azure Web Apps nie mają bezpośredniego dostępu do liczników wydajności, podzbiór dostępnych liczników jest zbierany przez Application Insights.

## <a name="view-counters"></a>Wyświetl liczniki

Okienko metryki zawiera domyślny zestaw liczników wydajności.

![Liczniki wydajności zgłoszone w Application Insights](./media/performance-counters/performance-counters.png)

Bieżące domyślne liczniki, które są skonfigurowane do zbierania dla aplikacji sieci Web ASP.NET:
- \\Czas procesora (%)
- Czas procesora (%) dla procesu \\ znormalizowany
- \\Bajty dostępne pamięci
- Żądania ASP.NET/s
- Zgłoszone wyjątki środowiska CLR platformy .NET/s
- Czas wykonywania ApplicationsRequest ASP.NET
- \\Prywatne bajty procesu
- \\Bajty danych we/wy procesu/s
- Żądania aplikacji ASP.NET \\ w kolejce aplikacji
- Procesor (_Total) \\ czas procesora (%)

Bieżące domyślne liczniki, które są skonfigurowane do zbierania dla ASP.NET Core aplikacji sieci Web:
- \\Czas procesora (%)
- Czas procesora (%) dla procesu \\ znormalizowany
- \\Bajty dostępne pamięci
- \\Prywatne bajty procesu
- \\Bajty danych we/wy procesu/s
- Procesor (_Total) \\ czas procesora (%)

## <a name="add-counters"></a>Dodawanie liczników

Jeśli żądany licznik wydajności nie znajduje się na liście metryk, można go dodać.

1. Dowiedz się, jakie liczniki są dostępne na serwerze, za pomocą tego polecenia programu PowerShell na serwerze lokalnym:

    `Get-Counter -ListSet *`

    (Zobacz [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter) ).
2. Otwórz ApplicationInsights.config.

   * Jeśli podczas projektowania dodano Application Insights do aplikacji, Edytuj ApplicationInsights.config w projekcie, a następnie wdróż go ponownie na serwerach.
3. Edytuj dyrektywę modułu zbierającego wydajność:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> Aplikacje ASP.NET Core nie są dostępne `ApplicationInsights.config` , a w związku z tym powyższa metoda jest nieprawidłowa dla aplikacji ASP.NET Core.

Można przechwycić zarówno liczniki standardowe, jak i te, które zostały zaimplementowane. `\Objects\Processes` jest przykładem licznika standardowego, który jest dostępny we wszystkich systemach Windows. `\Sales(photo)\# Items Sold` to przykład niestandardowego licznika, który może być zaimplementowany w usłudze sieci Web.

Format jest `\Category(instance)\Counter"` lub dla kategorii, które nie mają wystąpień `\Category\Counter` .

`ReportAs` jest wymagany w przypadku nazw liczników niezgodnych `[a-zA-Z()/-_ \.]+` -czyli zawierających znaki, które nie należą do następujących zestawów: litery, nawiasy okrągłe, ukośnik, łącznik, podkreślenie, spacja i kropka.

Jeśli określisz wystąpienie, będzie ono zbierane jako wymiar "CounterInstanceName" raportowanej metryki.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Zbieranie liczników wydajności w kodzie do aplikacji sieci Web ASP.NET lub aplikacji konsoli .NET/.NET Core
Aby zebrać liczniki wydajności systemu i wysłać je do Application Insights, można dostosować Poniższy fragment kodu:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Można też wykonać te same czynności z utworzonymi niestandardowymi metrykami:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Zbieranie liczników wydajności w kodzie dla ASP.NET Core aplikacji sieci Web

Zmodyfikuj `ConfigureServices` metodę w `Startup.cs` klasie jako poniżej.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Liczniki wydajności w analizie
W [analizie](../logs/log-query-overview.md)można wyszukiwać i wyświetlać raporty liczników wydajności.

Schemat **liczniki wydajności** uwidacznia `category` , `counter` nazwę i `instance` nazwę każdego licznika wydajności.  W telemetrii dla każdej aplikacji zobaczysz tylko liczniki dla tej aplikacji. Na przykład, aby zobaczyć, jakie liczniki są dostępne: 

![Liczniki wydajności w Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

("Wystąpienie" odnosi się do wystąpienia licznika wydajności, a nie wystąpienia serwera. Nazwa wystąpienia licznika wydajności zazwyczaj segmentuje liczniki, takie jak czas procesora według nazwy procesu lub aplikacji.

Aby uzyskać wykres dostępnej pamięci w ostatnim okresie: 

![Timechart pamięci w analizie Application Insights](./media/performance-counters/analytics-available-memory.png)

Podobnie jak w przypadku innych telemetrii, **liczniki wydajności** ma także kolumnę `cloud_RoleInstance` , która wskazuje tożsamość wystąpienia serwera hosta, na którym działa aplikacja. Na przykład, aby porównać wydajność aplikacji na różnych komputerach: 

![Wydajność segmentacja według wystąpienia roli w Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Liczba ASP.NET i Application Insights

*Jaka jest różnica między metrykami częstotliwości wyjątków i wyjątków?*

* *Częstotliwość wyjątków* to licznik wydajności systemu. Środowisko CLR zlicza wszystkie zgłoszone i Nieobsłużone wyjątki, a następnie dzieli sumę w interwale próbkowania o długość interwału. Zestaw Application Insights SDK zbiera ten wynik i wysyła go do portalu.

* *Wyjątki* to liczba raportów licznikaexception odebranych przez portal w interwale próbkowania wykresu. Zawiera tylko obsłużone wyjątki, w przypadku których w kodzie Zapisano wywołania Trackexception, i nie są uwzględniane wszystkie [Nieobsłużone wyjątki](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Liczniki wydajności dla aplikacji działających na platformie Azure Web Apps

Aplikacje ASP.NET i ASP.NET Core wdrożone na platformie Azure Web Apps działać w specjalnym środowisku piaskownicy. To środowisko nie zezwala na bezpośredni dostęp do liczników wydajności systemu. Jednak ograniczony podzbiór liczników jest prezentowany jako zmienne środowiskowe, jak opisano [tutaj](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Application Insights SDK for ASP.NET i ASP.NET Core zbiera liczniki wydajności z usługi Azure Web Apps z tych specjalnych zmiennych środowiskowych. W tym środowisku są dostępne tylko podzbiór liczników. pełną listę można znaleźć [tutaj.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Liczniki wydajności w aplikacjach ASP.NET Core

Obsługa liczników wydajności w ASP.NET Core jest ograniczona:

* Wersje [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.4.1 i nowsze zbierają liczniki wydajności, jeśli aplikacja działa w usłudze Azure Web Apps (Windows).
* Wersja zestawu SDK 2.7.1 i nowsze zbiera liczniki wydajności, jeśli aplikacja działa w systemie Windows i w wersji docelowej `NETSTANDARD2.0` lub nowszej.
* W przypadku aplikacji przeznaczonych dla .NET Framework wszystkie wersje zestawu SDK obsługują liczniki wydajności.
* Wersje SDK 2.8.0 i nowsze obsługują licznik procesora/pamięci w systemie Linux. Żaden inny licznik nie jest obsługiwany w systemie Linux. Zalecanym sposobem uzyskiwania liczników systemowych w systemie Linux (i innych środowiskach innych niż Windows) jest użycie [EventCounters](eventcounters.md)

## <a name="alerts"></a>Alerty
Podobnie jak w przypadku innych metryk, można [ustawić alert](../alerts/alerts-log.md) ostrzegający o tym, czy licznik wydajności wykracza poza określony limit. Otwórz okienko alerty, a następnie kliknij pozycję Dodaj alert.

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Śledzenie zależności](./asp-net-dependencies.md)
* [Śledzenie wyjątków](./asp-net-exceptions.md)

