---
title: Application Insights platformy Azure dla aplikacji sieci Web w języku JavaScript
description: Pobierz liczbę wyświetlanych stron i sesji, dane klienta sieci Web, aplikacje jednostronicowe (SPA) i śledź wzorce użycia. Wykrywanie wyjątków i problemów z wydajnością na stronach sieci Web w języku JavaScript.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 04cda044b002e226c49f8647d4705d7c0f2a514e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565269"
---
# <a name="application-insights-for-web-pages"></a>Usługa Application Insights dla stron sieci Web

Poznaj wydajność i użycie strony sieci Web lub aplikacji. Jeśli dodasz [Application Insights](app-insights-overview.md) do skryptu strony, uzyskasz chronometraż ładowania strony i wywołań AJAX, liczniki i szczegóły dotyczące wyjątków przeglądarki i błędów AJAX, a także liczby użytkowników i sesji. Wszystkie te dane możesz rozdzielić według strony, systemu operacyjnego klienta i wersji przeglądarki, lokalizacji geograficznej i innych wymiarów. Możesz ustawić alerty związane z liczbami błędów lub powolnym ładowaniem strony. A wstawiając wywołania śledzenia w kodzie JavaScript, możesz śledzić sposób użycia różnych funkcji aplikacji strony sieci Web.

Usługi Application Insights można używać z dowolnymi stronami sieci Web — wystarczy dodać krótki fragment kodu JavaScript. Jeśli usługa sieci Web jest w [języku Java](java-get-started.md) lub [ASP.NET](asp-net.md), można użyć zestawów SDK po stronie serwera w połączeniu z zestawem JavaScript SDK po stronie klienta, aby uzyskać kompleksowe zrozumienie wydajności aplikacji.

## <a name="adding-the-javascript-sdk"></a>Dodawanie zestawu SDK języka JavaScript

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia parametrów połączenia zamiast kluczy Instrumentacji. [Parametry połączenia](./sdk-connection-string.md?tabs=js) identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.

1. Najpierw potrzebujesz zasobu Application Insights. Jeśli nie masz jeszcze klucza zasobu i instrumentacji, postępuj zgodnie z [instrukcjami dotyczącymi tworzenia nowego zasobu](create-new-resource.md).
2. Skopiuj _klucz Instrumentacji_ (znany również jako "iKey") lub [Parametry połączenia](#connection-string-setup) dla zasobu, w którym chcesz wysłać dane telemetryczne języka JavaScript (z kroku 1). Zostanie on dodany do `instrumentationKey` `connectionString` ustawienia lub Application Insights zestawu SDK języka JavaScript.
3. Dodaj Application Insights JavaScript SDK do strony lub aplikacji sieci Web przy użyciu jednej z następujących dwóch opcji:
    * [Konfiguracja npm](#npm-based-setup)
    * [Fragment kodu JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Użyj tylko jednej metody, aby dodać zestaw SDK języka JavaScript do aplikacji. Jeśli używasz instalatora NPM, nie używaj fragmentu kodu i odwrotnie.

> [!NOTE]
> Instalator NPM instaluje zestaw JavaScript SDK jako zależność do projektu, włączając IntelliSense, podczas gdy fragment kodu pobiera zestaw SDK w czasie wykonywania. Obie obsługują te same funkcje. Jednak deweloperzy, którzy chcą uzyskać więcej niestandardowych zdarzeń i konfiguracji, zwykle wybierają ustawienia NPM, którzy użytkownicy poszukują szybkiego włączania dla tego fragmentu kodu.

### <a name="npm-based-setup"></a>Konfiguracja oparta na npm

Zainstaluj za pośrednictwem NPM.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **W tym pakiecie są zawarte tekst**, dlatego **nie** trzeba instalować oddzielnego pakietu wpisywania.
    
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

Jeśli aplikacja nie korzysta z npm, możesz bezpośrednio instrumentować strony sieci Web za pomocą Application Insights, wklejając ten fragment w górnej części każdej strony. Najlepiej, gdy powinien to być pierwszy skrypt w `<head>` sekcji, aby mógł monitorować ewentualne potencjalne problemy ze wszystkimi zależnościami i opcjonalnie dowolnymi błędami języka JavaScript. Jeśli używasz aplikacji Blazor Server, Dodaj fragment kodu w górnej części pliku `_Host.cshtml` w `<head>` sekcji.

Aby pomóc w śledzeniu wersji fragmentu używanej przez aplikację, rozpoczynając od wersji 2.5.5 zdarzenie wyświetlania strony będzie zawierać nowy tag "AI. Internal. fragment" zawierający zidentyfikowaną wersję fragmentu kodu.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Aby można było uzyskać czytelność i zmniejszyć liczbę błędów języka JavaScript, wszystkie możliwe opcje konfiguracji są wyświetlane w nowym wierszu kodu wstawki powyżej, jeśli nie chcesz zmieniać wartości w wierszu z komentarzem, możesz go usunąć.


#### <a name="reporting-script-load-failures"></a>Raportowanie błędów ładowania skryptu

Ta wersja fragmentu kodu wykrywa i zgłasza błędy podczas ładowania zestawu SDK z sieci CDN jako wyjątek do portalu Azure Monitor (w obszarze &gt; &gt; przeglądarki wyjątków) ten wyjątek zapewnia wgląd w błędy tego typu, aby mieć świadomość, że aplikacja nie zgłasza danych telemetrycznych (lub innych wyjątków) zgodnie z oczekiwaniami. Ten sygnał jest ważnym pomiarem w związku z utratą danych telemetrycznych, ponieważ nie załadowano ani zainicjować zestawu SDK, który może prowadzić do:
- W obszarze Raportowanie, w jaki sposób użytkownicy korzystają z witryny (lub próbować korzystać z niej).
- Brak telemetrii dotyczącej sposobu, w jaki użytkownicy końcowi korzystają z witryny;
- Brak błędów języka JavaScript, które mogłyby spowodować pomyślne zablokowanie użytkowników końcowych przy użyciu Twojej witryny.

Szczegóły dotyczące tego wyjątku można znaleźć na stronie Rozwiązywanie problemów z [błędem ładowania zestawu SDK](javascript-sdk-load-failure.md) .

Raportowanie tego błędu jako wyjątek do portalu nie korzysta z opcji konfiguracji ```disableExceptionTracking``` z konfiguracji usługi Application Insights. w związku z tym, jeśli ten błąd wystąpi, zawsze będzie raportowany przez fragment kodu, nawet jeśli obsługa funkcji Window. OnError została wyłączona.

Raportowanie błędów ładowania zestawu SDK nie jest jawnie obsługiwane w programie IE 8 (lub mniej). Pomaga to zmniejszyć rozmiar zminimalizowanego fragmentu kodu przez założenie, że większość środowisk nie ma wyłącznie programu IE 8 lub mniej. Jeśli masz to wymaganie, a chcesz otrzymywać te wyjątki, musisz dołączyć do pobrania Poly wypełnienie lub utworzyć własną wersję fragmentu, która używa ```XDomainRequest``` zamiast tego ```XMLHttpRequest``` , zaleca się użycie [dostarczonego kodu źródłowego fragmentu](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) jako punktu początkowego.

> [!NOTE]
> Jeśli używasz wcześniejszej wersji fragmentu kodu, zdecydowanie zalecamy zaktualizowanie do najnowszej wersji, aby otrzymywać te, które wcześniej nieraportowane problemy.

#### <a name="snippet-configuration-options"></a>Opcje konfiguracji fragmentów kodu

Wszystkie opcje konfiguracji zostały teraz przeniesione do końca skryptu, aby zapobiec przypadkowemu wprowadzeniu błędów JavaScript, które nie spowodują załadowania zestawu SDK, ale również spowodują wyłączenie raportowania błędu.

Każda opcja konfiguracji jest pokazana powyżej w nowym wierszu, jeśli nie chcesz przesłonić wartości domyślnej elementu wymienionego jako [opcjonalne], możesz usunąć ten wiersz, aby zminimalizować rozmiar wynikowej strony zwracanej.

Dostępne opcje konfiguracji to
 
| Nazwa | Typ | Opis
|------|------|----------------
| src | ciąg **[wymagany]** | Pełny adres URL, z którego ma zostać załadowany zestaw SDK. Ta wartość jest używana dla atrybutu "src" dynamicznie dodanego &lt; skryptu/ &gt; tagu. Możesz użyć publicznej lokalizacji usługi CDN lub własnej, hostowanej prywatnie.
| name | ciąg *[opcjonalny]* | Globalna nazwa dla zainicjowanego zestawu SDK, domyślnie `appInsights` . W związku z ```window.appInsights``` tym będzie to odwołanie do zainicjowanego wystąpienia. Uwaga: Jeśli podano wartość nazwy lub poprzednie wystąpienie zostanie przydzielone (za pośrednictwem nazwy globalnej appInsightsSDK), ta wartość nazwy również będzie zdefiniowana w globalnej przestrzeni nazw jako ```window.appInsightsSDK=<name value>``` , jest to wymagane przez kod inicjalizacji zestawu SDK, aby upewnić się, że inicjuje i aktualizuje poprawność szkieletu i metody proxy.
| LD | Liczba w MS *[opcjonalne]* | Definiuje opóźnienie ładowania oczekiwania przed próbą załadowania zestawu SDK. Wartość domyślna to 0ms, a każda wartość ujemna natychmiast doda tag skryptu do &lt; &gt; regionu głównego strony, co spowoduje zablokowanie zdarzenia ładowania strony do momentu załadowania skryptu (lub niepowodzenia).
| useXhr | wartość logiczna *[opcjonalnie]* | To ustawienie jest używane tylko w przypadku błędów ładowania zestawu SDK raportowania. Funkcja raportowania najpierw podejmie próbę użycia opcji Pobierz (), jeśli jest dostępna, a następnie powraca do XHR, co spowoduje, że ustawienie tej wartości na true spowoduje jedynie Pominięcie sprawdzania pobierania. Użycie tej wartości jest wymagane tylko wtedy, gdy aplikacja jest używana w środowisku, w którym pobieranie nie powiedzie się.
| crossOrigin | ciąg *[opcjonalny]* | Dołączając to ustawienie, tag skryptu dodany do pobrania zestawu SDK będzie zawierać atrybut crossOrigin o tej wartości ciągu. Gdy nie jest zdefiniowany (wartość domyślna), nie jest dodawany atrybut crossOrigin. Zalecane wartości nie są zdefiniowane (wartość domyślna); ""; lub "Anonymous" (dla wszystkich prawidłowych wartości zobacz [HTML Attribute `crossorigin` :](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) Documentation)
| cfg | Obiekt **[wymagany]** | Konfiguracja przeniesiona do Application Insights SDK podczas inicjowania.

### <a name="connection-string-setup"></a>Konfiguracja parametrów połączenia

Dla Instalatora NPM lub fragmentu można również skonfigurować wystąpienie Application Insights przy użyciu parametrów połączenia. Po prostu Zastąp `instrumentationKey` pole polem `connectionString` .
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Wysyłanie danych telemetrycznych do Azure Portal

Domyślnie Application Insights zestaw SDK języka JavaScript zbiera wiele elementów telemetrycznych, które są przydatne podczas określania kondycji aplikacji i środowiska użytkownika. Są one następujące:

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
Inicjatory telemetrii są używane do modyfikowania zawartości zebranej telemetrii przed wysłaniem z przeglądarki użytkownika. Można ich również użyć, aby zapobiec wysyłaniu pewnych danych telemetrycznych przez zwrócenie `false` . Do wystąpienia Application Insights można dodać wiele inicjatorów telemetrii i są one wykonywane w celu ich dodania.

Argument wejściowy `addTelemetryInitializer` jest wywołaniem zwrotnym, które przyjmuje [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) jako argument i zwraca `boolean` lub `void` . W przypadku powrotu `false` element telemetrii nie jest wysyłany, w przeciwnym razie przechodzi do następnego inicjatora telemetrii, jeśli istnieje lub jest wysyłany do punktu końcowego zbierania danych telemetrycznych.

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
Większość pól konfiguracji ma takie nazwy, że można je domyślnie określić jako FAŁSZ. Wszystkie pola są opcjonalne z wyjątkiem `instrumentationKey` .

| Nazwa | Opis | Domyślne |
|------|-------------|---------|
| instrumentationKey | **Wymagane**<br>Klucz Instrumentacji uzyskany w Azure Portal. | ciąg<br/>null |
| accountId | Opcjonalny identyfikator konta, jeśli aplikacja grupuje użytkowników na kontach. Bez spacji, przecinków, średników, równości lub pionowych słupków | ciąg<br/>null |
| sessionRenewalMs | Sesja jest rejestrowana, jeśli użytkownik jest nieaktywny przez ten czas w milisekundach. | numeryczne<br/>1800000<br/>(30 minut) |
| sessionExpirationMs | Sesja jest rejestrowana, jeśli będzie trwać przez ten czas w milisekundach. | numeryczne<br/>86400000<br/>(24 godziny) |
| maxBatchSizeInBytes | Maksymalny rozmiar wsadu danych telemetrycznych. Jeśli partia przekracza ten limit, zostanie ona natychmiast wysłana i zostanie uruchomiona nowa Partia zadań | numeryczne<br/>10 000 |
| maxBatchInterval | Jak długo należy wykonać partię danych telemetrycznych przed wysłaniem (w milisekundach) | numeryczne<br/>15000 |
| Wyłącz&#8203;ExceptionTracking | Jeśli wartość jest równa true, wyjątki nie są autozbierane. | boolean<br/> fałsz |
| disableTelemetry | Jeśli wartość jest równa true, dane telemetryczne nie są zbierane ani wysyłane. | boolean<br/>fałsz |
| enableDebug | W przypadku wartości true dane debugowania **wewnętrznego** są generowane jako wyjątek **zamiast** rejestrowania, niezależnie od ustawień rejestrowania zestawu SDK. Wartość domyślna to false. <br>**_Uwaga:_** Włączenie tego ustawienia spowoduje porzucenie danych telemetrycznych w przypadku wystąpienia błędu wewnętrznego. Może to być przydatne do szybkiego identyfikowania problemów z konfiguracją lub użyciem zestawu SDK. Jeśli nie chcesz utracić danych telemetrycznych podczas debugowania, rozważ użycie `consoleLoggingLevel` lub `telemetryLoggingLevel` zamiast `enableDebug` . | boolean<br/>fałsz |
| loggingLevelConsole | Rejestruje **wewnętrzne** błędy Application Insights w konsoli programu. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) | numeryczne<br/> 0 |
| loggingLevelTelemetry | Wysyła **wewnętrzne** błędy Application Insights jako dane telemetryczne. <br>0: off, <br>1: tylko błędy krytyczne, <br>2: wszystkiego (błędy & ostrzeżenia) | numeryczne<br/> 1 |
| diagnosticLogInterval | wewnętrz Interwał sondowania (w MS) dla wewnętrznej kolejki rejestrowania | numeryczne<br/> 10 000 |
| samplingPercentage | Procent zdarzeń, które będą wysyłane. Wartość domyślna to 100, co oznacza, że wszystkie zdarzenia są wysyłane. Ustaw tę opcję, jeśli chcesz zachować limit danych dla aplikacji o dużej skali. | numeryczne<br/>100 |
| autoTrackPageVisitTime | Jeśli wartość jest równa true, w pageview czas wyświetlania poprzedniej strony z instrumentacją jest śledzony i wysyłany jako Telemetria i uruchamiany jest nowy czasomierz dla bieżącego pageview. | boolean<br/>fałsz |
| disableAjaxTracking | W przypadku wartości true wywołania AJAX nie są autozbierane. | boolean<br/> fałsz |
| disableFetchTracking | W przypadku wartości true żądania pobrania nie są gromadzone w sposób autozbierany.|boolean<br/>true |
| overridePageViewDuration | Jeśli wartość jest równa true, domyślne zachowanie trackPageView jest zmieniane na koniec interwału czasu trwania wyświetlania strony w przypadku wywołania trackPageView. Jeśli wartość jest równa false i nie ma niestandardowego czasu trwania do trackPageView, wydajność wyświetlania stron jest obliczana przy użyciu interfejsu API czasu nawigacji. |boolean<br/>
| maxAjaxCallsPerView | Domyślnie 500 — określa, ile wywołań AJAX będzie monitorowanych dla każdego widoku strony. Ustaw wartość-1, aby monitorować wszystkie (nieograniczone) wywołania AJAX na stronie. | numeryczne<br/> 500 |
| disableDataLossAnalysis | W przypadku wartości false bufory wewnętrznych nadawców telemetrii będą sprawdzane przy uruchamianiu elementów, które nie zostały jeszcze wysłane. | boolean<br/> true |
| Wyłącz&#8203;CorrelationHeaders | W przypadku wartości false zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań zależności w celu skorelowania ich z odpowiednimi żądaniami po stronie serwera. | boolean<br/> fałsz |
| correlationHeader&#8203;ExcludedDomains | Wyłącz nagłówki korelacji dla określonych domen | ciąg []<br/>definicji |
| correlationHeader&#8203;ExcludePatterns | Wyłącz nagłówki korelacji przy użyciu wyrażeń regularnych | wyrażenie regularne []<br/>definicji |
| correlationHeader domeny&#8203; | Włącz nagłówki korelacji dla określonych domen | ciąg []<br/>definicji |
| disableFlush&#8203;OnBeforeUnload | W przypadku wartości true Metoda Flush nie zostanie wywołana, gdy wyzwalane są wyzwalacze zdarzeń onBeforeUnload | boolean<br/> fałsz |
| enableSessionStorageBuffer | W przypadku wartości true bufor z całą niewysłaną telemetrię jest przechowywany w magazynie sesji. Bufor jest przywracany podczas ładowania strony | boolean<br />true |
| cookieCfg | Domyślne użycie plików cookie włączone Zobacz ustawienia [ICookieCfgConfig](#icookiemgrconfig) dla pełnych wartości domyślnych. | [ICookieCfgConfig](#icookiemgrconfig)<br>(Od 2.6.0)<br/>definicji |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywać ani odczytywać żadnych danych z plików cookie. Należy zauważyć, że spowoduje to wyłączenie plików cookie użytkownika i sesji, a następnie renderowanie bloków użycia i środowiska. isCookieUseDisable jest przestarzała na rzecz disableCookiesUsage, gdy obie są udostępniane disableCookiesUsage ma pierwszeństwo.<br>(Od 2.6.0 v) A jeśli `cookieCfg.enabled` jest również zdefiniowana, będzie mieć pierwszeństwo przed tymi wartościami, można ponownie włączyć użycie plików cookie po zainicjowaniu za pośrednictwem programu Core. getCookieMgr (). SetEnabled (true). | alias dla [`cookieCfg.enabled`](#icookiemgrconfig)<br>fałsz |
| cookieDomain | Niestandardowa domena plików cookie. Jest to przydatne, jeśli chcesz udostępnić pliki cookie Application Insights w poddomenach.<br>(Od 2.6.0 v) Jeśli `cookieCfg.domain` jest zdefiniowany, będzie miał pierwszeństwo przed tą wartością. | alias dla [`cookieCfg.domain`](#icookiemgrconfig)<br>null |
| cookiePath | Niestandardowa ścieżka pliku cookie. Jest to przydatne, jeśli chcesz udostępnić pliki cookie Application Insights za bramą aplikacji.<br>Jeśli `cookieCfg.path` jest zdefiniowany, będzie miał pierwszeństwo przed tą wartością. | alias dla [`cookieCfg.path`](#icookiemgrconfig)<br>(Od 2.6.0)<br/>null |
| isRetryDisabled | W przypadku wartości false ponów próbę 206 (częściowe powodzenie), 408 (limit czasu), 429 (zbyt wiele żądań), 500 (wewnętrzny błąd serwera), 503 (Usługa niedostępna) i 0 (offline, tylko jeśli wykryto) | boolean<br/>fałsz |
| isStorageUseDisabled | Jeśli wartość jest równa true, zestaw SDK nie będzie przechowywał ani nie odczytał żadnych danych z magazynu lokalnego i w ramach sesji. | boolean<br/> fałsz |
| isBeaconApiDisabled | Jeśli wartość jest równa false, zestaw SDK wyśle wszystkie dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) | boolean<br/>true |
| onunloadDisableBeacon | Gdy karta zostanie ZAMKNIĘTA, zestaw SDK wyśle wszystkie pozostałe dane telemetryczne za pomocą [interfejsu API sygnałów](https://www.w3.org/TR/beacon) | boolean<br/> fałsz |
| sdkExtension | Ustawia nazwę rozszerzenia zestawu SDK. Dozwolone są tylko znaki alfanumeryczne. Nazwa rozszerzenia jest dodawana jako prefiks do tagu "AI. Internal. sdkVersion" (na przykład "ext_javascript: 2.0.0"). | ciąg<br/> null |
| isBrowserLink&#8203;TrackingEnabled | Jeśli wartość jest równa true, zestaw SDK będzie śledził wszystkie żądania [linku do przeglądarki](/aspnet/core/client-side/using-browserlink) . | boolean<br/>fałsz |
| appId | Identyfikator AppId jest używany dla korelacji między zależnościami AJAX, które są wykonywane po stronie klienta z żądaniami po stronie serwera. Gdy interfejs API sygnałów jest włączony, nie można go użyć automatycznie, ale można ustawić go ręcznie w konfiguracji. |ciąg<br/> null |
| Włącz&#8203;CorsCorrelation | Jeśli wartość jest równa true, zestaw SDK doda dwa nagłówki ("Request-ID" i "Request-context") do wszystkich żądań CORS do skorelowania wychodzących zależności AJAX z odpowiednimi żądaniami po stronie serwera. | boolean<br/>fałsz |
| namePrefix | Opcjonalna wartość, która będzie używana jako przyrostkowa nazwa dla localStorage i nazwy pliku cookie. | ciąg<br/>definicji |
| Włącz śledzenie&#8203;AutoRoute&#8203; | Automatyczne śledzenie zmian trasy w aplikacjach jednostronicowych (SPA). Jeśli wartość jest równa true, każda zmiana trasy wyśle nowy pageview do Application Insights. Zmiany trasy mieszania ( `example.com/foo#bar` ) są również rejestrowane jako nowe widoki stron.| boolean<br/>fałsz |
| enableRequest&#8203;HeaderTracking | W przypadku wartości true są śledzone nagłówki żądań programu AJAX &. | boolean<br/> fałsz |
| enableResponse&#8203;HeaderTracking | W przypadku wartości true są śledzone nagłówki odpowiedzi żądania pobrania & AJAX. | boolean<br/> fałsz |
| distributedTracingMode | Ustawia tryb śledzenia rozproszonego. Jeśli ustawiono tryb AI_AND_W3C lub tryb W3C, nagłówki kontekstowe śledzenia W3C (traceparent/tracestate) zostaną wygenerowane i uwzględnione we wszystkich żądaniach wychodzących. AI_AND_W3C jest zapewniana pod kątem zgodności z poprzednimi wersjami Application Insights usługi Instrumentacji. Zobacz przykład [tutaj](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).| `DistributedTracingModes`lub<br/>numeryczne<br/>(Od 2.6.0 v) `DistributedTracingModes.AI_AND_W3C`<br />(v 2.5.11 lub starszy) `DistributedTracingModes.AI` |
| Włącz&#8203;AjaxErrorStatusText | W przypadku wartości true Uwzględnij tekst danych błędu odpowiedzi w zdarzeniu zależności dla żądań AJAX zakończonych niepowodzeniem. | boolean<br/> fałsz |
| Włącz&#8203;AjaxPerfTracking |Flaga umożliwiająca wyszukiwanie i Dołączanie dodatkowego okna przeglądarki. czasy wydajności w raportowanych `ajax` (XHR i Fetch) metrykach raportowanych. | boolean<br/> fałsz |
| maxAjaxPerf&#8203;LookupAttempts | Maksymalna liczba przypadków, w których należy szukać okna. chronometraż wydajności (jeśli jest dostępny), jest to wymagane, ponieważ nie wszystkie przeglądarki wypełniają okno. wydajność przed zgłoszeniem końca żądania XHR oraz żądania pobrania dodane po zakończeniu.| numeryczne<br/> 3 |
| ajaxPerfLookupDelay | Czas oczekiwania przed ponowną próbą znalezienia systemu Windows. chronometraż wydajności dla `ajax` żądania, czas jest w milisekundach i jest przesyłany bezpośrednio do setTimeout (). | numeryczne<br/> 25 MS |
| enableUnhandled&#8203;PromiseRejection&#8203;śledzenia | W przypadku wartości true nieobsłużone odrzucania obietnic są automatycznie zbierane i raportowane jako błąd JavaScript. Gdy disableExceptionTracking ma wartość true (nie Śledź wyjątków), wartość konfiguracji zostanie zignorowana, a nieobsłużone odrzucenia obietnic nie zostaną zgłoszone. | boolean<br/> fałsz |
| Wyłącz weryfikację&#8203;InstrumentationKey&#8203; | W przypadku opcji true sprawdzanie poprawności klucza Instrumentacji jest pomijane. | boolean<br/>fałsz |
| enablePerfMgr | Jeśli ta wartość jest włączona (true), spowoduje to utworzenie lokalnego perfEvents dla kodu, który ma instrumentację emisji perfEvents (za pośrednictwem pomocnika doPerf ()). Może to służyć do identyfikowania problemów z wydajnością w ramach zestawu SDK na podstawie użycia lub opcjonalnie w ramach własnego kodu Instrumentacji. [Więcej szczegółów można znaleźć w dokumentacji podstawowej](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). Od 2.5.7 v | boolean<br/>fałsz |
| perfEvtsSendAll | Gdy _enablePerfMgr_ jest włączona, a [IPerfManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) uruchamia [INotificationManager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts). perfEvent () Ta flaga określa, czy zdarzenie jest wywoływane (i wysyłane do wszystkich odbiorników) dla wszystkich zdarzeń (true), czy tylko dla zdarzeń "Parent" (wartość domyślna to false &lt; &gt; ).<br />Nadrzędny [IPerfEvent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) jest zdarzeniem, w którym żadne inne IPerfEvent nie są nadal uruchomione w momencie tworzenia tego zdarzenia i Właściwość _nadrzędna_ nie ma wartości null ani nie jest zdefiniowana. Od 2.5.7 v |  boolean<br />fałsz |
| idLength | Określa domyślną długość używaną do generowania nowych wartości losowych sesji i identyfikatora użytkownika. Wartością domyślną jest 22, Poprzednia wartość domyślna to 5 (v 2.5.8 lub mniej), jeśli trzeba zachować poprzednią maksymalną długość, należy ustawić tę wartość na 5. |  numeryczne<br />22 |

## <a name="cookie-handling"></a>Obsługa plików cookie

W wersji 2.6.0 funkcja zarządzania plikami cookie jest teraz dostępna bezpośrednio z wystąpienia i można ją wyłączyć i ponownie włączyć po zainicjowaniu.

W przypadku wyłączenia w trakcie inicjowania za pośrednictwem `disableCookiesUsage` lub `cookieCfg.enabled` konfiguracji można teraz ponownie włączyć za pomocą funkcji [ICookieMgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) `setEnabled` .

Zarządzanie plikami cookie opartymi na wystąpieniach zastępuje również poprzednie funkcje globalne `disableCookies()` Coreutils `setCookie(...)` , `getCookie(...)` i `deleteCookie(...)` . Ponadto w celu skorzystania z ulepszeń w postaci drzewa, które wprowadzono również w ramach wersji 2.6.0, nie należy już używać funkcji globalnych.

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

Konfiguracja plików cookie dla zarządzania plikami cookie opartymi na wystąpieniach dodana w wersji 2.6.0.

| Nazwa | Opis | Typ i domyślny |
|------|-------------|------------------|
| enabled | Wartość logiczna wskazująca, czy używanie plików cookie przez zestaw SDK jest włączone przez bieżące wystąpienie. W przypadku wartości false wystąpienie zestawu SDK zainicjowane przez tę konfigurację nie będzie przechowywać ani odczytywać żadnych danych z plików cookie | boolean<br/> true |
| domena | Niestandardowa domena plików cookie. Jest to przydatne, jeśli chcesz udostępnić pliki cookie Application Insights w poddomenach. Jeśli nie zostanie podany, używa wartości z `cookieDomain` wartości głównej. | ciąg<br/>null |
| path | Określa ścieżkę do użycia w pliku cookie, jeśli nie zostanie podana, użyje dowolnej wartości z wartości głównej `cookiePath` . | ciąg <br/> / |
| getcookas | Funkcja pobierająca wartość nazwanego pliku cookie, jeśli nie zostanie podana, będzie używać wewnętrznego analizowania plików cookie/buforowania. | `(name: string) => string` <br/> null |
| setcookas | Funkcja, aby ustawić nazwany plik cookie o określonej wartości, wywoływanej tylko podczas dodawania lub aktualizowania pliku cookie. | `(name: string, value: string) => void` <br/> null |
| delCookie | Funkcja, aby usunąć nazwany plik cookie o określonej wartości, oddzielony od setcooka, aby uniknąć konieczności przeanalizowania wartości w celu ustalenia, czy plik cookie jest dodawany lub usuwany. Jeśli nie zostanie podany, użyje wewnętrznego analizowania plików cookie/buforowania. | `(name: string, value: string) => void` <br/> null |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>Uproszczone użycie nowego wystąpienia Menedżera plików cookie

- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). SetEnabled (true/false);
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). Set ("-Cooka", "% 20encoded% 20value");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). get (""-Cooka ");
- appInsights. [getCookieMgr ()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts). del ("kuchenke");

## <a name="enable-time-on-page-tracking"></a>Włącz śledzenie czasu na stronie

Przez ustawienie `autoTrackPageVisitTime: true` czas, przez jaki użytkownik spędza na każdej stronie, jest śledzony. Na każdym nowym PageView czas trwania, przez który użytkownik spędził na *poprzedniej* stronie, jest wysyłany jako [Metryka niestandardowa](../essentials/metrics-custom-overview.md) o nazwie `PageVisitTime` . Ta Metryka niestandardowa jest wyświetlana w [Eksplorator metryk](../essentials/metrics-getting-started.md) jako "Metryka oparta na dzienniku".

## <a name="enable-correlation"></a>Włącz korelację

Korelacja generuje i wysyła dane, które umożliwiają śledzenie rozproszone i udostępniają [mapę aplikacji](../app/app-map.md), [kompleksowy widok transakcji](../app/app-map.md#go-to-details)oraz inne narzędzia diagnostyczne.

Poniższy przykład pokazuje wszystkie możliwe konfiguracje wymagane do włączenia korelacji z poniższymi uwagami dotyczącymi scenariusza:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Jeśli którykolwiek z serwerów innych firm, z którym komunikuje się klient, nie może zaakceptować `Request-Id` `Request-Context` nagłówków i i nie można zaktualizować ich konfiguracji, należy umieścić je w liście wykluczeń za pośrednictwem `correlationHeaderExcludeDomains` właściwości konfiguracja. Ta właściwość obsługuje symbole wieloznaczne.

Po stronie serwera musi być możliwe akceptowanie połączeń z tymi nagłówkami. W zależności od `Access-Control-Allow-Headers` konfiguracji po stronie serwera często konieczne jest przeprowadzenie listy po stronie serwera przez ręczne dodanie `Request-Id` i `Request-Context` .

Access-Control-Allow-Headers: `Request-Id` , `Request-Context` , `<your header>`

> [!NOTE]
> W przypadku korzystania z OpenTelemtry lub Application Insights zestawów SDK opublikowanych w 2020 lub nowszych zaleca się użycie [WC3 TraceContext — śledzenie](https://www.w3.org/TR/trace-context/). Zobacz wskazówki dotyczące konfiguracji [tutaj](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Aplikacje jednostronicowe

Domyślnie ten zestaw SDK **nie** będzie obsługiwał zmiany trasy opartej na stanie, która występuje w aplikacjach jednostronicowych. Aby włączyć automatyczne śledzenie zmian trasy dla aplikacji jednostronicowej, możesz dodać `enableAutoRouteTracking: true` do konfiguracji instalacji.

Obecnie oferujemy oddzielną [wtyczkę reakcji](javascript-react-plugin.md), którą można zainicjować za pomocą tego zestawu SDK. Spowoduje to również przekazanie śledzenia zmian trasy, a także zebranie innych danych telemetrycznych związanych z reagowaniem.
> [!NOTE]
> Używaj `enableAutoRouteTracking: true` tylko wtedy, gdy **nie** używasz wtyczki do reagowania. Oba są w stanie wysyłać nowe PageViews w przypadku zmiany trasy. Jeśli oba są włączone, może być wysyłanych zduplikowanych PageViews.

## <a name="extensions"></a>Rozszerzenia

| Rozszerzenia |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Kliknij pozycję analiza autokolekcje](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Eksplorowanie danych po stronie przeglądarki i klienta

Dane przeglądarki/strony klienta można wyświetlać, przechodząc do **metryk** i dodając indywidualne metryki, które Cię interesują:

![Zrzut ekranu strony metryk w Application Insights pokazujący graficzne wyświetlanie danych metryk dla aplikacji sieci Web.](./media/javascript/page-view-load-time.png)

Możesz również wyświetlać dane z zestawu SDK języka JavaScript za pomocą środowiska przeglądarki w portalu.

Wybierz pozycję **przeglądarka** , a następnie wybierz pozycję **Błędy** lub **wydajność**.

![Zrzut ekranu strony przeglądarki w Application Insights pokazujący, jak dodać błędy przeglądarki lub wydajność przeglądarki do metryk, które można wyświetlić dla aplikacji sieci Web.](./media/javascript/browser.png)

### <a name="performance"></a>Wydajność

![Zrzut ekranu przedstawiający stronę wydajności w Application Insights pokazujący wyświetlane graficznie metryki operacji dla aplikacji sieci Web.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Zależności

![Zrzut ekranu strony wydajność w Application Insights pokazujący wyświetlane graficznie metryki zależności dla aplikacji sieci Web.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analiza

Aby wysłać zapytanie do telemetrii zebranej przez zestaw JavaScript SDK, wybierz przycisk **Wyświetl w dziennikach (analiza)** . Po dodaniu `where` instrukcji programu `client_Type == "Browser"` widoczne będą tylko dane z zestawu SDK języka JavaScript, a wszystkie Telemetria po stronie serwera zebrane przez inne zestawy SDK zostaną wykluczone.
 
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
2. Określ, które mapy źródłowe odpowiadają temu stosowi wywołań. Mapa źródłowa musi być zgodna z plikiem źródłowym ramki stosu, ale z sufiksem `.map`
3. Przeciągnij i upuść mapy źródłowe na stos wywołań w Azure Portal ![ animowany obraz pokazujący, jak przeciągać i upuszczać pliki map źródłowych z folderu kompilacji do okna stosu wywołań w Azure Portal.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Aby zapoznać się z lekkim doświadczeniem, możesz zamiast tego zainstalować wersję podstawową Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ta wersja zawiera minimalną liczbę funkcji i funkcjonalności systemu operacyjnego i polega na tym, że można ją skompilować w taki sam sposób, jak to możliwe. Na przykład nie wykonuje autokolekcjonowania (nieprzechwycone wyjątki, AJAX itp.). Interfejsy API służące do wysyłania określonych typów telemetrii, takich jak `trackTrace` , `trackException` itp., nie są uwzględnione w tej wersji, więc konieczne będzie udostępnienie własnego otoki. Jedynym dostępnym interfejsem API jest `track` . [Przykład](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) znajduje się tutaj.

## <a name="examples"></a>Przykłady

Przykłady możliwy do uruchomienia można znaleźć w temacie [Application Insights Samples SDK JavaScript](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Uaktualnianie ze starszej wersji Application Insights

Istotne zmiany w wersji zestawu SDK V2:
- Aby umożliwić lepsze sygnatury interfejsu API, niektóre wywołania interfejsu API, takie jak trackPageView i trackexception, zostały zaktualizowane. Uruchamianie w programie Internet Explorer 8 i wcześniejszych wersjach przeglądarki nie jest obsługiwane.
- Koperta telemetrii ma zmiany nazwy pola i struktury ze względu na aktualizacje schematu danych.
- Przeniesiono `context.operation` do `context.telemetryTrace` . Niektóre pola zostały również zmienione ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - Aby ręcznie odświeżyć bieżący identyfikator pageview (na przykład w aplikacjach SPA), użyj `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` .
    > [!NOTE]
    > Aby zachować unikatowy identyfikator śledzenia, z którego wcześniej korzystano `Util.newId()` , teraz Użyj `Util.generateW3CId()` . Oba ostatecznie kończą się IDENTYFIKATORem operacji.

Jeśli używasz bieżącego zestawu SDK PRODUKCYJNEgo usługi Application Insights (1.0.20) i chcesz sprawdzić, czy nowy zestaw SDK działa w środowisku uruchomieniowym, zaktualizuj adres URL w zależności od bieżącego scenariusza ładowania zestawu SDK.

- Pobierz za pośrednictwem scenariusza usługi CDN: zaktualizuj fragment kodu, którego obecnie używasz, aby wskazać następujący adres URL:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- scenariusz npm: Wywołaj `downloadAndSetup` pobieranie pełnego skryptu ApplicationInsights z usługi CDN i zainicjuj go za pomocą klucza Instrumentacji:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Test w środowisku wewnętrznym, aby sprawdzić, czy dane telemetryczne monitorowania działają zgodnie z oczekiwaniami. Jeśli wszystko działa, zaktualizuj podpisy interfejsów API odpowiednio do wersji zestawu SDK V2 i Wdróż ją w środowiskach produkcyjnych.

## <a name="sdk-performanceoverhead"></a>Wydajność/obciążenie zestawu SDK

O godzinie 36 KB formacie gzip i przyjmowaniu tylko około 15 MS do zainicjowania, Application Insights dodaje nieznaczną ilość loadtime do witryny sieci Web. Za pomocą fragmentu kodu można szybko ładować minimalne składniki biblioteki. W międzyczasie pełny skrypt zostanie pobrany w tle.

Podczas pobierania skryptu z usługi CDN wszystkie śledzenie strony jest umieszczane w kolejce. Gdy pobrany skrypt zakończy asynchroniczne inicjowanie, wszystkie zdarzenia, które zostały dodane do kolejki, są śledzone. W związku z tym nie utracisz żadnych danych telemetrycznych w całym cyklu życia strony. Ten proces instalacji zapewnia użytkownikom niezauważalny system, niewidoczny dla użytkowników.

> Podsumowanie:
> - ![wersja npm](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![skompresowany rozmiar gzip](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - Całkowity czas inicjowania ( **15 MS** )
> - Brak śledzenia **zerowej** w cyklu życia strony

## <a name="browser-support"></a>Obsługa przeglądarki

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Najnowsza ✔ Chrome |  Najnowsza ✔ Firefox | Przeglądarka IE 9 + & Edge ✔<br>Zgodne z IE 8 | Najnowsza ✔ | Przeglądarka Safari Najnowsza ✔ |

## <a name="es3ie8-compatibility"></a>Zgodność ES3/IE8

Jako zestaw SDK istnieje wielu użytkowników, którzy nie mogą kontrolować przeglądarek używanych przez ich klientów. W związku z tym musimy upewnić się, że ten zestaw SDK będzie nadal "działał" i nie przerywa wykonywania operacji JS po załadowaniu przez starszą przeglądarkę. Chociaż byłoby to idealne rozwiązanie do obsługi przeglądarek przeglądarki IE8 i starszej generacji (ES3), istnieje wielu dużych klientów/użytkowników, którzy nadal wymagają od stron "pracy" oraz że nie mogą kontrolować przeglądarki, której chcą używać użytkownicy końcowi.

Nie oznacza to, że obsługujemy tylko najniższy wspólny zestaw funkcji, tylko dlatego musimy zachować zgodność kodu ES3 oraz dodać nowe funkcje, które będą musiały zostać dodane w sposób, który nie spowoduje przerwania ES3 analizy kodu JavaScript i dodania jako funkcji opcjonalnej.

[Zobacz witrynę GitHub, aby uzyskać szczegółowe informacje na temat pomocy technicznej IE8](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Zestaw SDK open source

Application Insights zestawu SDK języka JavaScript jest otwartym źródłem do wyświetlania kodu źródłowego lub do współtworzenia projektu, odwiedzając [oficjalne repozytorium GitHub](https://github.com/Microsoft/ApplicationInsights-JS). 

Aby zapoznać się z najnowszymi aktualizacjami i poprawkami błędów [, zapoznaj się z informacjami o wersji](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Następne kroki
* [Śledzenie użycia](usage-overview.md)
* [Niestandardowe zdarzenia i metryki](api-custom-events-metrics.md)
* [Tworzenie — pomiar— nauka](usage-overview.md)
* [Rozwiązywanie problemów z niepowodzeniem ładowania zestawu SDK](javascript-sdk-load-failure.md)
