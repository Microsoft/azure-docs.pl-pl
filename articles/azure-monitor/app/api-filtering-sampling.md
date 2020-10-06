---
title: Filtrowanie i wstępne przetwarzanie w zestawie Application Insights SDK | Microsoft Docs
description: Napisz procesory telemetrii i inicjatory telemetrii dla zestawu SDK w celu filtrowania lub dodawania właściwości do danych przed wysłaniem telemetrii do portalu Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b3ccc3516d5b31f4c119c2d5a2bd11a63dbdc611
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758034"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtrowanie i wstępne przetwarzanie danych telemetrycznych w zestawie Application Insights SDK

Można napisać i skonfigurować wtyczki dla zestawu SDK Application Insights, aby dostosować sposób wzbogacania i przetwarzania danych telemetrycznych przed wysłaniem ich do usługi Application Insights.

* [Próbkowanie](sampling.md) zmniejsza ilość danych telemetrycznych bez wpływu na dane statystyczne. Przechowuje on wspólnie powiązane punkty danych, dzięki czemu można przechodzić między nimi, gdy diagnozowanie problemu. W portalu łączna liczba jest mnożona, aby skompensować próbkowanie.
* Filtrowanie za pomocą procesorów telemetrii umożliwia filtrowanie danych telemetrycznych w zestawie SDK przed ich wysłaniem do serwera. Na przykład można zmniejszyć ilość danych telemetrycznych, wykluczając żądania od robotów. Filtrowanie to bardziej podstawowe podejście do ograniczania ruchu niż próbkowanie. Zapewnia większą kontrolę nad tym, co jest przesyłane, ale ma wpływ na dane statystyczne. Na przykład można odfiltrować wszystkie pomyślne żądania.
* [Inicjatory telemetrii dodają lub modyfikują właściwości](#add-properties) do wszelkich danych telemetrycznych wysyłanych z aplikacji, które obejmują dane telemetryczne z modułów standardowych. Można na przykład dodać wartości obliczeniowe lub numery wersji, według których mają być filtrowane dane w portalu.
* [Interfejs API zestawu SDK](./api-custom-events-metrics.md) służy do wysyłania niestandardowych zdarzeń i metryk.

Przed rozpoczęciem:

* Zainstaluj odpowiedni zestaw SDK dla swojej aplikacji: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [non http/Worker dla platformy .NET/.NET Core](worker-service.md)lub [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtrowanie

Ta technika zapewnia bezpośrednią kontrolę nad tym, co jest dołączone lub wykluczone ze strumienia telemetrii. Filtrowanie może służyć do porzucania elementów telemetrycznych z wysyłania do Application Insights. Można użyć filtrowania w połączeniu z próbką lub oddzielnie.

Aby odfiltrować dane telemetryczne, napisz procesor telemetrii i zarejestruj go w usłudze `TelemetryConfiguration` . Wszystkie dane telemetryczne przechodzą przez procesor. Możesz wybrać opcję porzucenia ze strumienia lub przekazać ją do następnego procesora w łańcuchu. Dane telemetryczne z modułów standardowych, takich jak moduł zbierający żądania HTTP i moduł zbierający zależność, są uwzględniane przez śledzone dane telemetryczne. Na przykład można odfiltrować dane telemetryczne dotyczące żądań z robotów lub pomyślnych wywołań zależności.

> [!WARNING]
> Filtrowanie danych telemetrycznych wysyłanych z zestawu SDK przy użyciu procesorów może pochylić statystyki widoczne w portalu i utrudniać wykonywanie pokrewnych elementów.
>
> Zamiast tego należy rozważyć użycie [próbkowania](./sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Tworzenie procesora telemetrii (C#)

1. Aby utworzyć filtr, zaimplementuj `ITelemetryProcessor` .

    Procesory telemetrii konstruują łańcuch przetwarzania. Podczas tworzenia wystąpienia procesora telemetrii otrzymujesz odwołanie do następnego procesora w łańcuchu. Gdy punkt danych telemetrii jest przesyłany do metody procesu, wykonuje jego działanie, a następnie wywołuje (lub nie wywołuje) następnego procesora telemetrii w łańcuchu.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Dodaj procesor.

**Aplikacje** ASP.NET

Wstaw następujący fragment kodu w ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Można przekazać wartości ciągu z pliku. config, podając publiczne właściwości o nazwie w klasie.

> [!WARNING]
> Należy zachować ostrożność dopasowania nazwy typu i wszystkich nazw właściwości w pliku. config do nazw klas i właściwości w kodzie. Jeśli plik. config odwołuje się do nieistniejącego typu lub właściwości, zestaw SDK może w trybie dyskretnym nie mógł wysyłać żadnych danych telemetrycznych.
>

Alternatywnie można zainicjować filtr w kodzie. W odpowiedniej klasie inicjującej, na przykład AppStart w `Global.asax.cs` , włóż procesor do łańcucha:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Klienci telemetrii utworzeni po tym punkcie będą korzystać z procesorów.

ASP.NET **podstawowe/aplikacje usług roboczych**

> [!NOTE]
> Dodawanie procesora przez użycie `ApplicationInsights.config` lub `TelemetryConfiguration.Active` nieprawidłowe dla aplikacji ASP.NET Core lub użycie zestawu SDK Microsoft. ApplicationInsights. WorkerService.

W przypadku aplikacji pisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) lub [WorkerService](worker-service.md#adding-telemetry-processors)Dodawanie nowego procesora telemetrii odbywa się przy użyciu `AddApplicationInsightsTelemetryProcessor` metody rozszerzenia na `IServiceCollection` , jak pokazano. Ta metoda jest wywoływana w `ConfigureServices` metodzie `Startup.cs` klasy.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Przykładowe filtry

#### <a name="synthetic-requests"></a>Żądania syntetyczne

Odfiltruj testy botów i sieci Web. Mimo że Eksplorator metryk umożliwia odfiltrowanie źródeł syntetycznych, ta opcja zmniejsza ilość ruchu i rozmiaru pozyskiwania przez filtrowanie ich w samym zestawie SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Niepowodzenie uwierzytelniania

Odfiltruj żądania z odpowiedzią "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltruj szybkie zdalne wywołania zależności

Jeśli chcesz zdiagnozować tylko wywołania, które są powolne, odfiltruj szybkie.

> [!NOTE]
> To filtrowanie spowoduje pochylenie statystyk widocznych w portalu.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnozuj problemy zależności

W [tym blogu](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) opisano projekt służący do diagnozowania problemów z zależnościami przez automatyczne wysyłanie zwykłych poleceń ping do zależności.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplikacje sieci Web w języku JavaScript

**Filtrowanie za pomocą ITelemetryInitializer**

1. Utwórz funkcję wywołania zwrotnego inicjatora telemetrii. Funkcja wywołania zwrotnego przyjmuje `ITelemetryItem` jako parametr, który jest zdarzeniem, które jest przetwarzane. Powrót `false` z tego wywołania zwrotnego powoduje odfiltrowanie elementu telemetrii.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Dodaj wywołanie zwrotne inicjatora telemetrii:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Dodaj/Modyfikuj właściwości: ITelemetryInitializer

Za pomocą inicjatorów telemetrii można wzbogacać dane telemetryczne o dodatkowe informacje lub przesłaniać właściwości telemetryczne ustawione przez standardowe moduły telemetrii.

Na przykład Application Insights pakietu sieci Web zbiera dane telemetryczne o żądaniach HTTP. Domyślnie flaguje jako nieudane wszystkie żądania z kodem odpowiedzi >= 400. Jeśli jednak chcesz traktować 400 jako pomyślne, możesz podać inicjator telemetrii, który ustawia właściwość Success.

W przypadku dostarczania inicjatora telemetrii jest on wywoływany za każdym razem, gdy wywoływana jest jakakolwiek metoda Track * (). Obejmuje to `Track()` metody wywoływane przez standardowe moduły telemetrii. Zgodnie z Konwencją te moduły nie ustawiają żadnej właściwości, która została już ustawiona przez inicjator. Inicjatory telemetrii są wywoływane przed wywołaniem procesorów telemetrycznych. W związku z tym wszystkie wzbogacania wykonywane przez inicjatorów są widoczne dla procesorów.

**Definiowanie inicjatora**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **Apps: Załaduj inicjator**

W ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Alternatywnie można utworzyć wystąpienie inicjatora w kodzie, na przykład w Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Zobacz więcej [tego przykładu](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

ASP.NET **podstawowe/aplikacje usługi Worker: Załaduj inicjator**

> [!NOTE]
> Dodawanie inicjatora przy użyciu `ApplicationInsights.config` lub `TelemetryConfiguration.Active` nie jest prawidłowe dla aplikacji ASP.NET Core lub jeśli używasz zestawu SDK Microsoft. ApplicationInsights. WorkerService.

W przypadku aplikacji pisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) lub [WorkerService](worker-service.md#adding-telemetryinitializers)Dodawanie nowego inicjatora telemetrii odbywa się przez dodanie go do kontenera iniekcji zależności, jak pokazano. Jest to wykonywane w `Startup.ConfigureServices` metodzie.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inicjatory telemetrii JavaScript
*JavaScript*

Wstaw inicjatora telemetrii bezpośrednio po kodzie inicjalizacji uzyskanym z portalu:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Aby uzyskać podsumowanie nieniestandardowych właściwości dostępnych dla elementu telemetrii, zobacz [Application Insights Export Data Model](./export-data-model.md).

Możesz dodać dowolną liczbę inicjatorów. Są one wywoływane w kolejności, w jakiej zostały dodane.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus procesory telemetryczne języka Python

Aby przetwarzać dane telemetryczne przed ich wyeksportowaniem, procesory telemetryczne w OpenCensus Python są po prostu wywoływana funkcjami Funkcja wywołania zwrotnego musi akceptować typ danych [koperty](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) jako parametr. Aby odfiltrować dane telemetryczne z eksportu, upewnij się, że funkcja wywołania zwrotnego zwróci wartość `False` . Schemat dla Azure Monitor typów danych można zobaczyć na kopercie [w serwisie GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Możesz zmodyfikować `cloud_RoleName` , zmieniając `ai.cloud.role` atrybut w `tags` polu.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Możesz dodać dowolną liczbę procesorów. Są one wywoływane w kolejności, w jakiej zostały dodane. Jeśli jeden procesor zgłasza wyjątek, nie ma to wpływu na następujące procesory.

### <a name="example-telemetryinitializers"></a>Przykład TelemetryInitializers

#### <a name="add-a-custom-property"></a>Dodaj właściwość niestandardową

Poniższy przykładowy inicjator dodaje właściwość niestandardową do wszystkich śledzonych danych telemetrycznych.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Dodaj nazwę roli w chmurze

Poniższy przykładowy inicjator ustawia nazwę roli chmury dla każdej śledzonej telemetrii.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Dodaj informacje z obiektu HttpContext

Poniższy przykładowy inicjator odczytuje dane z [`HttpContext`](/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) i dołącza je do `RequestTelemetry` wystąpienia. `IHttpContextAccessor`Jest automatycznie udostępniany przez iniekcję zależności konstruktora.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor i ITelemetryInitializer

Jaka jest różnica między procesorami telemetrii i inicjatorami telemetrii?

* Niektóre z nich nakładają się na to, co możesz zrobić z nimi. Oba te elementy mogą służyć do dodawania lub modyfikowania właściwości telemetrii, chociaż zalecamy korzystanie z inicjatorów w tym celu.
* Inicjatory telemetrii są zawsze uruchamiane przed procesorami telemetrii.
* Inicjatory telemetrii mogą być wywoływane więcej niż raz. Według Konwencji nie ustawili żadnej właściwości, która została już ustawiona.
* Procesory telemetrii umożliwiają całkowite zastępowanie lub odrzucanie elementu telemetrii.
* Wszystkie zarejestrowane inicjatory telemetrii są gwarantowane dla każdego elementu telemetrii. W przypadku procesorów telemetrii zestaw SDK gwarantuje wywołanie pierwszego procesora telemetrii. Bez względu na to, czy pozostały zakres procesorów jest wywoływany przez poprzednie procesory telemetrii.
* Użyj inicjatorów telemetrii, aby wzbogacić dane telemetryczne z dodatkowymi właściwościami, lub Zastąp istniejący. Użyj procesora telemetrii do odfiltrowania danych telemetrycznych.

## <a name="troubleshoot-applicationinsightsconfig"></a>Rozwiązywanie problemów ApplicationInsights.config

* Upewnij się, że w pełni kwalifikowana nazwa typu i nazwa zestawu są poprawne.
* Upewnij się, że plik applicationinsights.config znajduje się w katalogu wyjściowym i zawiera wszystkie ostatnie zmiany.

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Przegląd interfejsu API](./api-custom-events-metrics.md)
* [Odwołanie ASP.NET](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>Kod zestawu SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Następne kroki
* [Wyszukaj zdarzenia i dzienniki](./diagnostic-search.md)
* [sond](./sampling.md)
* [Rozwiązywanie problemów](../faq.md)

