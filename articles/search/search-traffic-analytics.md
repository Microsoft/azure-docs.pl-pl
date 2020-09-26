---
title: Dane telemetryczne dla analizy ruchu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz analizę ruchu wyszukiwania dla platformy Azure Wyszukiwanie poznawcze, Zbieraj dane telemetryczne i zdarzenia zainicjowane przez użytkownika przy użyciu Application Insights, a następnie Analizuj wyniki w Power BI raport.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: d93ced4b45befec207494909de61d30a98d2a67e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333736"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>Zbieraj dane telemetryczne dla analizy ruchu wyszukiwania

Analiza ruchu wyszukiwania jest wzorcem do zbierania danych telemetrycznych dotyczących interakcji użytkowników z aplikacją Wyszukiwanie poznawcze platformy Azure, takich jak zdarzenia inicjowane przez użytkownika i dane wejściowe z klawiatury. Korzystając z tych informacji, można określić efektywność rozwiązania wyszukiwania, w tym popularne terminy wyszukiwania, częstotliwość kliknięć i dane wejściowe zapytania, które zwracają zero wyników.

Ten wzorzec przyjmuje zależność od [Application Insights](../azure-monitor/app/app-insights-overview.md) (funkcji [Azure monitor](../azure-monitor/index.yml)) do zbierania danych użytkownika. Należy dodać instrumentację do kodu klienta, zgodnie z opisem w tym artykule. Na koniec konieczne będzie przeanalizowanie danych przy użyciu mechanizmu raportowania. Zalecamy Power BI, ale możesz użyć pulpitu nawigacyjnego aplikacji lub dowolnego narzędzia, które nawiązuje połączenie z Application Insights.

> [!NOTE]
> Wzorzec opisany w tym artykule dotyczy zaawansowanych scenariuszy i strumienia kliknięć danych generowanych przez kod dodawany do klienta. W przeciwieństwie do tego dzienniki usługi są łatwe w konfiguracji, udostępniają szereg metryk i mogą być wykonywane w portalu bez konieczności wykonywania kodu. Włączenie rejestrowania jest zalecane w przypadku wszystkich scenariuszy. Aby uzyskać więcej informacji, zobacz [zbieranie i analizowanie danych dziennika](search-monitor-logs.md).

## <a name="identify-relevant-search-data"></a>Zidentyfikuj odpowiednie dane wyszukiwania

Aby uzyskać przydatne metryki dotyczące analizy ruchu wyszukiwania, należy rejestrować niektóre sygnały od użytkowników aplikacji wyszukiwania. Sygnalizują one zawartość, którą interesują użytkownicy, i że uznają je za istotne. Do analizy ruchu wyszukiwania należą:

+ Zdarzenia wyszukiwania generowane przez użytkownika: interesujące są tylko zapytania wyszukiwania inicjowane przez użytkownika. Żądania wyszukiwania używane do wypełniania aspektów, dodatkowej zawartości lub wszelkich informacji wewnętrznych nie są ważne i powodują pochylenie i odchylenia wyników.

+ Zdarzenia kliknięcia generowane przez użytkownika: na stronie wyników wyszukiwania zdarzenie kliknięcia zazwyczaj oznacza, że dokument jest odpowiednim wynikiem dla konkretnego zapytania wyszukiwania.

Łącząc wyszukiwanie i klikaj zdarzenia z IDENTYFIKATORem korelacji, uzyskasz dokładniejsze informacje o tym, jak dobrze działa funkcja wyszukiwania aplikacji.

## <a name="add-search-traffic-analytics"></a>Dodawanie analizy ruchu wyszukiwania

Na stronie [portalu](https://portal.azure.com) usługi Azure wyszukiwanie poznawcze strona wyszukiwania Analiza ruchu zawiera arkusz Ściągawka, który jest następujący: wzorzec telemetrii. Na tej stronie można wybrać lub utworzyć zasób Application Insights, pobrać klucz instrumentacji, skopiować fragmenty kodu, które można dostosować do rozwiązania, a następnie pobrać raport Power BI, który jest skompilowany na podstawie schematu odzwierciedlonego we wzorcu.

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

W języku C# **InstrumentationKey** znajduje się w konfiguracji aplikacji, na przykład appsettings.js, jeśli projekt jest ASP.NET. Zapoznaj się z powrotem z instrukcjami rejestracji, jeśli nie masz pewności co do lokalizacji klucza.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**Korzystanie z języka JavaScript**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>Krok 2. żądanie identyfikatora wyszukiwania dla korelacji

Aby skorelować żądania wyszukiwania z kliknięciami, należy mieć identyfikator korelacji, który odnosi się do tych dwóch oddzielnych zdarzeń. Usługa Azure Wyszukiwanie poznawcze zapewnia identyfikator wyszukiwania, gdy zostanie on zażądany przy użyciu nagłówka HTTP.

Identyfikator wyszukiwania umożliwia korelację metryk emitowanych przez Wyszukiwanie poznawcze platformy Azure dla samego żądania, z niestandardowymi metrykami, które są rejestrowane Application Insights.  

**Korzystanie z języka C#**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

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
> Zażądaj liczby zapytań generowanych przez użytkownika, dodając do zapytania wyszukiwania $count = true. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie dokumentów (REST)](/rest/api/searchservice/search-documents#counttrue--false).
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

1. W lewym okienku nawigacyjnym pulpitu nawigacyjnego usługi Azure Wyszukiwanie poznawcze w obszarze **Ustawienia**kliknij pozycję **Analiza ruchu wyszukiwania**.

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