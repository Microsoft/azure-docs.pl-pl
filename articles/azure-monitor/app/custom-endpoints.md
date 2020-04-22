---
title: Usługa Azure Application Insights zastępuje domyślne punkty końcowe sdk
description: Zmodyfikuj domyślne punkty końcowe SDK usługi Azure Monitor Application Insights dla regionów takich jak Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b43bd13c73f77c6292e2062db88d68a20e5bf480
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729533"
---
# <a name="application-insights-overriding-default-endpoints"></a>Zastępowanie domyślnych punktów końcowych usługi Application Insights

Aby wysłać dane ze usługi Application Insights do niektórych regionów, należy zastąpić domyślne adresy punktów końcowych. Każdy SDK wymaga nieco innych modyfikacji, z których wszystkie są opisane w tym artykule. Zmiany te wymagają dostosowania przykładowego kodu i `QuickPulse_Endpoint_Address`zastąpienia `TelemetryChannel_Endpoint_Address`wartości `Profile_Query_Endpoint_address` zastępczych dla programu , oraz rzeczywistych adresów punktów końcowych dla określonego regionu. Koniec tego artykułu zawiera łącza do adresów punktów końcowych dla regionów, w których ta konfiguracja jest wymagana.

> [!NOTE]
> [Parametry połączenia](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net) to nowa preferowana metoda ustawiania niestandardowych punktów końcowych w usłudze Application Insights.

---

## <a name="sdk-code-changes"></a>Zmiany kodu SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Plik applicationinsights.config jest automatycznie zastępowany w dowolnym momencie wykonania uaktualnienia SDK. Po wykonaniu uaktualnienia SDK należy ponownie wprowadzić wartości punktu końcowego określonego regionu.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Zmodyfikuj plik appsettings.json w projekcie w następujący sposób, aby dostosować główny punkt końcowy:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Wartości metryk live i punktu końcowego zapytania profilu można ustawić tylko za pomocą kodu. Aby zastąpić wartości domyślne dla wszystkich wartości punktu końcowego za `ConfigureServices` pomocą `Startup.cs` kodu, należy wprowadzić następujące zmiany w metodzie pliku:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

### <a name="azure-functions-v2x"></a>Usługi Azure Functions w wersji 2.x

Zainstaluj następujące pakiety w projekcie funkcji:

- Microsoft.ApplicationInsights w wersji 2.10.0
- Microsoft.ApplicationInsights.PerfCounterCollector w wersji 2.10.0
- Microsoft.ApplicationInsights.WindowsServer.TelemetryKannel w wersji 2.10.0

Następnie dodaj (lub zmodyfikuj) kod startowy aplikacji funkcyjnej:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Zmodyfikuj plik applicationinsights.xml, aby zmienić domyślny adres punktu końcowego.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Zmodyfikuj `application.properties` plik i dodaj:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Punkty końcowe można również skonfigurować za pomocą zmiennych środowiskowych:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać wskazówki dotyczące modyfikowania punktu końcowego pozyskiwania dla opencensus-python SDK, zapoznaj się z [repozytorium opencensus-python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Regiony wymagające modyfikacji punktu końcowego

Obecnie jedynymi regionami, które wymagają modyfikacji punktu końcowego, są [platformy Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) i Azure [China.](https://docs.microsoft.com/azure/china/resources-developer-guide)

|Region |  Nazwa punktu końcowego | Wartość |
|-----------------|:------------|:-------------|
| Azure China | Kanał telemetrii | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (Metryki na żywo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Zapytanie profilu |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Kanał telemetrii |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Metryki na żywo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Zapytanie profilu |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Jeśli obecnie używasz [interfejsu API REST usługi Application Insights,](https://dev.applicationinsights.io/
) który jest zwykle dostępny za pośrednictwem "api.applicationinsights.io", należy użyć punktu końcowego, który jest lokalny dla twojego regionu:

|Region |  Nazwa punktu końcowego | Wartość |
|-----------------|:------------|:-------------|
| Azure China | Interfejs API REST | `api.applicationinsights.azure.cn` |
| Azure Government | Interfejs API REST | `api.applicationinsights.us`|

> [!NOTE]
> Monitorowanie oparte na agentach/rozszerzeniach bez kodu dla usług Azure App Services nie jest **obecnie obsługiwane** w tych regionach. Jak tylko ta funkcja stanie się dostępna, ten artykuł zostanie zaktualizowany.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o niestandardowych modyfikacjach platformy Azure dla instytucji rządowych, zapoznaj się ze szczegółowymi wskazówkami dotyczącymi [monitorowania i zarządzania na platformie Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights)
- Aby dowiedzieć się więcej o platformie Azure China, zapoznaj się z [podręcznikiem Azure China Playbook](https://docs.microsoft.com/azure/china/).
