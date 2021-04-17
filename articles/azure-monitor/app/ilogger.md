---
title: Eksplorowanie dzienników śledzenia platformy .NET za pomocą narzędzia ILogger — Azure Application Insights
description: Przykłady użycia dostawcy azure Application Insights ILogger z ASP.NET Core i console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: a4781e3f0208d355c06df506bab3b0a3dd457078
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568594"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider for Microsoft.Extension.Logging

W tym artykule pokazano, jak używać programu do przechwytywania dzienników w konsoli `ApplicationInsightsLoggerProvider` i ASP.NET `ILogger` Core.
Aby dowiedzieć się więcej na temat rejestrowania, zobacz [Rejestrowanie w programie ASP.NET Core.](/aspnet/core/fundamentals/logging)

## <a name="aspnet-core-applications"></a>ASP.NET Core

`ApplicationInsightsLoggerProvider`program jest domyślnie włączony dla ASP.NET Core, gdy skonfigurowano [](./asp-net-core.md) usługę ApplicationInsights przy użyciu podejścia bez kodu [lub](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) kodu.

Tylko *dzienniki* ostrzeżenie i powyżej (ze wszystkich kategorii ) są domyślnie wysyłane `ILogger` do Application Insights. [](/aspnet/core/fundamentals/logging/#log-category) To zachowanie można [jednak dostosować.](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection) Aby przechwycić dzienniki ILogger z programu **Program.cs** lub **Startup.cs,** należy wykonać dodatkowe kroki. (Zobacz [Przechwytywanie dzienników ILogger z programów Startup.cs i Program.cs w ASP.NET Core).](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)

Jeśli chcesz po prostu używać bez `ApplicationInsightsLoggerProvider` żadnych innych Application Insights monitorowania, użyj następujących kroków.

1. Zainstaluj pakiet NuGet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. `Program.cs`Zmodyfikuj, jak pokazano poniżej:

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

Kod w kroku 2 `ApplicationInsightsLoggerProvider` konfiguruje . Poniższy kod przedstawia przykład klasy Controller, która używa do `ILogger` wysyłania dzienników. Dzienniki są przechwytywane przez Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Przechwytywanie dzienników ILogger z programów Startup.cs i Program.cs w ASP.NET Core

> [!NOTE]
> W ASP.NET Core 3.0 i nowszych nie jest już możliwe wstrzyknięcie do `ILogger` skryptów Startup.cs i Program.cs. Aby https://github.com/aspnet/Announcements/issues/353 uzyskać więcej informacji, zobacz .

`ApplicationInsightsLoggerProvider` może przechwytywać dzienniki z wczesnego uruchomienia aplikacji. Mimo że element ApplicationInsightsLoggerProvider jest automatycznie włączany w programie Application Insights (począwszy od wersji 2.7.1), nie ma klucza instrumentacji ustawionego do późniejszej części potoku. Dlatego przechwytywane będą tylko **dzienniki z kontrolera**/innych klas. Aby przechwycić każdy dziennik rozpoczynający się od plików **Program.cs** i **Startup.cs,** należy jawnie włączyć klucz instrumentacji dla applicationInsightsLoggerProvider. Ponadto konfiguracja *TelemetryConfiguration* nie jest w pełni skonfigurowana podczas logowania się z poziomu samego **pliku Program.cs** lub **Startup.cs.** Dlatego te dzienniki będą mieć minimalną konfigurację, która używa [inmemoryChannel,](./telemetry-channels.md)bez próbkowania [i](./sampling.md)żadnych standardowych inicjatorów telemetrii [ani procesorów](./api-filtering-sampling.md).

W poniższych przykładach pokazano tę możliwość za pomocą **programów Program.cs** i **Startup.cs.**

#### <a name="example-programcs"></a>Przykładowy program.cs

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

#### <a name="example-startupcs"></a>Przykładowy skrypt Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrowanie ze starego rejestratora ApplicationInsightsLoggerProvider

Zestaw SDK Microsoft.ApplicationInsights.AspNet w wersjach wcześniejszych niż 2.7.1 obsługił dostawcę rejestrowania, który jest teraz przestarzały. Ten dostawca został włączony za pomocą **metody rozszerzenia AddApplicationInsights()** ILoggerFactory. Zalecamy przeprowadzenie migracji do nowego dostawcy, co obejmuje dwa kroki:

1. Usuń wywołanie *ILoggerFactory.AddApplicationInsights()* z **metodyStartup.Configure(),** aby uniknąć podwójnego rejestrowania.
2. Ponownie zastosuj wszystkie reguły filtrowania w kodzie, ponieważ nie będą one przestrzegane przez nowego dostawcę. Przeciążenia funkcji *ILoggerFactory.AddApplicationInsights()* miały minimalne funkcje LogLevel lub filter. W przypadku nowego dostawcy filtrowanie jest częścią samej struktury rejestrowania. Nie jest to wykonywane przez dostawcę Application Insights danych. Dlatego wszelkie filtry dostarczane za pośrednictwem *przeciążeń ILoggerFactory.AddApplicationInsights()* powinny zostać usunięte. Reguły filtrowania powinny być udostępniane zgodnie z instrukcjami [dotyczącymi poziomu rejestrowania sterowania.](#control-logging-level) Jeśli używasz *appsettings.jsdo* filtrowania rejestrowania, będzie ono nadal działać z nowym dostawcą, ponieważ obaj używają tego samego aliasu dostawcy, *applicationInsights.*

Nadal możesz używać starego dostawcy. (Zostanie ona usunięta tylko w przypadku zmiany wersji głównych na 3. *xx.)* Zalecamy jednak migrację do nowego dostawcy z następujących powodów:

- Poprzedni dostawca nie obsługuje [zakresów dzienników](/aspnet/core/fundamentals/logging#log-scopes). W nowym dostawcy właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowe do zebranych danych telemetrycznych.
- Dzienniki można teraz przechwycić znacznie wcześniej w potoku uruchamiania aplikacji. Dzienniki z **klas Program** i **Startup** można teraz przechwytywać.
- W przypadku nowego dostawcy filtrowanie odbywa się na poziomie samej struktury. Dzienniki można filtrować do dostawcy Application Insights w taki sam sposób jak w przypadku innych dostawców, w tym dostawców wbudowanych, takich jak konsola, debugowanie i tak dalej. Te same filtry można również zastosować do wielu dostawców.
- W ASP.NET Core (w wersji 2.0 i [](https://github.com/aspnet/Announcements/issues/255) nowszych) zalecanym sposobem włączenia dostawców rejestrowania jest użycie metod rozszerzeń w programie ILoggingBuilder w samym pliku **Program.cs.**

> [!Note]
> Nowy dostawca jest dostępny dla aplikacji, których platformą docelową jest NETSTANDARD2.0 lub nowsza wersja. Od [wersji Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.14.0 nowy dostawca jest również dostępny dla aplikacji docelowych .NET Framework NET461 lub nowszych. Jeśli aplikacja jest przeznaczony dla starszych wersji programu .NET Core, takich jak .NET Core 1.1, lub jeśli jest przeznaczony dla programu .NET Framework mniej niż NET46, nadal używaj starego dostawcy.

## <a name="console-application"></a>Aplikacja konsolowa

> [!NOTE]
> Istnieje nowy zestaw SDK usługi Application Insights o nazwie [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) który może służyć do włączania funkcji Application Insights (rejestratora ILogger i innych telemetrii Application Insights) dla dowolnych aplikacji konsoli. Zaleca się użycie tego pakietu i skojarzonych instrukcji z tego [tematu.](./worker-service.md)

Jeśli chcesz po prostu użyć applicationInsightsLoggerProvider bez Application Insights monitorowania, należy wykonać następujące kroki.

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

W tym przykładzie użyto pakietu autonomicznego `Microsoft.Extensions.Logging.ApplicationInsights` . Domyślnie ta konfiguracja używa "minimalnej minimalnej" wartości TelemetryConfiguration do wysyłania danych do Application Insights. Minimalna wartość oznacza, że używany jest kanał InMemoryChannel. Nie ma próbkowania ani standardowych wartości TelemetryInitializers. To zachowanie można przesłonić dla aplikacji konsolowej, jak pokazano w poniższym przykładzie.

Zainstaluj ten dodatkowy pakiet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślną wartość TelemetryConfiguration przy **użyciuservices.Configure \<TelemetryConfiguration> ().** Ten przykład konfiguruje i `ServerTelemetryChannel` próbkuje. Dodaje ona niestandardowy interfejs ITelemetryInitializer do konfiguracji TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Poziom rejestrowania kontroli

`ILogger`Ma wbudowany mechanizm do stosowania [filtrowania dzienników.](/aspnet/core/fundamentals/logging#log-filtering) Umożliwia to kontrolowanie dzienników wysyłanych do każdego zarejestrowanego dostawcy, w tym Application Insights dostawcy. Filtrowanie można wykonać w konfiguracji (zazwyczaj przy użyciuappsettings.js *pliku)* lub w kodzie.

Poniższe przykłady pokazują, jak zastosować reguły filtrowania do `ApplicationInsightsLoggerProvider` programu .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Tworzenie reguł filtrowania w konfiguracji z appsettings.jsna

W przypadku dostawcy ApplicationInsightsLoggerProvider alias dostawcy to `ApplicationInsights` . W poniższej sekcji *appsettings.jsna instruowanie* dostawców rejestrowania, aby logować się na poziomie *Ostrzeżenie* i powyżej. Następnie zastępuje do rejestru kategorii, które zaczynają się `ApplicationInsightsLoggerProvider` od "Microsoft" na poziomie *Błąd* i powyżej.

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

### <a name="create-filter-rules-in-code"></a>Tworzenie reguł filtrowania w kodzie

Poniższy fragment kodu konfiguruje  dzienniki dla ostrzeżenie i  powyżej ze wszystkich kategorii i błąd i powyżej z kategorii, które zaczynają się od "Microsoft" do wysłania do `ApplicationInsightsLoggerProvider` . Ta konfiguracja jest taka sama jak w poprzedniej sekcji w sekcji dotyczącejappsettings.js *w programie*.

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

`ApplicationInsightsLoggingProvider` Obsługuje [zakresy dziennika](/aspnet/core/fundamentals/logging#log-scopes) i zakresy są domyślnie włączone.

Jeśli zakres jest typu , każda para klucz-wartość w kolekcji jest dodawana do telemetrii usługi `IReadOnlyCollection<KeyValuePair<string,object>>` Application Insights jako właściwości niestandardowe. W poniższym przykładzie dzienniki zostaną przechwycone jako i będą `TraceTelemetry` mieć właściwości ("MyKey", "MyValue").

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Jeśli dowolny inny typ jest używany jako zakres, będą one przechowywane we właściwości "Zakres" w telemetrii usługi Application Insights. W poniższym przykładzie właściwość `TraceTelemetry` będzie mieć właściwość o nazwie "Zakres" zawierającą zakres.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Jakie są stare i nowe wersje applicationInsightsLoggerProvider?

Zestaw [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zawiera wbudowany obiekt ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), który został włączony za pomocą metod rozszerzenia **ILoggerFactory.** Ten dostawca jest oznaczony jako przestarzały w wersji 2.7.1. Zostanie ona całkowicie usunięta podczas następnej zmiany wersji głównych. Sam [pakiet Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) nie jest przestarzały. Wymagane jest włączenie monitorowania żądań, zależności i tak dalej.

Sugerowaną alternatywą jest nowy autonomiczny pakiet [Microsoft.Extensions.Logging.ApplicationInsights,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)który zawiera ulepszony element ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) i metody rozszerzeń w programie ILoggerBuilder umożliwiające jego włączenie.

[Zestaw SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.1 jest zależny od nowego pakietu i automatycznie włącza przechwytywanie przez rejestratora.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Dlaczego niektóre dzienniki ILogger są wyświetlane dwa razy w Application Insights?

Duplikowanie może wystąpić, jeśli masz starszą (przestarzałą) wersję applicationInsightsLoggerProvider włączoną przez wywołanie `AddApplicationInsights` funkcji `ILoggerFactory` . Sprawdź, czy **metoda Configure** ma następujące elementy, i usuń ją:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

W przypadku podwójnego rejestrowania podczas debugowania z Visual Studio ustaw wartość false w kodzie, który Application Insights `EnableDebugLogger` w następujący sposób.  Ta duplikacja i poprawka ma zastosowanie tylko podczas debugowania aplikacji.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Zaktualizowano zestaw [SDK Microsoft.ApplicationInsights.AspNet w](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) wersji 2.7.1, a dzienniki z rejestratora są przechwytywane automatycznie. Jak mogę całkowicie wyłączyć tę funkcję?

Zobacz [sekcję Control logging level (Poziom rejestrowania kontroli),](#control-logging-level) aby dowiedzieć się, jak ogólnie filtrować dzienniki. Aby wyłączyć aplikację ApplicationInsightsLoggerProvider, użyj opcji `LogLevel.None` :

**W kodzie:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**W pliku konfiguracji:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Dlaczego niektóre dzienniki ILogger nie mają takich samych właściwości jak inne?

Application Insights przechwytuje i wysyła dzienniki ILogger przy użyciu tej samej funkcji TelemetryConfiguration, która jest używana dla każdej innej telemetrii. Istnieje jednak wyjątek. Domyślnie konfiguracja TelemetryConfiguration nie jest w pełni skonfigurowana podczas logowania się z pliku **Program.cs** lub **Startup.cs.** Dzienniki z tych miejsc nie będą mieć konfiguracji domyślnej, dlatego nie będą uruchomione wszystkie elementy TelemetryInitializers i TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Korzystam z pakietu autonomicznego Microsoft.Extensions.Logging.ApplicationInsights i chcę ręcznie rejestrować dodatkowe niestandardowe dane telemetryczne. Jak to zrobić?

W przypadku korzystania z pakietu autonomicznego program nie jest wstrzykiwany do kontenera di, dlatego należy utworzyć nowe wystąpienie klasy i użyć tej samej konfiguracji, z których korzysta dostawca rejestratora, jak pokazano w poniższym `TelemetryClient` `TelemetryClient` kodzie. Dzięki temu ta sama konfiguracja jest używana dla wszystkich niestandardowych danych telemetrycznych, a także telemetrii z rejestratora.

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
> Jeśli używasz pakietu Microsoft.ApplicationInsights.AspNetCore do włączenia Application Insights, zmodyfikuj ten kod, aby uzyskać `TelemetryClient` bezpośrednio w konstruktorze. Aby uzyskać przykład, zobacz często [zadawane pytania.](./asp-net-core.md#frequently-asked-questions)


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaki Application Insights telemetrii jest wytwarzanych na podstawie dzienników ILogger? A gdzie mogę zobaczyć dzienniki ILogger w Application Insights?

ApplicationInsightsLoggerProvider przechwytuje dzienniki ILogger i tworzy z nich dane TraceTelemetry. Jeśli obiekt Exception jest przekazywany do metody w obiekcie , tworzona jest metoda `Log` `ILogger` *ExceptionTelemetry* zamiast TraceTelemetry. Te elementy telemetrii można znaleźć w tych samych miejscach co inne dane TraceTelemetry lub ExceptionTelemetry Application Insights, w tym portal, analiza lub Visual Studio debuger lokalny.

Jeśli wolisz zawsze wysyłać dane TraceTelemetry, użyj tego fragmentu kodu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nie mam zainstalowanego zestawu SDK i używam rozszerzenia azure Web Apps, aby włączyć obsługę Application Insights dla aplikacji ASP.NET Core. Jak mogę używać nowego dostawcy? 

Rozszerzenie Application Insights w usłudze Azure Web Apps używa nowego dostawcy. Reguły filtrowania można modyfikować wappsettings.js *w pliku* aplikacji.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Korzystam z pakietu autonomicznego Microsoft.Extensions.Logging.ApplicationInsights i włączania dostawcy Application Insights przez wywołanie **konstruktora. AddApplicationInsights("ikey")**. Czy istnieje możliwość uzyskania klucza instrumentacji z konfiguracji?


Zmodyfikuj program.cs i appsettings.jsw następujący sposób:

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

   Odpowiednie sekcje z `appsettings.json` artykułu :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ten kod jest wymagany tylko w przypadku korzystania z autonomicznego dostawcy rejestrowania. W przypadku Application Insights monitorowania instrumentacji klucz instrumentacji jest ładowany automatycznie ze ścieżki konfiguracji *ApplicationInsights: InstrumentationKey*. Appsettings.jspowinien wyglądać tak:

   ```json
   {
     "ApplicationInsights":
       {
           "InstrumentationKey":"putrealikeyhere"
       }
   }
   ```

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia ciągów połączenia zamiast kluczy instrumentacji. [Parametrów połączenia](./sdk-connection-string.md?tabs=net) identyfikuje zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą przez zasób miejscem docelowym telemetrii. Konieczne będzie skopiowanie parametrów połączenia i dodanie ich do kodu aplikacji lub do zmiennej środowiskowej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na następujące tematy:

* [Logowanie w programie ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia .NET w programie Application Insights](./asp-net-trace-logs.md)

