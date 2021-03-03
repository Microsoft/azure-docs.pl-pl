---
title: Usługa Azure Application Insights przesłania domyślne punkty końcowe zestawu SDK
description: Zmodyfikuj domyślne Azure Monitor Application Insights punktów końcowych zestawu SDK dla regionów takich jak Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: 49a5abceaed08385c1d2c1be8c77e7c0df5aeed3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708479"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights Przesłanianie domyślnych punktów końcowych

Aby wysłać dane z Application Insights do określonych regionów, należy zastąpić domyślne adresy punktów końcowych. Każdy zestaw SDK wymaga nieco różnych modyfikacji, które zostały opisane w tym artykule. Te zmiany wymagają dostosowania przykładowego kodu i zastąpienia wartości zastępczych dla `QuickPulse_Endpoint_Address` , `TelemetryChannel_Endpoint_Address` i `Profile_Query_Endpoint_address` z rzeczywistymi adresami punktów końcowych dla danego regionu. Koniec tego artykułu zawiera linki do adresów punktów końcowych dla regionów, w których ta konfiguracja jest wymagana.

> [!NOTE]
> [Parametry połączenia](./sdk-connection-string.md?tabs=net) to nowa preferowana metoda ustawiania niestandardowych punktów końcowych w Application Insights.

---

## <a name="sdk-code-changes"></a>Zmiany kodu zestawu SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Plik applicationinsights.config jest automatycznie zastępowany podczas uaktualniania zestawu SDK. Po uaktualnieniu zestawu SDK Pamiętaj o ponownym wprowadzeniu wartości punktu końcowego specyficznego dla regionu.

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

Zmodyfikuj appsettings.jsw pliku w projekcie w następujący sposób, aby dostosować główny punkt końcowy:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Wartości metryk na żywo i punkt końcowy zapytania profilu można ustawić tylko za pośrednictwem kodu. Aby zastąpić wartości domyślne dla wszystkich wartości punktów końcowych za pośrednictwem kodu, wprowadź następujące zmiany w `ConfigureServices` metodzie `Startup.cs` pliku:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

W przypadku Azure Functions teraz zaleca się używanie [parametrów połączenia](./sdk-connection-string.md?tabs=net) ustawionych w ustawieniach aplikacji funkcji. Aby uzyskać dostęp do ustawień aplikacji dla funkcji, w okienku funkcje wybierz pozycję **Ustawienia**  >  **Konfiguracja**  >  **aplikacji ustawienia**. 

Nazwa: `APPLICATIONINSIGHTS_CONNECTION_STRING` wartość: `Connection String Value`

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

Zmodyfikuj `application.properties` plik i Dodaj:

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

Bieżący fragment kodu (wymieniony poniżej) jest w wersji "5". wersja jest zaszyfrowana w fragmencie kodu jako OHR: "#", a [Bieżąca wersja jest również dostępna w witrynie GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{nConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
    instrumentationKey:"INSTRUMENTATION_KEY",
    endpointUrl: "TelemetryChannel_Endpoint_Address"
}});
</script>
```

> [!NOTE]
> Aby można było uzyskać czytelność i zmniejszyć liczbę błędów języka JavaScript, wszystkie możliwe opcje konfiguracji są wyświetlane w nowym wierszu kodu wstawki powyżej, jeśli nie chcesz zmieniać wartości w wierszu z komentarzem, możesz go usunąć.

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać wskazówki dotyczące modyfikowania punktu końcowego pozyskiwania dla zestawu SDK opencensus-Python, zapoznaj się z [repozytorium opencensus-Python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Regiony wymagające modyfikacji punktu końcowego

Obecnie jedyne regiony, które wymagają modyfikacji punktów końcowych, to [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) i [Chiny platformy Azure](/azure/china/resources-developer-guide).

|Region (Region) |  Nazwa punktu końcowego | Wartość |
|-----------------|:------------|:-------------|
| Azure China | Kanał telemetrii | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (metryki na żywo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Zapytanie profilu |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Kanał telemetrii |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (metryki na żywo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Zapytanie profilu |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Jeśli obecnie używasz [interfejsu API REST Application Insights](https://dev.applicationinsights.io/
) , do którego zwykle uzyskuje się dostęp za pośrednictwem elementu "API.ApplicationInsights.IO", musisz użyć punktu końcowego, który jest lokalny dla regionu:

|Region (Region) |  Nazwa punktu końcowego | Wartość |
|-----------------|:------------|:-------------|
| Azure China | Interfejs API REST | `api.applicationinsights.azure.cn` |
| Azure Government | Interfejs API REST | `api.applicationinsights.us`|

> [!NOTE]
> W tych regionach **nie jest obecnie obsługiwane** monitorowanie oparte na agentach i rozszerzeniach w ramach agentów usługi Azure App Services. Po udostępnieniu tej funkcji ten artykuł zostanie zaktualizowany.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat modyfikacji niestandardowych Azure Government, zapoznaj się ze szczegółowymi wskazówkami dotyczącymi [monitorowania i zarządzania platformą Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights).
- Aby dowiedzieć się więcej o Chinach platformy Azure, zapoznaj się z [element playbookem platformy Azure w Chinach](/azure/china/).
