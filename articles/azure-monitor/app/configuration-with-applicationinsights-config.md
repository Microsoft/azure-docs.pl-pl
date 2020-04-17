---
title: Odwołanie do aplikacjiInsights.config — Azure | Dokumenty firmy Microsoft
description: Włącz lub wyłącz moduły zbierania danych i dodaj liczniki wydajności i inne parametry.
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: olegan
ms.openlocfilehash: 3375c24739da8663aa6a40deeb53e02e65d1f9bf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537563"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Konfigurowanie zestawu SDK usługi Application Insights za pomocą pliku ApplicationInsights.config lub xml
Pakiet SDK usługi Application Insights .NET składa się z wielu pakietów NuGet. [Pakiet podstawowy](https://www.nuget.org/packages/Microsoft.ApplicationInsights) udostępnia interfejs API do wysyłania danych telemetrycznych do usługi Application Insights. [Dodatkowe pakiety](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) zapewniają *moduły* telemetryczne i *inicjatory* do automatycznego śledzenia danych telemetrycznych z aplikacji i jej kontekstu. Dostosowując plik konfiguracyjny, można włączyć lub wyłączyć moduły telemetryczne i inicjatory oraz ustawić parametry dla niektórych z nich.

Plik konfiguracyjny ma nazwę `ApplicationInsights.config` lub `ApplicationInsights.xml`, w zależności od typu aplikacji. Jest on automatycznie dodawany do projektu po [zainstalowaniu większości wersji SDK][start]. Domyślnie podczas korzystania z zautomatyzowanego środowiska z projektów szablonów programu Visual Studio, które obsługują **dodawanie > dane telemetryczne usługi Application Insights,** plik ApplicationInsights.config jest tworzony w folderze głównym projektu i po spełnieniu jest kopiowany do folderu bin. Jest również dodawany do aplikacji sieci web przez [Monitor stanu na serwerze usług IIS][redfield]. Plik konfiguracji jest ignorowany, jeśli używane jest [rozszerzenie witryny sieci Web](azure-web-apps.md) lub rozszerzenie platformy Azure dla maszyny [wirtualnej platformy Azure i zestaw skalowania maszyny wirtualnej.](azure-vm-vmss-apps.md)

Nie ma równoważnego pliku do sterowania [sdk na stronie internetowej][client].

W tym dokumencie opisano sekcje widoczne w pliku konfiguracyjnym, jak kontrolują składniki SDK i które pakiety NuGet ładować te składniki.

> [!NOTE]
> ApplicationInsights.config i .xml instrukcje nie mają zastosowania do .NET Core SDK. Aby skonfigurować aplikacje .NET Core, postępuj zgodnie z [tym](../../azure-monitor/app/asp-net-core.md) przewodnikiem.

## <a name="telemetry-modules-aspnet"></a>Moduły telemetryczne (ASP.NET)
Każdy moduł telemetryczny zbiera określony typ danych i używa podstawowego interfejsu API do wysyłania danych. Moduły są instalowane przez różne pakiety NuGet, które również dodać wymagane wiersze do pliku .config.

Istnieje węzeł w pliku konfiguracyjnym dla każdego modułu. Aby wyłączyć moduł, usuń węzeł lub skomentuj go.

### <a name="dependency-tracking"></a>Śledzenie zależności
[Śledzenie zależności](../../azure-monitor/app/asp-net-dependencies.md) zbiera dane telemetryczne dotyczące połączeń aplikacji do baz danych i usług zewnętrznych i baz danych. Aby umożliwić pracę tego modułu na serwerze usług IIS, należy [zainstalować Monitor stanu][redfield].

Można również napisać własny kod śledzenia zależności za pomocą [interfejsu API TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Pakiet Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet.

Zależności mogą być zbierane automatycznie bez modyfikowania kodu przy użyciu agenta (bez kodu) dołączyć. Aby używać go w aplikacjach sieci Web platformy Azure, włącz [rozszerzenie usługi Application Insights](azure-web-apps.md). Aby używać go w usłudze Azure VM lub Azure virtual machine scale set włącz [rozszerzenie monitorowania aplikacji dla maszyny wirtualnej i zestaw skalowania maszyny wirtualnej.](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Kolektor wydajności
[Zbiera liczniki wydajności systemu,](../../azure-monitor/app/performance-counters.md) takie jak procesor, pamięć i obciążenie sieciowe z instalacji usług IIS. Można określić liczniki do zebrania, w tym liczniki wydajności, które zostały skonfigurowane samodzielnie.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Pakiet Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet.

### <a name="application-insights-diagnostics-telemetry"></a>Dane telemetryczne diagnostyki usługi Application Insights
Raporty `DiagnosticsTelemetryModule` błędy w samym kodzie instrumentacji usługi Application Insights. Na przykład jeśli kod nie może uzyskać `ITelemetryInitializer` dostępu do liczników wydajności lub jeśli zgłasza wyjątek. Śledzenie danych telemetrycznych śledzonych przez ten moduł pojawia się w [wyszukiwaniu diagnostycznym][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Tryb dewelopera
`DeveloperModeWithDebuggerAttachedTelemetryModule`wywiązuje `TelemetryChannel` się z usługi Application Insights do natychmiastowego wysyłania danych, jeden element telemetrii naraz, gdy debuger jest dołączony do procesu aplikacji. Zmniejsza to czas między momentem, gdy aplikacja śledzi dane telemetryczne i gdy pojawi się w portalu usługi Application Insights. Powoduje znaczne obciążenie przepustowości procesora i sieci.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Usługa Application Insights dla systemu Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pakiet NuGet

### <a name="web-request-tracking"></a>Śledzenie żądań sieci Web
Raportuje [czas odpowiedzi i kod wyników](../../azure-monitor/app/asp-net.md) żądań HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* Pakiet [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet

### <a name="exception-tracking"></a>Śledzenie wyjątków
`ExceptionTrackingTelemetryModule`śledzi nieobsługiwalne wyjątki w aplikacji internetowej. Zobacz [Błędy i wyjątki][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* Pakiet [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`- śledzi [niezaobserwowane wyjątki zadań](https://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`- śledzi nieobsługiwałem wyjątków dla ról procesu roboczego, usług systemu Windows i aplikacji konsoli.
* [Usługa Application Insights dla systemu Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pakiet NuGet.

### <a name="eventsource-tracking"></a>Śledzenie źródła zdarzeń
`EventSourceTelemetryModule`umożliwia skonfigurowanie zdarzeń EventSource, które mają być wysyłane do usługi Application Insights jako ślady. Aby uzyskać informacje na temat śledzenia zdarzeń eventsource, zobacz [Korzystanie ze zdarzeń EventSource](../../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Śledzenie zdarzeń ETW
`EtwCollectorTelemetryModule`umożliwia skonfigurowanie zdarzeń od dostawców ETW, które mają być wysyłane do usługi Application Insights jako ślady. Aby uzyskać informacje na temat śledzenia zdarzeń ETW, zobacz [Korzystanie ze zdarzeń ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Statystyki aplikacji firmy Microsoft.Application
Pakiet Microsoft.ApplicationInsights udostępnia [podstawowy interfejs API](https://msdn.microsoft.com/library/mt420197.aspx) zestawu SDK. Inne moduły telemetryczne używają tego, a także można [go użyć do zdefiniowania własnej telemetrii](../../azure-monitor/app/api-custom-events-metrics.md).

* Brak wpisu w aplikacjiInsights.config.
* [Pakiet Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet. Jeśli po prostu zainstalujesz ten NuGet, nie zostanie wygenerowany żaden plik .config.

## <a name="telemetry-channel"></a>Kanał telemetrii
[Kanał telemetrii](telemetry-channels.md) zarządza buforowania i transmisji danych telemetrycznych do usługi Application Insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`jest domyślnym kanałem dla aplikacji internetowych. Buforuje dane w pamięci i wykorzystuje mechanizmy ponawiania prób i magazynu dysku lokalnego dla bardziej niezawodne dostarczanie danych telemetrycznych.
* `Microsoft.ApplicationInsights.InMemoryChannel`jest lekkim kanałem telemetrycznym, który jest używany, jeśli żaden inny kanał nie jest skonfigurowany. 

## <a name="telemetry-initializers-aspnet"></a>Inicjatory telemetrii (ASP.NET)
Inicjatory telemetrii ustawiają właściwości kontekstu, które są wysyłane wraz z każdym elementem telemetrii.

Można [napisać własne inicjatorów,](../../azure-monitor/app/api-filtering-sampling.md#add-properties) aby ustawić właściwości kontekstu.

Standardowe inicjatory są ustawiane przez pakiety WindowsServer NuGet w sieci Web lub WindowsServer:

* `AccountIdTelemetryInitializer`ustawia właściwość AccountId.
* `AuthenticatedUserIdTelemetryInitializer`ustawia właściwość AuthenticatedUserId ustawioną przez zestaw javascript SDK.
* `AzureRoleEnvironmentTelemetryInitializer`aktualizuje `RoleName` `RoleInstance` i właściwości `Device` kontekstu dla wszystkich elementów telemetrii z informacjami wyodrębnione ze środowiska wykonawczego platformy Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer`aktualizuje `Version` właściwość `Component` kontekstu dla wszystkich elementów telemetrii z wartością wyodrębnione z pliku wyprodukowanego `BuildInfo.config` przez MS Build.
* `ClientIpHeaderTelemetryInitializer`aktualizuje `Ip` właściwość kontekstu `Location` wszystkich elementów `X-Forwarded-For` telemetrii na podstawie nagłówka HTTP żądania.
* `DeviceTelemetryInitializer`aktualizuje następujące właściwości `Device` kontekstu dla wszystkich elementów telemetrii.
  * `Type`jest ustawiona na "PC"
  * `Id`jest ustawiona na nazwę domeny komputera, na którym jest uruchomiona aplikacja sieci web.
  * `OemName`jest ustawiona na wartość `Win32_ComputerSystem.Manufacturer` wyodrębniona z pola przy użyciu usługi WMI.
  * `Model`jest ustawiona na wartość `Win32_ComputerSystem.Model` wyodrębniona z pola przy użyciu usługi WMI.
  * `NetworkType`jest ustawiona na wartość `NetworkInterface`wyodrębniona z pliku .
  * `Language`jest ustawiona na `CurrentCulture`nazwę .
* `DomainNameRoleInstanceTelemetryInitializer`aktualizuje `RoleInstance` właściwość `Device` kontekstu dla wszystkich elementów telemetrii z nazwą domeny komputera, na którym jest uruchomiona aplikacja sieci web.
* `OperationNameTelemetryInitializer`aktualizuje `Name` właściwość `RequestTelemetry` i `Name` właściwość `Operation` kontekstu wszystkich elementów telemetrii na podstawie metody HTTP, a także nazwy ASP.NET kontrolera MVC i akcji wywoływanej w celu przetworzenia żądania.
* `OperationIdTelemetryInitializer`lub `OperationCorrelationTelemetryInitializer` aktualizuje `Operation.Id` właściwość kontekstu wszystkich elementów telemetrycznych śledzonych podczas obsługi żądania za pomocą automatycznie wygenerowanego `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`aktualizuje `Id` właściwość `Session` kontekstu dla wszystkich elementów telemetrii o wartości wyodrębnionej `ai_session` z pliku cookie wygenerowanego przez kod instrumentacji JavaScript ApplicationInsights uruchomiony w przeglądarce użytkownika.
* `SyntheticTelemetryInitializer`lub `SyntheticUserAgentTelemetryInitializer` `User`aktualizuje `Session`właściwości `Operation` , i kontekstów wszystkich elementów telemetrii śledzonych podczas obsługi żądania ze źródła syntetycznego, takiego jak test dostępności lub bot wyszukiwarki. Domyślnie [Eksplorator metryk](../../azure-monitor/platform/metrics-charts.md) nie wyświetla syntetycznych danych telemetrycznych.

    Zestaw `<Filters>` identyfikujący właściwości żądań.
* `UserTelemetryInitializer`aktualizuje `Id` `AcquisitionDate` i właściwości `User` kontekstu dla wszystkich elementów telemetrii z wartościami wyodrębnianych z `ai_user` pliku cookie wygenerowanego przez kod instrumentacji JavaScript aplikacji działający w przeglądarce użytkownika.
* `WebTestTelemetryInitializer`ustawia identyfikator użytkownika, identyfikator sesji i syntetyczne właściwości źródłowe dla żądań HTTP, które pochodzą z [testów dostępności.](../../azure-monitor/app/monitor-web-app-availability.md)
  Zestaw `<Filters>` identyfikujący właściwości żądań.

W przypadku aplikacji platformy .NET uruchomionych `Microsoft.ApplicationInsights.ServiceFabric` w sieci szkieletowej usług można dołączyć pakiet NuGet. Ten pakiet `FabricTelemetryInitializer`zawiera , który dodaje właściwości sieci szkieletowej usług do elementów telemetrii. Aby uzyskać więcej informacji, zobacz [stronę GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) o właściwościach dodanych przez ten pakiet NuGet.

## <a name="telemetry-processors-aspnet"></a>Procesory telemetryczne (ASP.NET)
Procesory telemetryczne można filtrować i modyfikować każdy element telemetrii tuż przed wysłaniem go z SDK do portalu.

Można [napisać własne procesory telemetryczne](../../azure-monitor/app/api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Adaptacyjne próbkowanie procesora telemetrii (od 2.0.0-beta3)
Ta opcja jest domyślnie włączona. Jeśli aplikacja wysyła dużo danych telemetrycznych, ten procesor usuwa niektóre z nich.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

Parametr zawiera obiekt docelowy, który algorytm próbuje osiągnąć. Każde wystąpienie zestawu SDK działa niezależnie, więc jeśli serwer jest klastrem kilku maszyn, rzeczywista ilość danych telemetrycznych zostanie odpowiednio pomnożona.

[Dowiedz się więcej o próbkowaniu](../../azure-monitor/app/sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Próbkowanie danych telemetrycznych o stałej szybkości (od 2.0.0-beta1)
Istnieje również standardowy [procesor telemetrii próbkowania](../../azure-monitor/app/api-filtering-sampling.md) (od 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey (Narzędzie instrumentacyjne)
Określa to zasób usługi Application Insights, w którym są wyświetlane dane. Zazwyczaj tworzysz oddzielny zasób z osobnym kluczem dla każdej aplikacji.

Jeśli chcesz dynamicznie ustawić klucz — na przykład jeśli chcesz wysłać wyniki z aplikacji do różnych zasobów — możesz pominąć klucz z pliku konfiguracji i zamiast tego ustawić go w kodzie.

Aby ustawić klucz dla wszystkich wystąpień TelemetryClient, w tym standardowych modułów telemetrycznych. Należy to zrobić w metodzie inicjowania, takich jak global.aspx.cs w usłudze ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Jeśli chcesz tylko wysłać określony zestaw zdarzeń do innego zasobu, możesz ustawić klucz dla określonego telemetryclient:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Aby uzyskać nowy klucz, [utwórz nowy zasób w portalu usługi Application Insights][new].



## <a name="applicationid-provider"></a>Dostawca identyfikatora aplikacji

_Dostępne od wersji 2.6.0_

Celem tego dostawcy jest wyszukiwanie identyfikatora aplikacji na podstawie klucza instrumentacji. Identyfikator aplikacji znajduje się w RequestTelemetry i DependencyTelemetry i służy do określenia korelacji w portalu.

Jest to możliwe `TelemetryConfiguration.ApplicationIdProvider` po ustawieniu w kodzie lub w konfiguracji.

### <a name="interface-iapplicationidprovider"></a>Interfejs: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Udostępniamy dwie implementacje w [microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) `ApplicationInsightsApplicationIdProvider` `DictionaryApplicationIdProvider`sdk: i .

### <a name="applicationinsightsapplicationidprovider"></a>AplikacjaInsightsApplicationIdProvider

Jest to otoka wokół naszego interfejsu API profilu. Będzie ograniczać żądania i wyniki pamięci podręcznej.

Ten dostawca jest dodawany do pliku konfiguracyjnego podczas instalowania systemu [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) lub [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Ta klasa ma `ProfileQueryEndpoint`opcjonalną właściwość .
Domyślnie jest to `https://dc.services.visualstudio.com/api/profiles/{0}/appId`ustawienie .
Jeśli chcesz skonfigurować serwer proxy dla tej konfiguracji, zalecamy proxy adres podstawowy i{0}w tym "/api/profiles/ /appId". Należy zauważyć, że '{0}' jest zastępowany w czasie wykonywania na żądanie kluczem instrumentacji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pośrednictwem pliku ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pomocą kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Jest to dostawca statyczny, który będzie polegać na skonfigurowanych parach klucza instrumentacji / identyfikatora aplikacji.

Ta klasa ma `Defined`właściwość , która jest słownik<ciąg,ciąg> klucza instrumentacji do par identyfikatora aplikacji.

Ta klasa ma `Next` opcjonalną właściwość, która może służyć do konfigurowania innego dostawcy do użycia, gdy wymagany jest klucz instrumentacji, który nie istnieje w konfiguracji.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Przykładowa konfiguracja za pośrednictwem pliku ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Przykładowa konfiguracja za pomocą kodu:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o interfejsie API][api].

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: ../../azure-monitor/app/asp-net-exceptions.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[new]: ../../azure-monitor/app/create-new-resource.md 
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
