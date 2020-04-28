---
title: Parametry połączeń w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Jak używać ciągów połączeń.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 25eda0ae2b0d873fe9850e5b886489a5f2590e69
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410607"
---
# <a name="connection-strings"></a>Parametry połączeń

## <a name="overview"></a>Omówienie

Parametry połączenia zapewniają użytkownikom usługi Application Insight jedno ustawienie konfiguracji, eliminując potrzebę wielu ustawień serwera proxy. Bardzo przydatne w przypadku intranetowych serwerów sieci web, suwerennych lub hybrydowych środowisk chmury, które chcą wysyłać dane do usługi monitorowania.

The key value pairs provide an easy way for users to define a prefix suffix combination for each Application Insights (AI) service/ product.

> [!IMPORTANT]
> Nie zaleca się ustawiania zarówno parametry połączenia, jak i klucza instrumentacji. W przypadku, gdy użytkownik ustawi oba, w zależności od tego, który został ustawiony jako ostatni, będzie miał pierwszeństwo. 


## <a name="scenario-overview"></a>Omówienie scenariusza 

Scenariusze klientów, w których wizualizujemy to, mając największy wpływ:

- Wyjątki zapory lub przekierowania serwera proxy 

    W przypadkach, gdy wymagane jest monitorowanie intranetowego serwera sieci web, nasze wcześniejsze rozwiązanie poprosiło klientów o dodanie poszczególnych punktów końcowych usługi do konfiguracji. Aby uzyskać więcej informacji, zobacz [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Parametry połączenia oferują lepszą alternatywę, zmniejszając ten wysiłek do jednego ustawienia. Prosty prefiks, zmiana sufiksu umożliwia automatyczne zapełnianie i przekierowywanie wszystkich punktów końcowych do odpowiednich usług. 

- Suwerenne lub hybrydowe środowiska chmury

    Użytkownicy mogą wysyłać dane do zdefiniowanego [regionu azure government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
    Parametry połączenia umożliwiają definiowanie ustawień punktu końcowego dla serwerów intranetowych lub ustawień chmury hybrydowej. 

## <a name="getting-started"></a>Wprowadzenie

### <a name="finding-my-connection-string"></a>Czy znaleźć mój ciąg połączenia?

Parametry połączenia są wyświetlane w bloku Przegląd zasobu usługi Application Insights.

![ciąg połączenia na bloku przeglądu](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schemat

#### <a name="max-length"></a>Długość maksymalna

Maksymalna obsługiwana długość połączenia wynosi 4096 znaków.

#### <a name="key-value-pairs"></a>Pary klucz-wartość

Parametry połączenia składają się z listy ustawień reprezentowanych jako pary klucz-wartość oddzielone średnikami:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Składnia

- `InstrumentationKey`(np.: 0000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  Parametry połączenia są **polem wymaganym.**
- `Authorization`(np. (To ustawienie jest opcjonalne, ponieważ dziś obsługujemy tylko autoryzację ikey).
- `EndpointSuffix`(np. applicationinsights.azure.cn) Ustawienie sufiksu punktu końcowego poinstruuje zestaw SDK, z którym ma się połączyć chmura Azure. Zestaw SDK zmontuje pozostałą część punktu końcowego dla poszczególnych usług.
- Jawne punkty końcowe.
  Każda usługa może być jawnie zastąpiona w ciągu połączenia.
   - `IngestionEndpoint`(np.https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(np.https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(np.https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(np.https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Schemat punktu końcowego

`<prefix>.<suffix>`
- Prefiks: Definiuje usługę. 
- Sufiks: Definiuje wspólną nazwę domeny.

##### <a name="valid-suffixes"></a>Prawidłowe przyrostki

Oto lista prawidłowych przyrostków 
- applicationinsights.azure.cn
- applicationinsights.us


Zobacz też:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prawidłowe prefiksy

- [Połknienie danych telemetrycznych:](./app-insights-overview.md)`dc`
- [Dane na żywo:](./live-stream.md)`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Migawka](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Przykłady ciągu połączenia


### <a name="minimal-valid-connection-string"></a>Minimalny prawidłowy ciąg połączenia

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

W tym przykładzie ustawiono tylko klucz instrumentacji.

- Domyślnie schemat autoryzacji to "ikey" 
- Klucz oprzyrządowania: 00000000-0000-0000-0000-00000000000
- Identyfikatory identyfikatorów URI usługi regionalnej są oparte na [wartościach domyślnych zestawu SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) i będą łączyć się z publiczną globalną platformą Azure:
   - Spożyciu:https://dc.services.visualstudio.com/
   - Dane na żywo:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Debuger:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Ciąg połączenia z sufiksem punktu końcowego

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

W tym przykładzie ten ciąg połączenia określa sufiks punktu końcowego i SDK skonstruuje punkty końcowe usługi.

- Domyślnie schemat autoryzacji to "ikey" 
- Klucz oprzyrządowania: 00000000-0000-0000-0000-00000000000
- Identyfikatory URI usługi regionalnej są oparte na dostarczonym sufiksie punktu końcowego: 
   - Spożyciu:https://dc.ai.contoso.com
   - Dane na żywo:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Debuger:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Ciąg połączenia z jawnymi zastąpieniami punktów końcowych 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

W tym przykładzie ten ciąg połączenia określa jawne zastąpienia dla każdej usługi. SDK użyje dokładnych punktów końcowych dostarczonych bez modyfikacji.

- Domyślnie schemat autoryzacji to "ikey" 
- Klucz oprzyrządowania: 00000000-0000-0000-0000-00000000000
- Identyfikatory URI usługi regionalnej są oparte na jawnych wartościach zastępowania: 
   - Połknienie: https:\//custom.com:111/
   - Dane na żywo: https:\//custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Debuger: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Jak ustawić parametry połączenia

Parametry połączenia są obsługiwane w następujących wersjach SDK:
- .NET i .NET Core v2.12.0
- Java v2.5.1 i Java 3.0
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Parametry połączenia można ustawić za pomocą kodu, zmiennej środowiskowej lub pliku konfiguracyjnego.



### <a name="environment-variable"></a>Zmienna środowiskowa

- Ciąg połączenia:`APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemetryConfiguration.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net Jawnie ustaw:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Plik konfiguracyjny .net:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (wersja 2.5.x) Jawnie ustawiona:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

Plik ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Ważne: Javascript nie obsługuje używania zmiennych środowiskowych.

Za pomocą fragmentu kodu:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Konfiguracja ręczna:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Zalecamy użytkownikom ustawienie zmiennej środowiskowej.

Aby jawnie ustawić parametry połączenia:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę w czasie wykonywania za pomocą rozwiązań:

* [Zestaw aplikacji hostowanych usługami IIS dla maszyn wirtualnych platformy Azure i platformy Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serwer usług IIS](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Rozpocznij pracę w czasie programowania za pomocą rozwiązań:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
