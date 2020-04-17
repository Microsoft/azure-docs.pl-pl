---
title: Interfejs API usługi Application Insights dla zdarzeń niestandardowych i metryk | Dokumenty firmy Microsoft
description: Wstaw kilka wierszy kodu w urządzeniu lub aplikacji klasycznej, na stronie sieci Web lub w usłudze, aby śledzić użycie i diagnozować problemy.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: d6cb2f5ab418e8d3b5935fef535565ccf55a3906
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536951"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk

Wstaw kilka wierszy kodu w aplikacji, aby dowiedzieć się, co użytkownicy robią z nim lub aby pomóc w diagnozowaniu problemów. Dane telemetryczne można wysyłać z aplikacji urządzenia i komputerów klasycznych, klientów sieci Web i serwerów sieci Web. Użyj interfejsu API podstawowych danych telemetrycznych [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) do wysyłania niestandardowych zdarzeń i metryk oraz własnych wersji standardowych danych telemetrycznych. Ten interfejs API jest tym samym interfejsem API, którego używają standardowe moduły zbierające dane usługi Application Insights.

## <a name="api-summary"></a>Podsumowanie interfejsu API

Podstawowy interfejs API jest jednolity na wszystkich platformach, z wyjątkiem kilku odmian, takich jak `GetMetric`(.NET tylko).

| Metoda | Używana do |
| --- | --- |
| [`TrackPageView`](#page-views) |Strony, ekrany, ostrza lub formularze. |
| [`TrackEvent`](#trackevent) |Akcje użytkownika i inne zdarzenia. Służy do śledzenia zachowań użytkowników lub monitorowania wydajności. |
| [`GetMetric`](#getmetric) |Metryki zerowe i wielowymiarowe, centralnie skonfigurowana agregacja, tylko C#. |
| [`TrackMetric`](#trackmetric) |Pomiary wydajności, takie jak długości kolejek niezwiązanych z określonymi zdarzeniami. |
| [`TrackException`](#trackexception) |Rejestrowanie wyjątków dla diagnozy. Śledzenie, gdzie występują w odniesieniu do innych zdarzeń i zbadać ślady stosu. |
| [`TrackRequest`](#trackrequest) |Rejestrowanie częstotliwości i czasu trwania żądań serwera do analizy wydajności. |
| [`TrackTrace`](#tracktrace) |Komunikaty dziennika diagnostyki zasobów. Można również przechwytywać dzienniki innych firm. |
| [`TrackDependency`](#trackdependency) |Rejestrowanie czasu trwania i częstotliwości wywołań do składników zewnętrznych, od których zależy aplikacja. |

Właściwości [i metryki](#properties) można dołączyć do większości tych wywołań telemetrycznych.

## <a name="before-you-start"></a><a name="prep"></a>Przed rozpoczęciem

Jeśli nie masz jeszcze odwołania do SDK usługi Application Insights:

* Dodaj sdk usługi Application Insights do projektu:

  * [ASP.NET projekt](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Projekt Core](../../azure-monitor/app/asp-net-core.md)
  * [Projekt Java](../../azure-monitor/app/java-get-started.md)
  * [Projekt Node.js](../../azure-monitor/app/nodejs.md)
  * [JavaScript na każdej stronie internetowej](../../azure-monitor/app/javascript.md) 
* W kodzie urządzenia lub serwera sieci Web należy uwzględnić:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Pobierz wystąpienie TelemetryClient

Uzyskaj wystąpienie `TelemetryClient` (z wyjątkiem języka JavaScript na stronach internetowych):

W przypadku aplikacji [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) i aplikacji innych niż HTTP/Worker dla aplikacji [.NET/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) zaleca się uzyskanie wystąpienia `TelemetryClient` z kontenera iniekcji zależności, jak wyjaśniono w ich odpowiedniej dokumentacji.

Jeśli używasz AzureFunctions v2+ lub Azure WebJobs v3+ — postępuj zgodnie z tym dokumentem:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Dla każdego, kto widzi tę metodę jest przestarzałe wiadomości odwiedź [microsoft/ApplicationInsights-dotnet#1152,](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) aby uzyskać więcej informacji.

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient jest bezpieczne dla wątków.

W przypadku projektów ASP.NET i Java przychodzące żądania HTTP są przechwytywane automatycznie. Można utworzyć dodatkowe wystąpienia TelemetryClient dla innego modułu aplikacji. Na przykład może mieć jedno wystąpienie TelemetryClient w klasie oprogramowania pośredniczącego do raportowania zdarzeń logiki biznesowej. Można ustawić właściwości, takie jak Identyfikator użytkownika i Identyfikator urządzenia, aby zidentyfikować komputer. Te informacje są dołączone do wszystkich zdarzeń, które wysyła wystąpienie.

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

W projektach Node.js `new applicationInsights.TelemetryClient(instrumentationKey?)` można użyć do utworzenia nowego wystąpienia, ale jest to zalecane `defaultClient`tylko w scenariuszach wymagających izolowanej konfiguracji z singleton .

## <a name="trackevent"></a>TrackEvent (wychocie)

W usłudze Application Insights *zdarzenie niestandardowe* jest punktem danych, który można wyświetlić w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md) jako zagregowana liczba, a w [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md) jako pojedyncze wystąpienia. (Nie jest to związane z MVC lub innych framework "wydarzenia.")

Wstaw `TrackEvent` wywołania w kodzie, aby zliczyć różne zdarzenia. Jak często użytkownicy wybierają konkretną funkcję, jak często osiągają określone cele, a może jak często popełniają określone rodzaje błędów.

Na przykład w aplikacji do gier wysyłaj zdarzenie za każdym razem, gdy użytkownik wygra grę:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Zdarzenia niestandardowe w Analytics

Dane telemetryczne są `customEvents` dostępne w tabeli w [usłudze Application Insights Analytics](analytics.md). Każdy wiersz reprezentuje `trackEvent(..)` wywołanie w aplikacji.

Jeśli [próbkowanie](../../azure-monitor/app/sampling.md) jest w działaniu, itemCount właściwość pokazuje wartość większą niż 1. Na przykład itemCount==10 oznacza, że z 10 wywołań trackEvent(), proces próbkowania tylko przesyłane jeden z nich. Aby uzyskać prawidłową liczbę zdarzeń niestandardowych, należy `customEvents | summarize sum(itemCount)`użyć kodu, takiego jak .

## <a name="getmetric"></a>GetMetric ( GetMetric )

### <a name="examples"></a>Przykłady

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric (metryk)

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric nie jest preferowaną metodą wysyłania metryk. Metryki powinny być zawsze wstępnie zagregowane w okresie czasu przed wysłaniem. Użyj jednego z przeciążenia GetMetric(..), aby uzyskać obiekt metryki dostępu do możliwości wstępnej agregacji SDK. Jeśli implementujesz własną logikę wstępnej agregacji, można użyć TrackMetric() metody do wysyłania agregacji wynikowych. Jeśli aplikacja wymaga wysyłania oddzielnego elementu telemetrii przy każdej okazji bez agregacji w czasie, prawdopodobnie ma przypadek użycia danych telemetrycznych zdarzeń; zobacz TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Usługa Application Insights może wykres metryki, które nie są dołączone do określonych zdarzeń. Na przykład można monitorować długość kolejki w regularnych odstępach czasu. W przypadku danych poszczególne pomiary są mniej interesujące niż odmiany i trendy, dlatego przydatne są wykresy statystyczne.

Aby wysłać metryki do usługi Application `TrackMetric(..)` Insights, można użyć interfejsu API. Istnieją dwa sposoby wysyłania danych:

* Pojedyncza wartość. Za każdym razem, gdy wykonujesz pomiar w aplikacji, wysyłasz odpowiednią wartość do usługi Application Insights. Załóżmy na przykład, że masz metrykę opisującą liczbę elementów w kontenerze. W określonym okresie czasu najpierw umieścić trzy elementy w kontenerze, a następnie usunąć dwa elementy. W związku z `TrackMetric` tym należy wywołać `3` dwa razy: najpierw przekazując wartość, a następnie wartość `-2`. Usługa Application Insights przechowuje obie wartości w Twoim imieniu.

* Agregacja. Podczas pracy z metrykami, każdy pojedynczy pomiar rzadko jest interesujący. Zamiast tego ważne jest podsumowanie tego, co wydarzyło się w danym okresie. Takie podsumowanie nazywa się _agregacją_. W powyższym przykładzie suma metryki `1` zagregowanej dla tego okresu `2`jest i liczba wartości metryki jest . Korzystając z metody agregacji, `TrackMetric` można wywołać tylko raz na okres czasu i wysłać wartości agregacji. Jest to zalecane podejście, ponieważ może znacznie zmniejszyć koszty i wydajność, wysyłając mniej punktów danych do usługi Application Insights, jednocześnie zbierając wszystkie istotne informacje.

### <a name="examples"></a>Przykłady

#### <a name="single-values"></a>Pojedyncze wartości

Aby wysłać pojedynczą wartość metryki:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Niestandardowe dane w Analytics

Dane telemetryczne są `customMetrics` dostępne w tabeli w [usłudze Application Insights Analytics](analytics.md). Każdy wiersz reprezentuje `trackMetric(..)` wywołanie w aplikacji.

* `valueSum`- Jest to suma pomiarów. Aby uzyskać średnią wartość, `valueCount`podziel przez .
* `valueCount`- Liczba pomiarów, które zostały `trackMetric(..)` zagregowane w tym wywołaniu.

## <a name="page-views"></a>Wyświetlenia strony

W aplikacji urządzenia lub strony sieci Web dane telemetryczne widoku strony są wysyłane domyślnie podczas ładowania każdego ekranu lub strony. Ale można to zmienić, aby śledzić widoki stron w dodatkowych lub różnych godzinach. Na przykład w aplikacji, która wyświetla karty lub bloków, można śledzić stronę, gdy użytkownik otwiera nowy blok.

Dane użytkownika i sesji są wysyłane jako właściwości wraz z widokami stron, dzięki czemu wykresy użytkownika i sesji ożywają, gdy istnieje telemetria widoku strony.

### <a name="custom-page-views"></a>Niestandardowe widoki stron

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Jeśli masz kilka kart na różnych stronach HTML, możesz również określić adres URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Widoki stron chronometrażu

Domyślnie czasy zgłaszane jako **czas ładowania widoku strony** są mierzone od momentu wysłania żądania przez przeglądarkę, aż do wywołania zdarzenia wczytywanego strony przeglądarki.

Zamiast tego możesz:

* Ustaw jawny czas trwania w `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` [wywołaniu trackPageView:](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) .
* Użyj wywołań `startTrackPage` chronometrażu widoku strony i `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Nazwa używana jako pierwszy parametr kojarzy wywołania start i stop. Domyślnie jest to bieżąca nazwa strony.

Wynikowe czasy ładowania strony wyświetlane w Eksploratorze metryk są uzyskiwane z interwału między wywołaniami początkowymi i zatrzymywania. To od Ciebie zależy, jaki interwał rzeczywiście czas.

### <a name="page-telemetry-in-analytics"></a>Telemetria strony w Analytics

W [Analytics](analytics.md) dwie tabele pokazują dane z operacji przeglądarki:

* Tabela `pageViews` zawiera dane dotyczące adresu URL i tytułu strony
* Tabela `browserTimings` zawiera dane dotyczące wydajności klienta, takie jak czas przetwarzania przychodzących danych

Aby dowiedzieć się, jak długo trwa przetwarzanie różnych stron przez przeglądarkę:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Aby odkryć popularność różnych przeglądarek:

```kusto
pageViews
| summarize count() by client_Browser
```

Aby skojarzyć widoki strony z wywołaniami AJAX, dołącz do zależności:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>Prośba o śledzenie

SDK serwera używa TrackRequest do rejestrowania żądań HTTP.

Można również wywołać go samodzielnie, jeśli chcesz symulować żądania w kontekście, w którym nie masz uruchomiony moduł usługi sieci web.

Jednak zalecanym sposobem wysyłania danych telemetrycznych żądania jest, gdy żądanie działa jako <a href="#operation-context">kontekst operacji</a>.

## <a name="operation-context"></a>Kontekst operacji

Można skorelować elementy telemetrii ze sobą, kojarząc je z kontekstem operacji. Standardowy moduł śledzenia żądań robi to dla wyjątków i innych zdarzeń, które są wysyłane podczas przetwarzania żądania HTTP. W [wyszukiwarce](../../azure-monitor/app/diagnostic-search.md) i [analytics](analytics.md)można łatwo znaleźć wszystkie zdarzenia skojarzone z żądaniem przy użyciu jego identyfikatora operacji.

Zobacz [korelacji telemetrii w usłudze Application Insights, aby](../../azure-monitor/app/correlation.md) uzyskać więcej informacji na temat korelacji.

Podczas ręcznego śledzenia telemetrii, najprostszy sposób, aby zapewnić korelację telemetrii przy użyciu tego wzorca:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Wraz z ustawieniem `StartOperation` kontekstu operacji tworzy element telemetrii o typie, który określisz. Wysyła element telemetrii podczas usuwania operacji lub jeśli `StopOperation`jawnie wywołać . Jeśli używasz `RequestTelemetry` jako typ telemetrii, jego czas trwania jest ustawiony na przedział czasu między start i stop.

Elementy telemetrii zgłaszane w ramach operacji stają się "elementami podrzędnymi" takiej operacji. Konteksty operacji mogą być zagnieżdżone.

W wyszukiwaniu kontekst operacji jest używany do tworzenia listy **Elementy pokrewne:**

![Powiązane elementy](./media/api-custom-events-metrics/21.png)

Aby uzyskać więcej informacji na temat śledzenia operacji niestandardowych, zobacz Śledzenie operacji niestandardowych za [pomocą narzędzia Application Insights .NET SDK.](../../azure-monitor/app/custom-operations-tracking.md)

### <a name="requests-in-analytics"></a>Żądania w analytics

W [usłudze Application Insights](analytics.md)Analytics `requests` żądania są wyświetlane w tabeli.

Jeśli [próbkowanie](../../azure-monitor/app/sampling.md) jest w działaniu, itemCount właściwość pokaże wartość większą niż 1. Na przykład itemCount==10 oznacza, że z 10 wywołań trackRequest(), proces próbkowania tylko przesyłane jeden z nich. Aby uzyskać prawidłową liczbę żądań i średni czas trwania podzielony na segmenty według nazw żądań, użyj kodu, takiego jak:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException (Nieekscesytorię

Wysyłaj wyjątki do usługi Application Insights:

* Aby [je policzyć](../../azure-monitor/platform/metrics-charts.md), jako wskazanie częstotliwości problemu.
* Aby [zbadać poszczególne zdarzenia](../../azure-monitor/app/diagnostic-search.md).

Raporty zawierają ślady stosu.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

SDKs złapać wiele wyjątków automatycznie, więc nie zawsze trzeba wywołać TrackException jawnie.

* ASP.NET: Napisz [kod, aby złapać wyjątki](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Wyjątki są przechwytywane automatycznie](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Wyjątki są przechwytywane automatycznie. Jeśli chcesz wyłączyć automatyczną kolekcję, dodaj wiersz do fragmentu kodu wstawionego na stronach sieci Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Wyjątki w analytics

W [usłudze Application Insights Analytics](analytics.md) `exceptions` wyjątki są wyświetlane w tabeli.

Jeśli [pobieranie próbek](../../azure-monitor/app/sampling.md) jest `itemCount` w działaniu, właściwość pokazuje wartość większą niż 1. Na przykład itemCount==10 oznacza, że z 10 wywołań trackException(), proces próbkowania tylko przesłane jeden z nich. Aby uzyskać prawidłową liczbę wyjątków podzielonych na segmenty według typu wyjątku, należy użyć kodu, takiego jak:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Większość ważnych informacji o stosie jest już wyodrębniona do oddzielnych zmiennych, ale można rozłożyć strukturę, `details` aby uzyskać więcej. Ponieważ ta struktura jest dynamiczna, należy rzutować wynik do typu, którego oczekujesz. Przykład:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Aby skojarzyć wyjątki z powiązanymi żądaniami, użyj sprzężenia:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>Śledzenie śladu

Użyj TrackTrace, aby pomóc zdiagnozować problemy, wysyłając "ścieżka naciągliwość" do usługi Application Insights. Można wysyłać fragmenty danych diagnostycznych i sprawdzać je w [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md).

W [.NET Log karty](../../azure-monitor/app/asp-net-trace-logs.md) używają tego interfejsu API do wysyłania dzienników innych firm do portalu.

W języku Java dla [rejestratorów standardowych, takich jak Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) używać aplikacji Insights Log4j lub Logback Appenders do wysyłania dzienników innych firm do portalu.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript po stronie klienta/przeglądarki*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Rejestrowanie zdarzenia diagnostycznego, takiego jak wprowadzanie lub opuszczanie metody.

 Parametr | Opis
---|---
`message` | Dane diagnostyczne. Może być znacznie dłuższy niż nazwa.
`properties` | Mapa ciągu do ciągu: Dodatkowe dane używane do [filtrowania wyjątków](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) w portalu. Domyślnie jest pusty.
`severityLevel` | Obsługiwane wartości: [severityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Możesz wyszukiwać zawartość wiadomości, ale (w przeciwieństwie do wartości właściwości) nie można na niej filtrować.

Limit rozmiaru `message` jest znacznie wyższy niż limit właściwości.
Zaletą TrackTrace jest to, że można umieścić stosunkowo długie dane w wiadomości. Na przykład można tam zakodować dane POST.  

Ponadto można dodać poziom ważności do wiadomości. Podobnie jak inne dane telemetryczne, można dodać wartości właściwości, aby ułatwić filtrowanie lub wyszukiwanie różnych zestawów śladów. Przykład:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

W [wyszukiwarce](../../azure-monitor/app/diagnostic-search.md)można następnie łatwo odfiltrować wszystkie komunikaty o określonym poziomie ważności, które odnoszą się do określonej bazy danych.

### <a name="traces-in-analytics"></a>Ślady w analytics

W [usłudze Application Insights Analytics](analytics.md)wywołania `traces` TrackTrace są wyświetlane w tabeli.

Jeśli [próbkowanie](../../azure-monitor/app/sampling.md) jest w działaniu, itemCount właściwość pokazuje wartość większą niż 1. Na przykład itemCount==10 oznacza, że `trackTrace()`z 10 wywołań do , proces próbkowania tylko przesłane jeden z nich. Aby uzyskać poprawną liczbę wywołań śledzenia, należy `traces | summarize sum(itemCount)`użyć kodu, takiego jak .

## <a name="trackdependency"></a>ŚledzenieZależności

Użyj TrackDependency wywołania do śledzenia czasów odpowiedzi i wskaźników powodzenia wywołań do zewnętrznego fragmentu kodu. Wyniki są wyświetlane na wykresach zależności w portalu. Fragment kodu poniżej należy dodać wszędzie tam, gdzie jest wywoływane zależności.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Należy pamiętać, że moduły SDK serwera zawierają [moduł zależności,](../../azure-monitor/app/asp-net-dependencies.md) który automatycznie odnajduje i śledzi niektóre wywołania zależności — na przykład do baz danych i interfejsów API REST. Musisz zainstalować agenta na serwerze, aby moduł działał. 

W języku Java niektóre wywołania zależności mogą być automatycznie śledzone za pomocą [programu Java Agent](../../azure-monitor/app/java-agent.md).

Użyj tego połączenia, jeśli chcesz śledzić połączenia, których automatyczne śledzenie nie jest przechwytywać lub jeśli nie chcesz instalować agenta.

Aby wyłączyć standardowy moduł śledzenia zależności w języku C#, edytuj [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) i usuń odwołanie do `DependencyCollector.DependencyTrackingTelemetryModule`pliku . W języku Java, proszę nie instalować agenta java, jeśli nie chcesz automatycznie zbierać standardowe zależności.

### <a name="dependencies-in-analytics"></a>Zależności w analytics

W [usłudze Application Insights Analytics](analytics.md)w `dependencies` tabeli są wyświetlane wywołania śledzeniazależności.

Jeśli [próbkowanie](../../azure-monitor/app/sampling.md) jest w działaniu, itemCount właściwość pokazuje wartość większą niż 1. Na przykład itemCount==10 oznacza, że z 10 wywołań trackDependency(), proces próbkowania tylko przesłane jeden z nich. Aby uzyskać poprawną liczbę zależności podzielonych na segmenty według składnika docelowego, użyj kodu, takiego jak:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Aby skojarzyć zależności z powiązanymi żądaniami, użyj sprzężenia:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Dane opróżniania

Zwykle zestaw SDK wysyła dane w stałych odstępach czasu (zazwyczaj 30 sekund) lub gdy bufor jest pełny (zazwyczaj 500 elementów). Jednak w niektórych przypadkach można opróżnić bufor — na przykład, jeśli używasz SDK w aplikacji, która zostanie zamknięta.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Funkcja jest asynchroniczną dla [kanału telemetrii serwera](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

W idealnym przypadku metoda flush() powinna być używana w działaniu zamykania aplikacji.

## <a name="authenticated-users"></a>Uwierzytelnieni użytkownicy

W aplikacji internetowej użytkownicy są (domyślnie) identyfikowani za pomocą plików cookie. Użytkownik może być liczony więcej niż jeden raz, jeśli uzyskuje dostęp do aplikacji z innego komputera lub przeglądarki lub jeśli usunie pliki cookie.

Jeśli użytkownicy logują się do aplikacji, możesz uzyskać dokładniejszą liczbę, ustawiając uwierzytelniony identyfikator użytkownika w kodzie przeglądarki:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

W ASP.NET internetowej aplikacji MVC, na przykład:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Nie jest konieczne użycie rzeczywistej nazwy logowania użytkownika. Musi to być tylko identyfikator, który jest unikatowy dla tego użytkownika. Nie może zawierać spacji ani `,;=|`żadnych znaków .

Identyfikator użytkownika jest również ustawiony w pliku cookie sesji i wysyłany do serwera. Jeśli jest zainstalowany pakiet SDK serwera, uwierzytelniony identyfikator użytkownika jest wysyłany jako część właściwości kontekstu danych telemetrycznych klienta i serwera. Następnie można filtrować i wyszukiwać na nim.

Jeśli aplikacja grupuje użytkowników na konta, możesz również przekazać identyfikator konta (z tymi samymi ograniczeniami znaków).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

W [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md)można utworzyć wykres zlicza **konta Użytkowników, Uwierzytelnionych**i **Użytkowników.**

Można również [wyszukiwać](../../azure-monitor/app/diagnostic-search.md) punkty danych klienta z określonymi nazwami użytkowników i kontami.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrowanie, wyszukiwanie i segmentowanie danych przy użyciu właściwości

Właściwości i pomiary można dołączać do zdarzeń (a także do metryk, wyświetleń strony, wyjątków i innych danych telemetrycznych).

Właściwości są *wartościami* ciągu, których można użyć do filtrowania danych telemetrycznych w raportach użycia. Jeśli na przykład aplikacja udostępnia kilka gier, możesz dołączyć nazwę gry do każdego wydarzenia, aby zobaczyć, które gry są bardziej popularne.

Istnieje limit 8192 na długości ciągu. (Jeśli chcesz wysłać duże fragmenty danych, użyj parametru wiadomości TrackTrace).

*Metryki* są wartościami liczbowymi, które można przedstawić graficznie. Na przykład możesz sprawdzić, czy wyniki są stopniowo zwiększane przez graczy. Wykresy mogą być podzielone na segmenty według właściwości, które są wysyłane ze zdarzeniem, dzięki czemu można uzyskać oddzielne lub ułożone wykresy dla różnych gier.

Aby wartości metryki były poprawnie wyświetlane, powinny być większe lub równe 0.

Istnieją pewne [ograniczenia dotyczące liczby właściwości, wartości właściwości i metryki,](#limits) których można użyć.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Należy uważać, aby nie rejestrować danych osobowych w obiektach.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatywny sposób ustawiania właściwości i danych

Jeśli jest to wygodniejsze, można zebrać parametry zdarzenia w oddzielnym obiekcie:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Nie używać ponownie tego samego wystąpienia`event` elementu telemetrii (w tym przykładzie) do wywołania Track*() wiele razy. Może to spowodować wysłanie danych telemetrycznych z niepoprawną konfiguracją.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Niestandardowe pomiary i właściwości w Analytics

W [Analytics](analytics.md)niestandardowe metryki i `customMeasurements` właściwości `customDimensions` są wyświetlane w atrybutach i atrybutach każdego rekordu telemetrii.

Na przykład, jeśli dodano właściwość o nazwie "gra" do telemetrii żądania, to zapytanie zlicza wystąpienia różnych wartości "gry" i pokazuje średnią metryki niestandardowej "wynik":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Należy zauważyć, że:

* Po wyodrębnieniu wartości z niestandardowegoDymensions lub customZesury JSON, ma typ dynamiczny, `tostring` `todouble`a więc należy go oddać lub .
* Aby wziąć pod uwagę możliwość [pobierania próbek,](../../azure-monitor/app/sampling.md)należy użyć `sum(itemCount)`, nie `count()`.

## <a name="timing-events"></a><a name="timed"></a>Zdarzenia chronometrażu

Czasami chcesz wykres, jak długo trwa do wykonania akcji. Na przykład warto wiedzieć, jak długo użytkownicy rozważają wybór w grze. W tym celu można użyć parametru pomiaru.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Domyślne właściwości niestandardowych danych telemetrycznych

Jeśli chcesz ustawić domyślne wartości właściwości dla niektórych zdarzeń niestandardowych, które piszesz, można ustawić je w telemetryClient wystąpienia. Są one dołączone do każdego elementu telemetrii, który jest wysyłany z tego klienta.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Poszczególne wywołania telemetryczne można zastąpić wartości domyślne w słownikach właściwości.

*W przypadku klientów javascript korzystających*z sieci Web użyj inicjatorów telemetrii JavaScript.

*Aby dodać właściwości do wszystkich danych telemetrycznych,* w tym dane ze standardowych modułów zbierania, [należy zaimplementować `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Pobieranie próbek, filtrowanie i przetwarzanie danych telemetrycznych

Można napisać kod do przetwarzania danych telemetrycznych, zanim jest wysyłany z SDK. Przetwarzanie obejmuje dane wysyłane ze standardowych modułów telemetrycznych, takich jak zbieranie żądań HTTP i zbieranie zależności.

[Dodaj właściwości](../../azure-monitor/app/api-filtering-sampling.md#add-properties) do telemetrii, implementując `ITelemetryInitializer`. Na przykład można dodać numery wersji lub wartości, które są obliczane na podstawie innych właściwości.

[Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md#filtering) można modyfikować lub odrzucać dane telemetryczne przed `ITelemetryProcessor`wysłaniem z SDK przez wdrożenie . Możesz kontrolować, co jest wysyłane lub odrzucane, ale musisz uwzględnić wpływ na dane. W zależności od sposobu odrzucania elementów może być utrata możliwości przechodzenia między powiązanymi elementami.

[Próbkowanie](../../azure-monitor/app/api-filtering-sampling.md) to rozwiązanie pakowane, aby zmniejszyć ilość danych wysyłanych z aplikacji do portalu. Robi to bez wpływu na wyświetlane metryki. I robi to bez wpływu na zdolność do diagnozowania problemów, przechodząc między powiązanymi elementami, takimi jak wyjątki, żądania i widoki stron.

[Dowiedz się więcej](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Wyłączanie danych telemetrycznych

Aby *dynamicznie zatrzymać i rozpocząć* zbieranie i przesyłanie danych telemetrycznych:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Aby *wyłączyć wybrane moduły zbierające standardowe*- na przykład liczniki wydajności, żądania HTTP lub zależności - usunąć lub skomentować odpowiednie wiersze w pliku [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Można to zrobić, na przykład, jeśli chcesz wysłać własne dane TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Aby *wyłączyć wybrane moduły zbierające standardowe*— na przykład liczniki wydajności, żądania HTTP lub zależności — w czasie inicjowania, metody konfiguracji łańcucha do kodu inicjowania SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Aby wyłączyć te moduły zbierające po zainicjowaniu, użyj obiektu Konfiguracja:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Tryb dewelopera

Podczas debugowania warto przyspieszyć dane telemetryczne za pośrednictwem potoku, dzięki czemu można natychmiast wyświetlić wyniki. Można również uzyskać dodatkowe komunikaty, które ułatwiają śledzenie wszelkich problemów z telemetrii. Wyłącz go w produkcji, ponieważ może to spowolnić aplikację.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

W przypadku pliku Node.js można włączyć tryb deweloperski, włączając wewnętrzne rejestrowanie za pośrednictwem `setInternalLogging` i ustawienie `maxBatchSize` na 0, co powoduje, że dane telemetryczne mają być wysyłane natychmiast po jej zebraniu.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>Ustawianie klawisza instrumentacji dla wybranych niestandardowych danych telemetrycznych

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamiczny klucz oprzyrządowania

Aby uniknąć mieszania danych telemetrycznych ze środowisk deweloperskich, testowych i produkcyjnych, można [utworzyć oddzielne zasoby usługi Application Insights](../../azure-monitor/app/create-new-resource.md ) i zmienić ich klucze, w zależności od środowiska.

Zamiast owyzależniania klucza instrumentacji z pliku konfiguracyjnego, można go ustawić w kodzie. Ustaw klucz w metodzie inicjowania, takiej jak global.aspx.cs w usłudze ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Na stronach sieci Web można ustawić go ze stanu serwera sieci web, zamiast kodować go dosłownie do skryptu. Na przykład na stronie sieci Web wygenerowanej w aplikacji ASP.NET:

*JavaScript w razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetriaContext

TelemetryClient ma context właściwości, która zawiera wartości, które są wysyłane wraz ze wszystkimi danymi telemetrycznymi. Są one zwykle ustawiane przez standardowe moduły telemetryczne, ale można je również ustawić samodzielnie. Przykład:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Jeśli samodzielnie ustawisz dowolną z tych wartości, rozważ usunięcie odpowiedniego wiersza z [pliku ApplicationInsights.config,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)aby wartości i wartości standardowe nie były mylone.

* **Składnik:** Aplikacja i jej wersja.
* **Urządzenie**: Dane o urządzeniu, na którym aplikacja jest uruchomiona. (W aplikacjach sieci Web jest to serwer lub urządzenie klienckie, z których jest wysyłana dane telemetryczne).
* **InstrumentationKey:** Zasób usługi Application Insights na platformie Azure, w którym pojawia się dane telemetryczne. To zwykle podniósł z ApplicationInsights.config.
* **Lokalizacja:** Położenie geograficzne urządzenia.
* **Operacja**: W aplikacjach sieci web bieżące żądanie HTTP. W innych typach aplikacji można ustawić to tak, aby grupować zdarzenia razem.
  * **Identyfikator:** wygenerowana wartość, która koreluje różne zdarzenia, dzięki czemu podczas sprawdzania dowolnego zdarzenia w wyszukiwaniu diagnostycznym można znaleźć powiązane elementy.
  * **Nazwa**: Identyfikator, zwykle adres URL żądania HTTP.
  * **SyntheticSource**: Jeśli nie null lub puste, ciąg, który wskazuje, że źródło żądania został zidentyfikowany jako robota lub testu sieci web. Domyślnie jest wykluczony z obliczeń w Eksploratorze metryk.
* **Właściwości:** Właściwości, które są wysyłane ze wszystkimi danymi telemetrycznymi. Można go zastąpić w poszczególnych wywołaniach Track*.
* **Sesja**: Sesja użytkownika. Identyfikator jest ustawiony na wartość wygenerowaną, która jest zmieniana, gdy użytkownik nie był aktywny przez pewien czas.
* **Użytkownik**: Informacje o użytkowniku.

## <a name="limits"></a>Limity

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Aby uniknąć osiągnięcia limitu szybkości danych, należy użyć [próbkowania](../../azure-monitor/app/sampling.md).

Aby ustalić, jak długo dane są przechowywane, zobacz [Przechowywanie danych i prywatność](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Dokumenty referencyjne

* [ASP.NET referencje](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Dokumentacja języka Java](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [Odwołanie do języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>Kod zestawu SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pakiety systemu Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Zestaw SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Zestaw SDK dla platformy Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Pytania

* *Jakie wyjątki mogą Track_() wywołania zgłosić?*

    Brak. Nie trzeba zawijać je w klauzule try-catch. Jeśli SDK napotka problemy, będzie rejestrować komunikaty w danych wyjściowych konsoli debugowania i — jeśli wiadomości są za pośrednictwem — w wyszukiwaniu diagnostycznym.
* *Czy istnieje interfejs API REST, aby uzyskać dane z portalu?*

    Tak, [interfejs API dostępu do danych](https://dev.applicationinsights.io/). Inne sposoby wyodrębniania danych obejmują [eksport z Analytics do usługi Power BI](../../azure-monitor/app/export-power-bi.md ) i eksport [ciągły.](../../azure-monitor/app/export-telemetry.md)

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md)
* [Rozwiązywanie problemów](../../azure-monitor/app/troubleshoot-faq.md)
