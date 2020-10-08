---
title: Application Insights aplikacji usługi Worker (aplikacje inne niż HTTP)
description: Monitorowanie aplikacji .NET Core/. NET Framework bez protokołu HTTP z Azure Monitor Application Insights.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: 643edf81d6a98c8f423267b657feb9dfb6da1070
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816391"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Application Insights aplikacji usługi Worker (aplikacje inne niż HTTP)

[Zestaw Application Insights SDK dla usługi Worker](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) to nowy zestaw SDK, który najlepiej nadaje się do obciążeń innych niż http, takich jak obsługa komunikatów, zadania w tle, aplikacje konsolowe itp. Te typy aplikacji nie mają pojęcia przychodzącego żądania HTTP, takiego jak tradycyjna aplikacja sieci Web ASP.NET/ASP.NET Core, a tym samym nie są obsługiwane pakiety Application Insights dla aplikacji [ASP.NET](asp-net.md) i [ASP.NET Core](asp-net-core.md) .

Nowy zestaw SDK nie wykonuje żadnej kolekcji telemetrii. Zamiast tego wywołuje inne dobrze znane Application Insights, takie jak [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) itd. Ten zestaw SDK udostępnia metody rozszerzające na `IServiceCollection` potrzeby włączania i konfigurowania zbierania danych telemetrycznych.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

[Zestaw Application Insights SDK dla usługi Worker](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) najlepiej nadaje się w przypadku aplikacji innych niż HTTP bez względu na to, gdzie i jak działają. Jeśli aplikacja działa i ma łączność sieciową z platformą Azure, można zbierać dane telemetryczne. Monitorowanie Application Insights jest obsługiwane wszędzie tam, gdzie jest obsługiwany program .NET Core. Ten pakiet może być używany w nowo wprowadzonej [usłudze .NET Core 3,0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [zadaniach w tle w ASP.NET Core 2.1/2.2](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true), aplikacjach konsolowych (.NET Core/.NET Framework) itp.

## <a name="prerequisites"></a>Wymagania wstępne

Prawidłowy klucz Instrumentacji Application Insights. Ten klucz jest wymagany do wysyłania wszelkich danych telemetrycznych do Application Insights. Jeśli musisz utworzyć nowy zasób Application Insights, aby uzyskać klucz instrumentacji, zobacz [Tworzenie zasobu Application Insights](./create-new-resource.md).

## <a name="using-application-insights-sdk-for-worker-services"></a>Korzystanie z zestawu SDK Application Insights dla usług Worker

1. Zainstaluj pakiet [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) w aplikacji.
   Poniższy fragment kodu przedstawia zmiany, które należy dodać do `.csproj` pliku projektu.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Wywoływanie `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` metody rozszerzenia na `IServiceCollection` , dostarczenie klucza Instrumentacji. Ta metoda powinna być wywoływana na początku aplikacji. Dokładna lokalizacja zależy od typu aplikacji.

1. Pobierz `ILogger` wystąpienie lub `TelemetryClient` wystąpienie z kontenera iniekcji zależności (di) przez wywołanie `serviceProvider.GetRequiredService<TelemetryClient>();` lub użycie iniekcji konstruktora. Ten krok spowoduje wyzwolenie ustawienia `TelemetryConfiguration` modułów modułu i kolekcji automatycznie.

Szczegółowe instrukcje dotyczące poszczególnych typów aplikacji opisano w poniższych sekcjach.

## <a name="net-core-30-worker-service-application"></a>Aplikacja usługi programu .NET Core 3,0 Worker

Pełny przykład jest udostępniany [tutaj](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Pobierz i zainstaluj program [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Utwórz nowy projekt usługi procesu roboczego przy użyciu nowego szablonu projektu programu Visual Studio lub wiersza polecenia `dotnet new worker`
3. Zainstaluj pakiet [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) w aplikacji.

4. Dodaj `services.AddApplicationInsightsTelemetryWorkerService();` do `CreateHostBuilder()` metody w `Program.cs` klasie, tak jak w poniższym przykładzie:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Zmodyfikuj swój `Worker.cs` kod zgodnie z poniższym przykładem.

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

6. Skonfiguruj klucz Instrumentacji.

    Chociaż można podać klucz Instrumentacji jako argument `AddApplicationInsightsTelemetryWorkerService` , zalecamy określenie klucza Instrumentacji w konfiguracji. Poniższy przykład kodu pokazuje, jak określić klucz Instrumentacji w programie `appsettings.json` . Upewnij się `appsettings.json` , że podczas publikowania jest kopiowany do folderu głównego aplikacji.

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

Alternatywnie Określ klucz Instrumentacji w jednej z następujących zmiennych środowiskowych.
`APPINSIGHTS_INSTRUMENTATIONKEY` lub `ApplicationInsights:InstrumentationKey`

Na przykład: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
ORAZ `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Zazwyczaj `APPINSIGHTS_INSTRUMENTATIONKEY` określa klucz Instrumentacji dla aplikacji wdrożonych do Web Apps jako zadania w sieci Web.

> [!NOTE]
> Klucz Instrumentacji określony w kodzie usługi WINS przez zmienną środowiskową `APPINSIGHTS_INSTRUMENTATIONKEY` , która jest usługą WINS w porównaniu z innymi opcjami.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core zadań w tle za pomocą usług hostowanych

W [tym](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-2.2&preserve-view=true) dokumencie opisano sposób tworzenia zadań tła w aplikacji ASP.NET Core 2.1/2.2.

Pełny przykład jest udostępniany [tutaj](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Zainstaluj pakiet [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) w aplikacji.
2. Dodaj `services.AddApplicationInsightsTelemetryWorkerService();` do `ConfigureServices()` metody, jak w poniższym przykładzie:

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

Poniżej znajduje się kod, w `TimedHostedService` którym znajduje się logika zadań w tle.

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

3. Skonfiguruj klucz Instrumentacji.
   Użyj tego samego `appsettings.json` przykładu w powyższym przykładzie usługi procesu roboczego .NET Core 3,0.

## <a name="net-corenet-framework-console-application"></a>Aplikacja konsolowa platformy .NET Core/. NET Framework

Jak wspomniano na początku tego artykułu, nowy pakiet może służyć do włączania telemetria usługi Application Insights nawet w zwykłych aplikacjach konsolowych. Ten pakiet [`NetStandard2.0`](/dotnet/standard/net-standard) jest przeznaczony dla aplikacji konsolowych programu .NET Core 2,0 lub nowszego, w związku z czym można go używać w celu zapewnienia .NET Framework 4.7.2 lub wyższego.

Pełny przykład jest udostępniany [tutaj](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Zainstaluj pakiet [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) w aplikacji.

2. Zmodyfikuj Program.cs, jak pokazano poniżej.

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

Ta Aplikacja konsolowa również używa tego samego ustawienia domyślnego `TelemetryConfiguration` i można ją dostosować w taki sam sposób jak przykłady w poprzedniej sekcji.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Uruchom aplikację. Przykładowi pracownicy ze wszystkich powyższych elementów wysyłają wywołania http co sekundę do bing.com, a także emitują kilka dzienników przy użyciu `ILogger` . Te wiersze są zawijane wewnątrz `StartOperation` wywołania `TelemetryClient` , które jest używane do tworzenia operacji (w tym przykładzie `RequestTelemetry` o nazwie "Operation"). Application Insights będzie zbierać te dzienniki ILogger (domyślnie ostrzegawcze lub powyżej) oraz zależności i zostaną one skorelowane z `RequestTelemetry` relacją with Parent-podrzędny. Korelacja działa również między granicami procesu i sieci. Na przykład jeśli wywołanie zostało wykonane w innym monitorowanym składniku, zostanie ono skorelowane również z tym elementem nadrzędnym.

Ta niestandardowa operacja `RequestTelemetry` może być uważana za odpowiednik przychodzącego żądania sieci Web w typowej aplikacji sieci Web. Chociaż nie jest to konieczne do używania operacji, najlepiej pasuje do [modelu danych korelacji Application Insights](./correlation.md) — `RequestTelemetry` działając jako operacja nadrzędna, a każda Telemetria wygenerowana wewnątrz iteracji procesu roboczego jest traktowana jako logicznie należący do tej samej operacji. Takie podejście gwarantuje również, że wszystkie wygenerowane dane telemetryczne (automatyczne i ręczne) będą takie same `operation_id` . Jak próbkowanie jest oparte na `operation_id` , algorytm próbkowania utrzymuje lub porzuca wszystkie dane telemetryczne z jednej iteracji.

Poniżej przedstawiono listę wszystkich danych telemetrycznych automatycznie zbieranych przez Application Insights.

### <a name="live-metrics"></a>Metryki na żywo

Przy użyciu [metryk na żywo](./live-stream.md) można szybko sprawdzić, czy Application Insights monitorowanie jest prawidłowo skonfigurowane. Gdy dane telemetryczne pojawią się w portalu i w analizie, może to potrwać kilka minut, a na żywo metryki będą przedstawiać użycie procesora przez uruchomiony proces niemal w czasie rzeczywistym. Może również wyświetlać inne dane telemetryczne, takie jak żądania, zależności, ślady itp.

### <a name="ilogger-logs"></a>Dzienniki ILogger

Dzienniki emitowane za pośrednictwem `ILogger` ważności `Warning` lub nowsze są automatycznie przechwytywane. Postępuj zgodnie z dokumentacją [ILogger](ilogger.md#control-logging-level) , aby dostosowywać poziomy dzienników przechwytywane przez Application Insights.

### <a name="dependencies"></a>Zależności

Kolekcja zależności jest domyślnie włączona. W [tym](asp-net-dependencies.md#automatically-tracked-dependencies) artykule opisano zależności, które są zbierane automatycznie, a także kroki prowadzące do ręcznego śledzenia.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` Program jest domyślnie włączony i zbiera domyślny zestaw liczników z aplikacji platformy .NET Core 3,0. Samouczek [EventCounter](eventcounters.md) zawiera domyślny zestaw zebranych liczników. Zawiera również instrukcje dotyczące dostosowywania listy.

### <a name="manually-tracking-additional-telemetry"></a>Ręczne śledzenie dodatkowych danych telemetrycznych

Zestaw SDK automatycznie zbiera dane telemetryczne, jak wyjaśniono powyżej, w większości przypadków użytkownik będzie musiał wysłać dodatkową telemetrię do usługi Application Insights. Zalecanym sposobem śledzenia dodatkowych danych telemetrycznych jest uzyskanie wystąpienia `TelemetryClient` z iniekcji zależności, a następnie wywołanie jednej z obsługiwanych metod `TrackXXX()` [interfejsu API](api-custom-events-metrics.md) . Inny typowy przypadek użycia to [niestandardowe śledzenie operacji](custom-operations-tracking.md). Takie podejście przedstawiono w przykładach procesu roboczego.

## <a name="configure-the-application-insights-sdk"></a>Konfigurowanie zestawu SDK Application Insights

Wartość domyślna `TelemetryConfiguration` używana przez zestaw SDK usługi Worker jest podobna do konfiguracji automatycznej używanej w aplikacji ASP.NET lub ASP.NET Core, co oznacza, że TelemetryInitializers używany do wzbogacania danych telemetrycznych z programu `HttpContext` .

Aby zmienić konfigurację domyślną, można dostosować Application Insights zestawu SDK dla usługi procesu roboczego. Użytkownicy zestawu SDK Application Insights ASP.NET Core mogą znać zmianę konfiguracji przy użyciu ASP.NET Core wbudowanej [iniekcji zależności](/aspnet/core/fundamentals/dependency-injection). Zestaw SDK WorkerService jest również oparty na podobnej zasadzie. Wprowadź niemal wszystkie zmiany konfiguracji w `ConfigureServices()` sekcji, wywołując odpowiednie metody w `IServiceCollection` , jak pokazano poniżej.

> [!NOTE]
> Podczas korzystania z tego zestawu SDK zmiana konfiguracji przez modyfikację `TelemetryConfiguration.Active` nie jest obsługiwana, a zmiany nie zostaną odzwierciedlone.

### <a name="using-applicationinsightsserviceoptions"></a>Korzystanie z ApplicationInsightsServiceOptions

Kilka typowych ustawień można zmodyfikować, przechodząc `ApplicationInsightsServiceOptions` do `AddApplicationInsightsTelemetryWorkerService` , jak w poniższym przykładzie:

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

Należy pamiętać, że `ApplicationInsightsServiceOptions` w tym zestawie SDK znajduje się w przestrzeni nazw `Microsoft.ApplicationInsights.WorkerService` , a nie `Microsoft.ApplicationInsights.AspNetCore.Extensions` w ASP.NET Core SDK.

Najczęściej używane ustawienia w programie `ApplicationInsightsServiceOptions`

|Ustawienie | Opis | Domyślne
|---------------|-------|-------
|EnableQuickPulseMetricStream | Włącz/Wyłącz funkcję LiveMetrics | true
|EnableAdaptiveSampling | Włącz/Wyłącz próbkowanie adaptacyjne | true
|EnableHeartbeat | Funkcja włączania/wyłączania pulsu, która okresowo (domyślnie 15-minutowa) wysyła metrykę niestandardową o nazwie "HeartBeatState" z informacjami o środowisku uruchomieniowym, takim jak wersja platformy .NET, informacje dotyczące środowiska platformy Azure, jeśli ma to zastosowanie itd. | true
|AddAutoCollectedMetricExtractor | Włącz/Wyłącz Ekstraktor AutoCollectedMetrics, który jest TelemetryProcessor, który wysyła wstępnie zagregowane metryki dotyczące żądań/zależności przed pobraniem próbek. | true
|EnableDiagnosticsTelemetryModule | Włącz/Wyłącz `DiagnosticsTelemetryModule` . Wyłączenie tej ustawienia spowoduje zignorowanie następujących ustawień: `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Zobacz [ustawienia konfigurowalne w `ApplicationInsightsServiceOptions` programie](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) , aby uzyskać najbardziej aktualną listę.

### <a name="sampling"></a>Próbkowanie

Zestaw Application Insights SDK dla usługi Worker obsługuje zarówno stałe, jak i adaptacyjne próbkowanie. Próbkowanie adaptacyjne jest domyślnie włączone. Konfigurowanie próbkowania dla usługi Worker odbywa się tak samo jak w przypadku [aplikacji ASP.NET Core](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Dodawanie TelemetryInitializers

[Inicjatory telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) służą do definiowania właściwości, które są wysyłane ze wszystkimi danymi telemetrycznymi.

Dodaj nowe `TelemetryInitializer` do `DependencyInjection` kontenera, a zestaw SDK automatycznie doda je do `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Usuwanie TelemetryInitializers

Inicjatory telemetrii są obecne domyślnie. Aby usunąć wszystkie lub określonych inicjatorów telemetrii, użyj następującego przykładowego kodu *po* wywołaniu `AddApplicationInsightsTelemetryWorkerService()` .

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

### <a name="adding-telemetry-processors"></a>Dodawanie procesorów telemetrii

Do przy `TelemetryConfiguration` użyciu metody rozszerzenia w systemie można dodać niestandardowe procesory telemetrii `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . W [zaawansowanych scenariuszach filtrowania](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) używasz procesorów telemetrycznych, aby umożliwić bardziej bezpośrednią kontrolę nad tym, co zostało dołączone lub wykluczone z danych telemetrycznych wysyłanych do usługi Application Insights. Użyj poniższego przykładu.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurowanie lub usuwanie TelemetryModules domyślnego

Application Insights używa modułów telemetrycznych do automatycznego zbierania danych telemetrycznych dotyczących konkretnych obciążeń, bez konieczności ręcznego śledzenia.

Następujące moduły automatycznego zbierania są domyślnie włączone. Te moduły są odpowiedzialne za automatyczne zbieranie danych telemetrycznych. Można je wyłączyć lub skonfigurować, aby zmienić zachowanie domyślne.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` -(Obecnie występuje problem obejmujący ten moduł telemetrii. Tymczasowe obejście problemu można znaleźć w witrynie [GitHub 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Aby skonfigurować wszystkie ustawienia domyślne `TelemetryModule` , należy użyć metody rozszerzenia `ConfigureTelemetryModule<T>` na `IServiceCollection` , jak pokazano w poniższym przykładzie.

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

### <a name="configuring-telemetry-channel"></a>Konfigurowanie kanału telemetrii

Domyślny kanał to `ServerTelemetryChannel` . Można go zastąpić, jak pokazano w poniższym przykładzie.

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

Jeśli chcesz warunkowo i dynamicznie wyłączyć telemetrię, możesz rozwiązać `TelemetryConfiguration` wystąpienie z kontenerem iniekcji zależności ASP.NET Core dowolnym miejscu w kodzie i ustawić `DisableTelemetry` dla niego flagę.

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

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak mogę śledzić dane telemetryczne, które nie są zbierane automatycznie?

Pobierz wystąpienie programu przy `TelemetryClient` użyciu iniekcji konstruktora i Wywołaj wymaganą `TrackXXX()` metodę. Nie zalecamy tworzenia nowych `TelemetryClient` wystąpień. Pojedyncze wystąpienie `TelemetryClient` jest już zarejestrowane w `DependencyInjection` kontenerze, które jest udostępniane `TelemetryConfiguration` z resztą telemetrii. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy wymaga konfiguracji, która jest oddzielona od reszty telemetrii.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Czy mogę użyć środowiska IDE programu Visual Studio, aby dołączyć Application Insights do projektu usługi procesu roboczego?

Dołączanie do środowiska IDE programu Visual Studio jest obecnie obsługiwane tylko w przypadku aplikacji ASP.NET/ASP.NET Core. Ten dokument zostanie zaktualizowany, gdy program Visual Studio będzie obsługiwał obsługę aplikacji usługi procesu roboczego dołączania.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Czy można włączyć monitorowanie Application Insights przy użyciu narzędzi takich jak monitor stanu?

Nie. [Monitor stanu](./monitor-performance-live-website-now.md) i [Monitor stanu v2](./status-monitor-v2-overview.md) obsługują obecnie tylko ASP.NET 4. x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Czy w przypadku uruchamiania aplikacji w systemie Linux są obsługiwane wszystkie funkcje?

Tak. Obsługa funkcji dla tego zestawu SDK jest taka sama na wszystkich platformach z następującymi wyjątkami:

* Liczniki wydajności są obsługiwane tylko w systemie Windows z wyjątkiem procesora CPU/pamięci procesu pokazanej w metrykach na żywo.
* Mimo że `ServerTelemetryChannel` jest włączona domyślnie, jeśli aplikacja działa w systemie Linux lub MacOS, kanał nie tworzy automatycznie lokalnego folderu magazynu, aby zapewnić telemetrię tymczasowo w przypadku problemów z siecią. Z powodu tego ograniczenia dane telemetryczne są tracone w przypadku wystąpienia tymczasowych problemów z siecią lub serwerem. Aby obejść ten problem, należy skonfigurować lokalny folder dla kanału:

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

[Aplikacja konsolowa platformy .NET Core](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) Użyj tego przykładu, jeśli używasz aplikacji konsolowej, która jest zapisywana w platformie .NET Core (2,0 lub nowszej) lub .NET Framework (4.7.2 lub nowszy)

[Zadania w tle ASP .NET Core z HostedServices](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Użyj tego przykładu, jeśli używasz programu Asp.Net Core 2.1/2.2 i tworzysz zadania w tle zgodnie z oficjalnymi wskazówkami [tutaj](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&preserve-view=true)

[Usługa procesu roboczego .NET Core 3,0](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) Użyj tego przykładu, jeśli masz aplikację usługi procesu roboczego platformy .NET Core 3,0 zgodnie z oficjalnymi wskazówkami [tutaj](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-3.0&preserve-view=true#worker-service-template)

## <a name="open-source-sdk"></a>Zestaw SDK open source

[Odczytuj i współtworzyć kod](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="next-steps"></a>Następne kroki

* [Użyj interfejsu API,](./api-custom-events-metrics.md) aby wysyłać własne zdarzenia i metryki w celu uzyskania szczegółowego widoku wydajności i użycia aplikacji.
* [Śledź dodatkowe zależności, które nie są automatycznie śledzone](./auto-collect-dependencies.md).
* [Wzbogacanie lub filtrowanie danych telemetrycznych,](./api-filtering-sampling.md)które są zbierane.
* [Wstrzykiwanie zależności w ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).
