---
title: Application Insights platformy Azure dla aplikacji konsolowych | Microsoft Docs
description: Monitoruj aplikacje sieci Web pod kątem dostępności, wydajności i użycia.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: aa39a1eca04621fc4db75f755402d3679403e814
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96920603"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights aplikacji konsolowych .NET

[Application Insights](./app-insights-overview.md) umożliwia monitorowanie aplikacji sieci Web pod kątem dostępności, wydajności i użycia.

Potrzebna jest subskrypcja z [Microsoft Azure](https://azure.com). Zaloguj się przy użyciu konto Microsoft, który może być używany w przypadku systemu Windows, usługi Xbox Live lub innych usług firmy Microsoft w chmurze. Twój zespół może mieć subskrypcję organizacyjną na platformie Azure: poproszenie właściciela o dodanie Cię do niego przy użyciu konto Microsoft.

> [!NOTE]
> *Zdecydowanie zaleca* się korzystanie z pakietu [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) i powiązanych instrukcji z tego [miejsca](./worker-service.md) w przypadku wszystkich aplikacji konsolowych. Ten pakiet jest przeznaczony dla tego pakietu [`NetStandard2.0`](/dotnet/standard/net-standard) i dlatego może być używany w środowisku .NET Core 2,1 lub nowszym oraz .NET Framework 4.7.2 lub wyższy.

## <a name="getting-started"></a>Wprowadzenie

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia parametrów połączenia zamiast kluczy Instrumentacji. [Parametry połączenia](./sdk-connection-string.md?tabs=net) identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.

* W witrynie [Azure Portal](https://portal.azure.com)[utwórz zasób usługi Application Insights](./create-new-resource.md). W obszarze Typ aplikacji wybierz pozycję **Ogólne**.
* Wykonaj kopię klucza instrumentacji. Znajdź klucz na liście rozwijanej **podstawowe** informacje o utworzonym nowym zasobie.
* Zainstaluj najnowszy pakiet [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) .
* Ustaw klucz Instrumentacji w kodzie przed śledzeniem danych telemetrycznych (lub Ustaw zmienną środowiskową APPINSIGHTS_INSTRUMENTATIONKEY). Następnie powinno być możliwe ręczne śledzenie danych telemetrycznych i wyświetlanie ich w Azure Portal

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> Dane telemetryczne nie są wysyłane natychmiast. Elementy telemetrii są wsadowe i wysyłane przez zestaw ApplicationInsights SDK. W aplikacjach konsolowych, które opuszczają bezpośrednio po wywołaniu `Track()` metody, dane telemetryczne mogą nie być wysyłane, chyba że `Flush()` `Sleep` / `Delay` zostaną wykonane przed zakończeniem działania aplikacji, jak pokazano w [pełnym przykładzie](#full-example) w dalszej części tego artykułu. `Sleep` nie jest wymagane, jeśli używasz programu `InMemoryChannel` . Istnieje aktywny problem dotyczący potrzeb `Sleep` , który jest śledzony w tym miejscu: [ApplicationInsights-dotnet/Issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* Zainstaluj najnowszą wersję pakietu [Microsoft. ApplicationInsights. DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) — automatycznie śledzi protokół http, SQL lub inne zewnętrzne wywołania zależności.

Możesz inicjować i konfigurować Application Insights z kodu lub przy użyciu `ApplicationInsights.config` pliku. Upewnij się, że inicjalizacja odbywa się tak wcześnie, jak to możliwe. 

> [!NOTE]
> Instrukcje odwołujące się do **ApplicationInsights.config** mają zastosowanie tylko do aplikacji, które są przeznaczone dla .NET Framework i nie mają zastosowania do aplikacji .NET Core.

### <a name="using-config-file"></a>Korzystanie z pliku konfiguracji

Domyślnie zestaw Application Insights SDK szuka `ApplicationInsights.config` pliku w katalogu roboczym, gdy `TelemetryConfiguration` jest tworzony

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Możesz również określić ścieżkę do pliku konfiguracji.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Aby uzyskać więcej informacji, zobacz [Dokumentacja pliku konfiguracji](configuration-with-applicationinsights-config.md).

Pełny przykład pliku konfiguracji można uzyskać, instalując najnowszą wersję pakietu [Microsoft. ApplicationInsights. WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) . Poniżej znajduje się **minimalna** konfiguracja kolekcji zależności, która jest równoważna z przykładem kodu.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Konfigurowanie zbierania danych telemetrycznych z kodu
> [!NOTE]
> Odczytywanie pliku konfiguracji nie jest obsługiwane w programie .NET Core. Możesz rozważyć użycie [zestawu SDK Application Insights dla ASP.NET Core](./asp-net-core.md)

* Podczas uruchamiania aplikacji Utwórz i skonfiguruj `DependencyTrackingTelemetryModule` wystąpienie — muszą one być singleton i muszą zostać zachowane w okresie istnienia aplikacji.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Dodaj typowe inicjatory telemetrii

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Jeśli utworzono konfigurację z zwykłym `TelemetryConfiguration()` konstruktorem, należy również włączyć obsługę korelacji. **Nie jest to konieczne** , jeśli odczytasz konfigurację z pliku, użyto `TelemetryConfiguration.CreateDefault()` lub `TelemetryConfiguration.Active` .

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Możesz również zainstalować i zainicjować moduł zbierający licznik wydajności, jak opisano [tutaj](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Pełny przykład

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Następne kroki
* [Monitoruj zależności](./asp-net-dependencies.md) , aby zobaczyć, czy REST, SQL lub inne zasoby zewnętrzne spowalniają pracę.
* [Użyj interfejsu API,](./api-custom-events-metrics.md) aby wysyłać własne zdarzenia i metryki w celu uzyskania bardziej szczegółowego widoku wydajności i użycia aplikacji.

