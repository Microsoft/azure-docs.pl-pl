---
title: Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights
description: Rozpocznij monitorowanie nowej aplikacji przy użyciu nowego klucza instrumentacji
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 74f0eeecb303d68af8aaa48b5c1806ceeef4d318
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486373"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights

Usługa Azure Application Insights monitoruje dostępność, wydajność i użycie Twoich aplikacji. Tutaj dowiesz się, jak skonfigurować je dla witryny programu SharePoint.

> [!NOTE]
> Ze względu na kwestie bezpieczeństwa nie można bezpośrednio dodać skryptu opisanego w tym artykule do stron sieci Web w nowoczesnych interfejsie użytkownika programu SharePoint. Alternatywnie można użyć [programu SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) , aby utworzyć niestandardowe rozszerzenie, którego można użyć do zainstalowania Application Insights w witrynach programu SharePoint.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
W witrynie [Azure Portal](https://portal.azure.com) utwórz nowy zasób usługi Application Insights. Wybierz ASP.NET jako typ aplikacji.

![Kliknij opcję Właściwości, zaznacz klucz i naciśnij klawisze Ctrl+C](./media/sharepoint/001.png)

Okno, które zostanie otwarte, to miejsce, gdzie zobaczysz dane o wydajności i użyciu aplikacji. Aby wrócić do niego przy następnym logowaniu do platformy Azure, musisz znaleźć odpowiedni kafelek na ekranie startowym. Alternatywnie kliknij przycisk Przeglądaj, aby go znaleźć.

## <a name="add-the-script-to-your-web-pages"></a>Dodawanie skryptu do stron internetowych

Bieżący fragment kodu (wymieniony poniżej) jest w wersji "5". wersja jest zaszyfrowana w fragmencie kodu jako OHR: "#", a [Bieżąca wersja jest również dostępna w witrynie GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  instrumentationKey:"INSTRUMENTATION_KEY"
}});
</script>
```

> [!NOTE]
> W adresie URL programu SharePoint jest używany inny format modułu "...\ai.2.gbl.min.js" (należy zwrócić uwagę na dodatkowe **. GBL.**) ten format modułu alternatywnego jest wymagany, aby uniknąć problemu spowodowanego przez kolejność ładowania skryptów, co spowoduje niepowodzenie zainicjowania zestawu SDK i spowoduje utratę zdarzeń telemetrii.
>
> Przyczyną problemu jest załadowanie i zainicjowanie requireJS przed zestawem SDK.

Wstaw skrypt tuż przed &lt; tagiem/Head na &gt; każdej stronie, którą chcesz śledzić. Jeśli witryna sieci Web ma stronę wzorcową, możesz umieścić skrypt w tym miejscu. Na przykład w projekcie MVC programu ASP.NET możesz go umieścić w pliku View\Shared\_Layout.cshtml

Skrypt zawiera klucz instrumentacji, który kieruje dane telemetryczne do zasobu usługi Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Dodawanie kodu do stron witryny
#### <a name="on-the-master-page"></a>Na stronie wzorcowej
Jeśli możesz edytować stronę wzorcową witryny, będzie ona umożliwiać monitorowanie każdej strony witryny.

Wyrejestruj stronę wzorcową i edytuj ją za pomocą programu SharePoint Designer lub innego dowolnego edytora.

![Zrzut ekranu pokazujący sposób edycji strony wzorcowej przy użyciu narzędzia Administrator Designer lub innego edytora.](./media/sharepoint/03-master.png)

Dodaj kod tuż przed </head> seryjn. 

![Zrzut ekranu pokazujący, gdzie dodać kod do strony witryny.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Lub na poszczególnych stronach
Aby monitorować ograniczony zestaw stron, dodaj skrypt oddzielnie do każdej strony. 

Wstaw składnik Web Part i osadź w nim fragment kodu.

![Zrzut ekranu pokazujący Dodawanie skryptu do monitorowania ograniczonego zestawu stron.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Wyświetlanie danych aplikacji
Ponownie wdróż aplikację.

Wróć do bloku aplikacji w witrynie [Azure Portal](https://portal.azure.com).

Pierwsze zdarzenia będą wyświetlane w obszarze wyszukiwania. 

![Zrzut ekranu pokazujący nowe dane, które można wyświetlić w aplikacji.](./media/sharepoint/09-search.png)

Jeśli oczekujesz większej ilości danych, kliknij przycisk Odśwież po kilku sekundach.

## <a name="capturing-user-id"></a>Przechwytywanie identyfikatora użytkownika
Fragment kodu standardowej strony sieci Web nie przechwytuje identyfikatora użytkownika z programu SharePoint, ale możesz to zrobić za pomocą niewielkiej modyfikacji.

1. Skopiuj klucz instrumentacji aplikacji z listy rozwijanej Podstawy w usłudze Application Insights. 

    ![Zrzut ekranu pokazujący kopiowanie Instrumentacji aplikacji z listy rozwijanej podstawowe w Application Insights.](./media/sharepoint/02-props.png)

1. Zastąp klucz instrumentacji ciągiem „XXXX” we fragmencie kodu poniżej. 
2. Osadź skrypt w aplikacji programu SharePoint zamiast fragmentu kodu pobranego z portalu.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Następne kroki
* [Testy sieci Web](./monitor-web-app-availability.md) do monitorowania dostępności witryny.
* [Usługa Application Insights](./app-insights-overview.md) dla innych typów aplikacji.

<!--Link references-->
