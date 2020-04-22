---
title: Usługa Azure Application Insights dla aplikacji ASP.NET podstawowych | Dokumenty firmy Microsoft
description: Monitoruj ASP.NET podstawowe aplikacje internetowe pod kątem dostępności, wydajności i użycia.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 1a9a81d76df7f14fb99b8521e7bfa2edff6c9e9e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687388"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Usługa Application Insights dla aplikacji ASP.NET podstawowych

W tym artykule opisano, jak włączyć usługę Application Insights dla aplikacji [ASP.NET Core.](https://docs.microsoft.com/aspnet/core) Po wykonaniu instrukcji w tym artykule usługa Application Insights będzie zbierać żądania, zależności, wyjątki, liczniki wydajności, pulsy i dzienniki z aplikacji ASP.NET Core.

Przykład, którego użyjemy w tym miejscu, to [aplikacja MVC,](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app) która jest przeznaczona dla osób. `netcoreapp2.2` Instrukcje te można zastosować do wszystkich aplikacji ASP.NET Core.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

[SDK usługi Application Insights dla ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) może monitorować aplikacje bez względu na to, gdzie i jak działają. Jeśli aplikacja jest uruchomiona i ma łączność sieciową z platformą Azure, można zbierać dane telemetryczne. Monitorowanie usługi Application Insights jest obsługiwane wszędzie tam, gdzie obsługiwany jest program .NET Core. Okładki wsparcia:
* **System operacyjny**: Windows, Linux lub Mac.
* **Metoda hostingu:** W procesie lub poza procesem.
* **Metoda wdrażania:** Zależne od struktury lub samodzielne.
* **Serwer www:** IIS (Internet Information Server) lub Kestrel.
* **Platforma hostingowa:** Funkcja aplikacji sieci Web usługi Azure App Service, Azure VM, Docker, Usługa Azure Kubernetes (AKS) i tak dalej.
* **Wersja .NET Core Runtime:** 1.XX, 2.XX lub 3.XX
* **IDE:** Visual Studio, VS Code lub wiersz polecenia.

> [!NOTE]
> Jeśli używasz ASP.NET Core 3.X wraz ze aplikacją Insights, użyj wersji [2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) lub nowszej. Jest to jedyna wersja, która obsługuje ASP.NET Core 3.X.

## <a name="prerequisites"></a>Wymagania wstępne

- Działająca aplikacja ASP.NET Core. Jeśli chcesz utworzyć aplikację ASP.NET Core, wykonaj ten [ASP.NET tutorialu Core](https://docs.microsoft.com/aspnet/core/getting-started/).
- Prawidłowy klucz instrumentacji usługi Application Insights. Ten klucz jest wymagany do wysyłania danych telemetrycznych do usługi Application Insights. Jeśli chcesz utworzyć nowy zasób usługi Application Insights, aby uzyskać klucz instrumentacji, zobacz [Tworzenie zasobu usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>Włączanie danych telemetrycznych po stronie serwera usługi Application Insights (Visual Studio)

1. Otwórz projekt w programie Visual Studio.

    > [!TIP]
    > Jeśli chcesz, można skonfigurować kontrolę źródła dla projektu, dzięki czemu można śledzić wszystkie zmiany wprowadzone przez usługa Application Insights. Aby włączyć kontrolę źródła, wybierz pozycję **Dodawanie pliku** > **do formantu źródła**.

2. Wybierz **pozycję Program Project** > **Add Application Insights Telemetry**.

3. Wybierz **pozycję Wprowadzenie**. Tekst tego zaznaczenia może się różnić w zależności od wersji programu Visual Studio. Niektóre starsze wersje używają przycisku **Start Free.**

4. Wybierz subskrypcję. Następnie wybierz **pozycję Rejestr zasobów** > **Register**.

5. Po dodaniu usługi Application Insights do projektu, sprawdź, aby potwierdzić, że używasz najnowszej stabilnej wersji SDK. Przejdź do **witryny Project** > **Manage NuGet Packages** > **Microsoft.ApplicationInsights.AspNetCore**. Jeśli chcesz, wybierz pozycję **Aktualizuj**.

     ![Zrzut ekranu przedstawiający, gdzie należy wybrać pakiet usługi Application Insights do aktualizacji](./media/asp-net-core/update-nuget-package.png)

6. Jeśli po opcjonalnej wskazówki i dodał projekt do kontroli źródła, przejdź do **widoku** > **zmiany****Eksploratora** > zespołu . Następnie wybierz każdy plik, aby wyświetlić widok różnicowy zmian wprowadzonych przez dane telemetryczne usługi Application Insights.

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>Włącz dane telemetryczne po stronie serwera usługi Application Insights (bez programu Visual Studio)

1. Zainstaluj [pakiet NuGet SDK usługi Application Insights dla ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore). Zalecamy, aby zawsze korzystać z najnowszej stabilnej wersji. Pełne informacje o wersji dla SDK można znaleźć w [repozytorium GitHub typu open source](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases).

    Poniższy przykład kodu pokazuje zmiany, które mają `.csproj` zostać dodane do pliku projektu.

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. Dodaj `services.AddApplicationInsightsTelemetry();` do `ConfigureServices()` metody `Startup` w klasie, jak w tym przykładzie:

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. Ustaw klucz oprzyrządowania.

    Chociaż klucz instrumentacji można podać jako `AddApplicationInsightsTelemetry`argument do , zaleca się określenie klucza instrumentacji w konfiguracji. Poniższy przykładowy kod pokazuje, jak `appsettings.json`określić klucz oprzyrządowania w . Upewnij `appsettings.json` się, że jest kopiowany do folderu głównego aplikacji podczas publikowania.

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    Alternatywnie należy określić klucz oprzyrządowania w jednej z następujących zmiennych środowiskowych:

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    Przykład:

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    Zazwyczaj `APPINSIGHTS_INSTRUMENTATIONKEY` określa klucz instrumentacji dla aplikacji wdrożonych w usłudze Azure Web Apps.

    > [!NOTE]
    > Klucz instrumentacji określony w kodzie `APPINSIGHTS_INSTRUMENTATIONKEY`wygrywa nad zmienną środowiskową, która wygrywa nad innymi opcjami.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Uruchom aplikację i złóż do niej żądania. Dane telemetryczne powinny teraz przepływać do usługi Application Insights. SDK usługi Application Insights automatycznie zbiera przychodzące żądania sieci web do aplikacji, wraz z następującymi danemi telemetrycznymi.

### <a name="live-metrics"></a>Metryki na żywo

[Metryki na żywo](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream) można szybko sprawdzić, czy monitorowanie usługi Application Insights jest poprawnie skonfigurowany. Chociaż może upłynąć kilka minut, zanim dane telemetryczne zaczną pojawiać się w portalu i analizy, Live Metrics pokaże użycie procesora CPU uruchomionego procesu w czasie zbliżonym do rzeczywistego. Może również wyświetlać inne dane telemetryczne, takie jak żądania, zależności, ślady itp.

### <a name="ilogger-logs"></a>Dzienniki ILogger

Dzienniki emitowane `ILogger` przez `Warning` ważności lub większe są przechwytywane automatycznie. Postępuj zgodnie [z dokami iLogger,](ilogger.md#control-logging-level) aby dostosować poziomy dziennika są przechwytywane przez usługa Application Insights.

### <a name="dependencies"></a>Zależności

Zbieranie zależności jest domyślnie włączone. [W tym](asp-net-dependencies.md#automatically-tracked-dependencies) artykule wyjaśniono zależności, które są zbierane automatycznie, a także zawierają kroki do śledzenia ręcznego.

### <a name="performance-counters"></a>Liczniki wydajności

Obsługa [liczników wydajności](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/) w ASP.NET Core jest ograniczona:

* Zestaw SDK w wersji 2.4.1 i nowszych zbiera liczniki wydajności, jeśli aplikacja jest uruchomiona w usłudze Azure Web Apps (Windows).
* SDK w wersji 2.7.1 i nowszych zbierać liczniki wydajności, jeśli aplikacja jest uruchomiona w systemie Windows i obiektów docelowych `NETSTANDARD2.0` lub nowszych.
* W przypadku aplikacji przeznaczonych dla platformy .NET Framework wszystkie wersje liczników wydajności sdk obsługują.
* SDK w wersji 2.8.0 i nowszych obsługują licznik procesorów/pamięci w systemie Linux. Żaden inny licznik nie jest obsługiwany w systemie Linux. Zalecanym sposobem uzyskania liczników systemowych w systemie Linux (i innych środowiskach innych niż Windows) jest użycie [EventCounters](#eventcounter)

### <a name="eventcounter"></a>Licznik zdarzeń

`EventCounterCollectionModule`domyślnie jest włączona i będzie zbierać domyślny zestaw liczników z aplikacji .NET Core 3.X. [Samouczek EventCounter](eventcounters.md) zawiera listę domyślnego zestawu zebranych liczników. Posiada również instrukcje dotyczące dostosowywania listy.

## <a name="enable-client-side-telemetry-for-web-applications"></a>Włączanie telemetrii po stronie klienta dla aplikacji sieci Web

Powyższe kroki są wystarczające, aby ułatwić rozpoczęcie zbierania danych telemetrycznych po stronie serwera. Jeśli aplikacja ma składniki po stronie klienta, wykonaj następne kroki, aby rozpocząć zbieranie [danych telemetrycznych użycia](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview).

1. W `_ViewImports.cshtml`, dodać wstrzyknięcie:

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. W `_Layout.cshtml`, `HtmlHelper` wstawić na `<head>` końcu sekcji, ale przed każdym innym skryptem. Jeśli chcesz zgłosić niestandardowe dane telemetryczne JavaScript ze strony, wstrzykuj ją po tym urywek:

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
Alternatywnie do `FullScript` korzystania `ScriptBody` z jest dostępny począwszy od SDK v2.14. Użyj tego, jeśli chcesz `<script>` kontrolować tag, aby ustawić zasady zabezpieczeń zawartości:

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

Nazwy `.cshtml` plików, do których odwołuje się wcześniej, pochodzą z domyślnego szablonu aplikacji MVC. Ostatecznie, jeśli chcesz poprawnie włączyć monitorowanie po stronie klienta dla aplikacji, fragment kodu `<head>` JavaScript musi pojawić się w sekcji każdej strony aplikacji, którą chcesz monitorować. Ten cel można osiągnąć dla tego szablonu aplikacji, dodając `_Layout.cshtml`fragment kodu JavaScript do pliku . 

Jeśli projekt nie zawiera `_Layout.cshtml`, nadal można dodać [monitorowanie po stronie klienta.](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring) Można to zrobić, dodając fragment kodu JavaScript do równoważnego `<head>` pliku, który kontroluje wszystkie strony w aplikacji. Możesz też dodać fragment kodu do wielu stron, ale to rozwiązanie jest trudne do utrzymania i ogólnie go nie zalecamy.

## <a name="configure-the-application-insights-sdk"></a>Konfigurowanie sdk usługi Application Insights

Można dostosować SDK usługi Application Insights dla ASP.NET Core, aby zmienić domyślną konfigurację. Użytkownicy ASP.NET SDK usługi Application Insights mogą być zaznajomieni ze zmianą konfiguracji przy użyciu `ApplicationInsights.config` lub modyfikowaniu `TelemetryConfiguration.Active`. Konfigurację można zmienić inaczej dla ASP.NET Core. Dodaj ASP.NET core SDK do aplikacji i skonfiguruj go za pomocą [wbudowanego iniekcji zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)ASP.NET Core . Należy wprowadzić prawie wszystkie `ConfigureServices()` zmiany `Startup.cs` konfiguracji w metodzie klasy, chyba że jesteś skierowany w inny sposób. W poniższych sekcjach znajdują się więcej informacji.

> [!NOTE]
> W aplikacjach ASP.NET Core zmiana konfiguracji `TelemetryConfiguration.Active` przez modyfikację nie jest obsługiwana.

### <a name="using-applicationinsightsserviceoptions"></a>Korzystanie z applicationinsightsserviceoptions

Można zmodyfikować kilka typowych `ApplicationInsightsServiceOptions` `AddApplicationInsightsTelemetry`ustawień, przechodząc do , jak w tym przykładzie:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

Pełna lista ustawień w`ApplicationInsightsServiceOptions`

|Ustawienie | Opis | Domyślne
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | Włączanie/wyłączanie`PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | Włączanie/wyłączanie`RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | Włączanie/wyłączanie`EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | Włączanie/wyłączanie`DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  Włączanie/wyłączanie`AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  Włączanie/wyłączanie`AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Włącz/Wyłącz funkcję LiveMetrics | true
|WłączAdaptiveSampling | Włączanie/wyłączanie próbkowania adaptacyjnego | true
|Włączheartbeat | Włącz/Wyłącz tętno funkcji, która okresowo (domyślnie 15 minut) wysyła metrykę niestandardową o nazwie "HeartBeatState" z informacjami o środowisku uruchomieniowym, takich jak .NET Version, Informacje o środowisku platformy Azure, jeśli dotyczy, itp. | true
|AddAutoCollectedMetricExtractor | Włącz/Wyłącz ekstraktor AutocollectedMetrics, który jest TelemetryProcessor, który wysyła wstępnie zagregowane metryki dotyczące żądań/zależności przed próbkowaniem. | true
|RequestCollectionOptions.TrackExceptions | Włącz/Wyłącz raportowanie nieobsługiwanych śledzenia wyjątków przez moduł kolekcja żądania. | false w NETSTANDARD2.0 (ponieważ wyjątki są śledzone za pomocą ApplicationInsightsLoggerProvider), prawda inaczej.

Zobacz [konfigurowalne `ApplicationInsightsServiceOptions` ustawienia](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) na liście najbardziej aktualne.

### <a name="sampling"></a>Próbkowanie

Zestaw SDK usługi Application Insights dla ASP.NET Core obsługuje zarówno próbkowanie o stałym tempie, jak i adaptacyjne. Adaptacyjne próbkowanie jest domyślnie włączone. 

Aby uzyskać więcej informacji, zobacz [Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET Core](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications).

### <a name="adding-telemetryinitializers"></a>Dodawanie telemetriiInitializatory

Inicjatory [telemetrii](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) służą do definiowania właściwości globalnych, które są wysyłane ze wszystkimi telemetriami.

Dodaj dowolny `TelemetryInitializer` nowy `DependencyInjection` do kontenera, jak pokazano w poniższym kodzie. SDK automatycznie pobiera wszystkie, `TelemetryInitializer` które są dodawane do kontenera. `DependencyInjection`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>Usuwanie telemetriiInitializatory

Inicjatory telemetrii są domyślnie obecne. Aby usunąć wszystkie lub określone inicjatory telemetrii, `AddApplicationInsightsTelemetry()`użyj następującego przykładowego kodu *po* wywołaniu .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

Do niestandardowych procesorów telemetrycznych można dodać `TelemetryConfiguration` przy użyciu metody `AddApplicationInsightsTelemetryProcessor` rozszerzenia w programie `IServiceCollection`. Procesory telemetryczne są używane w [zaawansowanych scenariuszach filtrowania](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer). Użyj poniższego przykładu.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Konfigurowanie lub usuwanie domyślnych modeli telemetrycznych

Usługa Application Insights używa modułów telemetrycznych do automatycznego zbierania przydatnych danych telemetrycznych dotyczących określonych obciążeń bez konieczności ręcznego śledzenia przez użytkownika.

Następujące moduły automatycznej kolekcji są domyślnie włączone. Moduły te są odpowiedzialne za automatyczne zbieranie danych telemetrycznych. Można je wyłączyć lub skonfigurować, aby zmienić ich domyślne zachowanie.

* `RequestTrackingTelemetryModule`- Zbiera RequestTelemetry z przychodzących żądań sieci web.
* `DependencyTrackingTelemetryModule`- Zbiera DependencyTelemetry z wychodzących wywołań http i sql.
* `PerformanceCollectorModule`- Zbiera Wydajność Systemu WindowsCounters.
* `QuickPulseTelemetryModule`- Zbiera dane telemetryczne do wyświetlania w portalu Live Metrics.
* `AppServicesHeartbeatTelemetryModule`- Zbiera bicia serca (które są wysyłane jako metryki niestandardowe), o środowisku usługi Azure App Service, w którym aplikacja jest hostowana.
* `AzureInstanceMetadataTelemetryModule`- Zbiera bicia serca (które są wysyłane jako metryki niestandardowe), o środowisku maszyny Wirtualnej platformy Azure, w którym aplikacja jest hostowana.
* `EventCounterCollectionModule`- Zbiera [EventCounters.](eventcounters.md) Ten moduł jest nową funkcją i jest dostępny w wersji SDK 2.8.0 lub nowszej.

Aby skonfigurować `TelemetryModule`dowolną wartość domyślną, użyj metody `ConfigureTelemetryModule<T>` rozszerzenia na `IServiceCollection`, jak pokazano w poniższym przykładzie.

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

Począwszy od wersji 2.12.2, [`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) zawiera łatwą opcję wyłączenia dowolnego z domyślnych modułów.

### <a name="configuring-a-telemetry-channel"></a>Konfigurowanie kanału telemetryczne

Domyślnym kanałem jest `ServerTelemetryChannel`. Można go zastąpić, jak pokazano w poniższym przykładzie.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>Dynamiczne wyłączanie telemetrii

Jeśli chcesz wyłączyć telemetrii warunkowo i `TelemetryConfiguration` dynamicznie, można rozwiązać wystąpienie z ASP.NET Core `DisableTelemetry` kontenera iniekcji zależności w dowolnym miejscu w kodzie i ustawić flagę na nim.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

Powyższe nie uniemożliwia modułom automatycznej kolekcji zbierania danych telemetrycznych. Tylko wysyłanie danych telemetrycznych do usługi Application Insights zostanie wyłączone z powyższym podejściem. Jeśli określony moduł automatycznej zbiórki nie jest pożądany, najlepiej [usunąć moduł telemetryczny](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-application-insights-support-aspnet-core-3x"></a>Czy usługa Application Insights obsługuje ASP.NET Core 3.X?

Tak. Aktualizacja do [pakietu SDK usługi Application Insights dla ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.8.0 lub nowszej. Starsze wersje SDK nie obsługują ASP.NET Core 3.X.

Ponadto jeśli używasz instrukcji opartych na programie Visual Studio [w tym miejscu,](#enable-application-insights-server-side-telemetry-visual-studio)zaktualizuj do najnowszej wersji programu Visual Studio 2019 (16.3.0) do wbudowanego. Poprzednie wersje programu Visual Studio nie obsługują automatycznego dołączania dla aplikacji ASP.NET Core 3.X.

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Jak śledzić dane telemetryczne, które nie są zbierane automatycznie?

Pobierz wystąpienie `TelemetryClient` za pomocą iniekcji konstruktora i wywołać wymaganą `TrackXXX()` metodę na nim. Nie zaleca się tworzenia `TelemetryClient` nowych wystąpień w aplikacji ASP.NET Core. Wystąpienie `TelemetryClient` singleton jest już `DependencyInjection` zarejestrowane w `TelemetryConfiguration` kontenerze, który udostępnia z resztą danych telemetrycznych. Tworzenie nowego `TelemetryClient` wystąpienia jest zalecane tylko wtedy, gdy wymaga konfiguracji, która jest oddzielona od reszty danych telemetrycznych.

W poniższym przykładzie pokazano, jak śledzić dodatkowe dane telemetryczne z kontrolera.

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

Aby uzyskać więcej informacji na temat raportowania danych niestandardowych w usłudze Application Insights, zobacz [Odwołanie do interfejsu API metryk niestandardowych usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/).

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>Niektóre szablony programu Visual Studio używane UseApplicationInsights() metody rozszerzenia na IWebHostBuilder, aby włączyć application insights. Czy to użycie jest nadal ważne?

Chociaż metoda `UseApplicationInsights()` rozszerzenia jest nadal obsługiwana, jest oznaczona jako przestarzała w aplikacji Application Insights SDK w wersji 2.8.0. Zostanie on usunięty w następnej głównej wersji SDK. Zalecany sposób, aby włączyć dane `AddApplicationInsightsTelemetry()` telemetryczne usługi Application Insights jest przy użyciu, ponieważ zapewnia przeciążenia do kontrolowania niektórych konfiguracji. Ponadto w ASP.NET aplikacji Core 3.X `services.AddApplicationInsightsTelemetry()` jest jedynym sposobem, aby włączyć wgląd w aplikacje.

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>Wdrażam aplikację ASP.NET Core w aplikacjach sieci Web. Czy nadal należy włączyć rozszerzenie usługi Application Insights z aplikacji sieci Web?

Jeśli pakiet SDK jest zainstalowany w czasie kompilacji, jak pokazano w tym artykule, nie trzeba włączać [rozszerzenia usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps) z portalu usługi App Service. Nawet jeśli rozszerzenie jest zainstalowane, zostanie wycofane, gdy wykryje, że SDK jest już dodany do aplikacji. Jeśli włączysz usługę Application Insights z rozszerzenia, nie trzeba instalować i aktualizować sdk. Ale jeśli włączysz usługę Application Insights, wykonując instrukcje w tym artykule, masz większą elastyczność, ponieważ:

   * Dane telemetryczne usługi Application Insights będą nadal działać w:
       * Wszystkie systemy operacyjne, w tym Windows, Linux i Mac.
       * Wszystkie tryby publikowania, w tym samodzielne lub zależne od struktury.
       * Wszystkie struktury docelowe, w tym pełne .NET Framework.
       * Wszystkie opcje hostingu, w tym aplikacje sieci Web, maszyny wirtualne, linux, kontenery, usługa Azure Kubernetes i hosting nienawiązywania platformy Azure.
       * Wszystkie wersje .NET Core, w tym wersje zapoznawcza.
   * Telemetrii można wyświetlić lokalnie podczas debugowania z programu Visual Studio.
   * Można śledzić dodatkowe dane telemetryczne niestandardowe przy użyciu interfejsu `TrackXXX()` API.
   * Masz pełną kontrolę nad konfiguracją.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Czy mogę włączyć monitorowanie usługi Application Insights przy użyciu narzędzi, takich jak Monitor stanu?

Nie. [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) i [monitor stanu w wersji 2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) obsługują obecnie tylko ASP.NET 4.x.

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Czy usługa Application Insights jest automatycznie włączona dla aplikacji ASP.NET Core 2.0?

Metapakiet `Microsoft.AspNetCore.All` 2.0 zawierał pakiet SDK usługi Application Insights (wersja 2.1.0). Jeśli aplikacja zostanie uruchomione w obszarze debuger programu Visual Studio, visual studio włącza application insights i pokazuje dane telemetryczne lokalnie w samym IDE. Dane telemetryczne nie zostały wysłane do usługi Usługi Application Insights, chyba że określono klucz instrumentacji. Zalecamy wykonanie instrukcji opisanych w tym artykule, aby włączyć usługę Application Insights, nawet dla aplikacji 2.0.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Jeśli uruchomię aplikację w systemie Linux, czy wszystkie funkcje są obsługiwane?

Tak. Obsługa funkcji dla SDK jest taka sama we wszystkich platformach, z następującymi wyjątkami:

* SDK zbiera [liczniki zdarzeń](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters) w systemie Linux, ponieważ [liczniki wydajności](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters) są obsługiwane tylko w systemie Windows. Większość danych jest taka sama.
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>Czy ten SDK jest obsługiwany dla nowych aplikacji szablonów usługi worker service .NET Core 3.X?

Ten SDK `HttpContext`wymaga , a zatem nie działa w żadnych aplikacji innych niż HTTP, w tym .NET Core 3.X aplikacji usługi worker service. Zapoznaj się z [tym](worker-service.md) dokumentem, aby włączyć wgląd w aplikacje w takich aplikacjach przy użyciu nowo wydanego sdk microsoft.ApplicationInsights.WorkerService.

## <a name="open-source-sdk"></a>SDK typu open source

[Przeczytaj i przyczynić się do kodu](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates).

## <a name="video"></a>Film wideo

- Zapoznaj się z tym zewnętrznym wideo krok po kroku, aby [skonfigurować aplikację Application Insights za pomocą programów .NET Core i Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od podstaw.
- Zapoznaj się z tym zewnętrznym wideo krok po kroku, aby [skonfigurować aplikację Application Insights za pomocą programu .NET Core i visual studio code](https://youtu.be/ygGt84GDync) od podstaw.

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z przepływami użytkowników,](../../azure-monitor/app/usage-flows.md) aby dowiedzieć się, jak użytkownicy poruszają się po aplikacji.
* [Skonfiguruj kolekcję migawek,](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) aby zobaczyć stan kodu źródłowego i zmiennych w momencie zalicania wyjątku.
* [Użyj interfejsu API,](../../azure-monitor/app/api-custom-events-metrics.md) aby wysłać własne zdarzenia i metryki, aby uzyskać szczegółowy widok wydajności i użycia aplikacji.
* Użyj [testów dostępności,](../../azure-monitor/app/monitor-web-app-availability.md) aby stale sprawdzać aplikację z całego świata.
* [Iniekcja zależności w rdzeniu ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)
