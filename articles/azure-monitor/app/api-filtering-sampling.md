---
title: Filtrowanie i przetwarzanie wstępne w sdk usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Zapisz procesory telemetryczne i inicjatory telemetrii dla SDK, aby filtrować lub dodawać właściwości do danych przed wysłaniem danych telemetrycznych do portalu usługi Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8b81849726ad546a24ce1bb56a139b384eb54c42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405364"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrowanie i wstępne przetwarzanie danych telemetrycznych w sedku usługi Application Insights

Można pisać i konfigurować dodatki dla SDK usługi Application Insights, aby dostosować sposób wzbogacania i przetwarzania danych telemetrycznych przed wysłaniem ich do usługi Application Insights.

* [Próbkowanie](sampling.md) zmniejsza ilość danych telemetrycznych bez wpływu na statystyki. Przechowuje razem powiązane punkty danych, dzięki czemu można poruszać się między nimi podczas diagnozowania problemu. W portalu całkowita liczba jest mnożona w celu skompensowania próbkowania.
* Filtrowanie za pomocą procesorów telemetrycznych umożliwia odfiltrowanie danych telemetrycznych w sdk przed wysłaniem go do serwera. Na przykład można zmniejszyć ilość danych telemetrycznych, wykluczając żądania z robotów. Filtrowanie jest bardziej podstawowym podejściem do zmniejszania ruchu niż próbkowanie. Pozwala ci na większą kontrolę nad tym, co jest przesyłane, ale musisz mieć świadomość, że ma to wpływ na statystyki - na przykład, jeśli odfiltrowujesz wszystkie pomyślne żądania.
* [Inicjatory telemetrii dodają lub modyfikują właściwości](#add-properties) do dowolnej telemetrii wysyłanej z aplikacji, w tym dane telemetryczne z modułów standardowych. Na przykład można dodać wartości obliczeniowe; lub numery wersji, za pomocą których mają być filtrowane dane w portalu.
* [Interfejs API SDK](../../azure-monitor/app/api-custom-events-metrics.md) służy do wysyłania niestandardowych zdarzeń i metryk.

Przed rozpoczęciem:

* Zainstaluj odpowiedni pakiet SDK dla aplikacji: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Non HTTP/Worker dla .NET/.NET Core](worker-service.md)lub [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtrowanie

Ta technika zapewnia bezpośrednią kontrolę nad tym, co jest zawarte lub wykluczone ze strumienia telemetrii. Filtrowanie może służyć do upuszczania elementów telemetrycznych z wysyłane do usługi Application Insights. Można go używać w połączeniu z próbkowania lub oddzielnie.

Aby filtrować dane telemetryczne, należy napisać `TelemetryConfiguration`procesor telemetrii i zarejestrować go w pliku . Wszystkie dane telemetryczne przechodzi przez procesor i można wybrać, aby upuścić go ze strumienia lub przekazać go do następnego procesora w łańcuchu. Obejmuje to dane telemetryczne ze standardowych modułów, takich jak moduł zbierający żądania HTTP i moduł zbierający zależności, oraz dane telemetryczne, które zostały śledzone samodzielnie. Można na przykład odfiltrować dane telemetryczne dotyczące żądań z robotów lub pomyślne wywołania zależności.

> [!WARNING]
> Filtrowanie danych telemetrycznych wysyłanych z sdk przy użyciu procesorów można wypaczyć statystyki, które są widoczne w portalu i utrudnić śledzenie powiązanych elementów.
>
> Zamiast tego należy rozważyć użycie [pobierania próbek](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Tworzenie procesora telemetrii (C#)

1. Aby utworzyć filtr, `ITelemetryProcessor`należy zaimplementować plik .

    Należy zauważyć, że procesory telemetryczne konstruować łańcuch przetwarzania. Podczas tworzenia wystąpienia procesora telemetrii, są podane odwołanie do następnego procesora w łańcuchu. Gdy punkt danych telemetrii jest przekazywany do Process metody, wykonuje swoją pracę, a następnie wywołuje (lub nie) następny procesor telemetrii w łańcuchu.

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

**aplikacje ASP.NET** Wstaw ten fragment kodu w pliku ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Wartości ciągów można przekazać z pliku .config, udostępniając publiczne właściwości nazwane w klasie.

> [!WARNING]
> Należy pamiętać, aby dopasować nazwę typu i nazwy właściwości w pliku .config do klasy i nazwy właściwości w kodzie. Jeśli plik .config odwołuje się do nieistniejącego typu lub właściwości, sdk sdk może po cichu nie wysłać żadnych danych telemetrycznych.
>

**Alternatywnie** można zainicjować filtr w kodzie. W odpowiedniej klasie inicjowania — `Global.asax.cs` na przykład AppStart in — włóż procesor do łańcucha:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients utworzone po tym punkcie użyje procesorów.

**aplikacje ASP.NET Core/ Worker Service**

> [!NOTE]
> Dodawanie procesora przy użyciu `ApplicationInsights.config` lub przy użyciu `TelemetryConfiguration.Active` jest nieprawidłowe dla aplikacji ASP.NET Core lub jeśli używasz microsoft.ApplicationInsights.WorkerService SDK.

W przypadku aplikacji napisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) `TelemetryProcessor` lub [WorkerService](worker-service.md#adding-telemetry-processors)dodawanie `IServiceCollection`nowego odbywa się przy użyciu `AddApplicationInsightsTelemetryProcessor` metody rozszerzenia na , jak pokazano poniżej. Ta metoda jest `ConfigureServices` wywoływana `Startup.cs` w metodzie klasy.

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

Odfiltruj boty i testy internetowe. Chociaż Eksplorator metryk umożliwia odfiltrowanie źródeł syntetycznych, ta opcja zmniejsza ruch i rozmiar pozyskiwania, filtrując je w samym SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Uwierzytelnianie nie powiodło się

Odfiltruj żądania za pomocą odpowiedzi "401".

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Odfiltruj szybkie połączenia zależności zdalnej

Jeśli chcesz tylko zdiagnozować połączenia, które są powolne, odfiltruj szybkie.

> [!NOTE]
> Spowoduje to wypaczenie statystyk widocznych na portalu.
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

#### <a name="diagnose-dependency-issues"></a>Diagnozowanie problemów z zależnościami

[W tym blogu](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) opisano projekt do diagnozowania problemów z zależnościami przez automatyczne wysyłanie regularnych pingów do zależności.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplikacje internetowe JavaScript

**Filtrowanie przy użyciu itelemetryInitializer**

1. Utwórz funkcję wywołania zwrotnego inicjatora telemetrii. Funkcja wywołania `ITelemetryItem` zwrotnego przyjmuje jako parametr, który jest zdarzeniem, które jest przetwarzane. Powrót `false` z tego wywołania zwrotnego powoduje, że element telemetrii ma zostać odfiltrowany.  

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

## <a name="addmodify-properties-itelemetryinitializer"></a>Dodawanie/modyfikowanie właściwości: ITelemetryInitializer


Inicjatory telemetrii można wzbogacić dane telemetryczne o dodatkowe informacje i/lub zastąpić właściwości telemetrii ustawione przez standardowe moduły telemetryczne.

Na przykład pakiet Application Insights for Web zbiera dane telemetryczne dotyczące żądań HTTP. Domyślnie flagi jako nie powiodło się każde żądanie z kodem odpowiedzi >= 400. Ale jeśli chcesz traktować 400 jako sukces, można podać inicjatora telemetrii, który ustawia Success właściwości.

Jeśli podasz inicjatora telemetrii, jest wywoływana za każdym razem, gdy wywoływana jest którakolwiek z metod Track*(). Obejmuje `Track()` to metody wywoływane przez standardowe moduły telemetryczne. Zgodnie z konwencją te moduły nie ustawiają żadnej właściwości, która została już ustawiona przez inicjatora. Inicjatory telemetrii są wywoływane przed wywołaniem procesorów telemetrycznych. Tak więc wszelkie wzbogacenia wykonane przez inicjatory są widoczne dla procesorów.

**Zdefiniuj inicjatora**

*C #*

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

**ASP.NET aplikacje: Załaduj inicjatora**

W aplikacjiInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternatywnie* można utworzyć wystąpienia inicjatora w kodzie, na przykład w Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Zobacz więcej tego przykładu.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET aplikacje Core/ Worker Service: Załaduj inicjatora**

> [!NOTE]
> Dodawanie inicjatora `ApplicationInsights.config` przy `TelemetryConfiguration.Active` użyciu lub przy użyciu jest nieprawidłowe dla aplikacji ASP.NET Core lub jeśli używasz microsoft.ApplicationInsights.WorkerService SDK.

W przypadku aplikacji napisanych przy użyciu [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) `TelemetryInitializer` lub [WorkerService](worker-service.md#adding-telemetryinitializers)dodawanie nowego odbywa się przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Odbywa się `Startup.ConfigureServices` to metodą.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inicjalizatory telemetryczne JavaScript
*JavaScript*

Wstaw inicjatora telemetrii natychmiast po kod inicjowania, który został wprowadzony z portalu:

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

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Aby uzyskać podsumowanie właściwości innych niż niestandardowe dostępnych na podstawie danych telemetrycznych, zobacz [Model danych eksportu aplikacji Insights](../../azure-monitor/app/export-data-model.md).

Można dodać dowolną liczbę inicjatorów, a są one wywoływane w kolejności, w jakiej są dodawane.

### <a name="opencensus-python-telemetry-processors"></a>Procesory telemetryczne OpenCensus Python

Procesory telemetryczne w OpenCensus Python są po prostu funkcje wywołania zwrotnego wywoływane do przetwarzania danych telemetrycznych przed ich eksportem. Funkcja wywołania zwrotnego musi akceptować typ danych [koperty](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) jako jego parametr. Aby odfiltrować dane telemetryczne przed eksportem, upewnij się, że funkcja wywołania zwrotnego zwraca `False`. Schemat typów danych usługi Azure Monitor można zobaczyć w kopertach [tutaj](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Można to `cloud_RoleName` zmodyfikować, `ai.cloud.role` zmieniając atrybut `tags` w polu.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
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
Możesz dodać dowolną liczbę procesorów i są one wywoływane w kolejności, w jakiej są dodawane. Jeśli jeden procesor powinien zgłosić wyjątek, nie ma to wpływu na następujące procesory.

### <a name="example-telemetryinitializers"></a>Przykład telemetryinilizatory

#### <a name="add-custom-property"></a>Dodawanie właściwości niestandardowej

Poniższy przykładowy inicjator dodaje właściwość niestandardową do każdej śledzonej telemetrii.

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

#### <a name="add-cloud-role-name"></a>Dodawanie nazwy roli w chmurze

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

#### <a name="add-information-from-httpcontext"></a>Dodawanie informacji z protokołu HttpContext

Poniższy przykładowy inicjator odczytuje dane z [`HttpContext`](https://docs.microsoft.com/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) i `RequestTelemetry` dołącza go do wystąpienia. Jest `IHttpContextAccessor` automatycznie dostarczany za pośrednictwem iniekcji zależności konstruktora.

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

Jaka jest różnica między procesorami telemetrii a inicjatorami telemetrii?

* Istnieją pewne nakładanie się w co można zrobić z nimi: oba mogą służyć do dodawania lub modyfikowania właściwości telemetrii, choć zaleca się użycie inicjatorów w tym celu.
* TelemetryInilizatory zawsze działają przed TelemetryProcessors.
* TelemetryInilizatory mogą być wywoływane więcej niż jeden raz. Zgodnie z konwencją nie ustawiają żadnej właściwości, która została już ustawiona.
* TelemetryProcessors umożliwiają całkowite zastąpienie lub odrzucenie elementu telemetrii.
* Wszystkie zarejestrowane TelemetryInilizatory są gwarantowane do wywołania dla każdego elementu telemetrii. W przypadku procesorów telemetrycznych SDK gwarantuje wywołanie pierwszego procesora telemetrii. O tym, czy pozostałe procesory są wywoływane, czy nie, zadecydują poprzednie procesory telemetryczne.
* Użyj TelemetryInitializers wzbogacenie danych telemetrycznych o dodatkowe właściwości lub zastąpienie istniejącego. Użyj telemetryprocesora do filtrowania danych telemetrycznych.

## <a name="troubleshooting-applicationinsightsconfig"></a>Rozwiązywanie problemów z usługą ApplicationInsights.config

* Upewnij się, że w pełni kwalifikowana nazwa typu i nazwa zestawu są poprawne.
* Upewnij się, że plik applicationinsights.config znajduje się w katalogu wyjściowym i zawiera wszelkie ostatnie zmiany.

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Omówienie interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET referencje](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Kod SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Następne kroki
* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md)
* [Próbkowanie](../../azure-monitor/app/sampling.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
