---
title: Analiza użycia za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zapoznaj się z użytkownikami i Dowiedz się, co robią z Twoją aplikacją.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489042"
---
# <a name="usage-analysis-with-application-insights"></a>Analiza użycia za pomocą usługi Application Insights

Które funkcje aplikacji sieci Web lub mobilnej są najbardziej popularne? Czy użytkownicy uzyskują swoje cele przy użyciu swojej aplikacji? Czy są one porzucane w określonych punktach i są zwracane później?  [Usługa Azure Application Insights](./app-insights-overview.md) pozwala uzyskać zaawansowane informacje dotyczące sposobu korzystania z aplikacji przez użytkowników. Za każdym razem, gdy aktualizujesz aplikację, możesz ocenić, jak dobrze działa dla użytkowników. Korzystając z tej wiedzy, można podejmować decyzje oparte na danych dotyczące kolejnych cykli tworzenia oprogramowania.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>Wysyłanie danych telemetrycznych z aplikacji

Najlepsze środowisko jest uzyskiwane przez zainstalowanie Application Insights zarówno w kodzie serwera aplikacji, jak i na stronach sieci Web. Składniki klienta i serwera aplikacji wysyłają dane telemetryczne z powrotem do Azure Portal na potrzeby analizy.

1. **Kod serwera:** Zainstaluj odpowiedni moduł dla [ASP.NET](./asp-net.md), [platformy Azure](./app-insights-overview.md), [środowiska Java](./java-get-started.md), [Node.js](./nodejs.md)lub [innej](./platforms.md) aplikacji.

    * *Nie chcesz instalować kodu serwera? Po prostu [Utwórz zasób usługi Azure Application Insights](./create-new-resource.md).*

2. **Kod strony sieci Web:** Przed zamknięciem Dodaj następujący skrypt do strony sieci Web ``</head>`` . Zastąp klucz Instrumentacji odpowiednią wartością dla Application Insights zasobu:
    
    Bieżący fragment kodu (wymieniony poniżej) jest w wersji "5". wersja jest zaszyfrowana w fragmencie kodu jako OHR: "#", a [Bieżąca wersja jest również dostępna w witrynie GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
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

    Aby dowiedzieć się więcej na temat zaawansowanych konfiguracji monitorowania witryn sieci Web, zapoznaj się z [artykułem dotyczącym zestawu SDK języka JavaScript](./javascript.md).

3. **Kod aplikacji mobilnej:** Użyj zestawu SDK App Center, aby zbierać zdarzenia z aplikacji, a następnie wysyłać kopie tych zdarzeń do Application Insights do analizy, [postępując zgodnie z tym przewodnikiem](../app/mobile-center-quickstart.md).

4. **Pobierz dane telemetryczne:** Uruchom projekt w trybie debugowania przez kilka minut, a następnie wyszukaj wyniki w bloku przegląd w Application Insights.

    Opublikuj swoją aplikację, aby monitorować wydajność aplikacji i dowiedzieć się, co użytkownicy robią z aplikacją.

## <a name="explore-usage-demographics-and-statistics"></a>Eksplorowanie danych demograficznych dotyczących użycia i statystyk
Dowiedz się, w jaki sposób użytkownicy korzystają z Twojej aplikacji, jakie strony są najbardziej interesujące, w których znajdują się Twoje osoby, których przeglądarek i systemów operacyjnych używają. 

Raporty Użytkownicy i sesje filtrują dane według stron lub zdarzeń niestandardowych i segmentują je według właściwości, takich jak lokalizacja, środowisko i strona. Możesz również dodać własne filtry.

![Przechwytywanie ekranu pokazuje stronę przegląd użytkowników dla fikcyjnej firmy.](./media/usage-overview/users.png)  

Szczegółowe informacje o interesujących wzorcach z prawej strony zestawu danych.  

* Raport **Użytkownicy** liczy liczbę unikatowych użytkowników, którzy uzyskują dostęp do stron w wybranych okresach. W przypadku aplikacji sieci Web użytkownicy są zliczane przy użyciu plików cookie. Jeśli ktoś uzyskuje dostęp do witryny z różnymi przeglądarkami lub komputerami klienckimi lub czyści pliki cookie, zostaną one zliczone więcej niż jeden raz.
* Raport **sesje** liczy liczbę sesji użytkowników, które uzyskują dostęp do witryny. Sesja to okres działania użytkownika zakończony przez okres nieaktywności dłuższy niż pół godziny.

[Więcej informacji o narzędziach użytkownicy, sesje i zdarzenia](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Przechowywanie — ilu użytkowników wraca?

Przechowywanie ułatwia zrozumienie, jak często użytkownicy zwracają się do korzystania z aplikacji, w oparciu o kohorty użytkowników, którzy wykonali pewne działania biznesowe w określonym przedziale czasu. 

- Informacje o konkretnych funkcjach, które powodują, że użytkownicy mogą wrócić więcej niż inne 
- Formy poformowania w oparciu o prawdziwe dane użytkownika 
- Określanie, czy przechowywanie jest problemem w produkcie 

![Przechwytywanie ekranu zawiera stronę przegląd przechowywania, która zawiera informacje o tym, jak często użytkownicy zwracają się do korzystania z aplikacji.](./media/usage-overview/retention.png) 

Kontrolki przechowywania na górze umożliwiają zdefiniowanie określonych zdarzeń i zakresu czasu w celu obliczenia okresu przechowywania. Wykres w środku daje wizualną reprezentację ogólnej wartości procentowej przechowywania według określonego zakresu czasu. Wykres na dole reprezentuje indywidualne przechowywanie w danym okresie czasu. Ten poziom szczegółowości pozwala zrozumieć, co robią Użytkownicy, i co może wpłynąć na zwracanie użytkowników w celu uzyskania bardziej szczegółowego stopnia szczegółowości.  

[Więcej informacji o narzędziu do przechowywania](usage-retention.md)

## <a name="custom-business-events"></a>Niestandardowe zdarzenia biznesowe

Aby jasno zrozumieć, co użytkownicy robią z aplikacją, warto wstawić wiersze kodu do rejestrowania zdarzeń niestandardowych. Zdarzenia te mogą śledzić wszystkie szczegółowe czynności użytkownika, takie jak kliknięcie określonych przycisków, do bardziej znaczących wydarzeń, takich jak zakup lub przegranie gry.

Możesz również użyć [wtyczki Autocollection analizy](javascript-click-analytics-plugin.md) do zbierania zdarzeń niestandardowych.

Chociaż w niektórych przypadkach wyświetlenia stron mogą reprezentować przydatne zdarzenia, nie jest to ogólnie prawdziwe. Użytkownik może otworzyć stronę produktu bez kupowania produktu. 

W przypadku określonych zdarzeń firmy można stworzyć wykres postępów użytkowników w witrynie. Możesz sprawdzić preferencje dotyczące różnych opcji oraz miejsce ich porzucenia lub mieć problemy. Korzystając z tej wiedzy, można podejmować świadome decyzje dotyczące priorytetów w zaległościach związanych z programowaniem.

Zdarzenia mogą być rejestrowane po stronie klienta aplikacji:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Lub po stronie serwera:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Można dołączyć wartości właściwości do tych zdarzeń, aby można było filtrować lub dzielić zdarzenia podczas inspekcji w portalu. Ponadto do każdego zdarzenia jest dołączony standardowy zestaw właściwości, taki jak identyfikator użytkownika anonimowego, który umożliwia śledzenie sekwencji działań poszczególnych użytkowników.

Dowiedz się więcej na temat [niestandardowych zdarzeń](./api-custom-events-metrics.md#trackevent) i [Właściwości](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Zdarzenia wycinków i indeksów

W narzędziach użytkownicy, sesje i zdarzenia można wydzielić zdarzenia niestandardowe i indeksy według użytkownika, nazwy zdarzenia i właściwości.
![Przechwytywanie ekranu pokazuje stronę przegląd użytkowników dla fikcyjnej firmy.](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Zaprojektuj telemetrię przy użyciu aplikacji

Podczas projektowania każdej funkcji aplikacji należy wziąć pod uwagę sposób mierzenia jej sukcesu z użytkownikami. Zdecyduj, jakie zdarzenia biznesowe należy nagrać, i Zakoduj wywołania śledzenia dla tych zdarzeń w aplikacji od początku.

## <a name="a--b-testing"></a>A | Testowanie B
Jeśli nie wiesz, która odmiana funkcji będzie bardziej pomyślna, zwolnij oba z nich, udostępniając każdy dostęp różnym użytkownikom. Zmierz sukces każdego z nich, a następnie przejdź do ujednoliconej wersji.

W tej metodzie należy dołączyć różne wartości właściwości do wszystkich danych telemetrycznych wysyłanych przez poszczególne wersje aplikacji. Można to zrobić przez zdefiniowanie właściwości w aktywnym TelemetryContext. Te domyślne właściwości są dodawane do każdej wiadomości telemetrycznej wysyłanej przez aplikację — nie tylko wiadomości niestandardowych, ale również dla standardowej telemetrii.

W portalu Application Insights Przefiltruj i Podziel dane na wartości właściwości, aby porównać różne wersje.

W tym celu należy [skonfigurować inicjator telemetrii](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**Aplikacje ASP.NET**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

W inicjatorze aplikacji sieci Web, takim jak Global. asax. cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**Aplikacje platformy ASP.NET Core**

> [!NOTE]
> Dodawanie inicjatora przy użyciu `ApplicationInsights.config` lub użycie `TelemetryConfiguration.Active` nie jest prawidłowe dla aplikacji ASP.NET Core. 

W przypadku aplikacji [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) dodanie nowego `TelemetryInitializer` jest wykonywane przez dodanie go do kontenera iniekcji zależności, jak pokazano poniżej. Jest to wykonywane w `ConfigureServices` metodzie `Startup.cs` klasy.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Wszystkie nowe TelemetryClients automatycznie dodają określoną wartość właściwości. Poszczególne zdarzenia telemetrii mogą przesłonić wartości domyślne.

## <a name="next-steps"></a>Następne kroki
   - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
   - [Lejki](usage-funnels.md)
   - [Noszą](usage-retention.md)
   - [Przepływy użytkownika](usage-flows.md)
   - [Skoroszyty](../visualize/workbooks-overview.md)
