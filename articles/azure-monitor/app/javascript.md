---
title: Application Insights platformy Azure dla aplikacji sieci Web w języku JavaScript
description: Pobierz liczbę wyświetlanych stron i sesji, dane klienta sieci Web, aplikacje jednostronicowe (SPA) i śledź wzorce użycia. Wykrywanie wyjątków i problemów z wydajnością na stronach sieci Web w języku JavaScript.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 50ce0d57ec7395c69bf65e41b67f0cb005a43cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854981"
---
# <a name="application-insights-for-web-pages"></a>Usługa Application Insights dla stron sieci Web

Poznaj wydajność i użycie strony sieci Web lub aplikacji. Jeśli dodasz [Application Insights](app-insights-overview.md) do skryptu strony, uzyskasz chronometraż ładowania strony i wywołań AJAX, liczniki i szczegóły dotyczące wyjątków przeglądarki i błędów AJAX, a także liczby użytkowników i sesji. Wszystkie te dane możesz rozdzielić według strony, systemu operacyjnego klienta i wersji przeglądarki, lokalizacji geograficznej i innych wymiarów. Możesz ustawić alerty związane z liczbami błędów lub powolnym ładowaniem strony. A wstawiając wywołania śledzenia w kodzie JavaScript, możesz śledzić sposób użycia różnych funkcji aplikacji strony sieci Web.

Usługi Application Insights można używać z dowolnymi stronami sieci Web — wystarczy dodać krótki fragment kodu JavaScript. Jeśli usługa sieci Web jest w [języku Java](java-get-started.md) lub [ASP.NET](asp-net.md), można użyć zestawów SDK po stronie serwera w połączeniu z zestawem JavaScript SDK po stronie klienta, aby uzyskać kompleksowe zrozumienie wydajności aplikacji.

## <a name="adding-the-javascript-sdk"></a>Dodawanie zestawu SDK języka JavaScript

1. Najpierw potrzebujesz zasobu Application Insights. Jeśli nie masz jeszcze klucza zasobu i instrumentacji, postępuj zgodnie z [instrukcjami dotyczącymi tworzenia nowego zasobu](create-new-resource.md).
2. Skopiuj klucz Instrumentacji z zasobu, w którym chcesz wysłać dane telemetryczne języka JavaScript.
3. Dodaj Application Insights JavaScript SDK do strony lub aplikacji sieci Web przy użyciu jednej z następujących dwóch opcji:
    * [Konfiguracja npm](#npm-based-setup)
    * [Fragment kodu JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Użyj tylko jednej metody, aby dodać zestaw SDK języka JavaScript do aplikacji. Jeśli używasz instalatora NPM, nie używaj fragmentu kodu i odwrotnie.

> [!NOTE]
> Instalator NPM instaluje zestaw JavaScript SDK jako zależność do projektu, włączając IntelliSense, podczas gdy fragment kodu pobiera zestaw SDK w czasie wykonywania. Obie obsługują te same funkcje. Jednak deweloperzy, którzy chcą uzyskać więcej niestandardowych zdarzeń i konfiguracji, zwykle wybierają ustawienia NPM, którzy użytkownicy poszukują szybkiego włączania dla tego fragmentu kodu.

### <a name="npm-based-setup"></a>Konfiguracja oparta na npm

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Konfiguracja oparta na fragmentach kodu

Jeśli aplikacja nie korzysta z npm, możesz bezpośrednio instrumentować strony sieci Web za pomocą Application Insights, wklejając ten fragment w górnej części każdej strony. Najlepiej, gdy powinien to być pierwszy skrypt w `<head>` sekcji, aby mógł monitorować ewentualne potencjalne problemy ze wszystkimi zależnościami. Jeśli używasz aplikacji Blazor Server, Dodaj fragment kodu w górnej części pliku `_Host.cshtml` w `<head>` sekcji.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Wysyłanie danych telemetrycznych do Azure Portal

Domyślnie Application Insights zestaw SDK języka JavaScript zbiera wiele elementów telemetrycznych, które są przydatne podczas określania kondycji aplikacji i środowiska użytkownika. Należą do nich następujące elementy:

- **Nieprzechwycone wyjątki** w aplikacji, w tym informacje o
    - Ślad stosu
    - Szczegóły wyjątku i komunikat z błędem
    - Numer kolumny & wiersza (error)
    - Adres URL, pod który został zgłoszony błąd
- Żądania **zależności sieci** wykonane przez aplikację **XHR** i **Fetch** (pobieranie kolekcji jest wyłączone domyślnie) żądania, zawierają informacje
    - Adres URL źródła zależności
    - Metoda & polecenia używana do żądania zależności
    - Czas trwania żądania
    - Kod wyniku i stan powodzenia żądania
    - Identyfikator (jeśli istnieje) użytkownika wysyłającego żądanie
    - Kontekst korelacji (jeśli istnieje), w którym jest wykonywane żądanie
- **Informacje o użytkowniku** (na przykład lokalizacja, Sieć, adres IP)
- **Informacje o urządzeniu** (na przykład przeglądarka, system operacyjny, wersja, język, model)
- **Informacje o sesji**

### <a name="telemetry-initializers"></a>Inicjatory telemetrii
Inicjatory telemetrii są używane do modyfikowania zawartości zebranej telemetrii przed wysłaniem z przeglądarki użytkownika. Można ich również użyć, aby zapobiec wysyłaniu pewnych danych telemetrycznych przez `false`zwrócenie. Do wystąpienia Application Insights można dodać wiele inicjatorów telemetrii i są one wykonywane w celu ich dodania.

Argument wejściowy `addTelemetryInitializer` jest wywołaniem zwrotnym, które przyjmuje [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) jako argument i zwraca `boolean` lub. `void` W przypadku `false`powrotu element telemetrii nie jest wysyłany, w przeciwnym razie przechodzi do następnego inicjatora telemetrii, jeśli istnieje lub jest wysyłany do punktu końcowego zbierania danych telemetrycznych.

Przykład użycia inicjatorów telemetrii:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfigurowanie
Większość pól konfiguracji ma takie nazwy, że można je domyślnie określić jako FAŁSZ. Wszystkie pola są opcjonalne z wyjątkiem `instrumentationKey`.

| Nazwa | Domyślny | Opis |
|------|---------|-------------|
| instrumentationKey | wartość null | **Wymagane**<br>Klucz Instrumentacji uzyskany w Azure Portal. |
| accountId | wartość null | Opcjonalny identyfikator konta, jeśli aplikacja grupuje użytkowników na kontach. Bez spacji, przecinków, średników, równości lub pionowych słupków |
| sessionRenewalMs | 1800000 | Sesja jest rejestrowana, jeśli użytkownik jest nieaktywny przez ten czas w milisekundach. Wartość domyślna to 30 minut |
| sessionExpirationMs | 86400000 | Sesja jest rejestrowana, jeśli będzie trwać przez ten czas w milisekundach. Wartość domyślna to 24 godziny |
| maxBatchSizeInBytes | 10 000 | Maksymalny rozmiar wsadu danych telemetrycznych. Jeśli partia przekracza ten limit, zostanie ona natychmiast wysłana i zostanie uruchomiona nowa Partia zadań |
| maxBatchInterval | 15000 | Jak długo należy wykonać partię danych telemetrycznych przed wysłaniem (w milisekundach) |
| disableExceptionTracking | fałsz | W przypadku wartości true wyjątki nie są zbierane. Wartość domyślna to false. |
| disableTelemetry | fałsz | Jeśli wartość jest równa true, dane telemetryczne nie są zbierane ani wysyłane. Wartość domyślna to false. |
| enableDebug | fałsz | W przypadku wartości true dane debugowania **wewnętrznego** są generowane jako wyjątek **zamiast** rejestrowania, niezależnie od ustawień rejestrowania zestawu SDK. Wartość domyślna to false. <br>***Uwaga:*** Włączenie tego ustawienia spowoduje porzucenie danych telemetrycznych w przypadku wystąpienia błędu wewnętrznego. Może to być przydatne do szybkiego identyfikowania problemów z konfiguracją lub użyciem zestawu SDK. Jeśli nie chcesz utracić danych telemetrycznych podczas debugowania, rozważ użycie `consoleLoggingLevel` lub `telemetryLoggingLevel` zamiast `enableDebug`. |
| loggingLevelConsole | 0 | Rejestruje **wewnętrzne** błędy Application Insights w konsoli programu. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) |
| loggingLevelTelemetry | 1 | Wysyła **wewnętrzne** błędy Application Insights jako dane telemetryczne. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) |
| diagnosticLogInterval | 10 000 | wewnętrz Interwał sondowania (w MS) dla wewnętrznej kolejki rejestrowania |
| samplingPercentage | 100 | Procent zdarzeń, które będą wysyłane. Wartość domyślna to 100, co oznacza, że wszystkie zdarzenia są wysyłane. Ustaw tę opcję, jeśli chcesz zachować limit danych dla aplikacji o dużej skali. |
| autoTrackPageVisitTime | fałsz | Jeśli wartość jest równa true, w pageview czas wyświetlania poprzedniej strony z instrumentacją jest śledzony i wysyłany jako Telemetria i uruchamiany jest nowy czasomierz dla bieżącego pageview. Wartość domyślna to false. |
| disableAjaxTracking | fałsz | W przypadku wartości true wywołania AJAX nie są autozbierane. Wartość domyślna to false. |
| disableFetchTracking | true | W przypadku wartości true żądania pobrania nie są gromadzone w sposób autozbierany. Wartość domyślna to true |
| overridePageViewDuration | fałsz | Jeśli wartość jest równa true, domyślne zachowanie trackPageView jest zmieniane na koniec interwału czasu trwania wyświetlania strony w przypadku wywołania trackPageView. Jeśli wartość jest równa false i nie ma niestandardowego czasu trwania do trackPageView, wydajność wyświetlania stron jest obliczana przy użyciu interfejsu API czasu nawigacji. Wartość domyślna to false. |
| maxAjaxCallsPerView | 500 | Domyślnie 500 — określa, ile wywołań AJAX będzie monitorowanych dla każdego widoku strony. Ustaw wartość-1, aby monitorować wszystkie (nieograniczone) wywołania AJAX na stronie. |
| disableDataLossAnalysis | true | W przypadku wartości false bufory wewnętrznych nadawców telemetrii będą sprawdzane przy uruchamianiu elementów, które nie zostały jeszcze wysłane. |
| disableCorrelationHeaders | fałsz | W przypadku wartości false zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań zależności w celu skorelowania ich z odpowiednimi żądaniami po stronie serwera. Wartość domyślna to false. |
| correlationHeaderExcludedDomains |  | Wyłącz nagłówki korelacji dla określonych domen |
| correlationHeaderDomains |  | Włącz nagłówki korelacji dla określonych domen |
| disableFlushOnBeforeUnload | fałsz | Wartość domyślna to false. W przypadku wartości true Metoda Flush nie zostanie wywołana, gdy wyzwalane są wyzwalacze zdarzeń onBeforeUnload |
| enableSessionStorageBuffer | true | Wartość domyślna to true. W przypadku wartości true bufor z całą niewysłaną telemetrię jest przechowywany w magazynie sesji. Bufor jest przywracany podczas ładowania strony |
| isCookieUseDisabled | fałsz | Wartość domyślna to false. Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywać ani odczytywać żadnych danych z plików cookie.|
| cookieDomain | wartość null | Niestandardowa domena plików cookie. Jest to przydatne, jeśli chcesz udostępnić pliki cookie Application Insights w poddomenach. |
| isRetryDisabled | fałsz | Wartość domyślna to false. W przypadku wartości false ponów próbę 206 (częściowe powodzenie), 408 (limit czasu), 429 (zbyt wiele żądań), 500 (wewnętrzny błąd serwera), 503 (Usługa niedostępna) i 0 (offline, tylko jeśli wykryto) |
| isStorageUseDisabled | fałsz | Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywał ani nie odczytał żadnych danych z magazynu lokalnego i w ramach sesji. Wartość domyślna to false. |
| isBeaconApiDisabled | true | Jeśli wartość jest równa false, zestaw SDK wyśle wszystkie dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | fałsz | Wartość domyślna to false. gdy karta zostanie ZAMKNIĘTA, zestaw SDK wyśle wszystkie pozostałe dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) |
| sdkExtension | wartość null | Ustawia nazwę rozszerzenia zestawu SDK. Dozwolone są tylko znaki alfanumeryczne. Nazwa rozszerzenia jest dodawana jako prefiks do tagu "AI. Internal. sdkVersion" (na przykład "ext_javascript: 2.0.0"). Wartość domyślna to null. |
| isBrowserLinkTrackingEnabled | fałsz | Wartość domyślna to false. Jeśli wartość jest równa true, zestaw SDK będzie śledził wszystkie żądania [linku do przeglądarki](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) . |
| appId | wartość null | Identyfikator AppId jest używany dla korelacji między zależnościami AJAX, które są wykonywane po stronie klienta z żądaniami po stronie serwera. Gdy interfejs API sygnałów jest włączony, nie można go użyć automatycznie, ale można ustawić go ręcznie w konfiguracji. Wartość domyślna to null |
| enableCorsCorrelation | fałsz | Jeśli wartość jest równa true, zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań CORS do skorelowania wychodzących zależności AJAX z odpowiednimi żądaniami po stronie serwera. Wartość domyślna to false |
| namePrefix | definicji | Opcjonalna wartość, która będzie używana jako przyrostkowa nazwa dla localStorage i nazwy pliku cookie.
| enableAutoRouteTracking | fałsz | Automatyczne śledzenie zmian trasy w aplikacjach jednostronicowych (SPA). Jeśli wartość jest równa true, każda zmiana trasy wyśle nowy pageview do Application Insights. Zmiany trasy mieszania (`example.com/foo#bar`) są również rejestrowane jako nowe widoki stron.
| enableRequestHeaderTracking | fałsz | W przypadku wartości true są śledzone nagłówki żądań dla programu AJAX &. wartość domyślna to false.
| enableResponseHeaderTracking | fałsz | W przypadku wartości true są śledzone nagłówki odpowiedzi żądania pobrania & AJAX, wartość domyślna to false.
| distributedTracingMode | `DistributedTracingModes.AI` | Ustawia tryb śledzenia rozproszonego. Jeśli ustawiono tryb AI_AND_W3C lub tryb W3C, nagłówki kontekstowe śledzenia W3C (traceparent/tracestate) zostaną wygenerowane i uwzględnione we wszystkich żądaniach wychodzących. AI_AND_W3C jest zapewniana pod kątem zgodności z poprzednimi wersjami Application Insights usługi Instrumentacji.

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Domyślnie ten zestaw SDK **nie** będzie obsługiwał zmiany trasy opartej na stanie, która występuje w aplikacjach jednostronicowych. Aby włączyć automatyczne śledzenie zmian trasy dla aplikacji jednostronicowej, możesz dodać `enableAutoRouteTracking: true` do konfiguracji instalacji.

Obecnie oferujemy oddzielną [wtyczkę reakcji](#react-extensions), którą można zainicjować za pomocą tego zestawu SDK. Spowoduje to również przekazanie śledzenia zmian trasy, a także zebranie [innych danych telemetrycznych związanych z reagowaniem](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

> [!NOTE]
> Używaj `enableAutoRouteTracking: true` tylko wtedy, gdy **nie** używasz wtyczki do reagowania. Oba są w stanie wysyłać nowe PageViews w przypadku zmiany trasy. Jeśli oba są włączone, może być wysyłanych zduplikowanych PageViews.

## <a name="configuration-autotrackpagevisittime"></a>Konfiguracja: autoTrackPageVisitTime

Przez ustawienie `autoTrackPageVisitTime: true`czas, przez jaki użytkownik spędza na każdej stronie, jest śledzony. Na każdym nowym PageView czas trwania, przez który użytkownik spędził na *poprzedniej* stronie, jest wysyłany jako [Metryka niestandardowa](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview) o nazwie `PageVisitTime`. Ta Metryka niestandardowa jest wyświetlana w [Eksplorator metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) jako "Metryka oparta na dzienniku".

## <a name="react-extensions"></a>Przereaguj rozszerzenia

| Rozszerzenia |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Zareaguj na natywny](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Eksplorowanie danych po stronie przeglądarki i klienta

Dane przeglądarki/strony klienta można wyświetlać, przechodząc do **metryk** i dodając indywidualne metryki, które Cię interesują:

![](./media/javascript/page-view-load-time.png)

Możesz również wyświetlać dane z zestawu SDK języka JavaScript za pomocą środowiska przeglądarki w portalu.

Wybierz pozycję **przeglądarka** , a następnie wybierz pozycję **Błędy** lub **wydajność**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Wydajność

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Zależności

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analiza

Aby wysłać zapytanie do telemetrii zebranej przez zestaw JavaScript SDK, wybierz przycisk **Wyświetl w dziennikach (analiza)** . Po dodaniu `where` instrukcji programu `client_Type == "Browser"`widoczne będą tylko dane z zestawu SDK języka JavaScript, a wszystkie Telemetria po stronie serwera zebrane przez inne zestawy SDK zostaną wykluczone.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Obsługa mapy źródłowej

Zminimalizowanego stosu wywołań danych telemetrycznych dotyczących wyjątków może być unminified w Azure Portal. Wszystkie istniejące integracje w panelu Szczegóły wyjątku będą działały z nowo unminified stosu wywołań.

#### <a name="link-to-blob-storage-account"></a>Link do konta magazynu obiektów BLOB

Możesz połączyć zasób Application Insights z własnym kontenerem Blob Storage platformy Azure, aby automatycznie unminify stosy wywołań. Aby rozpocząć, zobacz [Automatyczne wsparcie mapy źródłowej](./source-map-support.md).

### <a name="drag-and-drop"></a>Przeciągnij i upuść

1. Wybierz element telemetrii wyjątku w Azure Portal, aby wyświetlić jego "kompleksowe" Szczegóły transakcji.
2. Określ, które mapy źródłowe odpowiadają temu stosowi wywołań. Mapa źródłowa musi być zgodna z plikiem źródłowym ramki stosu, ale z sufiksem`.map`
3. Przeciągnij i upuść mapy źródłowe na stos wywołań w Azure Portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Aby zapoznać się z lekkim doświadczeniem, możesz zamiast tego zainstalować wersję podstawową Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ta wersja zawiera minimalną liczbę funkcji i funkcjonalności systemu operacyjnego i polega na tym, że można ją skompilować w taki sam sposób, jak to możliwe. Na przykład nie wykonuje autokolekcjonowania (nieprzechwycone wyjątki, AJAX itp.). Interfejsy API służące do wysyłania określonych typów telemetrii, takich jak `trackTrace`, `trackException`itp., nie są uwzględnione w tej wersji, więc konieczne będzie udostępnienie własnego otoki. Jedynym dostępnym interfejsem API jest `track`. [Przykład](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) znajduje się tutaj.

## <a name="examples"></a>Przykłady

Przykłady możliwy do uruchomienia można znaleźć w temacie [Application Insights przykłady kodu JavaScript SDK](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uaktualnianie ze starszej wersji Application Insights

Istotne zmiany w wersji zestawu SDK V2:
- Aby umożliwić lepsze sygnatury interfejsu API, niektóre wywołania interfejsu API, takie jak trackPageView i trackexception, zostały zaktualizowane. Uruchamianie w programie Internet Explorer 8 i wcześniejszych wersjach przeglądarki nie jest obsługiwane.
- Koperta telemetrii ma zmiany nazwy pola i struktury ze względu na aktualizacje schematu danych.
- `context.operation` Przeniesiono `context.telemetryTrace`do. Niektóre pola zostały również zmienione (`operation.id` --> `telemetryTrace.traceID`).
  - Aby ręcznie odświeżyć bieżący identyfikator pageview (na przykład w aplikacjach SPA), użyj `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`.
    > [!NOTE]
    > Aby zachować unikatowy identyfikator śledzenia, z którego wcześniej korzystano `Util.newId()`, teraz Użyj `Util.generateW3CId()`. Oba ostatecznie kończą się IDENTYFIKATORem operacji.

Jeśli używasz bieżącego zestawu SDK PRODUKCYJNEgo usługi Application Insights (1.0.20) i chcesz sprawdzić, czy nowy zestaw SDK działa w środowisku uruchomieniowym, zaktualizuj adres URL w zależności od bieżącego scenariusza ładowania zestawu SDK.

- Pobierz za pośrednictwem scenariusza usługi CDN: zaktualizuj fragment kodu, którego obecnie używasz, aby wskazać następujący adres URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- scenariusz npm: Wywołaj `downloadAndSetup` pobieranie pełnego skryptu ApplicationInsights z usługi CDN i zainicjuj go za pomocą klucza Instrumentacji:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Test w środowisku wewnętrznym, aby sprawdzić, czy dane telemetryczne monitorowania działają zgodnie z oczekiwaniami. Jeśli wszystko działa, zaktualizuj podpisy interfejsów API odpowiednio do wersji zestawu SDK V2 i Wdróż ją w środowiskach produkcyjnych.

## <a name="sdk-performanceoverhead"></a>Wydajność/obciążenie zestawu SDK

Po zaledwie 25 KB formacie gzip i zainicjowaniu tylko ~ 15 MS, Application Insights dodaje nieznaczną ilość loadtime do witryny sieci Web. Za pomocą fragmentu kodu można szybko ładować minimalne składniki biblioteki. W międzyczasie pełny skrypt zostanie pobrany w tle.

Podczas pobierania skryptu z usługi CDN wszystkie śledzenie strony jest umieszczane w kolejce. Gdy pobrany skrypt zakończy asynchroniczne inicjowanie, wszystkie zdarzenia, które zostały dodane do kolejki, są śledzone. W związku z tym nie utracisz żadnych danych telemetrycznych w całym cyklu życia strony. Ten proces instalacji zapewnia użytkownikom niezauważalny system, niewidoczny dla użytkowników.

> Podsumowanie:
> - **25 KB** formacie gzip
> - Całkowity czas inicjowania ( **15 MS** )
> - Brak śledzenia **zerowej** w cyklu życia strony

## <a name="browser-support"></a>Obsługa przeglądarki

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Najnowsza ✔ Chrome |  Najnowsza ✔ Firefox | Przeglądarka IE 9 + & Edge ✔ | Najnowsza ✔ | Przeglądarka Safari Najnowsza ✔ |

## <a name="open-source-sdk"></a>Zestaw SDK open source

Application Insights zestawu SDK języka JavaScript jest otwartym źródłem do wyświetlania kodu źródłowego lub do współtworzenia projektu, odwiedzając [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>Następne kroki
* [Śledzenie użycia](usage-overview.md)
* [Niestandardowe zdarzenia i metryki](api-custom-events-metrics.md)
* [Tworzenie — pomiar— nauka](usage-overview.md)
