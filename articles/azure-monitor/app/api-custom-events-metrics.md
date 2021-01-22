---
title: Application Insights API dla niestandardowych zdarzeń i metryk | Microsoft Docs
description: Wstaw kilka wierszy kodu z urządzenia lub aplikacji klasycznej, strony sieci Web lub usługi, aby śledzić użycie i diagnozować problemy.
ms.topic: conceptual
ms.date: 05/11/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8fecca4875ba291da093bf1eea596eef290f80c8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678116"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk

Wstaw kilka wierszy kodu w aplikacji, aby dowiedzieć się, co użytkownicy robią z nim lub aby pomóc zdiagnozować problemy. Możesz wysyłać dane telemetryczne z aplikacji urządzenia i pulpitu, klientów sieci Web i serwerów sieci Web. Za pomocą interfejsu API telemetrii [Azure Application Insights](./app-insights-overview.md) Core można wysyłać niestandardowe zdarzenia i metryki oraz własne wersje telemetrii standardowej. Ten interfejs API jest tym samym interfejsem API, który jest używany przez moduły zbierające dane standardowe Application Insights.

## <a name="api-summary"></a>Podsumowanie interfejsu API

Podstawowy interfejs API jest jednolity na wszystkich platformach, niezależnie od kilku wariantów, takich jak `GetMetric` (tylko platforma .NET).

| Metoda | Sposób użycia |
| --- | --- |
| [`TrackPageView`](#page-views) |Strony, ekrany, bloki lub formularze. |
| [`TrackEvent`](#trackevent) |Akcje użytkownika i inne zdarzenia. Służy do śledzenia zachowania użytkownika lub do monitorowania wydajności. |
| [`GetMetric`](#getmetric) |Metryki jednowymiarowe i wielowymiarowe, w centralnie skonfigurowanej agregacji, tylko w języku C#. |
| [`TrackMetric`](#trackmetric) |Pomiary wydajności, takie jak długość kolejki, nie są związane z określonymi zdarzeniami. |
| [`TrackException`](#trackexception) |Wyjątki rejestrowania dla diagnostyki. Śledzenie tam, gdzie występują w odniesieniu do innych zdarzeń i sprawdzanie śladów stosu. |
| [`TrackRequest`](#trackrequest) |Rejestrowanie częstotliwości i czasu trwania żądań serwera na potrzeby analizy wydajności. |
| [`TrackTrace`](#tracktrace) |Komunikaty dziennika diagnostyki zasobów. Możesz również przechwytywać dzienniki innych firm. |
| [`TrackDependency`](#trackdependency) |Rejestrowanie czasu trwania i częstotliwości wywołań dla składników zewnętrznych, od których zależy aplikacja. |

Można [dołączać właściwości i metryki](#properties) do większości tych wywołań telemetrii.

## <a name="before-you-start"></a><a name="prep"></a>Przed rozpoczęciem

Jeśli nie masz jeszcze odwołania do Application Insights SDK:

* Dodaj zestaw Application Insights SDK do projektu:

  * [Projekt ASP.NET](./asp-net.md)
  * [Projekt ASP.NET Core](./asp-net-core.md)
  * [Projekt Java](./java-get-started.md)
  * [ ProjektNode.js](./nodejs.md)
  * [Język JavaScript na każdej stronie sieci Web](./javascript.md) 
* W kodzie urządzenia lub serwera sieci Web należy uwzględnić następujące:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Język Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Pobierz wystąpienie TelemetryClient

Pobierz wystąpienie `TelemetryClient` (z wyjątkiem języka JavaScript w stronach sieci Web):

W przypadku aplikacji [ASP.NET Core](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) i [innych niż http/Worker dla aplikacji platformy .NET/.NET Core](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) zaleca się uzyskanie wystąpienia `TelemetryClient` z kontenera iniekcji zależności zgodnie z opisem w odpowiedniej dokumentacji.

Jeśli używasz AzureFunctions v2 + lub Azure WebJobs v3 +-wykonaj następujące czynności: https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
Dla każdej osoby, która widzi tę metodę, są przestarzałe komunikaty. Aby uzyskać więcej informacji, odwiedź stronę [Microsoft/ApplicationInsights-dotnet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) .

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

TelemetryClient jest bezpieczny wątkowo.

W przypadku projektów ASP.NET i Java przychodzące żądania HTTP są przechwytywane automatycznie. Możesz chcieć utworzyć dodatkowe wystąpienia TelemetryClient dla innego modułu aplikacji. Na przykład może istnieć jedno wystąpienie TelemetryClient w klasie pośredniczącej do raportowania zdarzeń logiki biznesowej. Możesz ustawić właściwości, takie jak UserId i DeviceId, aby zidentyfikować maszynę. Te informacje są dołączone do wszystkich zdarzeń wysyłanych przez wystąpienie.

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

W projektach Node.js można użyć `new applicationInsights.TelemetryClient(instrumentationKey?)` programu do utworzenia nowego wystąpienia, ale jest to zalecane tylko w scenariuszach, które wymagają wyizolowanej konfiguracji z pojedynczej `defaultClient` .

## <a name="trackevent"></a>Poleceń trackEvent

W Application Insights *zdarzeniu niestandardowym* jest punkt danych, który można wyświetlić w [Eksplorator metryk](../platform/metrics-charts.md) jako zagregowana liczba, a w przypadku [wyszukiwania diagnostycznego](./diagnostic-search.md) jako pojedyncze wystąpienia. (Nie jest to związane ze zdarzeniami MVC ani innymi platformami ").

Wstaw `TrackEvent` wywołania w kodzie, aby obliczyć różne zdarzenia. Jak często użytkownicy wybierają konkretną funkcję, jak często osiągają określone cele, lub mogą często wprowadzać określone typy błędów.

Na przykład w aplikacji do gier Wyślij zdarzenie za każdym razem, gdy użytkownik wygrywa z gry:

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

### <a name="custom-events-in-analytics"></a>Zdarzenia niestandardowe w analizie

Dane telemetryczne są dostępne w `customEvents` tabeli na [karcie Dzienniki Application Insights](../log-query/log-query-overview.md) lub [środowisko użycia](usage-overview.md). Zdarzenia mogą pochodzić z `trackEvent(..)` lub [kliknąć wtyczki autokolekcji analizy](javascript-click-analytics-plugin.md).

 

Jeśli [próbkowanie](./sampling.md) jest w operacji, właściwość itemCount pokazuje wartość większą niż 1. Na przykład itemCount = = 10 oznacza, że z 10 wywołań do poleceń trackEvent () proces próbkowania przekazał tylko jeden z nich. Aby uzyskać poprawną liczbę zdarzeń niestandardowych, należy w związku z tym użyć kodu, takiego jak `customEvents | summarize sum(itemCount)` .

## <a name="getmetric"></a>GetMetric

Aby dowiedzieć się, jak efektywnie używać wywołania GetMetric () do przechwytywania wstępnie zagregowanych metryk dla aplikacji .NET i .NET Core, przejdź do dokumentacji usługi [Getmetrics](./get-metric.md) .

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric nie jest preferowaną metodą wysyłania metryk. Metryki powinny zawsze być wstępnie agregowane w danym okresie czasu przed wysłaniem. Użyj jednego z przeciążeń GetMetric (...), aby uzyskać obiekt metryki na potrzeby uzyskiwania dostępu do funkcji wstępnej agregacji zestawu SDK. Jeśli wdrażasz własną logikę przed agregacją, możesz użyć metody TrackMetric (), aby wysłać wyniki zagregowane. Jeśli aplikacja wymaga wysyłania oddzielnego elementu telemetrii przy każdej okazji bez agregacji w czasie, to być będzie przypadek użycia dla telemetrii zdarzeń; Zobacz TelemetryClient. poleceń trackEvent (Microsoft. ApplicationInsights. DataContracts. EventTelemetry).

Application Insights mogą wykresów metryk, które nie są dołączone do określonych zdarzeń. Można na przykład monitorować długość kolejki w regularnych odstępach czasu. Za pomocą metryk poszczególne pomiary są mniej interesujące niż wahania i trendy, dlatego są przydatne wykresy statystyczne.

Aby wysłać metryki do Application Insights, można użyć `TrackMetric(..)` interfejsu API. Istnieją dwa sposoby wysłania metryki:

* Pojedyncza wartość. Za każdym razem, gdy wykonujesz pomiary w aplikacji, wysyłaj odpowiednią wartość do Application Insights. Załóżmy na przykład, że masz metrykę opisującą liczbę elementów w kontenerze. W określonym przedziale czasu najpierw należy umieścić trzy elementy w kontenerze, a następnie usunąć dwa elementy. W związku z tym należy wywoływać `TrackMetric` dwa razy: najpierw przekazując wartość `3` , a następnie wartość `-2` . Application Insights przechowuje obie wartości w Twoim imieniu.

* Agregacji. Podczas pracy z metrykami każdy pojedynczy pomiar jest rzadko interesujący. Zamiast tego podsumowanie informacji o tym, co się stało w określonym przedziale czasu, jest ważne. Takie podsumowanie jest nazywane _agregacją_. W powyższym przykładzie zagregowana suma metryk dla tego okresu jest `1` i liczba wartości metryki `2` . W przypadku użycia podejścia agregacji, można wywołać tylko `TrackMetric` raz dla każdego okresu i wysłać wartości zagregowane. Jest to zalecane podejście, ponieważ może znacząco zmniejszyć koszty i obciążenie wydajności przez wysłanie mniejszej liczby punktów danych do Application Insights, przy jednoczesnym zebraniu wszystkich istotnych informacji.

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

### <a name="custom-metrics-in-analytics"></a>Metryki niestandardowe w analizie

Dane telemetryczne są dostępne w `customMetrics` tabeli w [Application Insights Analytics](../log-query/log-query-overview.md). Każdy wiersz reprezentuje wywołanie `trackMetric(..)` w aplikacji.

* `valueSum` -Suma pomiarów. Aby uzyskać wartość średnią, Podziel przez `valueCount` .
* `valueCount` -Liczba pomiarów, które zostały zagregowane w tym `trackMetric(..)` wywołaniu.

## <a name="page-views"></a>Wyświetlenia stron

W aplikacji na urządzeniu lub stronie sieci Web widok strony jest domyślnie wysyłany podczas ładowania każdego ekranu lub strony. Można jednak zmienić to, aby śledzić widoki stron w dodatkowych lub różnych porach. Na przykład w aplikacji, która wyświetla karty lub bloki, możesz chcieć śledzić stronę za każdym razem, gdy użytkownik otworzy nowy blok.

Dane użytkownika i sesji są wysyłane jako właściwości wraz z widokami stron, dzięki czemu wykresy użytkownika i sesji są aktywne w przypadku wyświetlenia danych telemetrycznych widoku strony.

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

Jeśli masz kilka kart w różnych stronach HTML, możesz określić za pomocą adresu URL:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Widok strony chronometrażu

Domyślnie czasy raportowane jako **czas ładowania widoku strony** są mierzone od momentu, gdy przeglądarka wysyła żądanie, dopóki nie zostanie wywołane zdarzenie ładowania strony w przeglądarce.

Zamiast tego można:

* Ustaw jawny czas trwania wywołania [trackPageView](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) : `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);` .
* Użyj wywołań chronometrażu widoku strony `startTrackPage` i `stopTrackPage` .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Nazwa używana jako pierwszy parametr kojarzy uruchamianie i zatrzymywanie wywołań. Domyślnie jest to nazwa bieżącej strony.

Powstałe czasy trwania ładowania stron wyświetlane w Eksplorator metryk pochodzą od interwału między wywołaniami Rozpocznij i Zatrzymaj. Zależy to od tego, jaki interwał rzeczywiście się przekroczy.

### <a name="page-telemetry-in-analytics"></a>Dane telemetryczne strony w analizie

W obszarze [Analiza](../log-query/log-query-overview.md) dwie tabele zawierają dane z operacji przeglądarki:

* `pageViews`Tabela zawiera dane o adresie URL i tytule strony
* `browserTimings`Tabela zawiera dane o wydajności klienta, takie jak czas przetwarzania danych przychodzących

Aby dowiedzieć się, jak długo przeglądarka ma przetwarzać różne strony:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Aby poznać popularne różne przeglądarki:

```kusto
pageViews
| summarize count() by client_Browser
```

Aby skojarzyć widoki stron z wywołaniami AJAX, Dołącz z zależnościami:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Zestaw SDK serwera używa TrackRequest do rejestrowania żądań HTTP.

Możesz również wywołać ją samodzielnie, jeśli chcesz symulować żądania w kontekście, w którym nie jest uruchomiony moduł usługi sieci Web.

Jednak zalecanym sposobem wysłania telemetrii żądania jest miejsce, gdzie żądanie działa jako <a href="#operation-context">kontekst operacji</a>.

## <a name="operation-context"></a>Kontekst operacji

Elementy telemetrii można skorelować ze sobą, kojarząc je z kontekstem operacji. Standardowy moduł śledzenia żądań wykonuje te wyjątki i inne zdarzenia, które są wysyłane podczas przetwarzania żądania HTTP. W obszarze [Wyszukiwanie](./diagnostic-search.md) i [Analiza](../log-query/log-query-overview.md)możesz łatwo znaleźć wszystkie zdarzenia skojarzone z żądaniem przy użyciu identyfikatora operacji.

Aby uzyskać więcej informacji na temat korelacji, zobacz [korelacja telemetrii w Application Insights](./correlation.md) .

Podczas ręcznego śledzenia telemetrii, najprostszym sposobem zapewnienia korelacji telemetrii za pomocą tego wzorca:

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

Wraz z ustawieniem kontekstu operacji program `StartOperation` tworzy element telemetrii określonego typu. Wysyła elementu telemetrii podczas usuwania operacji lub w przypadku jawnego wywołania metody `StopOperation` . Jeśli używasz `RequestTelemetry` jako typ telemetrii, jego czas trwania jest ustawiany na interwał czasu między rozpoczęciem a zatrzymaniem.

Elementy telemetrii zgłoszone w ramach zakresu operacji stają się elementami podrzędnymi tej operacji. Konteksty operacji mogą być zagnieżdżane.

W obszarze Wyszukiwanie, kontekst operacji służy do tworzenia listy **elementów pokrewnych** :

![Elementy pokrewne](./media/api-custom-events-metrics/21.png)

Aby uzyskać więcej informacji na temat niestandardowego śledzenia operacji, zobacz [śledzenie operacji niestandardowych przy użyciu zestawu SDK platformy Application Insights .NET](./custom-operations-tracking.md) .

### <a name="requests-in-analytics"></a>Żądania w analizie

W [Application Insights Analytics](../log-query/log-query-overview.md)żądania są wyświetlane w `requests` tabeli.

Jeśli [próbkowanie](./sampling.md) jest w operacji, właściwość itemCount będzie zawierać wartość większą niż 1. Na przykład itemCount = = 10 oznacza, że z 10 wywołań do trackRequest () proces próbkowania przekazał tylko jeden z nich. Aby uzyskać poprawną liczbę żądań i średni czas trwania segmentów przez nazwy żądań, należy użyć kodu takiego jak:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>Metody trackexception

Wyślij wyjątki do Application Insights:

* Aby [je zliczyć](../platform/metrics-charts.md), jako wskazanie częstotliwości problemu.
* Aby [przejrzeć poszczególne wystąpienia](./diagnostic-search.md).

Raporty obejmują ślady stosu.

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

Zestawy SDK automatycznie przechwytują wiele wyjątków, więc nie zawsze trzeba jawnie wywołać metodę Trackexception.

* ASP.NET: [Napisz kod, aby przechwytywać wyjątki](./asp-net-exceptions.md).
* Java EE: [wyjątki są przechwytywane automatycznie](./java-get-started.md#exceptions-and-request-failures).
* JavaScript: wyjątki są przechwytywane automatycznie. Jeśli chcesz wyłączyć automatyczne zbieranie danych, Dodaj wiersz do fragmentu kodu, który został wstawiony do stron sieci Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Wyjątki w analizie

W [Application Insights Analytics](../log-query/log-query-overview.md)wyjątki są wyświetlane w `exceptions` tabeli.

Jeśli [próbkowanie](./sampling.md) jest w operacji, `itemCount` Właściwość pokazuje wartość większą niż 1. Na przykład itemCount = = 10 oznacza, że z 10 wywołań do śledzeniaexception () proces próbkowania przekazał tylko jeden z nich. Aby uzyskać poprawną liczbę wyjątków ujętych przez typ wyjątku, należy użyć kodu takiego jak:

```kusto
exceptions
| summarize sum(itemCount) by type
```

Większość ważnych informacji stosu jest już wyodrębnionych do oddzielnych zmiennych, ale można ściągnąć `details` strukturę, aby uzyskać więcej. Ponieważ ta struktura jest dynamiczna, należy rzutować wynik na oczekiwany typ. Przykład:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Aby skojarzyć wyjątki z odpowiednimi żądaniami, użyj sprzężenia:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Użyj TrackTrace, aby pomóc zdiagnozować problemy, wysyłając "odsyłający ślad" do Application Insights. Można wysyłać fragmenty danych diagnostycznych i sprawdzać je w [przeszukiwaniu diagnostycznym](./diagnostic-search.md).

Na [kartach dzienników](./asp-net-trace-logs.md) .NET Użyj tego interfejsu API do wysyłania dzienników innych firm do portalu.

W języku Java dla [rejestratorów standardowych, takich jak Log4J, Logback](./java-trace-logs.md) używają Application Insights Log4J lub Logback do wysyłania dzienników innych firm do portalu.

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

*Klient/przeglądarka JavaScript*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Rejestruj zdarzenie diagnostyczne, takie jak wprowadzanie lub opuszczanie metody.

 Parametr | Opis
---|---
`message` | Dane diagnostyczne. Może być o wiele więcej niż nazwa.
`properties` | Mapa ciągu do ciągu: dodatkowe dane używane do [filtrowania wyjątków](#properties) w portalu. Wartość domyślna to Empty.
`severityLevel` | Obsługiwane wartości: [SeverityLevel. TS](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Można wyszukiwać zawartość wiadomości, ale (w przeciwieństwie do wartości właściwości), nie można na niej filtrować.

Limit rozmiaru `message` jest znacznie wyższy niż limit właściwości.
Zaletą TrackTrace jest możliwość umieszczania stosunkowo długich danych w komunikacie. Na przykład możesz kodować dane POST w tym miejscu.  

Ponadto do wiadomości można dodać poziom ważności. Podobnie jak w przypadku innych telemetrii, można dodać wartości właściwości, aby ułatwić filtrowanie lub wyszukiwanie różnych zestawów śladów. Przykład:

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

W obszarze [wyszukiwania](./diagnostic-search.md)można łatwo odfiltrować wszystkie komunikaty o określonym poziomie ważności, które odnoszą się do określonej bazy danych.

### <a name="traces-in-analytics"></a>Ślady w analizie

W programie [Application Insights Analytics](../log-query/log-query-overview.md)wywołania TrackTrace są wyświetlane w `traces` tabeli.

Jeśli [próbkowanie](./sampling.md) jest w operacji, właściwość itemCount pokazuje wartość większą niż 1. Na przykład itemCount = = 10 oznacza, że z 10 wywołań do `trackTrace()` , proces próbkowania przesyła tylko jeden z nich. Aby uzyskać poprawną liczbę wywołań śledzenia, należy użyć tego kodu, takiego jak `traces | summarize sum(itemCount)` .

## <a name="trackdependency"></a>TrackDependency

Użyj wywołania TrackDependency, aby śledzić czasy odpowiedzi i szybkość powodzeń wywołań do zewnętrznego fragmentu kodu. Wyniki są wyświetlane na wykresach zależności w portalu. Poniższego fragmentu kodu należy dodać wszędzie tam, gdzie jest wykonywane wywołanie zależności.

> [!NOTE]
> Dla platform .NET i .NET Core można alternatywnie użyć `TelemetryClient.StartOperation` metody (rozszerzenia), która wypełnia `DependencyTelemetry` właściwości potrzebne do korelacji, a także inne właściwości, takie jak godzina rozpoczęcia i czas trwania, aby nie trzeba było tworzyć czasomierza niestandardowego, jak w poniższych przykładach. Aby uzyskać więcej informacji, zapoznaj się [z sekcją w tym artykule dotyczącym wychodzącego śledzenia zależności](./custom-operations-tracking.md#outgoing-dependencies-tracking).

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
    dependencyTelemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
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

Należy pamiętać, że zestawy SDK serwera obejmują [moduł zależności](./asp-net-dependencies.md) , który odnajduje i śledzi pewne wywołania zależności automatycznie — na przykład do baz danych i interfejsów API REST. Musisz zainstalować agenta na serwerze, aby moduł działał. 

W języku Java niektóre wywołania zależności mogą być automatycznie śledzone przy użyciu [agenta Java](./java-agent.md).

To wywołanie jest używane, jeśli chcesz śledzić wywołania, które nie są przechwytywane przez automatyczne śledzenie, lub jeśli nie chcesz instalować agenta.

Aby wyłączyć standardowy moduł śledzenia zależności w języku C#, Edytuj [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) i usuń odwołanie do `DependencyCollector.DependencyTrackingTelemetryModule` . W języku Java nie należy instalować programu Java Agent, jeśli nie chcesz automatycznie zbierać zależności standardowych.

### <a name="dependencies-in-analytics"></a>Zależności w analizie

W [Application Insights analizie](../log-query/log-query-overview.md)wywołania trackDependency są wyświetlane w `dependencies` tabeli.

Jeśli [próbkowanie](./sampling.md) jest w operacji, właściwość itemCount pokazuje wartość większą niż 1. Na przykład itemCount = = 10 oznacza, że z 10 wywołań do trackDependency () proces próbkowania przekazał tylko jeden z nich. Aby uzyskać poprawną liczbę zależności ujętych przez składnik docelowy, należy użyć kodu takiego jak:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Aby skojarzyć zależności ze swoimi żądaniami, użyj sprzężenia:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Opróżnianie danych

Zwykle zestaw SDK wysyła dane w stałych interwałach (zwykle 30 s) lub zawsze, gdy bufor jest pełny (zazwyczaj 500 elementy). Jednak w niektórych przypadkach może być konieczne opróżnienie buforu — na przykład, jeśli używasz zestawu SDK w aplikacji, która jest zamykana.

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

Funkcja jest asynchroniczna dla [kanału telemetrii serwera](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Najlepiej używać metody Flush () w działaniu zamykania aplikacji.

## <a name="authenticated-users"></a>Uwierzytelnieni użytkownicy

W aplikacji sieci Web użytkownicy są (domyślnie) [identyfikowane przez pliki cookie](./usage-segmentation.md#the-users-sessions-and-events-segmentation-tool). Użytkownik może być liczony więcej niż raz, jeśli uzyskuje dostęp do aplikacji z innej maszyny lub przeglądarki lub usunie pliki cookie.

Jeśli użytkownicy logują się do aplikacji, można uzyskać dokładniejszą liczbę, ustawiając identyfikator użytkownika uwierzytelnionego w kodzie przeglądarki:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

W aplikacji ASP.NET Web MVC, na przykład:

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

Nie jest konieczne używanie rzeczywistej nazwy logowania użytkownika. Musi to być tylko identyfikator, który jest unikatowy dla tego użytkownika. Nie może zawierać spacji ani żadnego ze znaków `,;=|` .

Identyfikator użytkownika jest również ustawiany w pliku cookie sesji i wysyłany na serwer. W przypadku zainstalowania zestawu SDK serwera identyfikator uwierzytelnionego użytkownika jest wysyłany jako część właściwości kontekstu dla telemetrii klienta i serwera. Następnie można filtrować i wyszukiwać.

Jeśli aplikacja grupuje użytkowników, możesz również przekazać identyfikator konta (z tymi samymi ograniczeniami dotyczącymi znaków).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

W [Eksplorator metryk](../platform/metrics-charts.md)można utworzyć wykres, który zlicza **użytkowników, uwierzytelnione** i **konta użytkowników**.

Możesz również [wyszukać](./diagnostic-search.md) punkty danych klienta przy użyciu określonych nazw użytkowników i kont.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrowanie, wyszukiwanie i segmentacja danych przy użyciu właściwości

Można dołączać właściwości i pomiary do zdarzeń (a także do metryk, wyświetleń stron, wyjątków i innych danych telemetrii).

*Właściwości* to wartości ciągu, których można użyć do filtrowania danych telemetrycznych w raportach użycia. Jeśli na przykład Twoja aplikacja zawiera kilka gier, możesz dołączyć nazwę gry do każdego zdarzenia, aby zobaczyć, które gry są bardziej popularne.

Długość ciągu jest ograniczona do 8192. (Jeśli chcesz wysyłać duże fragmenty danych, użyj parametru message elementu TrackTrace).

*Metryki* to wartości liczbowe, które mogą być prezentowane graficznie. Na przykład możesz chcieć zobaczyć, czy istnieje stopniowe zwiększenie wyników osiągniętych przez graczy. Wykresy mogą być segmentacjne przez właściwości, które są wysyłane ze zdarzeniem, dzięki czemu można uzyskać osobne lub Skumulowane wykresy dla różnych gier.

Aby wartości metryk były poprawnie wyświetlane, powinny być większe lub równe 0.

Istnieją pewne [ograniczenia dotyczące liczby właściwości, wartości właściwości i metryk](#limits) , których można użyć.

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
> Zadbaj o to, aby rejestrować dane osobowe we właściwościach.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternatywny sposób ustawiania właściwości i metryk

Jeśli jest to bardziej wygodne, można zbierać parametry zdarzenia w osobnym obiekcie:

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
> Nie używaj ponownie tego samego wystąpienia elementu telemetrii ( `event` w tym przykładzie) do wywołania ścieżki * () wielokrotnie. Może to spowodować wysłanie danych telemetrycznych z nieprawidłową konfiguracją.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Niestandardowe pomiary i właściwości w analizie

W [analizie](../log-query/log-query-overview.md)metryki niestandardowe i właściwości są wyświetlane `customMeasurements` w `customDimensions` atrybutach i poszczególnych rekordach telemetrii.

Na przykład, jeśli dodano właściwość o nazwie "Game" do danych telemetrycznych żądania, ta kwerenda zlicza wystąpienia różnych wartości "Game" i pokaże średnią metryki niestandardowej "Score":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Zwróć uwagę, że:

* W przypadku wyodrębnienia wartości z formatu JSON customDimensions lub customMeasurements jest on typu dynamicznego i dlatego należy go rzutować `tostring` lub `todouble` .
* Aby wziąć pod uwagę możliwość [próbkowania](./sampling.md), należy użyć `sum(itemCount)` , nie `count()` .

## <a name="timing-events"></a><a name="timed"></a> Zdarzenia chronometrażu

Czasami chcesz utworzyć wykres, jak długo trwa wykonywanie akcji. Na przykład możesz chcieć wiedzieć, jak długo użytkownicy mogą rozważyć wybór w grze. W tym celu można użyć parametru pomiaru.

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

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Właściwości domyślne dla telemetrii niestandardowej

Jeśli chcesz ustawić domyślne wartości właściwości dla niektórych zdarzeń niestandardowych, które można napisać, możesz je ustawić w wystąpieniu TelemetryClient. Są one dołączone do każdego elementu telemetrii, który jest wysyłany z tego klienta.

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

Poszczególne wywołania telemetrii mogą przesłonić wartości domyślne w swoich słownikach właściwości.

*W przypadku klientów sieci Web w języku JavaScript* używaj inicjatorów telemetrycznych języka JavaScript.

*Aby dodać właściwości do wszystkich danych telemetrycznych*, w tym dane ze standardowych modułów kolekcji, [Zaimplementuj `ITelemetryInitializer`](./api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Próbkowanie, filtrowanie i przetwarzanie danych telemetrycznych

Można napisać kod, aby przetworzyć dane telemetryczne przed ich wysłaniem z zestawu SDK. Przetwarzanie obejmuje dane wysyłane z standardowych modułów telemetrycznych, takich jak kolekcja żądań HTTP i kolekcja zależności.

[Dodaj właściwości](./api-filtering-sampling.md#add-properties) do telemetrii przez implementację `ITelemetryInitializer` . Można na przykład dodać numery wersji lub wartości, które są obliczane na podstawie innych właściwości.

[Filtrowanie](./api-filtering-sampling.md#filtering) może modyfikować lub odrzucać dane telemetryczne przed ich wysłaniem z zestawu SDK przez implementację `ITelemetryProcessor` . Kontrolujesz dane wysyłane lub odrzucane, ale musisz uwzględnić wpływ na metryki. W zależności od sposobu odrzucania elementów można utracić możliwość nawigowania między powiązanymi elementami.

[Próbkowanie](./api-filtering-sampling.md) to spakowane rozwiązanie umożliwiające zmniejszenie ilości danych wysyłanych z aplikacji do portalu. Nie ma to wpływu na wyświetlane metryki. I nie wpływa to na zdolność do diagnozowania problemów, przechodząc między pokrewnymi elementami, takimi jak wyjątki, żądania i wyświetlenia stron.

[Dowiedz się więcej](./api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Wyłączanie telemetrii

Aby *dynamicznie zatrzymywać i uruchamiać* zbieranie i przesyłanie danych telemetrycznych:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Aby *wyłączyć wybrane standardowe moduły zbierające*— na przykład liczniki wydajności, żądania HTTP lub zależności — Usuń lub Dodaj komentarz do odpowiednich wierszy w [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Możesz to zrobić na przykład, jeśli chcesz wysłać własne dane TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Aby *wyłączyć wybrane standardowe moduły zbierające*— na przykład liczniki wydajności, żądania HTTP lub zależności — w czasie inicjalizacji, metody konfiguracji łańcucha do kodu INICJUJĄCEGO zestaw SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Aby wyłączyć te moduły zbierające po zainicjowaniu, użyj obiektu konfiguracji: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Tryb dewelopera

Podczas debugowania warto przeanalizować dane telemetryczne za pomocą potoku, aby można było natychmiast zobaczyć wyniki. Uzyskasz również dodatkowe komunikaty, które pomagają śledzić wszelkie problemy związane z telemetrią. Przełączaj ją w środowisku produkcyjnym, ponieważ może to spowolnić aplikację.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

W przypadku Node.js można włączyć tryb dewelopera, włączając rejestrowanie wewnętrzne przy użyciu `setInternalLogging` i ustawiając `maxBatchSize` wartość 0, co powoduje wysłanie danych telemetrycznych od razu po ich zebraniu.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a> Ustawianie klucza Instrumentacji dla wybranej niestandardowej telemetrii

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a> Dynamiczny klucz Instrumentacji

Aby uniknąć mieszania danych telemetrycznych ze środowiska deweloperskiego, testowego i produkcyjnego, można [utworzyć oddzielne zasoby Application Insights](./create-new-resource.md) i zmienić ich klucze, w zależności od środowiska.

Zamiast pobierać klucz Instrumentacji z pliku konfiguracji, można ustawić go w kodzie. Ustaw klucz w metodzie inicjującej, na przykład global.aspx.cs w usłudze ASP.NET:

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

W obszarze strony sieci Web możesz chcieć ustawić ją ze stanu serwera internetowego, zamiast kodować go do skryptu. Na przykład na stronie sieci Web wygenerowanej w aplikacji ASP.NET:

*Kod JavaScript w Razor*

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

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient ma Właściwość kontekstu, która zawiera wartości, które są wysyłane wraz ze wszystkimi danymi telemetrii. Są one zazwyczaj ustawiane przez standardowe moduły telemetrii, ale można je również ustawić samodzielnie. Przykład:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Jeśli samodzielnie ustawisz dowolne z tych wartości, Rozważ usunięcie odpowiedniego wiersza z [ApplicationInsights.config](./configuration-with-applicationinsights-config.md), dzięki czemu wartości i wartości standardowych nie zostaną pomylone.

* **Składnik**: aplikacja i jej wersja.
* **Urządzenie**: dane dotyczące urządzenia, na którym działa aplikacja. (W usłudze Web Apps jest to serwer lub urządzenie klienckie, z którego jest wysyłana Telemetria).
* **InstrumentationKey**: zasób Application Insights na platformie Azure, w którym pojawia się dane telemetryczne. Zwykle jest ona pobierana z ApplicationInsights.config.
* **Lokalizacja**: Lokalizacja geograficzna urządzenia.
* **Operacja**: w usłudze Web Apps bieżące żądanie HTTP. W przypadku innych typów aplikacji można ustawić tę opcję, aby grupować zdarzenia jednocześnie.
  * **ID**: wygenerowana wartość, która jest skorelowana z różnymi zdarzeniami, dzięki czemu podczas inspekcji dowolnego zdarzenia w przeszukiwaniu diagnostycznym można znaleźć powiązane elementy.
  * **Name**: identyfikator, zazwyczaj adres URL żądania HTTP.
  * **SyntheticSource**: Jeśli nie ma wartości null lub pustej, ciąg, który wskazuje, że źródło żądania zostało zidentyfikowane jako robot lub test sieci Web. Domyślnie jest on wykluczony z obliczeń w Eksplorator metryk.
* **Właściwości**: właściwości, które są wysyłane ze wszystkimi danymi telemetrycznymi. Może być przesłonięty w pojedynczych wywołaniach śledzenia *.
* **Sesja**: sesja użytkownika. Identyfikator jest ustawiony na wygenerowaną wartość, która jest zmieniana, gdy użytkownik nie był aktywny przez pewien czas.
* **Użytkownik**: informacje o użytkowniku.

## <a name="limits"></a>Limity

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Aby uniknąć osiągnięcia limitu szybkości danych, należy użyć [próbkowania](./sampling.md).

Aby określić, jak długo są przechowywane dane, zobacz [przechowywanie i prywatność danych](./data-retention-privacy.md).

## <a name="reference-docs"></a>Dokumentacja dokumentacji

* [Odwołanie ASP.NET](/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Dokumentacja języka Java](/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [Dokumentacja języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## <a name="sdk-code"></a>Kod zestawu SDK

* [Zestaw SDK platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pakiety systemu Windows Server](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Zestaw SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Zestaw SDK dla platformy Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [Zestaw SDK dla języka JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="questions"></a>Pytania

* *Jakie wyjątki mogą Track_ () zgłaszanie wywołań?*

    Brak. Nie trzeba zawijać ich w klauzulach try-catch. Jeśli zestaw SDK napotyka problemy, będzie rejestrował komunikaty w danych wyjściowych konsoli debugowania i — Jeśli komunikaty są przeszukiwane przez program, podczas wyszukiwania diagnostycznego.
* *Czy istnieje interfejs API REST umożliwiający pobieranie danych z portalu?*

    Tak, [interfejs API dostępu do danych](https://dev.applicationinsights.io/). Inne sposoby wyodrębnienia danych obejmują [Eksportowanie z analizy do Power BI](./export-power-bi.md) i [eksportu ciągłego](./export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>Następne kroki

* [Wyszukaj zdarzenia i dzienniki](./diagnostic-search.md)
* [Rozwiązywanie problemów](../faq.md)