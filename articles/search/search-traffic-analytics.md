---
title: Dane telemetryczne dla analizy ruchu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz analizę ruchu wyszukiwania dla platformy Azure Wyszukiwanie poznawcze, Zbieraj dane telemetryczne i zdarzenia zainicjowane przez użytkownika przy użyciu Application Insights, a następnie Analizuj wyniki w Power BI raport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/29/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 89d067162dacb7a0ca25de826630e1986f1035e1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485473"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Zbieraj dane telemetryczne dla analizy ruchu wyszukiwania

Analiza ruchu wyszukiwania jest wzorcem do zbierania danych telemetrycznych dotyczących interakcji użytkowników z aplikacją Wyszukiwanie poznawcze platformy Azure, takich jak zdarzenia inicjowane przez użytkownika i dane wejściowe z klawiatury. Korzystając z tych informacji, można określić efektywność rozwiązania wyszukiwania, w tym popularne terminy wyszukiwania, częstotliwość kliknięć i dane wejściowe zapytania, które zwracają zero wyników.

Ten wzorzec przyjmuje zależność od [Application Insights](../azure-monitor/app/app-insights-overview.md) (funkcji [Azure monitor](../azure-monitor/index.yml)) do zbierania danych użytkownika. Należy dodać instrumentację do kodu klienta, zgodnie z opisem w tym artykule. Na koniec konieczne będzie przeanalizowanie danych przy użyciu mechanizmu raportowania. Zalecamy Power BI, ale możesz użyć pulpitu nawigacyjnego aplikacji lub dowolnego narzędzia, które nawiązuje połączenie z Application Insights.

> [!NOTE]
> Wzorzec opisany w tym artykule dotyczy zaawansowanych scenariuszy i strumienia kliknięć danych generowanych przez kod dodawany do klienta. W przeciwieństwie do tego dzienniki usługi są łatwe w konfiguracji, udostępniają szereg metryk i mogą być wykonywane w portalu bez konieczności wykonywania kodu. Włączenie rejestrowania jest zalecane w przypadku wszystkich scenariuszy. Aby uzyskać więcej informacji, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Zidentyfikuj odpowiednie dane wyszukiwania

Aby uzyskać przydatne metryki dotyczące analizy ruchu wyszukiwania, należy rejestrować niektóre sygnały od użytkowników aplikacji wyszukiwania. Sygnalizują one zawartość, którą interesują użytkownicy, i że uznają je za istotne. Do analizy ruchu wyszukiwania należą:

+ Zdarzenia wyszukiwania generowane przez użytkownika: interesujące są tylko zapytania wyszukiwania inicjowane przez użytkownika. Inne żądania wyszukiwania, takie jak te używane do wypełniania aspektów lub pobierania informacji wewnętrznych, nie są ważne. Pamiętaj, aby tylko przyrządować zdarzenia inicjowane przez użytkownika, aby uniknąć pochylenia lub pochylenia w wynikach.

+ Zdarzenia kliknięcia generowane przez użytkownika: na stronie wyników wyszukiwania zdarzenie kliknięcia zazwyczaj oznacza, że dokument jest odpowiednim wynikiem dla konkretnego zapytania wyszukiwania.

Łącząc wyszukiwanie i klikaj zdarzenia z IDENTYFIKATORem korelacji, uzyskasz dokładniejsze informacje o tym, jak dobrze działa funkcja wyszukiwania aplikacji.

## <a name="add-search-traffic-analytics"></a>Dodawanie analizy ruchu wyszukiwania

Na stronie [portalu](https://portal.azure.com) usługi Azure wyszukiwanie poznawcze Otwórz stronę wyszukiwania Analiza ruchu, aby uzyskać dostęp do arkusza Ściągawka, który jest następujący: wzorzec telemetrii. Na tej stronie można wybrać lub utworzyć zasób Application Insights, pobrać klucz instrumentacji, skopiować fragmenty kodu, które można dostosować do rozwiązania, a następnie pobrać raport Power BI, który jest skompilowany na podstawie schematu odzwierciedlonego we wzorcu.

![Strona wyszukiwania Analiza ruchu w portalu](media/search-traffic-analytics/azuresearch-trafficanalytics.png "Strona wyszukiwania Analiza ruchu w portalu")

## <a name="1---set-up-application-insights"></a>1 — Skonfiguruj Application Insights

Wybierz istniejący zasób Application Insights lub [Utwórz go](../azure-monitor/app/create-new-resource.md) , jeśli jeszcze go nie masz. Jeśli używasz strony Analiza ruchu wyszukiwania, możesz skopiować klucz instrumentacji, której aplikacja musi połączyć się z Application Insights.

Gdy masz zasób Application Insights, możesz wykonać [instrukcje dotyczące obsługiwanych języków i platform](../azure-monitor/app/platforms.md) , aby zarejestrować aplikację. Rejestracja to po prostu dodanie klucza Instrumentacji z Application Insights do kodu, który konfiguruje skojarzenie. Klucz można znaleźć w portalu lub na stronie wyszukiwania Analiza ruchu w przypadku wybrania istniejącego zasobu.

Skrót, który działa dla niektórych typów projektów programu Visual Studio, jest uwzględniony w poniższych krokach. Tworzy zasób i rejestruje aplikację w zaledwie kilka kliknięć.

1. W przypadku programów Visual Studio i ASP.NET Development Otwórz rozwiązanie i wybierz pozycję **Project**  >  **Add Telemetria usługi Application Insights**.

1. Kliknij pozycję **Rozpocznij**.

1. Zarejestruj swoją aplikację, podając konto Microsoft, subskrypcję platformy Azure i zasób Application Insights (domyślnie jest to nowy zasób). Kliknij pozycję **Zarejestruj**.

W tym momencie aplikacja jest skonfigurowana do monitorowania aplikacji, co oznacza, że wszystkie obciążenia stron są śledzone przy użyciu metryk domyślnych. Aby uzyskać więcej informacji o poprzednich krokach, zobacz [Włączanie telemetrii po stronie serwera Application Insights](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio).

## <a name="2---add-instrumentation"></a>2 — Dodawanie Instrumentacji

Ten krok to miejsce, w którym można tworzyć własne aplikacje do wyszukiwania przy użyciu zasobów Application Insights utworzonych w powyższym kroku. Ten proces zawiera cztery kroki, rozpoczynając od tworzenia klienta telemetrii.

### <a name="step-1-create-a-telemetry-client"></a>Krok 1. Tworzenie klienta telemetrii

Utwórz obiekt, który wysyła zdarzenia do Application Insights. Możesz dodać instrumentację do kodu aplikacji po stronie serwera lub kodu po stronie klienta uruchomionego w przeglądarce, wyrażoną tutaj jako warianty języka C# i JavaScript (dla innych języków, zobacz kompletną listę [obsługiwanych platform i struktur](../azure-monitor/app/platforms.md). Wybierz podejście, które zapewnia odpowiednią głębokość informacji.

Dane telemetryczne po stronie serwera przechwytuje metryki w warstwie aplikacji, na przykład w aplikacjach uruchamianych jako usługa sieci Web w chmurze lub jako aplikacja lokalna w sieci firmowej. Dane telemetryczne po stronie serwera przechwytują zdarzenia wyszukiwania i klikania, położenie dokumentu w wynikach oraz informacje o zapytaniu, ale gromadzenie danych będzie ograniczone do dowolnych informacji dostępnych w tej warstwie.

Na kliencie może być dodatkowy kod, który manipuluje danymi wejściowymi zapytania, dodaje nawigację lub zawiera kontekst (na przykład zapytania inicjowane ze strony głównej zamiast strony produktu). W przypadku opisywania rozwiązania można wybrać instrumentację po stronie klienta, aby dane telemetryczne odzwierciedlały dodatkowe szczegóły. Informacje o tym, jak zbierane są dodatkowe szczegóły, wykraczają poza zakres tego wzorca, ale można przeglądać [Application Insights dla stron sieci Web](../azure-monitor/app/javascript.md#explore-browserclient-side-data) , aby uzyskać więcej kierunku. 

**Korzystanie z języka C#**

W języku C# **InstrumentationKey** powinien być zdefiniowany w konfiguracji aplikacji, na przykład appsettings.js, jeśli projekt jest ASP.NET. Zapoznaj się z powrotem z instrukcjami rejestracji, jeśli nie masz pewności co do lokalizacji klucza.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Korzystanie z języka JavaScript**

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
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
}});
</script>
```

> [!NOTE]
> Aby można było uzyskać czytelność i zmniejszyć liczbę błędów języka JavaScript, wszystkie możliwe opcje konfiguracji są wyświetlane w nowym wierszu kodu wstawki powyżej, jeśli nie chcesz zmieniać wartości w wierszu z komentarzem, możesz go usunąć.


### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2. żądanie identyfikatora wyszukiwania dla korelacji

Aby skorelować żądania wyszukiwania z kliknięciami, należy mieć identyfikator korelacji, który odnosi się do tych dwóch oddzielnych zdarzeń. Usługa Azure Wyszukiwanie poznawcze zapewnia identyfikator wyszukiwania, gdy zostanie on zażądany przy użyciu nagłówka HTTP.

Identyfikator wyszukiwania umożliwia korelację metryk emitowanych przez Wyszukiwanie poznawcze platformy Azure dla samego żądania, z niestandardowymi metrykami, które są rejestrowane Application Insights.

**Użyj języka C# (nowszy v11 SDK)**

Najnowszy zestaw SDK wymaga użycia potoku HTTP do ustawienia nagłówka w sposób opisany w tym [przykładzie](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Pipeline.md#implementing-a-syncronous-policy).

```csharp
// Create a custom policy to add the correct headers
public class SearchIdPipelinePolicy : HttpPipelineSynchronousPolicy
{
    public override void OnSendingRequest(HttpMessage message)
    {
        message.Request.Headers.SetValue("x-ms-azs-return-searchid", "true");
    }
}
```

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Azure.Search.Documents

SearchClientOptions clientOptions = new SearchClientOptions();
clientOptions.AddPolicy(new SearchIdPipelinePolicy(), HttpPipelinePosition.PerCall);

var client = new SearchClient("<SearchServiceName>", "<IndexName>", new AzureKeyCredential("<QueryKey>"), options: clientOptions);

Response<SearchResults<SearchDocument>> response = await client.SearchAsync<SearchDocument>(searchText: searchText, searchOptions: options);
string searchId = string.Empty;
if (response.GetRawResponse().Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Użyj języka C# (starszy zestaw SDK v10)**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>));

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**Korzystanie z języka JavaScript (wywoływanie interfejsów API REST)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>Krok 3. rejestrowanie zdarzeń wyszukiwania

Za każdym razem, gdy użytkownik wystawia żądanie wyszukiwania, należy je zarejestrować jako zdarzenie wyszukiwania z następującym schematem na Application Insights zdarzeniu niestandardowym. Pamiętaj, aby rejestrować tylko zapytania wyszukiwania generowane przez użytkownika.

+ **SearchServiceName**: (String) nazwa usługi wyszukiwania
+ **SearchId**: (GUID) unikatowy identyfikator zapytania wyszukiwania (znajduje się w odpowiedzi wyszukiwania)
+ **IndexName**: (String) indeks usługi wyszukiwania do zbadania
+ **QueryTerms**: (String) warunki wyszukiwania wprowadzone przez użytkownika
+ **Właściwości resultcount dla**: (int) liczba zwróconych dokumentów (znajduje się w odpowiedzi na wyszukiwanie)
+ **ScoringProfile**: (String) nazwa używanego profilu oceniania (jeśli istnieje)

> [!NOTE]
> Zażądaj liczby zapytań generowanych przez użytkownika, dodając do zapytania wyszukiwania $count = true. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie dokumentów (REST)](/rest/api/searchservice/search-documents#query-parameters).
>

**Korzystanie z języka C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**Korzystanie z języka JavaScript**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>Krok 4. Dziennik zdarzeń kliknięcia

Za każdym razem, gdy użytkownik kliknie dokument, jest to sygnał, który musi być zarejestrowany na potrzeby analizy wyszukiwania. Użyj niestandardowych zdarzeń Application Insights, aby rejestrować te zdarzenia z następującym schematem:

+ **ServiceName: (** ciąg) nazwa usługi wyszukiwania
+ **SearchId**: (GUID) unikatowy identyfikator powiązanego zapytania wyszukiwania
+ **Maperze identyfikatorów dokumentów**: (String) — identyfikator dokumentu
+ **Pozycja**: (int), ranga dokumentu na stronie wyników wyszukiwania

> [!NOTE]
> Pozycja odwołuje się do kolejności kardynalnej w aplikacji. Możesz ustawić tę liczbę, o ile jest ona zawsze taka sama, aby umożliwić porównywanie.
>

**Korzystanie z języka C#**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**Korzystanie z języka JavaScript**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 — analizowanie w Power BI

Po przydzieleniu aplikacji i zweryfikowaniu, że aplikacja jest prawidłowo połączona z Application Insights, można pobrać wstępnie zdefiniowany szablon raportu w celu przeanalizowania danych w programie Power BI Desktop. Raport zawiera wstępnie zdefiniowane wykresy i tabele przydatne do analizowania dodatkowych danych przechwyconych na potrzeby analizy ruchu wyszukiwania.

1. W lewym okienku nawigacyjnym pulpitu nawigacyjnego usługi Azure Wyszukiwanie poznawcze w obszarze **Ustawienia** kliknij pozycję **Analiza ruchu wyszukiwania**.

1. Na stronie **Analiza ruchu wyszukiwania** w kroku 3 kliknij pozycję **Pobierz Power BI Desktop** , aby zainstalować Power BI.

   ![Pobierz raporty Power BI](./media/search-traffic-analytics/get-use-power-bi.png "Pobierz raporty Power BI")

1. Na tej samej stronie kliknij pozycję **pobierz Power BI raport**.

1. Raport zostanie otwarty w Power BI Desktop i zostanie wyświetlony monit o połączenie z Application Insights i podanie poświadczeń. Informacje o połączeniu można znaleźć na Azure Portal stronach dla zasobu Application Insights. W polu poświadczenia podaj tę samą nazwę użytkownika i hasło, które są używane podczas logowania do portalu.

   ![Połączenie z usługą Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Połączenie z usługą Application Insights")

1. Kliknij przycisk **Załaduj**.

Raport zawiera wykresy i tabele, które ułatwiają podejmowanie bardziej świadomych decyzji dotyczących poprawy wydajności i przydatności wyszukiwania.

Metryki obejmują następujące elementy:

+ Wyszukaj wolumin i najpopularniejsze pary dokumentów terminowych: warunki, które powodują, że ten sam dokument kliknięto, uporządkowany według kliknięć.
+ Wyszukiwania bez kliknięć: warunki dla najważniejszych zapytań, które rejestrują brak kliknięć

Poniższy zrzut ekranu pokazuje, jak wygląda wbudowany raport, jeśli użyto wszystkich elementów schematu.

![Pulpit nawigacyjny Power BI dla platformy Azure Wyszukiwanie poznawcze](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Pulpit nawigacyjny Power BI dla platformy Azure Wyszukiwanie poznawcze")

## <a name="next-steps"></a>Następne kroki

Instrumentacja aplikacji wyszukiwania pozwala uzyskać zaawansowane i szczegółowe dane dotyczące usługi wyszukiwania.

Więcej informacji na temat [Application Insights](../azure-monitor/app/app-insights-overview.md) można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/application-insights/) , aby dowiedzieć się więcej o różnych warstwach usług.

Dowiedz się więcej na temat tworzenia niezwykłych raportów. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Power BI Desktop](/power-bi/fundamentals/desktop-getting-started) .
