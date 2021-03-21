---
title: Get-Metric w Azure Monitor Application Insights
description: Dowiedz się, jak efektywnie używać wywołania GetMetric () do przechwytywania lokalnie wstępnie zagregowanych metryk dla aplikacji .NET i .NET Core za pomocą Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719784"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Niestandardowa kolekcja metryk w oprogramowaniu .NET i .NET Core

Azure Monitor Application Insights .NET i .NET Core SDK mają dwie różne metody zbierania niestandardowych metryk, `TrackMetric()` i `GetMetric()` . Kluczową różnicą między tymi dwoma metodami jest agregacja lokalna. `TrackMetric()` Brak agregacji wstępnej podczas `GetMetric()` agregacji wstępnej. Zalecanym podejściem jest użycie agregacji, dlatego `TrackMetric()` nie jest już preferowaną metodą zbierania metryk niestandardowych. W tym artykule przedstawiono sposób użycia metody GetMetric () i niektóre racjonalne uzasadnienie jego działania.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric a GetMetric

`TrackMetric()` wysyła pierwotne dane telemetryczne oznaczające metrykę. Wysłanie pojedynczego elementu telemetrii dla każdej wartości jest nieefektywne. `TrackMetric()` jest również nieefektywna pod względem wydajności, ponieważ każdy `TrackMetric(item)` przechodzi przez pełny zestaw SDK dla inicjatorów i procesorów telemetrycznych. W przeciwieństwie `TrackMetric()` `GetMetric()` do, obsługuje lokalną wstępną agregację dla Ciebie, a następnie przesyła tylko zagregowaną metrykę podsumowania w stałym interwale wynoszącym 1 minutę. Dlatego jeśli trzeba dokładnie monitorować pewną niestandardową metrykę na sekundę lub nawet w milisekundach, można to zrobić, jednocześnie tylko koszt ruchu magazynu i sieci jest monitorowany co minutę. Znacznie zmniejsza to ryzyko związane z ograniczaniem wydajności, ponieważ łączna liczba elementów telemetrycznych, które muszą zostać przesłane dla zagregowanej metryki, jest znacznie ograniczona.

W Application Insights metryki niestandardowe zebrane za pośrednictwem `TrackMetric()` i `GetMetric()` nie podlegają [pobieraniu próbek](./sampling.md). Próbkowanie ważnych metryk może prowadzić do scenariuszy, w których można było utworzyć alerty dotyczące tych metryk. Nigdy nie próbkuje metryk niestandardowych, zazwyczaj można mieć pewność, że w przypadku naruszenia progów alertów zostanie uruchomiony alert.  Jednak ze względu na to, że metryki niestandardowe nie są próbkowane, istnieją pewne potencjalne problemy.

Jeśli chcesz śledzić trendy w metryce co sekundę, lub nawet bardziej szczegółowy interwał, może to skutkować:

- Zwiększono koszty magazynowania danych. Istnieje koszt związany z ilością wysyłanych danych do Azure Monitor. (Im więcej danych jest wysyłanych, tym większy koszt monitorowania).
- Zwiększony koszt ruchu sieciowego/wydajności. (W niektórych scenariuszach może to być kosztem wydajności pieniężnej i aplikacji).
- Ryzyko związane z ograniczaniem pozyskiwania. (Usługa Azure Monitor odrzuca punkty danych ("dławienia"), gdy aplikacja wysyła bardzo wysoką liczbę telemetrii w krótkim czasie.

Ograniczanie przepustowości ma szczególne znaczenie w takich przypadkach, jak próbkowanie, ograniczanie przepustowości może prowadzić do nieodebranych alertów, ponieważ Warunek wyzwalający alert może wystąpić lokalnie, a następnie zostać porzucony w punkcie końcowym pozyskiwania z powodu zbyt dużej ilości danych. Dlatego nie zalecamy używania programów .NET i .NET Core, `TrackMetric()` chyba że zaimplementowano własną logikę agregacji lokalnej. Jeśli próbujesz śledzić każde wystąpienie zdarzenia występujące w danym okresie, może się okazać, że [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) jest to lepsza dopasowanie. Należy pamiętać, że w przeciwieństwie do metryk niestandardowych, zdarzenia niestandardowe podlegają próbkowaniu. Oczywiście można nadal używać `TrackMetric()` nawet bez konieczności pisania własnej wstępnej agregacji, ale jeśli tak, należy pamiętać o pułapek.

Podsumowując `GetMetric()` jest zalecanym podejściem, ponieważ wykonuje wstępne agregacja, gromadzi wartości ze wszystkich wywołań śledzenia () i wysyła podsumowanie/agregację co minutę. Może to znacznie zmniejszyć koszty i obciążenie wydajności, wysyłając mniejszą liczbę punktów danych, zachowując jednocześnie wszystkie istotne informacje.

> [!NOTE]
> Tylko zestawy SDK .NET i .NET Core mają metodę GetMetric (). Jeśli używasz języka Java, możesz użyć [metryk Micrometer](./micrometer-java.md) lub `TrackMetric()` . W przypadku języka JavaScript i Node.js nadal można korzystać z programu `TrackMetric()` , ale należy pamiętać o zastrzeżeniach, które zostały opisane w poprzedniej sekcji. W przypadku języka Python można użyć [OpenCensus.](./opencensus-python.md#metrics) destandards do wysyłania metryk niestandardowych, ale implementacja metryk jest inna.

## <a name="getting-started-with-getmetric"></a>Wprowadzenie do GetMetric

Nasze przykłady wykorzystują podstawową aplikację usługi roboczej .NET Core 3,1. Jeśli chcesz dokładnie replikować środowisko testowe, które było używane w tych przykładach, wykonaj kroki 1-6 w [artykule monitorowanie usługi procesu roboczego](./worker-service.md#net-core-30-worker-service-application) , aby dodać Application Insights do szablonu projektu podstawowego usługi roboczej. Te pojęcia dotyczą wszystkich ogólnych aplikacji, w których można używać zestawu SDK, w tym aplikacji sieci Web i aplikacji konsolowych.

### <a name="sending-metrics"></a>Wysyłanie metryk

Zastąp zawartość `worker.cs` pliku następującym:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Jeśli zostanie uruchomiony powyższy kod i zobaczysz dane telemetryczne wysyłane za pośrednictwem okna danych wyjściowych programu Visual Studio lub narzędzia takie jak Telerik programu Fiddler, zostanie wyświetlona pętla while, która jest wykonywana wielokrotnie bez wysyłania danych telemetrycznych, a następnie zostanie wysłany pojedynczy element telemetrii o 60-sekundowym znaku, który w przypadku naszego testu wygląda następująco: :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Ten pojedynczy element telemetrii reprezentuje zagregowane wartości 41 różnych pomiarów metryk. Ze względu na to, że wysłaliśmy wartość o tej samej wartości ponad i przez ponowną próbę, mamy *odchyleniu Standard (stDev)* z wartości 0 z identyczną wartością *maksymalną (max)* i *minimalną (min)* . Właściwość *Value* reprezentuje sumę wszystkich wartości, które zostały zagregowane.

> [!NOTE]
> Funkcja GetMetric nie obsługuje śledzenia ostatniej wartości (tj. "miernika") ani śledzenia histogramów/dystrybucji.

Jeśli sprawdzimy nasz zasób Application Insights w środowisku dzienników (analiza), ten element telemetrii będzie wyglądać następująco:

![Widok zapytania Log Analytics](./media/get-metric/log-analytics.png)

> [!NOTE]
> Nieprzetworzony element telemetrii nie zawierał jawnej sumy właściwości/pola po pobraniu dla Ciebie nowej wartości. W tym przypadku zarówno `value` Właściwość, jak i `valueSum` reprezentuje to samo.

Dostęp do danych telemetrycznych metryk niestandardowych można uzyskać również w sekcji [_metryki_](../essentials/metrics-charts.md) portalu. Zarówno jako [Metryka oparta na dzienniku, jak i niestandardowa](pre-aggregated-metrics-log-metrics.md). (Poniższy zrzut ekranu przedstawia przykład opartego na dzienniku). ![Widok Eksploratora metryk](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Odwołanie do pamięci podręcznej dla użycia o wysokiej przepływności

W niektórych przypadkach wartości metryk są obserwowane bardzo często. Na przykład usługa o wysokiej przepływności, która przetwarza 500 żądań na sekundę, może chcieć wyemitować 20 metryk telemetrii dla każdego żądania. Oznacza to, że śledzenie wartości 10 000 na sekundę. W takich scenariuszach o wysokiej przepływności użytkownicy mogą potrzebować pomocy zestawu SDK, unikając niektórych wyszukiwań.

Na przykład w tym przypadku przykład przeprowadzi wyszukiwanie dla dojścia dla metryki "ComputersSold", a następnie śledzone wartość 42. Zamiast tego dojście może być buforowane dla wielu wywołań śledzenia:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Oprócz buforowania uchwytu metryki, powyższy przykład również został obniżony `Task.Delay` do 50 milisekund, aby pętla była wykonywana częściej, co skutkuje 772 `TrackValue()` wywołań.

## <a name="multi-dimensional-metrics"></a>Metryki wielowymiarowe

W przykładach w poprzedniej sekcji przedstawiono metryki jednowymiarowe. Metryki mogą być również wielowymiarowe. Obecnie obsługujemy do 10 wymiarów.

 Oto przykład sposobu tworzenia jednowymiarowej metryki:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Uruchomienie tego kodu przez co najmniej 60 sekund spowoduje wysłanie trzech odrębnych elementów telemetrycznych do platformy Azure, z których każdy reprezentuje agregację jednego z trzech czynników. Tak jak przed sprawdzeniem ich w widoku dzienników (analiza):

![Widok usługi log Analytics dla metryki wielowymiarowej](./media/get-metric/log-analytics-multi-dimensional.png)

Jak również w środowisku Eksploratora metryk:

![Metryki niestandardowe](./media/get-metric/custom-metrics.png)

Należy jednak zauważyć, że nie można podzielić metryki według nowego wymiaru niestandardowego ani wyświetlić niestandardowego wymiaru przy użyciu widoku metryk:

![Obsługa dzielenia](./media/get-metric/splitting-support.png)

Domyślnie metryki wielowymiarowe w środowisku Eksploratora metryk nie są włączane w Application Insights zasobów.

### <a name="enable-multi-dimensional-metrics"></a>Włącz metryki wielowymiarowe

Aby włączyć metryki wielowymiarowe dla zasobu Application Insights, wybierz pozycję **użycie i szacowane koszty**.  >  **metryki niestandardowe**  >  **umożliwiają powiadamianie o niestandardowych wymiarach metryk**  >  . Więcej informacji na ten temat można znaleźć [tutaj](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

Po dokonaniu zmiany i wysłaniu nowej wielowymiarowej telemetrii będziesz mieć możliwość **zastosowania dzielenia**.

> [!NOTE]
> Tylko nowo wysłane metryki po włączeniu funkcji w portalu będą miały zapisane wymiary.

![Zastosuj podział](./media/get-metric/apply-splitting.png)

I Wyświetl agregacje metryk dla każdego wymiaru _FormFactor_ :

![Czynniki postaci](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>Jak używać MetricIdentifier, gdy istnieje więcej niż trzy wymiary

Obecnie 10 wymiarów jest obsługiwanych, ale więcej niż trzy wymiary wymagają użycia `MetricIdentifier` :

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Konfiguracja metryki niestandardowej

Aby zmienić konfigurację metryk, należy to zrobić w miejscu, w którym została zainicjowana Metryka.

### <a name="special-dimension-names"></a>Specjalne nazwy wymiarów

Metryki nie używają kontekstu telemetrii `TelemetryClient` używanego do uzyskiwania dostępu do nich, ale specjalne nazwy wymiarów dostępne jako stałe w `MetricDimensionNames` klasie to najlepsze obejście tego ograniczenia.

Agregacje metryk wysyłane przez Poniższy "rozmiar żądania operacji specjalnej" — Metryka **nie** będzie miała `Context.Operation.Name` ustawionej wartości "operacja specjalna". Natomiast `TrackMetric()` wszystkie inne TrackXXX () zostaną `OperationName` prawidłowo ustawione na "operacja specjalna".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

W takiej sytuacji należy użyć specjalnych nazw wymiaru wymienionych w klasie, `MetricDimensionNames` Aby określić `TelemetryContext` wartości.

Na przykład, gdy agregacja metryki wynikająca z następnej instrukcji jest wysyłana do punktu końcowego Application Insights w chmurze, jego `Context.Operation.Name` pole danych zostanie ustawione na "operacja specjalna":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Wartości tego wymiaru specjalnego zostaną skopiowane do `TelemetryContext` i nie będą używane jako wymiar "normal". Jeśli chcesz również zachować wymiar operacji dla normalnej eksploracji metrycznej, musisz utworzyć oddzielny wymiar dla tego celu:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Wymiarowanie i wymiary szeregów czasowych

 Aby zapobiec przypadkowemu użyciu zasobów przez podsystem telemetrii, można kontrolować maksymalną liczbę serii danych na metrykę. Domyślne limity nie przekraczają 1000 całkowitej ilości danych na metrykę i nie więcej niż 100 różne wartości na wymiar.

 W kontekście wymiaru i szeregów czasowych, których używamy, `Metric.TrackValue(..)` Aby upewnić się, że limity są spełnione. Jeśli limity zostały już osiągnięte, `Metric.TrackValue(..)` program zwróci wartość "false", która nie będzie śledzona. W przeciwnym razie zwróci wartość "true". Jest to przydatne, jeśli dane dla metryk pochodzą z danych wejściowych użytkownika.

`MetricConfiguration`Konstruktor przyjmuje pewne opcje zarządzania różnymi seriami w ramach odpowiedniej metryki i obiektu klasy implementującej `IMetricSeriesConfiguration` , który określa zachowanie agregacji dla poszczególnych serii metryk:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` to maksymalna liczba szeregów czasowych danych, które może zawierać Metryka. Po osiągnięciu tego limitu wywołania `TrackValue()` nie będą śledzone.
* `valuesPerDimensionLimit` ogranicza liczbę unikatowych wartości na wymiar w podobny sposób.
* `restrictToUInt32Values` Określa, czy mają być śledzone tylko nieujemne wartości całkowite.

Oto przykład sposobu wysyłania komunikatu, aby dowiedzieć się, czy przekroczono limity limitu:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej ](./worker-service.md)o monitorowaniu aplikacji usługi Worker.
* Aby uzyskać więcej szczegółowych informacji na temat [metryk opartych na dzienniku i wstępnie agregowanych](./pre-aggregated-metrics-log-metrics.md).
* [Eksplorator metryk](../essentials/metrics-getting-started.md)
* Jak włączyć Application Insights [aplikacji ASP.NET Core](asp-net-core.md)
* Jak włączyć Application Insights dla [aplikacji ASP.NET](asp-net.md)
