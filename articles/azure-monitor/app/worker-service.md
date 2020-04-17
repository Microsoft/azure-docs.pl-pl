---
title: Usługa Application Insights dla aplikacji usługi worker service (aplikacje inne niż HTTP)
description: Monitorowanie aplikacji programu .NET Core/.NET Framework innych niż HTTP za pomocą usługi Azure Monitor Application Insights.
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: f043140e5a342d114f777ad16bba588790b7f8cc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536730"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Usługa Application Insights dla aplikacji usługi worker service (aplikacje inne niż HTTP)

Usługa Application Insights wydaje nowy sdk, o nazwie `Microsoft.ApplicationInsights.WorkerService`, który najlepiej nadaje się do obciążeń innych niż HTTP, takich jak wiadomości, zadania w tle, aplikacje konsoli itp. Tego typu aplikacje nie mają pojęcia przychodzącego żądania HTTP, takiego jak tradycyjna ASP.NET/ASP.NET podstawowa aplikacja sieci Web, a zatem korzystanie z pakietów usługi Application Insights dla [aplikacji ASP.NET](asp-net.md) lub [ASP.NET Core](asp-net-core.md) nie jest obsługiwane.

Nowy sdk nie wykonuje żadnej kolekcji telemetrii przez siebie. Zamiast tego przynosi w innych znanych aplikacji Insights auto kolektorów, takich jak [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) itp. Ten moduł SDK udostępnia `IServiceCollection` metody rozszerzenia, aby włączyć i skonfigurować zbieranie danych telemetrycznych.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

The [Application Insights SDK for Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) is best suited for non-HTTP applications no matter where or how they run. Jeśli aplikacja jest uruchomiona i ma łączność sieciową z platformą Azure, można zbierać dane telemetryczne. Monitorowanie usługi Application Insights jest obsługiwane wszędzie tam, gdzie obsługiwany jest program .NET Core. Ten pakiet może być używany w nowo [wprowadzonej usłudze worker service .NET Core 3.0,](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) [zadaniach w tle w Asp.Net Core 2.1/2.2,](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)aplikacjach konsoli (.NET Core/ .NET Framework) itp.

## <a name="prerequisites"></a>Wymagania wstępne

Prawidłowy klucz instrumentacji usługi Application Insights. Ten klucz jest wymagany do wysyłania danych telemetrycznych do usługi Application Insights. Jeśli chcesz utworzyć nowy zasób usługi Application Insights, aby uzyskać klucz instrumentacji, zobacz [Tworzenie zasobu usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="using-application-insights-sdk-for-worker-services"></a>Korzystanie z sdk usługi usługi workers dla aplikacji Insights

1. Zainstaluj pakiet [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikacji.
   Poniższy fragment kodu zawiera zmiany, które należy dodać do `.csproj` pliku projektu.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Metoda `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` rozszerzenia `IServiceCollection`wywołania , podając klucz oprzyrządowania. Ta metoda powinna być wywoływana na początku aplikacji. Dokładna lokalizacja zależy od typu aplikacji.

1. Pobieranie `ILogger` wystąpienia lub `TelemetryClient` wystąpienia z kontenera iniekcji `serviceProvider.GetRequiredService<TelemetryClient>();` zależności (DI) przez wywołanie lub użycie iniekcji konstruktora. Ten krok spowoduje utworzenie `TelemetryConfiguration` i automatyczne moduły zbierania.

Szczegółowe instrukcje dla każdego typu aplikacji są opisane w poniższych sekcjach.

## <a name="net-core-30-worker-service-application"></a>Aplikacja usługi dla procesów procesowych .NET Core 3.0

Pełny przykład jest udostępniany [tutaj](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Pobierz i zainstaluj [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Tworzenie nowego projektu usługi worker service przy użyciu nowego szablonu projektu lub wiersza polecenia programu Visual Studio`dotnet new worker`
3. Zainstaluj pakiet [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikacji.

4. Dodaj `services.AddApplicationInsightsTelemetryWorkerService();` do `CreateHostBuilder()` metody `Program.cs` w klasie, jak w tym przykładzie:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Zmodyfikuj swój `Worker.cs` jak na poniższym przykładzie.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Ustaw klucz oprzyrządowania.

    Chociaż klucz instrumentacji można podać jako `AddApplicationInsightsTelemetryWorkerService`argument do , zaleca się określenie klucza instrumentacji w konfiguracji. Poniższy przykładowy kod pokazuje, jak `appsettings.json`określić klucz oprzyrządowania w . Upewnij `appsettings.json` się, że jest kopiowany do folderu głównego aplikacji podczas publikowania.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Alternatywnie należy określić klucz instrumentacji w jednej z następujących zmiennych środowiskowych.
`APPINSIGHTS_INSTRUMENTATIONKEY` lub `ApplicationInsights:InstrumentationKey`

Na przykład: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
Lub`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Zazwyczaj `APPINSIGHTS_INSTRUMENTATIONKEY` określa klucz instrumentacji dla aplikacji wdrożonych w aplikacjach sieci Web jako zadania sieci Web.

> [!NOTE]
> Klucz instrumentacji określony w kodzie `APPINSIGHTS_INSTRUMENTATIONKEY`wygrywa nad zmienną środowiskową, która wygrywa nad innymi opcjami.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Podstawowe zadania w tle z usługami hostowanymi

[W tym](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio) dokumencie opisano sposób tworzenia zadań w tle w aplikacji ASP.NET Core 2.1/2.2.

Pełny przykład jest udostępniany [tutaj](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Zainstaluj pakiet Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikacji.
2. Dodaj `services.AddApplicationInsightsTelemetryWorkerService();` do `ConfigureServices()` metody, jak w tym przykładzie:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

Poniżej znajduje `TimedHostedService` się kod, w którym znajduje się logika zadania w tle.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Ustaw klucz oprzyrządowania.
   Użyj tego `appsettings.json` samego z .NET Core 3.0 Worker Service przykład powyżej.

## <a name="net-corenet-framework-console-application"></a>Aplikacja konsoli .NET Core/.NET Framework

Jak wspomniano na początku tego artykułu, nowy pakiet może służyć do włączania danych telemetrycznych usługi Application Insights nawet z zwykłej aplikacji konsoli. Ten pakiet [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)jest przeznaczony dla aplikacji , a zatem może służyć do aplikacji konsoli w .NET Core 2.0 lub nowszych i .NET Framework 4.7.2 lub nowszych.

Pełny przykład jest udostępniany [tutaj](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Zainstaluj pakiet Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) do aplikacji.

2. Zmodyfikuj Program.cs jak na poniższym przykładzie.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Ta aplikacja konsoli używa również `TelemetryConfiguration`tej samej domyślnej i można ją dostosować w taki sam sposób, jak przykłady we wcześniejszej sekcji.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Uruchom aplikację. Przykładowych pracowników ze wszystkich powyższych sprawia, że wywołanie http co sekundę do bing.com, a także emituje kilka dzienników przy użyciu protokołu ILogger. Te wiersze `StartOperation` są `TelemetryClient`zawijane wewnątrz wywołania , który `RequestTelemetry` jest używany do tworzenia operacji (w tym przykładzie o nazwie "operacja"). Usługa Application Insights będzie zbierać te dzienniki ILogger (ostrzeżenie lub powyżej domyślnie) `RequestTelemetry` i zależności i będą one skorelowane z relacji nadrzędny podrzędny. Korelacja działa również przez granicę procesu/sieci. Na przykład jeśli wywołanie zostało wykonane do innego monitorowanego składnika, a następnie będzie skorelowane z tym elementem nadrzędnym, jak również.

Ta niestandardowa `RequestTelemetry` operacja może być uważana za odpowiednik przychodzącego żądania sieci web w typowej aplikacji sieci Web. Chociaż nie jest konieczne użycie operacji, najlepiej pasuje do [modelu danych korelacji usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/correlation) — z `RequestTelemetry` działając jako operacja nadrzędna i wszystkie dane telemetryczne generowane wewnątrz iteracji procesu roboczego są traktowane jako logicznie należące do tej samej operacji. Takie podejście zapewnia również, że wszystkie generowane dane telemetryczne (automatyczne i ręczne) będą miały to samo `operation_id`. Ponieważ próbkowanie `operation_id`jest oparte na algorytm próbkowania zachowuje lub upuszcza wszystkie dane telemetryczne z pojedynczej iteracji.

Poniżej wymieniono pełną dane telemetryczne zbierane automatycznie przez aplikację Application Insights.

### <a name="live-metrics"></a>Metryki na żywo

[Metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) można szybko sprawdzić, czy monitorowanie usługi Application Insights jest poprawnie skonfigurowany. Chociaż może upłynąć kilka minut, zanim dane telemetryczne zaczną pojawiać się w portalu i analizy, Live Metrics pokaże użycie procesora CPU uruchomionego procesu w czasie zbliżonym do rzeczywistego. Może również wyświetlać inne dane telemetryczne, takie jak żądania, zależności, ślady itp.

### <a name="ilogger-logs"></a>Dzienniki ILogger

Dzienniki emitowane `ILogger` przez `Warning` ważności lub większe są przechwytywane automatycznie. Postępuj zgodnie [z dokami iLogger,](ilogger.md#control-logging-level) aby dostosować poziomy dziennika są przechwytywane przez usługa Application Insights.

### <a name="dependencies"></a>Zależności

Zbieranie zależności jest domyślnie włączone. [W tym](asp-net-dependencies.md#automatically-tracked-dependencies) artykule wyjaśniono zależności, które są zbierane automatycznie, a także zawierają kroki do śledzenia ręcznego.

### <a name="eventcounter"></a>Licznik zdarzeń

`EventCounterCollectionModule`jest domyślnie włączona i będzie zbierać domyślny zestaw liczników z aplikacji .NET Core 3.0. [Samouczek EventCounter](eventcounters.md) zawiera listę domyślnego zestawu zebranych liczników. Posiada również instrukcje dotyczące dostosowywania listy.

### <a name="manually-tracking-additional-telemetry"></a>Ręczne śledzenie dodatkowych danych telemetrycznych

Podczas gdy SDK automatycznie zbiera dane telemetryczne, jak wyjaśniono powyżej, w większości przypadków użytkownik będzie musiał wysłać dodatkowe dane telemetryczne do usługi Application Insights. Zalecanym sposobem śledzenia dodatkowych danych telemetrycznych `TelemetryClient` jest uzyskanie wystąpienia z iniekcji `TrackXXX()` zależności, a następnie wywołanie jednej z obsługiwanych metod [interfejsu API](api-custom-events-metrics.md) na nim. Innym typowym przypadkiem użycia jest [niestandardowe śledzenie operacji.](custom-operations-tracking.md) Takie podejście jest pokazane w przykładach worker powyżej.

## <a name="configure-the-application-insights-sdk"></a>Konfigurowanie sdk usługi Application Insights

Wartość `TelemetryConfiguration` domyślna używana przez sdk usługi procesu roboczego jest podobna do konfiguracji automatycznej używanej w ASP.NET lub ASP.NET podstawowej `HttpContext`aplikacji, pomniejszonej o dane TelemetryInitializatory używane do wzbogacania danych telemetrycznych z programu .

Można dostosować SDK usługi Worker Insights aplikacji, aby zmienić domyślną konfigurację. Użytkownicy ASP.NET core SDK usługi Application Insights mogą być zaznajomieni ze zmianą konfiguracji przy użyciu [wbudowanego iniekcji zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)ASP.NET Core. WorkerService SDK jest również oparty na podobnych zasadach. Wprowadzać prawie wszystkie `ConfigureServices()` zmiany konfiguracji w `IServiceCollection`sekcji, wywołując odpowiednie metody na , jak opisano poniżej.

> [!NOTE]
> Podczas korzystania z tego sdk, `TelemetryConfiguration.Active` zmiana konfiguracji przez modyfikowanie nie jest obsługiwana, a zmiany nie zostaną odzwierciedlone.

### <a name="using-applicationinsightsserviceoptions"></a>Korzystanie z applicationinsightsserviceoptions

Można zmodyfikować kilka typowych `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetryWorkerService`ustawień, przechodząc do , jak w tym przykładzie:

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

Należy `ApplicationInsightsServiceOptions` zauważyć, że w tym SDK znajduje się w obszarze nazw, `Microsoft.ApplicationInsights.WorkerService` w przeciwieństwie do `Microsoft.ApplicationInsights.AspNetCore.Extensions` ASP.NET Core SDK.

Często używane ustawienia w`ApplicationInsightsServiceOptions`

|Ustawienie | Opis | Domyślne
|---------------|-------|-------
|EnableQuickPulseMetricStream | Włącz/Wyłącz funkcję LiveMetrics | true
|WłączAdaptiveSampling | Włączanie/wyłączanie próbkowania adaptacyjnego | true
|Włączheartbeat | Włącz/Wyłącz tętno funkcji, która okresowo (domyślnie 15 minut) wysyła metrykę niestandardową o nazwie "HeartBeatState" z informacjami o środowisku uruchomieniowym, takich jak .NET Version, Informacje o środowisku platformy Azure, jeśli dotyczy, itp. | true
|AddAutoCollectedMetricExtractor | Włącz/Wyłącz ekstraktor AutocollectedMetrics, który jest TelemetryProcessor, który wysyła wstępnie zagregowane metryki dotyczące żądań/zależności przed próbkowaniem. | true

Zobacz [konfigurowalne `ApplicationInsightsServiceOptions` ustawienia](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) na liście najbardziej aktualne.

### <a name="sampling"></a>Próbkowanie

Zestaw SDK usługi worker dla usługi usługi worker umożliwia zarówno próbkowanie o stałym, jak i adaptacyjne. Adaptacyjne próbkowanie jest domyślnie włączone. Konfigurowanie próbkowania dla usługi procesu roboczego odbywa się w taki sam sposób, jak w przypadku [aplikacji ASP.NET podstawowych](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Dodawanie telemetriiInitializatory

Inicjatory [telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) służą do definiowania właściwości, które są wysyłane ze wszystkimi telemetriami.

Dodaj do `TelemetryInitializer` kontenera `DependencyInjection` wszystkie nowe, a SDK `TelemetryConfiguration`automatycznie doda je do pliku .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Usuwanie telemetriiInitializatory

Inicjatory telemetrii są domyślnie obecne. Aby usunąć wszystkie lub określone inicjatory telemetrii, `AddApplicationInsightsTelemetryWorkerService()`użyj następującego przykładowego kodu *po* wywołaniu .

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Dodawanie procesorów telemetrycznych

Do niestandardowych procesorów telemetrycznych można dodać `TelemetryConfiguration` przy użyciu metody `AddApplicationInsightsTelemetryProcessor` rozszerzenia w programie `IServiceCollection`. Procesory telemetryczne są używane w [zaawansowanych scenariuszach filtrowania,](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer) aby umożliwić bardziej bezpośrednią kontrolę nad tym, co jest zawarte lub wykluczone z danych telemetrycznych wysyłanych do usługi Application Insights. Użyj poniższego przykładu.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurowanie lub usuwanie domyślnych modeli telemetrycznych

Usługa Application Insights używa modułów telemetrycznych do automatycznego zbierania danych telemetrycznych dotyczących określonych obciążeń bez konieczności ręcznego śledzenia.

Następujące moduły automatycznej kolekcji są domyślnie włączone. Moduły te są odpowiedzialne za automatyczne zbieranie danych telemetrycznych. Można je wyłączyć lub skonfigurować, aby zmienić ich domyślne zachowanie.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

Aby skonfigurować `TelemetryModule`dowolną wartość domyślną, użyj metody `ConfigureTelemetryModule<T>` rozszerzenia na `IServiceCollection`, jak pokazano w poniższym przykładzie.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Konfigurowanie kanału telemetryczne

Domyślnym kanałem jest `ServerTelemetryChannel`. Można go zastąpić, jak pokazano w poniższym przykładzie.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamiczne wyłączanie telemetrii

Jeśli chcesz wyłączyć telemetrii warunkowo i `TelemetryConfiguration` dynamicznie, można rozwiązać wystąpienie z ASP.NET Core `DisableTelemetry` kontenera iniekcji zależności w dowolnym miejscu w kodzie i ustawić flagę na nim.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak śledzić dane telemetryczne, które nie są zbierane automatycznie?

Pobierz wystąpienie `TelemetryClient` za pomocą iniekcji konstruktora i wywołać wymaganą `TrackXXX()` metodę na nim. Nie zaleca się tworzenia `TelemetryClient` nowych wystąpień. Wystąpienie `TelemetryClient` singleton jest już `DependencyInjection` zarejestrowane w `TelemetryConfiguration` kontenerze, który udostępnia z resztą danych telemetrycznych. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy wymaga konfiguracji, która jest oddzielona od reszty danych telemetrycznych.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Czy można używać środowiska IDE programu Visual Studio do dołączania usługi Application Insights do projektu usługi worker service?

Dołączanie IDE programu Visual Studio jest obecnie obsługiwane tylko dla aplikacji ASP.NET/ASP.NET podstawowych. Ten dokument zostanie zaktualizowany, gdy program Visual Studio dostarcza obsługę dołączania aplikacji usługi worker.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Czy mogę włączyć monitorowanie usługi Application Insights przy użyciu narzędzi, takich jak Monitor stanu?

Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i [monitor stanu w wersji 2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) obsługują obecnie tylko ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Jeśli uruchomię aplikację w systemie Linux, czy wszystkie funkcje są obsługiwane?

Tak. Obsługa funkcji dla tego SDK jest taka sama we wszystkich platformach, z następującymi wyjątkami:

* Liczniki wydajności są obsługiwane tylko w systemie Windows, z wyjątkiem procesora CPU/pamięci procesu pokazanego w metrykach live.
* Mimo `ServerTelemetryChannel` że jest domyślnie włączona, jeśli aplikacja jest uruchomiona w systemie Linux lub MacOS, kanał nie automatycznie utworzyć folderlokalny magazyn, aby zachować dane telemetryczne tymczasowo, jeśli występują problemy z siecią. Z powodu tego ograniczenia dane telemetryczne są tracone w przypadku wystąpienia tymczasowych problemów z siecią lub serwerem. Aby obejść ten problem, skonfiguruj folder lokalny dla kanału:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Przykładowe aplikacje

[Aplikacja konsoli .NET Core](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Użyj tego przykładu, jeśli używasz aplikacji konsoli napisanej w .NET Core (2.0 lub nowszym) lub .NET Framework (4.7.2 lub nowszym)

[Podstawowe zadania w tle PLATFORMY ASP .NET z usługami hostowanymi](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Użyj tego przykładu, jeśli jesteś w Asp.Net Core 2.1/2.2 i tworzysz zadania w tle zgodnie z oficjalnymi wskazówkami [tutaj](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)

[Usługa robocza .NET Core 3.0](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Użyj tego przykładu, jeśli masz aplikację .NET Core 3.0 Worker Service zgodnie z oficjalnymi wskazówkami [tutaj](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK typu open source

[Przeczytaj i przyczynić się do kodu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Następne kroki

* [Użyj interfejsu API,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysłać własne zdarzenia i metryki, aby uzyskać szczegółowy widok wydajności i użycia aplikacji.
* [Śledzenie dodatkowych zależności, które nie są automatycznie śledzone.](../../azure-monitor/app/auto-collect-dependencies.md)
* [Wzbogać lub przefiltruj automatycznie zebrane dane telemetryczne](../../azure-monitor/app/api-filtering-sampling.md).
* [Iniekcja zależności w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).
