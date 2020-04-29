---
title: Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights
description: Rozpocznij monitorowanie nowej aplikacji przy użyciu nowego klucza instrumentacji
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 395e8d667985318f4a084428c6fd4c395ee8b956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671447"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Monitorowanie witryny programu SharePoint za pomocą usługi Application Insights
Usługa Azure Application Insights monitoruje dostępność, wydajność i użycie Twoich aplikacji. Tutaj dowiesz się, jak skonfigurować je dla witryny programu SharePoint.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights
W witrynie [Azure Portal](https://portal.azure.com) utwórz nowy zasób usługi Application Insights. Wybierz ASP.NET jako typ aplikacji.

![Kliknij opcję Właściwości, zaznacz klucz i naciśnij klawisze Ctrl+C](./media/sharepoint/001.png)

Okno, które zostanie otwarte, to miejsce, gdzie zobaczysz dane o wydajności i użyciu aplikacji. Aby wrócić do niego przy następnym logowaniu do platformy Azure, musisz znaleźć odpowiedni kafelek na ekranie startowym. Alternatywnie kliknij przycisk Przeglądaj, aby go znaleźć.

## <a name="add-the-script-to-your-web-pages"></a>Dodawanie skryptu do stron internetowych

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Wstaw skrypt tuż przed tagiem &lt;/head&gt; na każdej stronie, którą chcesz śledzić. Jeśli witryna sieci Web ma stronę wzorcową, możesz umieścić skrypt w tym miejscu. Na przykład w projekcie MVC programu ASP.NET możesz go umieścić w pliku View\Shared\_Layout.cshtml

Skrypt zawiera klucz instrumentacji, który kieruje dane telemetryczne do zasobu usługi Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Dodawanie kodu do stron witryny
#### <a name="on-the-master-page"></a>Na stronie wzorcowej
Jeśli możesz edytować stronę wzorcową witryny, będzie ona umożliwiać monitorowanie każdej strony witryny.

Wyrejestruj stronę wzorcową i edytuj ją za pomocą programu SharePoint Designer lub innego dowolnego edytora.

![](./media/sharepoint/03-master.png)

Dodaj kod przed tagiem </head>. 

![](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Lub na poszczególnych stronach
Aby monitorować ograniczony zestaw stron, dodaj skrypt oddzielnie do każdej strony. 

Wstaw składnik Web Part i osadź w nim fragment kodu.

![](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Wyświetlanie danych aplikacji
Ponownie wdróż aplikację.

Wróć do bloku aplikacji w witrynie [Azure Portal](https://portal.azure.com).

Pierwsze zdarzenia będą wyświetlane w obszarze wyszukiwania. 

![](./media/sharepoint/09-search.png)

Jeśli oczekujesz większej ilości danych, kliknij przycisk Odśwież po kilku sekundach.

## <a name="capturing-user-id"></a>Przechwytywanie identyfikatora użytkownika
Fragment kodu standardowej strony sieci Web nie przechwytuje identyfikatora użytkownika z programu SharePoint, ale możesz to zrobić za pomocą niewielkiej modyfikacji.

1. Skopiuj klucz instrumentacji aplikacji z listy rozwijanej Podstawy w usłudze Application Insights. 

    ![](./media/sharepoint/02-props.png)

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
* [Testy sieci Web](../../azure-monitor/app/monitor-web-app-availability.md) do monitorowania dostępności witryny.
* [Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) dla innych typów aplikacji.

<!--Link references-->


