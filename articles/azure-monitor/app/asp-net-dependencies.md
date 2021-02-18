---
title: Śledzenie zależności na platformie Azure Application Insights | Microsoft Docs
description: Monitoruj wywołania zależności z lokalnej lub Microsoft Azure aplikacji sieci Web z Application Insights.
ms.topic: conceptual
ms.date: 08/26/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 05b6c29b121cbf42cf0ebe12b2879e50735db7ea
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652007"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Śledzenie zależności w usłudze Azure Application Insights 

*Zależność* to składnik, który jest wywoływany przez aplikację. Zwykle jest to usługa wywoływana przy użyciu protokołu HTTP lub baza danych bądź system plików. [Application Insights](./app-insights-overview.md) mierzy czas trwania wywołań zależności, bez względu na to, czy wystąpił błąd, czy nie, wraz z dodatkowymi informacjami, takimi jak nazwa zależności i tak dalej. Można zbadać konkretne wywołania zależności i skorelować je z żądaniami i wyjątkami.

## <a name="automatically-tracked-dependencies"></a>Automatycznie śledzone zależności

Zestawy SDK Application Insights dla platform .NET i .NET Core są dostarczane z systemem `DependencyTrackingTelemetryModule` , który jest modułem telemetrii, który automatycznie zbiera zależności. Ta kolekcja zależności jest włączana automatycznie dla aplikacji [ASP.NET](./asp-net.md) i [ASP.NET Core](./asp-net-core.md) , gdy jest skonfigurowana zgodnie z powiązanymi oficjalnymi dokumentami. `DependencyTrackingTelemetryModule`program jest dostarczany [](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) jako pakiet NuGet i zostaje automatycznie uruchomiony w przypadku używania jednego z pakietów NuGet `Microsoft.ApplicationInsights.Web` lub `Microsoft.ApplicationInsights.AspNetCore` .

 `DependencyTrackingTelemetryModule` obecnie śledzi następujące zależności automatycznie:

|Zależności |Szczegóły|
|---------------|-------|
|Protokół http/https | Lokalne lub zdalne wywołania http/https |
|Wywołania WCF| Śledzone automatycznie tylko w przypadku użycia powiązań opartych na protokole HTTP.|
|SQL | Wywołania wykonane przy użyciu `SqlClient` . Zobacz [ten](#advanced-sql-tracking-to-get-full-sql-query) sposób przechwytywania zapytania SQL.  |
|[Azure Storage (obiekt BLOB, tabela, kolejka)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Wywołania wykonane za pomocą klienta usługi Azure Storage. |
|[Zestaw SDK klienta EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Wersja 1.1.0 i nowsza. |
|[Zestaw SDK klienta ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Wersja 3.0.0 i nowsza. |
|Azure Cosmos DB | Śledzone automatycznie tylko wtedy, gdy jest używany protokół HTTP/HTTPS. Tryb TCP nie zostanie przechwycony przez Application Insights. |

Jeśli nie masz zależności lub korzystasz z innego zestawu SDK, upewnij się, że znajduje się na liście [autozbieranych zależności](./auto-collect-dependencies.md). Jeśli zależność nie jest zbierana domyślnie, można ją ręcznie śledzić przy użyciu [wywołania zależności śledzenia](./api-custom-events-metrics.md#trackdependency).

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Skonfiguruj automatyczne śledzenie zależności w aplikacjach konsolowych

Aby automatycznie śledzić zależności od aplikacji konsolowych platformy .NET, należy zainstalować pakiet NuGet `Microsoft.ApplicationInsights.DependencyCollector` i zainicjować go `DependencyTrackingTelemetryModule` w następujący sposób:

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

Dla aplikacji konsoli .NET Core TelemetryConfiguration. Active jest przestarzałe. Zapoznaj się ze wskazówkami zawartymi w [dokumentacji usługi Worker](./worker-service.md) i dokumentacji dotyczącej [monitorowania ASP.NET Core](./asp-net-core.md)

### <a name="how-automatic-dependency-monitoring-works"></a>Jak działa automatyczne monitorowanie zależności?

Zależności są zbierane automatycznie przy użyciu jednej z następujących metod:

* Używanie Instrumentacji kodu bajtowego dookoła metod SELECT. (InstrumentationEngine z StatusMonitor lub rozszerzenia aplikacji sieci Web platformy Azure)
* Wywołania zwrotne EventSource
* Wywołania zwrotne DiagnosticSource (w najnowszych zestawach SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Ręczne śledzenie zależności

Poniżej przedstawiono kilka przykładów zależności, które nie są zbierane automatycznie, a tym samym wymagają śledzenia ręcznego.

* Azure Cosmos DB jest śledzony automatycznie tylko wtedy, gdy używany jest [protokół http/https](../../cosmos-db/performance-tips.md#networking) . Tryb TCP nie zostanie przechwycony przez Application Insights.
* Redis

Dla tych zależności, które nie są automatycznie zbierane przez zestaw SDK, można je śledzić ręcznie przy użyciu [interfejsu API TrackDependency](api-custom-events-metrics.md#trackdependency) , który jest używany przez standardowe moduły automatycznego zbierania danych.

Na przykład, Jeśli kompilujesz swój kod z zestawem, który nie został napisany samodzielnie, możesz przeznaczyć na niego wszystkie wywołania, aby dowiedzieć się, jaki wkład ma w czasie odpowiedzi. Aby te dane były wyświetlane na wykresach zależności w Application Insights, wyślij je przy użyciu `TrackDependency` .

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

Alternatywnie, `TelemetryClient` dostarcza metody rozszerzające, `StartOperation` `StopOperation` które mogą być używane do ręcznego śledzenia zależności, jak pokazano [poniżej](custom-operations-tracking.md#outgoing-dependencies-tracking)

Jeśli chcesz wyłączyć standardowy moduł śledzenia zależności, usuń odwołanie do DependencyTrackingTelemetryModule w [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) dla aplikacji ASP.NET. W przypadku aplikacji ASP.NET Core należy postępować zgodnie z instrukcjami znajdującymi się [tutaj](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Śledzenie wywołań AJAX ze stron sieci Web

W przypadku stron sieci Web zestaw SDK Application Insights JavaScript automatycznie zbiera wywołania AJAX jako zależności.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Zaawansowane śledzenie SQL, aby uzyskać pełne zapytanie SQL

W przypadku wywołań SQL nazwa serwera i bazy danych są zawsze zbierane i przechowywane jako nazwa zebranych elementów `DependencyTelemetry` . Istnieje dodatkowe pole o nazwie "Data", które może zawierać pełny tekst zapytania SQL.

W przypadku aplikacji ASP.NET Core teraz wymagane jest zalogowanie do kolekcji tekstu SQL za pomocą
```csharp
services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) => { module. EnableSqlCommandTextInstrumentation = true; });
```

W przypadku aplikacji ASP.NET cały tekst zapytania SQL jest zbierany przy użyciu instrumentacji kodu bajtowego, która wymaga użycia aparatu Instrumentacji lub pakietu NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) zamiast biblioteki System. Data. SqlClient. Kroki specyficzne dla platformy umożliwiające pełną kolekcję zapytań SQL zostały opisane poniżej:

| Platforma | Kroki, które trzeba wykonać, aby uzyskać pełne zapytanie SQL |
| --- | --- |
| Aplikacja internetowa platformy Azure |W panelu sterowania aplikacji sieci Web [Otwórz blok Application Insights](../../azure-monitor/app/azure-web-apps.md) i Włącz polecenia SQL w obszarze .NET |
| Serwer IIS (maszyna wirtualna platformy Azure, premium itd.) | Użyj pakietu NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) lub użyj modułu Monitor stanu PowerShell, aby [zainstalować aparat Instrumentacji](../../azure-monitor/app/status-monitor-v2-api-reference.md#enable-instrumentationengine) i ponownie uruchomić usługi IIS. |
| Azure Cloud Service | Dodaj [zadanie uruchamiania, aby zainstalować StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Twoja aplikacja powinna zostać dołączona do zestawu ApplicationInsights SDK w czasie kompilacji przez zainstalowanie pakietów NuGet dla aplikacji [ASP.NET](./asp-net.md) lub [ASP.NET Core](./asp-net-core.md) |
| IIS Express | Użyj pakietu NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .
| Zadania internetowe platformy Azure | Użyj pakietu NuGet [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) .

Oprócz kroków specyficznych dla platformy należy **również jawnie zadecydować, aby włączyć zbieranie poleceń SQL** , modyfikując plik applicationInsights.config:

```xml
<TelemetryModules>
  <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
    <EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
  </Add>
```

W powyższych przypadkach poprawna metoda sprawdzania poprawności aparatu Instrumentacji jest poprawnie zainstalowana, sprawdzając, czy wersja zestawu SDK zebrana `DependencyTelemetry` to "rddp". element "rdddsd" lub "rddf" wskazuje zależności, które są zbierane za pośrednictwem wywołania zwrotnego DiagnosticSource lub EventSource, i dlatego pełne zapytanie SQL nie zostanie przechwycone.

## <a name="where-to-find-dependency-data"></a>Gdzie można znaleźć dane zależności

* [Mapa aplikacji](app-map.md) wizualizuje zależności między aplikacją i sąsiednimi składnikami.
* [Diagnostyka transakcji](transaction-diagnostics.md) przedstawia ujednolicone, skorelowane dane serwera.
* [Karta przeglądarki](javascript.md) pokazuje wywołania AJAX z przeglądarek użytkowników.
* Kliknij pozycję przez powolne lub Nieudane żądania, aby sprawdzić ich wywołania zależności.
* [Analiza](#logs-analytics) danych zależności może służyć do wykonywania zapytań.

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a> Diagnozuj wolne żądania

Każde zdarzenie żądania jest skojarzone z wywołaniami zależności, wyjątkami i innymi zdarzeniami, które są śledzone podczas przetwarzania żądania przez aplikację. Dlatego jeśli niektóre żądania są wykonywane nieprawidłowo, można sprawdzić, czy jest to spowodowane powolnymi odpowiedziami z zależności.

### <a name="tracing-from-requests-to-dependencies"></a>Śledzenie żądań do zależności

Otwórz kartę **wydajność** i przejdź do karty **zależności** u góry obok pozycji operacje.

Kliknij **nazwę zależności** w obszarze Ogólne. Po wybraniu zależności zostanie wyświetlony wykres rozkładu czasu trwania tej zależności po prawej stronie.

![Na karcie Wydajność kliknij kartę zależność u góry, a następnie nazwę zależności na wykresie.](./media/asp-net-dependencies/2-perf-dependencies.png)

Kliknij przycisk niebieskich **przykładów** w prawym dolnym rogu, a następnie na przykład, aby wyświetlić szczegółowe informacje o transakcji.

![Kliknij przykład, aby wyświetlić szczegółowe informacje o transakcji](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profilowanie aktywnej witryny

Nie ma pomysłu, w jakim czasie nachodzą? [Profiler Application Insights](../../azure-monitor/app/profiler.md) śledzi połączenia HTTP z aktywną witryną i wyświetla funkcje w kodzie, które zajęły najdłuższy czas.

## <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Żądania nieudane mogą być również skojarzone z niepowodzeniem wywołaniami zależności.

Możemy przejść do karty **Błędy** po lewej stronie, a następnie kliknąć kartę **zależności** u góry.

![Kliknij wykres nieudanych żądań](./media/asp-net-dependencies/4-fail.png)

W tym miejscu będzie można zobaczyć liczbę nieudanych zależności. Aby uzyskać więcej szczegółów na temat wystąpienia awarii, spróbuj kliknąć nazwę zależności w dolnej tabeli. Możesz kliknąć przycisk niebieskie **zależności** w prawym dolnym rogu, aby uzyskać szczegółowe informacje o transakcjach.

## <a name="logs-analytics"></a>Dzienniki (analiza)

Zależności można śledzić w [języku zapytań Kusto](/azure/kusto/query/). Oto kilka przykładów.

* Znajdź wszystkie nieudane wywołania zależności:

``` Kusto

    dependencies | where success != "True" | take 10
```

* Znajdź wywołania AJAX:

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


* Znajdź wywołania AJAX skojarzone z widokami stron:

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Jak automatyczne wywołania raportu modułu zbierającego zależności nie powiodły się do zależności?*

* Wywołania zależności zakończone niepowodzeniem mają ustawioną wartość false dla pola "Success". `DependencyTrackingTelemetryModule` nie zgłasza `ExceptionTelemetry` . Pełen model danych dla zależności został opisany [tutaj](data-model-dependency-telemetry.md).

### <a name="how-do-i-calculate-ingestion-latency-for-my-dependency-telemetry"></a>*Jak mogę obliczyć opóźnienia pozyskiwania danych telemetrycznych zależności?*

```kusto
dependencies
| extend E2EIngestionLatency = ingestion_time() - timestamp 
| extend TimeIngested = ingestion_time()
```

### <a name="how-do-i-determine-the-time-the-dependency-call-was-initiated"></a>*Jak mogę określić czas zainicjowania wywołania zależności?*

W widoku zapytania Log Analytics `timestamp` reprezentuje moment zainicjowania wywołania TrackDependency (), które występuje natychmiast po odebraniu odpowiedzi na wywołanie zależności. Aby obliczyć czas rozpoczęcia wywołania zależności, należy wykonać `timestamp` i odjąć `duration` liczbę zarejestrowanych wywołań zależności.

## <a name="open-source-sdk"></a>Zestaw SDK open source
Podobnie jak w przypadku każdego zestawu Application Insights SDK, moduł kolekcji zależności jest również typu open source. Odczytaj i współtworzyj kod lub zgłoś problemy w [oficjalnym repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Następne kroki

* [Wyjątki](./asp-net-exceptions.md)
* [Dane strony & użytkownika](./javascript.md)
* [Dostępność](./monitor-web-app-availability.md)

