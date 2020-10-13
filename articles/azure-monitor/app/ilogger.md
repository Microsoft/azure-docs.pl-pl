---
title: Eksplorowanie dzienników śledzenia .NET z ILogger — Azure Application Insights
description: Przykłady użycia dostawcy usługi Azure Application Insights ILogger z aplikacjami ASP.NET Core i konsolowymi.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 987d5b78c5fe680f43ff6a001e7a31a8ae9f6124
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931465"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider dla Microsoft. Extension. Logging

W tym artykule przedstawiono sposób korzystania `ApplicationInsightsLoggerProvider` z programu do przechwytywania `ILogger` dzienników w konsoli programu i aplikacji ASP.NET Core.
Aby dowiedzieć się więcej o rejestrowaniu, zobacz [Rejestrowanie w ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core aplikacji

`ApplicationInsightsLoggerProvider` Program jest domyślnie włączony dla aplikacji ASP.NET Core, gdy ApplicationInsights jest skonfigurowany przy użyciu metody [Code](./asp-net-core.md) lub [Code-less](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) .

Tylko *ostrzeżenia* i powyższe `ILogger` dzienniki (ze wszystkich [kategorii](/aspnet/core/fundamentals/logging/#log-category)) są domyślnie wysyłane do Application Insights. Można jednak [dostosować to zachowanie](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). Do przechwycenia dzienników ILogger z **program.cs** lub **Startup.cs**jest wymagane wykonanie dodatkowych czynności. (Zobacz [przechwytywanie dzienników ILogger z Startup.cs i program.cs w aplikacjach ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Jeśli chcesz tylko korzystać z programu `ApplicationInsightsLoggerProvider` bez żadnych innych Application Insights monitorowania, wykonaj następujące czynności.

1. Zainstaluj pakiet NuGet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Zmodyfikuj `Program.cs` jak pokazano poniżej:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Kod w kroku 2 konfiguruje `ApplicationInsightsLoggerProvider` . Poniższy kod przedstawia przykładową klasę kontrolera, która używa `ILogger` do wysyłania dzienników. Dzienniki są przechwytywane przez Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Przechwytywanie dzienników ILogger z Startup.cs i Program.cs w aplikacjach ASP.NET Core

> [!NOTE]
> W ASP.NET Core 3,0 i nowszych nie jest już możliwe wprowadzanie `ILogger` w Startup.cs i program.cs. https://github.com/aspnet/Announcements/issues/353Aby uzyskać więcej informacji, zobacz.

`ApplicationInsightsLoggerProvider` program może przechwycić dzienniki przed wczesnym uruchomieniem aplikacji. Mimo że usługa ApplicationInsightsLoggerProvider jest automatycznie włączana w Application Insights (począwszy od wersji 2.7.1), nie ma ustawionego klucza instrumentacji do późniejszego potoku. W związku z tym tylko dzienniki z klas/inne **kontrolera**będą przechwytywane. Aby przechwycić każdy dziennik zaczynający się od **program.cs** i **Startup.cs** , należy jawnie włączyć klucz Instrumentacji dla ApplicationInsightsLoggerProvider. Ponadto *TelemetryConfiguration* nie jest w pełni skonfigurowany podczas rejestrowania się z **program.cs** lub **Startup.cs** . Te dzienniki będą mieć minimalną konfigurację, która używa [InMemoryChannel](./telemetry-channels.md), bez [próbkowania](./sampling.md)ani nie ma standardowych [inicjatorów telemetrii ani procesorów](./api-filtering-sampling.md).

Poniższe przykłady przedstawiają tę możliwość przy użyciu **program.cs** i **Startup.cs**.

#### <a name="example-programcs"></a>Przykład Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Przykład Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrowanie ze starego ApplicationInsightsLoggerProvider

Wersje zestawu SDK Microsoft. ApplicationInsights. AspNet przed 2.7.1 są obsługiwane przez dostawcę rejestrowania, który jest już przestarzały. Ten dostawca został włączony za pomocą metody rozszerzenia **AddApplicationInsights ()** ILoggerFactory. Zalecamy przeprowadzenie migracji do nowego dostawcy, który obejmuje dwa kroki:

1. Usuń wywołanie *ILoggerFactory. AddApplicationInsights ()* z metody **Startup.Configuruj ()** , aby uniknąć podwójnego rejestrowania.
2. Zastosuj ponownie wszystkie reguły filtrowania w kodzie, ponieważ nie będą one przestrzegane przez nowego dostawcę. Przeciążenia *ILoggerFactory. AddApplicationInsights ()* wymagały minimalnych funkcji LogLevel lub Filter. W przypadku nowego dostawcy filtrowanie jest częścią samej struktury rejestrowania. Nie zostało to zrobione przez dostawcę Application Insights. Należy usunąć wszystkie filtry, które są dostarczane za pośrednictwem przeciążeń *ILoggerFactory. AddApplicationInsights ()* . I reguły filtrowania powinny być podane zgodnie z instrukcjami [kontroli poziomu rejestrowania](#control-logging-level) . Jeśli używasz *appsettings.js* do filtrowania rejestrowania, będzie on nadal działał z nowym dostawcą, ponieważ oba używają tego samego aliasu dostawcy, *ApplicationInsights*.

Nadal możesz używać starego dostawcy. (Zostanie on usunięty tylko w wersji głównej, zmiana na 3). *XX*), ale zalecamy przeprowadzenie migracji do nowego dostawcy z następujących powodów:

- Poprzedni dostawca nie obsługuje [zakresów dzienników](/aspnet/core/fundamentals/logging#log-scopes). W nowym dostawcy właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowe do zebranych danych telemetrycznych.
- Dzienniki można teraz przechwycić znacznie wcześniej w potoku uruchamiania aplikacji. Dzienniki z **programu** i klasy **uruchamiania** mogą teraz być przechwytywane.
- W przypadku nowego dostawcy Filtrowanie odbywa się na poziomie platformy. Dzienniki można filtrować do dostawcy Application Insights w taki sam sposób jak w przypadku innych dostawców, w tym w przypadku dostawców wbudowanych, takich jak konsole, debugowanie i tak dalej. Można również zastosować te same filtry do wielu dostawców.
- W ASP.NET Core (2,0 i nowszych) zalecanym sposobem  [włączania dostawców rejestrowania](https://github.com/aspnet/Announcements/issues/255) jest użycie metod rozszerzających ILoggingBuilder w **program.cs** .

> [!Note]
> Nowy dostawca jest dostępny dla aplikacji przeznaczonych dla wersji STANDARD 2.0 lub nowszej. Od [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) wersja 2.14.0, nowy dostawca jest również dostępny dla aplikacji przeznaczonych .NET Framework NET461 lub nowszych. Jeśli aplikacja jest przeznaczona dla starszych wersji programu .NET Core, takich jak .NET Core 1,1, lub jeśli jest przeznaczona dla .NET Framework mniejszej niż NET46, Kontynuuj korzystanie z starego dostawcy.

## <a name="console-application"></a>Aplikacja konsolowa

> [!NOTE]
> Istnieje nowy Application Insights SDK o nazwie [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , który może służyć do włączania Application Insights (ILogger i innych Application Insights telemetrii) dla każdej aplikacji konsolowej. Zalecane jest korzystanie z tego pakietu i powiązanych instrukcji w [tym miejscu](./worker-service.md).

Jeśli chcesz tylko używać ApplicationInsightsLoggerProvider bez żadnych innych Application Insights monitorowania, wykonaj następujące czynności.

Zainstalowane pakiety:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

W tym przykładzie jest użyty pakiet autonomiczny `Microsoft.Extensions.Logging.ApplicationInsights` . Domyślnie ta konfiguracja używa TelemetryConfiguration "bCzy minimum" do wysyłania danych do Application Insights. Minimum od zera oznacza, że InMemoryChannel jest używanym kanałem. Nie ma próbkowania ani standardowego TelemetryInitializers. Takie zachowanie można zastąpić dla aplikacji konsolowej, jak pokazano w poniższym przykładzie.

Zainstaluj ten dodatkowy pakiet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślny TelemetryConfiguration za pomocą metody **services.Configuruj \<TelemetryConfiguration> ()** . Ten przykład konfiguruje `ServerTelemetryChannel` i pobiera próbkowanie. Dodaje niestandardowy ITelemetryInitializer do TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Kontrola poziomu rejestrowania

`ILogger` ma wbudowany mechanizm zastosowania [filtrowania dzienników](/aspnet/core/fundamentals/logging#log-filtering). Pozwala to kontrolować dzienniki wysyłane do każdego zarejestrowanego dostawcy, w tym dostawcę Application Insights. Filtrowanie można wykonać w konfiguracji (zazwyczaj przy użyciu *appsettings.jsw* pliku) lub w kodzie.

W poniższych przykładach pokazano, jak zastosować reguły filtru do programu `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Utwórz reguły filtru w konfiguracji z appsettings.jsna

W przypadku ApplicationInsightsLoggerProvider alias dostawcy to `ApplicationInsights` . W poniższej sekcji *appsettings.jsna tym* instruują dostawców dzienników na ogół na *Ostrzeżenie* na poziomie i powyżej. Następnie zastępuje `ApplicationInsightsLoggerProvider` Kategorie do dziennika, które zaczynają się od "Microsoft" na poziomie *błędu* i powyżej.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Tworzenie reguł filtru w kodzie

Poniższy fragment kodu konfiguruje dzienniki pod kątem *ostrzeżenia* i powyżej ze wszystkich kategorii oraz dla *błędu* i powyżej z kategorii, które zaczynają się od "Microsoft" do wysłania do `ApplicationInsightsLoggerProvider` . Ta konfiguracja jest taka sama jak w poprzedniej sekcji *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Zakresy rejestrowania

`ApplicationInsightsLoggingProvider` Obsługa [zakresów dzienników](/aspnet/core/fundamentals/logging#log-scopes) i zakresów jest domyślnie włączona.

Jeśli zakres jest typu `IReadOnlyCollection<KeyValuePair<string,object>>` , każda para klucz-wartość w kolekcji zostanie dodana do telemetrii usługi Application Insights jako właściwości niestandardowe. W poniższym przykładzie dzienniki będą przechwytywane jako `TraceTelemetry` i będą miały ("klucze", "wartość") we właściwościach.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Jeśli jakikolwiek inny typ jest używany jako zakres, zostaną one zapisane w ramach właściwości "Scope" w telemetrii usługi Application Insights. W poniższym przykładzie zostanie wydana `TraceTelemetry` Właściwość o nazwie "Scope" zawierająca zakres.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Jakie są stare i nowe wersje programu ApplicationInsightsLoggerProvider?

[Zestaw Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zawiera wbudowaną ApplicationInsightsLoggerProvider (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), która została włączona za pomocą metod rozszerzenia **ILoggerFactory** . Ten dostawca został oznaczony jako przestarzały w wersji 2.7.1. Zostanie on całkowicie usunięty w następnej zmianie wersji głównej. Sam pakiet [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) . Jest to wymagane do włączenia monitorowania żądań, zależności i tak dalej.

Sugerowana alternatywa to nowy pakiet autonomiczny [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), który zawiera udoskonaloną metodę ApplicationInsightsLoggerProvider (Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) i metody rozszerzenia na ILoggerBuilder w celu jej włączenia.

[Zestaw SDK Microsoft. ApplicationInsights. ASPNET](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.1 ma zależność od nowego pakietu i włącza automatyczne przechwytywanie ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Dlaczego niektóre dzienniki ILogger są wyświetlane dwukrotnie w Application Insights?

Duplikowanie może wystąpić, jeśli masz starszą (teraz przestarzałą) wersję programu ApplicationInsightsLoggerProvider, która została włączona przez wywołanie metody `AddApplicationInsights` `ILoggerFactory` . Sprawdź, czy metoda **konfiguracji** ma następujące elementy, i usuń ją:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Jeśli podczas debugowania z programu Visual Studio występuje podwójne rejestrowanie, `EnableDebugLogger` w kodzie, który umożliwia Application Insights, należy ustawić  *wartość false* . To duplikowanie i naprawa ma zastosowanie tylko w przypadku debugowania aplikacji.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Zaktualizowano do [Microsoft. ApplicationInsights. ASPNET SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) wersja 2.7.1, a dzienniki z ILogger są przechwytywane automatycznie. Jak mogę wyłączyć tę funkcję całkowicie?

Zobacz sekcję [Kontrola poziomu rejestrowania](#control-logging-level) , aby dowiedzieć się, jak ogólnie filtrować dzienniki. Aby wyłączyć ApplicationInsightsLoggerProvider, użyj `LogLevel.None` :

**W kodzie:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**W konfiguracji:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Dlaczego niektóre dzienniki ILogger mają takie same właściwości jak inne?

Application Insights przechwytuje i wysyła dzienniki ILogger przy użyciu tego samego TelemetryConfiguration, który jest używany przez każdą inną telemetrię. Ale wystąpił wyjątek. Domyślnie TelemetryConfiguration nie jest w pełni skonfigurowany podczas rejestrowania z **program.cs** lub **Startup.cs**. Dzienniki z tych miejsc nie mają konfiguracji domyślnej, dlatego nie będą działać wszystkie TelemetryInitializers i TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Używam autonomicznego pakietu Microsoft. Extensions. Logging. ApplicationInsights i chcę ręcznie rejestrować kilka dodatkowych danych telemetrycznych. Jak to zrobić?

W przypadku korzystania z pakietu autonomicznego program `TelemetryClient` nie jest wprowadzany do kontenera di, dlatego należy utworzyć nowe wystąpienie programu `TelemetryClient` i użyć tej samej konfiguracji, która jest używana przez dostawcę rejestratora, jak pokazano w poniższym kodzie. Gwarantuje to, że ta sama konfiguracja jest używana dla wszystkich niestandardowych danych telemetrycznych, a także do telemetrii z ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Jeśli używasz pakietu Microsoft. ApplicationInsights. AspNetCore do włączania Application Insights, zmodyfikuj ten kod, aby uzyskać `TelemetryClient` bezpośrednio w konstruktorze. Na przykład zapoznaj się z tematem [często zadawanych pytań](./asp-net-core.md#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaki Application Insights typ telemetrii jest produkowany z dzienników ILogger? Lub gdzie mogę zobaczyć ILogger dzienników w Application Insights?

ApplicationInsightsLoggerProvider przechwytuje dzienniki ILogger i tworzy TraceTelemetry z nich. Jeśli obiekt wyjątku jest przesyłany do `Log` metody on `ILogger` , *ExceptionTelemetry* jest tworzony zamiast TraceTelemetry. Te elementy telemetrii mogą znajdować się w tych samych miejscach co inne TraceTelemetry lub ExceptionTelemetry dla Application Insights, w tym do portalu, analizy lub lokalnego debugera programu Visual Studio.

Jeśli wolisz zawsze wysyłać TraceTelemetry, użyj tego fragmentu kodu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nie mam zainstalowanego zestawu SDK i używam rozszerzenia Web Apps platformy Azure, aby włączyć Application Insights dla aplikacji ASP.NET Core. Jak mogę użyć nowego dostawcy? 

Rozszerzenie Application Insights na platformie Azure Web Apps używa nowego dostawcy. Reguły filtrowania można modyfikować w *appsettings.js* pliku dla aplikacji.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Używam samodzielnego pakietu Microsoft. Extensions. Logging. ApplicationInsights i włączania dostawcy Application Insights przez wywoływanie **konstruktora. AddApplicationInsights ("iKey")**. Czy istnieje opcja pobrania klucza Instrumentacji z konfiguracji?


Zmodyfikuj Program.cs i appsettings.jsw następujący sposób:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Odpowiednia sekcja z `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ten kod jest wymagany tylko w przypadku korzystania z autonomicznego dostawcy rejestrowania. Do regularnego monitorowania Application Insights klucz Instrumentacji jest ładowany automatycznie ze ścieżki konfiguracyjnej *ApplicationInsights: Instrumentationkey*. Appsettings.jspowinien wyglądać następująco:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na następujące tematy:

* [Logowanie ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia .NET w Application Insights](./asp-net-trace-logs.md)

