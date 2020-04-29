---
title: Monitorowanie usług Node.js za pomocą usługi Azure Application Insights | Microsoft Docs
description: Monitoruj wydajność i diagnozuj problemy w usługach Node.js za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 38336e3faf3764233dd94bffbfb24421e054496a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411580"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorowanie usług i aplikacji Node.js za pomocą usługi Application Insights

[Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitoruje usługi i składniki zaplecza po wdrożeniu, aby ułatwić odnajdywanie i Szybkie diagnozowanie wydajności i innych problemów. Usługi Application Insights dla usług środowiska Node.js hostowanych w centrum danych można używać w maszynach wirtualnych platformy Azure i aplikacjach internetowych, a nawet w innych chmurach publicznych.

Aby móc odbierać, przechowywać i eksplorować dane monitorowania, uwzględnij zestaw SDK w kodzie, a następnie skonfiguruj odpowiedni zasób usługi Application Insights na platformie Azure. Zestaw SDK wysyła dane do zasobu na potrzeby przyszłej analizy i eksploracji.

Zestaw Node.js SDK może automatycznie monitorować przychodzące i wychodzące żądania HTTP, wyjątki i niektóre metryki systemu. Począwszy od wersji 0,20, zestaw SDK może również monitorować niektóre popularne [pakiety innych](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)firm, takie jak MongoDB, MySQL i Redis. Wszystkie zdarzenia związane z przychodzącym żądaniem HTTP są skorelowane, aby zapewnić szybsze rozwiązanie problemów.

Do ręcznego instrumentowania i monitorowania dodatkowych aspektów aplikacji i systemu można używać interfejsu API TelemetryClient. Interfejs API TelemetryClient został szczegółowo opisany w dalszej części tego artykułu.

## <a name="get-started"></a>Wprowadzenie

Wykonaj następujące zadania w celu skonfigurowania monitorowania aplikacji lub usługi.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz subskrypcję platformy Azure, lub [bezpłatnie uzyskaj nową][azure-free-offer]. Jeśli Twoja organizacja ma już subskrypcję platformy Azure, administrator może dodać Cię do niej, wykonując [te instrukcje][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a>Konfigurowanie zasobu Application Insights


1. Zaloguj się w witrynie [Azure Portal][portal].
2. Wybierz pozycję **Utwórz zasób** > **Narzędzia** > deweloperskie**Application Insights**. Zasób zawiera punkt końcowy do odbierania danych telemetrycznych, magazyn danych, zapisane raporty oraz pulpity nawigacyjne, konfigurację reguły i alertu oraz inne elementy.

3. Na stronie tworzenia zasobu w polu **Typ aplikacji** wybierz pozycję **Aplikacja Node.js**. Typ aplikacji określa domyślne tworzone pulpity nawigacyjne i raporty. (Dowolny zasób usługi Application Insights może w rzeczywistości zbierać dane dla dowolnego języka i platformy).

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Konfigurowanie zestawu Node.js SDK

Uwzględnij zestaw SDK w aplikacji, aby mógł zbierać dane. 

1. Skopiuj klucza instrumentacji zasobu (nazywany również *ikey*) z witryny Azure Portal. Usługa Application Insights używa klucza ikey do mapowania danych z zasobu platformy Azure. Aby zestaw SDK mógł korzystać z klucza ikey, należy określić ten klucz w zmiennej środowiskowej lub w kodzie.  

   ![Kopiowanie klucza instrumentacji](./media/nodejs/instrumentation-key-001.png)

2. Dodaj bibliotekę zestawu Node.js SDK do zależności aplikacji przy użyciu pliku package.json. W folderze głównym aplikacji uruchom polecenie:

   ```bash
   npm install applicationinsights --save
   ```

3. Jawnie załaduj bibliotekę w kodzie. Ponieważ zestaw SDK wprowadza instrumentację do wielu innych bibliotek, załaduj go możliwie jak najszybciej, nawet przed innymi instrukcjami `require`. 

   Na początku pierwszego pliku js dodaj poniższy kod. Metoda `setup` konfiguruje klucz ikey (a tym samym zasób platformy Azure) w taki sposób, aby był używany domyślnie dla wszystkich śledzonych elementów.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   Klucz ikey można również podać za pośrednictwem zmiennej środowiskowej APPINSIGHTS\_INSTRUMENTATIONKEY, zamiast przekazywać go ręcznie do metody `setup()` lub `new appInsights.TelemetryClient()`. Takie podejście umożliwia oddzielenie kluczy ikey od zatwierdzonego kodu źródłowego i określenie różnych kluczy ikey dla różnych środowisk.

   Dodatkowe opcje konfiguracji opisano w poniższych sekcjach.

   Zestaw SDK możesz wypróbować bez wysyłania danych telemetrycznych przez ustawienie elementu `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor-your-app"></a><a name="monitor"></a>Monitorowanie aplikacji

Zestaw SDK automatycznie zbiera dane telemetryczne dotyczące środowiska uruchomieniowego Node.js i niektórych popularnych modułów innych firm. Użyj swojej aplikacji, aby wygenerować niektóre z tych danych.

Następnie w witrynie [Azure Portal][portal] przejdź do usługi Application Insights i otwórz utworzony zasób. W obszarze **Oś czasu przeglądu** wyszukaj kilka pierwszych punktów danych. Aby wyświetlić bardziej szczegółowe dane, wybierz inne składniki wykresów.

Aby wyświetlić topologię odnajdowaną dla aplikacji, wybierz przycisk **Mapa aplikacji**. Wybierz składniki na mapie, aby zobaczyć więcej szczegółów.

![Mapa prostej aplikacji](./media/nodejs/application-map-002.png)

Aby uzyskać więcej informacji o aplikacji i rozwiązać problemy, w sekcji **ZBADAJ** użyj innych dostępnych widoków.

![Sekcja Zbadaj](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>Brak danych?

Zestaw SDK przesyła dane partiami, dlatego niektóre elementy w portalu mogą być wyświetlane z opóźnieniem. Jeśli nie widzisz danych w swoim zasobie, wypróbuj następujące sposoby:

* Kontynuuj korzystanie z aplikacji. Wykonaj więcej akcji, aby wygenerować więcej telemetrii.
* Kliknij przycisk **Odśwież** w widoku zasobu portalu. Wykresy są okresowo odświeżane samoczynnie, ale odświeżanie ręczne jest wykonywane od razu.
* Upewnij się, że [wymagane porty wychodzące](../../azure-monitor/app/ip-addresses.md) zostały otwarte.
* Użyj pozycji [Wyszukaj](../../azure-monitor/app/diagnostic-search.md), aby znaleźć określone zdarzenia.
* Zapoznaj się z sekcją [Często zadawane pytania][FAQ].


## <a name="sdk-configuration"></a>Konfiguracja zestawu SDK

Metody konfiguracji i domyślne wartości zestawu SDK są wymienione w poniższym przykładzie kodu.

Aby w pełni skorelować zdarzenia w usłudze, należy ustawić parametr `.setAutoDependencyCorrelation(true)`. Po ustawieniu tej opcji zestaw SDK może śledzić kontekst między asynchronicznymi wywołaniami zwrotnymi w środowisku Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>Interfejs API TelemetryClient

Pełny opis interfejsu API TelemetryClient można znaleźć w temacie [Application Insights API for custom events and metrics (Interfejs API usługi Application Insights na potrzeby metryk i zdarzeń niestandardowych)](../../azure-monitor/app/api-custom-events-metrics.md).

Zestaw Node.js SDK usługi Application Insights umożliwia śledzenie dowolnego żądania, zdarzenia, metryki lub wyjątku. W poniższym przykładzie kodu pokazano niektóre z interfejsów API, których możesz użyć:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Śledzenie zależności

Aby śledzić zależności, użyj następującego kodu:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Dodawanie właściwości niestandardowych do wszystkich zdarzeń

Aby dodać właściwość niestandardową do wszystkich zdarzeń, użyj następującego kodu:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Śledzenie żądań HTTP GET

Aby śledzić żądania HTTP GET, użyj następującego kodu:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Śledzenie czasu uruchamiania serwera

Aby śledzić czas uruchamiania serwera, użyj poniższego kodu:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie telemetrii w portalu](../../azure-monitor/app/overview-dashboard.md)
* [Zapisywanie zapytań analizy za pośrednictwem danych telemetrycznych](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

