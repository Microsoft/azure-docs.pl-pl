---
title: Monitorowanie usług Node.js za pomocą usługi Azure Application Insights | Microsoft Docs
description: Monitoruj wydajność i diagnozuj problemy w usługach Node.js za pomocą usługi Application Insights.
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eba74bcf2b4b047b3ed881342db4f7b1011f928
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585754"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Monitorowanie usług i aplikacji Node.js za pomocą usługi Application Insights

[Application Insights](./app-insights-overview.md) monitoruje usługi i składniki zaplecza po wdrożeniu, aby ułatwić odnajdywanie i Szybkie diagnozowanie wydajności i innych problemów. Application Insights dla usług Node.js, które są hostowane w centrum danych, na maszynach wirtualnych platformy Azure i aplikacjach sieci Web, a nawet w innych chmurach publicznych.

Aby móc odbierać, przechowywać i eksplorować dane monitorowania, uwzględnij zestaw SDK w kodzie, a następnie skonfiguruj odpowiedni zasób usługi Application Insights na platformie Azure. Zestaw SDK wysyła dane do zasobu na potrzeby przyszłej analizy i eksploracji.

Zestaw Node.js SDK może automatycznie monitorować przychodzące i wychodzące żądania HTTP, wyjątki i niektóre metryki systemu. Począwszy od wersji 0,20, zestaw SDK może również monitorować niektóre popularne [pakiety innych](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)firm, takie jak MongoDB, MySQL i Redis. Wszystkie zdarzenia związane z przychodzącym żądaniem HTTP są skorelowane, aby zapewnić szybsze rozwiązanie problemów.

Do ręcznego instrumentowania i monitorowania dodatkowych aspektów aplikacji i systemu można używać interfejsu API TelemetryClient. Interfejs API TelemetryClient został szczegółowo opisany w dalszej części tego artykułu.

## <a name="get-started"></a>Rozpoczęcie pracy

Wykonaj następujące zadania w celu skonfigurowania monitorowania aplikacji lub usługi.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz subskrypcję platformy Azure, lub [bezpłatnie uzyskaj nową][azure-free-offer]. Jeśli Twoja organizacja ma już subskrypcję platformy Azure, administrator może dodać Cię do niej, wykonując [te instrukcje][add-aad-user].

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a> Konfigurowanie zasobu Application Insights

1. Zaloguj się w witrynie [Azure Portal][portal].
2. [Tworzenie zasobu Application Insights](create-new-resource.md)

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Konfigurowanie zestawu Node.js SDK

Uwzględnij zestaw SDK w aplikacji, aby mógł zbierać dane.

> [!IMPORTANT]
> Nowe regiony platformy Azure **wymagają** użycia parametrów połączenia zamiast kluczy Instrumentacji. [Parametry połączenia](./sdk-connection-string.md?tabs=nodejs) identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.

1. Skopiuj klucz Instrumentacji zasobu (nazywany także *iKey*) z nowo utworzonego zasobu. Usługa Application Insights używa klucza ikey do mapowania danych z zasobu platformy Azure. Aby zestaw SDK mógł korzystać z klucza ikey, należy określić ten klucz w zmiennej środowiskowej lub w kodzie.  

   ![Kopiowanie klucza instrumentacji](./media/nodejs/instrumentation-key-001.png)

2. Dodaj bibliotekę zestawu Node.js SDK do zależności aplikacji przy użyciu pliku package.json. W folderze głównym aplikacji uruchom polecenie:

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > W przypadku korzystania z języka TypeScript nie należy instalować oddzielnych pakietów "wpisywania". Ten pakiet NPM zawiera wbudowane definicje typów.

3. Jawnie załaduj bibliotekę w kodzie. Ponieważ zestaw SDK wprowadza instrumentację do wielu innych bibliotek, załaduj go możliwie jak najszybciej, nawet przed innymi instrukcjami `require`.

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  Można też podać iKey za pośrednictwem zmiennej środowiskowej `APPINSIGHTS_INSTRUMENTATIONKEY` , zamiast przekazywać ją ręcznie do  `setup()` lub `new appInsights.TelemetryClient()` . Takie podejście umożliwia oddzielenie kluczy ikey od zatwierdzonego kodu źródłowego i określenie różnych kluczy ikey dla różnych środowisk. Aby skonfigurować ręczne wywoływanie `appInsights.setup('[your ikey]');` .

    Dodatkowe opcje konfiguracji opisano w poniższych sekcjach.

    Zestaw SDK możesz wypróbować bez wysyłania danych telemetrycznych przez ustawienie elementu `appInsights.defaultClient.config.disableAppInsights = true`.

5. Rozpocznij automatyczne zbieranie i wysyłanie danych przez wywołanie metody `appInsights.start();` .

### <a name="monitor-your-app"></a><a name="monitor"></a> Monitorowanie aplikacji

Zestaw SDK automatycznie zbiera dane telemetryczne dotyczące środowiska uruchomieniowego Node.js i niektórych popularnych modułów innych firm. Użyj swojej aplikacji, aby wygenerować niektóre z tych danych.

Następnie w witrynie [Azure Portal][portal] przejdź do usługi Application Insights i otwórz utworzony zasób. W obszarze **Oś czasu przeglądu** wyszukaj kilka pierwszych punktów danych. Aby wyświetlić bardziej szczegółowe dane, wybierz inne składniki wykresów.

Aby wyświetlić topologię odnalezioną dla aplikacji, można użyć [mapy aplikacji](app-map.md).

#### <a name="no-data"></a>Brak danych

Zestaw SDK przesyła dane partiami, dlatego niektóre elementy w portalu mogą być wyświetlane z opóźnieniem. Jeśli nie widzisz danych w swoim zasobie, wypróbuj następujące sposoby:

* Kontynuuj korzystanie z aplikacji. Wykonaj więcej akcji, aby wygenerować więcej telemetrii.
* Kliknij przycisk **Odśwież** w widoku zasobu portalu. Wykresy są okresowo odświeżane samoczynnie, ale odświeżanie ręczne jest wykonywane od razu.
* Upewnij się, że [wymagane porty wychodzące](./ip-addresses.md) zostały otwarte.
* Użyj pozycji [Wyszukaj](./diagnostic-search.md), aby znaleźć określone zdarzenia.
* Zapoznaj się z sekcją [Często zadawane pytania][FAQ].

## <a name="basic-usage"></a>Podstawowe użycie

Dla wbudowanej kolekcji żądań HTTP, popularnych zdarzeń biblioteki innych firm, Nieobsłużone wyjątki i metryki systemu:

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> Jeśli klucz Instrumentacji jest ustawiony w zmiennej środowiskowej `APPINSIGHTS_INSTRUMENTATIONKEY` , `.setup()` można wywołać bez argumentów. Dzięki temu można łatwo korzystać z różnych kluczy iKey w różnych środowiskach.

Załaduj bibliotekę Application Insights, jak najszybciej, jak `require("applicationinsights")` to możliwe w skryptach przed załadowaniem innych pakietów. Jest to potrzebny, aby Biblioteka Application Insights mogła przygotować nowsze pakiety do śledzenia. Jeśli wystąpią konflikty z innymi bibliotekami przy użyciu podobnego przygotowania, spróbuj załadować bibliotekę Application Insights po nich.

Ze względu na sposób, w jaki język JavaScript obsługuje wywołania zwrotne, do śledzenia żądań między zewnętrznymi zależnościami i późniejszymi wywołaniami zwrotnymi trzeba wykonać dodatkowe czynności. Domyślnie to dodatkowe śledzenie jest włączone; Wyłącz go, wywołując `setAutoDependencyCorrelation(false)` zgodnie z opisem w poniższej sekcji [konfiguracji](#sdk-configuration) .

## <a name="migrating-from-versions-prior-to-022"></a>Migrowanie z wersji wcześniejszych niż 0,22

Istnieją pewne zmiany między wersjami wcześniejszymi niż wersja 0,22 i późniejsza. Te zmiany zostały zaprojektowane w celu zapewnienia spójności z innymi zestawami Application Insights SDK i umożliwiają przyszłe rozszerzanie.

Ogólnie rzecz biorąc, można przeprowadzić migrację za pomocą następujących czynności:

- Zastąp odwołania `appInsights.client` do `appInsights.defaultClient` .
- Zastąp odwołania `appInsights.getClient()` do `new appInsights.TelemetryClient()`
- Zamień wszystkie argumenty na wartość Client. Track * metody z pojedynczym obiektem zawierającym nazwane właściwości jako argumenty. Zapoznaj się ze wskazówką typu wbudowanego dla środowiska IDE lub [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) dla każdego typu telemetrii.

Jeśli uzyskujesz dostęp do funkcji konfiguracji zestawu SDK bez łączenia ich z usługą `appInsights.setup()` , możesz teraz znaleźć te funkcje na `appInsights.Configurations` (na przykład `appInsights.Configuration.setAutoCollectDependencies(true)` ). Przejrzyj zmiany w konfiguracji domyślnej w następnej sekcji.

## <a name="sdk-configuration"></a>Konfiguracja zestawu SDK

`appInsights`Obiekt zawiera wiele metod konfiguracji. Są one wymienione w poniższym fragmencie kodu z wartościami domyślnymi.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

Aby w pełni skorelować zdarzenia w usłudze, należy ustawić parametr `.setAutoDependencyCorrelation(true)`. Po ustawieniu tej opcji zestaw SDK może śledzić kontekst między asynchronicznymi wywołaniami zwrotnymi w środowisku Node.js.

Zapoznaj się z opisami w wbudowanym podpowiedzi typu IDE lub [ApplicationInsights. TS](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) , aby uzyskać szczegółowe informacje na temat tego formantu i opcjonalnych argumentów pomocniczych.

> [!NOTE]
>  Domyślnie `setAutoCollectConsole` jest skonfigurowany do *wykluczania* wywołań do `console.log` (i innych metod konsoli). Zbierane będą tylko wywołania obsługiwanych rejestratorów innych firm (na przykład Winston i Bunyan). Możesz zmienić to zachowanie, aby uwzględnić wywołania `console` metod przy użyciu `setAutoCollectConsole(true, true)` .

### <a name="sampling"></a>Próbkowanie

Domyślnie zestaw SDK wyśle wszystkie zebrane dane do usługi Application Insights. Jeśli zbierasz dużo danych, możesz włączyć próbkowanie, aby zmniejszyć ilość wysyłanych danych. Ustaw `samplingPercentage` pole na `config` obiekcie klienta, aby to zrobić. Ustawienie `samplingPercentage` wartości 100 (wartość domyślna) oznacza, że wszystkie dane zostaną wysłane, a wartość 0 oznacza, że nic nie zostanie wysłane.

Jeśli używasz automatycznej korelacji, wszystkie dane skojarzone z pojedynczym żądaniem zostaną dołączone lub wykluczone jako jednostka.

Dodaj kod, taki jak następujące, aby włączyć próbkowanie:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>Wiele ról aplikacji z wieloma składnikami

Jeśli aplikacja składa się z wielu składników, które mają być Instrumentacja przy użyciu tego samego klucza Instrumentacji i nadal widzisz te składniki jako osobne jednostki w portalu, tak jakby były używane osobne klucze Instrumentacji (na przykład jako osobne węzły na mapie aplikacji), może być konieczne ręczne skonfigurowanie pola rolename w celu odróżnienia danych telemetrycznych składnika od innych składników wysyłających dane Application Insights do

Użyj następujących opcji, aby ustawić pole rolename:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>Automatyczna Instrumentacja innych firm

W celu śledzenia kontekstu dla wywołań asynchronicznych niektóre zmiany są wymagane w bibliotekach innych firm, takich jak MongoDB i Redis. Domyślnie, Application Insights będzie używać [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) do małpy poprawek niektórych z tych bibliotek. Można to wyłączyć przez ustawienie `APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` zmiennej środowiskowej.

> [!NOTE]
> Po ustawieniu tej zmiennej środowiskowej zdarzenia mogą nie być już poprawnie skojarzone z odpowiednią operacją.

 Pojedyncze poprawki małpy mogą być wyłączone przez ustawienie `APPLICATION_INSIGHTS_NO_PATCH_MODULES` zmiennej środowiskowej na rozdzielaną przecinkami listę pakietów do wyłączenia (na przykład `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` ) w celu uniknięcia zastosowania poprawek `console` pakietów i `redis` .

Obecnie istnieją dziewięć pakietów, które są instrumentami:,,,,,,, `bunyan` `console` `mongodb` `mongodb-core` `mysql` `redis` `winston` `pg` i `pg-pool` . Aby uzyskać informacje o tym, które wersje tych pakietów są poprawione, odwiedź [plik readme dotyczący diagnostyki kanału diagnostycznego](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md) .

`bunyan`, `winston` , I `console` poprawki generują Application Insights zdarzeń śledzenia w zależności od tego, czy `setAutoCollectConsole` jest włączona. Pozostałe będą generować Application Insights zdarzenia zależności w zależności od tego, czy `setAutoCollectDependencies` jest włączona.

### <a name="live-metrics"></a>Metryki na żywo

Aby włączyć wysyłanie metryk na żywo z aplikacji na platformę Azure, użyj programu `setSendLiveMetrics(true)` . Filtrowanie metryk na żywo w portalu nie jest obecnie obsługiwane.

### <a name="extended-metrics"></a>Metryki rozszerzone

> [!NOTE]
> Możliwość wysyłania rozszerzonych metryk natywnych została dodana w wersji 1.4.0

Aby włączyć wysyłanie rozszerzonych metryk natywnych z aplikacji na platformę Azure, zainstaluj oddzielny pakiet metryk macierzystych. Zestaw SDK zostanie automatycznie załadowany podczas instalacji i rozpocznie zbieranie Node.js natywnych metryk.

```bash
npm install applicationinsights-native-metrics
```

Obecnie pakiet metryk macierzystych wykonuje autozbieranie czasu procesora CPU dla wyrzucania elementów bezużytecznych, taktów pętli zdarzeń i użycia sterty:

- **Odzyskiwanie pamięci**: ilość czasu procesora CPU spędzona na każdym typie wyrzucania elementów bezużytecznych oraz liczba wystąpień każdego typu.
- **Pętla zdarzeń**: ile wystąpiło Takty i ile czasu procesora CPU było w sumie.
- **Sterta a nie sterta**: jaka część użycia pamięci aplikacji znajduje się w stercie lub bez sterty.

### <a name="distributed-tracing-modes"></a>Rozproszone tryby śledzenia

Domyślnie zestaw SDK wyśle nagłówki zrozumiałe dla innych aplikacji/usług objętych Instrumentacją Application Insights SDK. Opcjonalnie można włączyć wysyłanie/otrzymywanie nagłówków [kontekstowych śledzenia W3C](https://github.com/w3c/trace-context) oprócz istniejących nagłówków AI, więc nie spowoduje to przerwania korelacji z żadną z istniejących starszych usług. Włączenie nagłówków W3C umożliwi aplikacji korelację z innymi usługami bez Instrumentacji Application Insights, ale należy przyjąć ten standard W3C.

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>Interfejs API TelemetryClient

Pełny opis interfejsu API TelemetryClient można znaleźć w temacie [Application Insights API for custom events and metrics (Interfejs API usługi Application Insights na potrzeby metryk i zdarzeń niestandardowych)](./api-custom-events-metrics.md).

Zestaw Node.js SDK usługi Application Insights umożliwia śledzenie dowolnego żądania, zdarzenia, metryki lub wyjątku. W poniższym przykładzie kodu pokazano niektóre z interfejsów API, których możesz użyć:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
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
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

Przykładowe narzędzie używające `trackMetric` do mierzenia, jak długo trwa planowanie pętli zdarzeń:  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>Dodawanie właściwości niestandardowych do wszystkich zdarzeń

Aby dodać właściwość niestandardową do wszystkich zdarzeń, użyj następującego kodu:

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Śledzenie żądań HTTP GET

Użyj następującego kodu, aby ręcznie śledzić żądania HTTP GET:

> [!NOTE]
> Wszystkie żądania są domyślnie śledzone. Aby wyłączyć automatyczne zbieranie, należy wywołać metodę. setAutoCollectRequests (false) przed wywołaniem metody Start ().

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

Alternatywnie można śledzić żądania przy użyciu `trackNodeHttpRequest` metody:

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
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

### <a name="flush"></a>Płukan

Domyślnie dane telemetryczne są buforowane przez 15 sekund przed wysłaniem do serwera pozyskiwania. Jeśli aplikacja ma krótkie cykl życia (np. Narzędzie interfejsu wiersza polecenia), może być konieczne ręczne opróżnianie buforowanej telemetrii po zakończeniu działania aplikacji `appInsights.defaultClient.flush()` .

Jeśli zestaw SDK wykryje, że aplikacja uległa awarii, spowoduje to wywołanie opróżniania dla Ciebie, `appInsights.defaultClient.flush({ isAppCrashing: true })` . W przypadku opcji opróżniania `isAppCrashing` aplikacja zakłada się, że jest w nietypowym stanie, nie jest odpowiednia do wysyłania telemetrii. Zamiast tego zestaw SDK zapisze wszystkie buforowane dane telemetryczne w [magazynie trwałym](./data-retention-privacy.md#nodejs) i umożliwią zakończenie działania aplikacji. Gdy aplikacja zostanie uruchomiona ponownie, podejmie próbę wysłania wszelkich danych telemetrycznych, które zostały zapisane w magazynie trwałym.

### <a name="preprocess-data-with-telemetry-processors"></a>Przetwarzaj wstępnie dane za pomocą procesorów telemetrii

Zebrane dane można przetwarzać i filtrować przed wysłaniem ich do przechowywania za pomocą *procesorów telemetrii*. Procesory telemetrii są wywoływane jeden według jednego w kolejności, w jakiej zostały dodane przed wysłaniem elementu telemetrii do chmury.

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

Jeśli procesor telemetrii zwróci wartość false, ten element telemetrii nie zostanie wysłany.

Wszystkie procesory telemetrii odbierają dane telemetryczne i ich koperty do inspekcji i modyfikowania. Odbiera również obiekt kontekstu. Zawartość tego obiektu jest definiowana przez `contextObjects` parametr podczas wywoływania metody śledzenia dla ręcznie śledzonych danych telemetrycznych. W przypadku automatycznie zbieranych danych telemetrycznych ten obiekt jest wypełniany dostępnymi informacjami o żądaniu i zawartością żądania trwałego, zgodnie z podaną wartością `appInsights.getCorrelationContext()` (jeśli jest włączona automatyczna korelacja zależności).

Typ TypeScript dla procesora telemetrii to:

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

Na przykład procesor, który usuwa stosy danych śledzenia z wyjątków, może zostać zapisany i dodany w następujący sposób:

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>Korzystanie z wielu kluczy Instrumentacji

Można utworzyć wiele zasobów Application Insights i wysyłać do nich różne dane przy użyciu odpowiednich kluczy Instrumentacji ("iKey").

 Na przykład:

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>Zaawansowane opcje konfiguracji

Obiekt Client zawiera `config` Właściwość z wieloma opcjonalnymi ustawieniami dla zaawansowanych scenariuszy. Można je ustawić w następujący sposób:

```javascript
client.config.PROPERTYNAME = VALUE;
```

Te właściwości są specyficzne dla klienta, dlatego można je skonfigurować `appInsights.defaultClient` niezależnie od klientów utworzonych za pomocą programu `new appInsights.TelemetryClient()` .

| Właściwość                        | Opis                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Identyfikator zasobu Application Insights.                                                      |
| endpointUrl                     | Punkt końcowy pozyskiwania, do którego mają zostać wysłane ładunki telemetrii.                                                      |
| quickPulseHost                  | Host Live Metrics Stream, do którego mają być wysyłane dane telemetryczne metryk na żywo.                                            |
| proxyHttpUrl                    | Serwer proxy dla ruchu HTTP zestawu SDK (opcjonalny, domyślnie ściągnięty ze `http_proxy` zmiennej środowiskowej).     |
| proxyHttpsUrl                   | Serwer proxy dla ruchu HTTPS zestawu SDK (opcjonalny, domyślnie ściągnięty ze `https_proxy` zmiennej środowiskowej).   |
| httpAgent                       | Http. Agent do użycia dla ruchu HTTP zestawu SDK (opcjonalne, domyślnie niezdefiniowane).                                   |
| httpsAgent                      | Protokół https. Agent do użycia dla ruchu HTTPS zestawu SDK (opcjonalnie, domyślnie niezdefiniowany).                                 |
| maxBatchSize                    | Maksymalna liczba elementów telemetrycznych do uwzględnienia w ładunku w punkcie końcowym pozyskiwania (wartość domyślna `250` ).   |
| maxBatchIntervalMs              | Maksymalna ilość czasu oczekiwania na nawiązanie połączenia z maxBatchSize (domyślnie `15000` ).               |
| disableAppInsights              | Flaga wskazująca, czy transmisja danych telemetrycznych jest wyłączona (wartość domyślna `false` ).                                 |
| samplingPercentage              | Wartość procentowa śledzonych elementów telemetrycznych, które powinny zostać przesłane (domyślnie `100` ).                      |
| correlationIdRetryIntervalMs    | Czas oczekiwania przed ponowieniem próby pobrania identyfikatora dla korelacji między składnikami (domyślnie `30000` ).     |
| correlationHeaderExcludedDomains| Lista domen do wykluczenia z iniekcji nagłówka korelacji między składnikami (domyślnie zobacz [Konfiguracja. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts)).|

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie telemetrii w portalu](./overview-dashboard.md)
* [Zapisywanie zapytań analizy za pośrednictwem danych telemetrycznych](../logs/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
