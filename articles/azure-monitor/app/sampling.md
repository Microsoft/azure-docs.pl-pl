---
title: Próbkowanie danych telemetrycznych w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Jak kontrolować głośność telemetrii.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 6e930b0dc610f74e1a341b2f57f4c211a85a49d3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605040"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights

Próbkowanie jest funkcją w [usłudze Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Jest to zalecany sposób zmniejszenia ruchu telemetryczne, koszty danych i kosztów magazynu przy jednoczesnym zachowaniu statystycznie poprawnej analizy danych aplikacji. Próbkowanie pomaga również uniknąć ograniczania danych telemetrycznych w usłudze Application Insights. Filtr próbkowania wybiera elementy, które są powiązane, dzięki czemu można nawigować między elementami podczas wykonywania badań diagnostycznych.

Gdy liczby metryki są prezentowane w portalu, są one ponownie znormalizowane, aby uwzględnić próbkowanie. Minimalizuje to wszelkie skutki dla statystyk.

## <a name="brief-summary"></a>Krótkie podsumowanie

* Istnieją trzy różne rodzaje pobierania próbek: adaptacyjne pobieranie próbek, pobieranie próbek o stałej szybkości i pobieranie próbek połkowych.
* Adaptacyjne próbkowanie jest domyślnie włączone we wszystkich najnowszych wersjach zestawów SDK (Application Insights ASP.NET i ASP.NET Core Software Development Kits). Jest również używany przez [usługę Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* Próbkowanie o stałej szybkości jest dostępne w najnowszych wersjach SDK usługi Application Insights dla ASP.NET, ASP.NET Core, Java (zarówno agenta, jak i SDK) i Pythona.
* Próbkowanie pozyskiwania działa w punkcie końcowym usługi application insights. Ma ona zastosowanie tylko wtedy, gdy nie ma innego pobierania próbek. Jeśli zestaw SDK próbki danych telemetrycznych, pobieranie próbkowania jest wyłączona.
* W przypadku aplikacji sieci web, jeśli rejestrujesz zdarzenia niestandardowe i trzeba upewnić się, że `OperationId` zestaw zdarzeń jest zachowywany lub odrzucany razem, zdarzenia muszą mieć taką samą wartość.
* Jeśli piszesz zapytania Analytics, należy [wziąć pod uwagę próbkowanie](../../azure-monitor/log-query/aggregations.md). W szczególności, zamiast po prostu zliczać rekordy, należy użyć `summarize sum(itemCount)`.
* Niektóre typy danych telemetrycznych, w tym metryki wydajności i metryki niestandardowe, są zawsze przechowywane niezależnie od tego, czy próbkowanie jest włączone, czy nie.

W poniższej tabeli podsumowano typy próbkowania dostępne dla każdego SDK i typ aplikacji:

| Application Insights SDK | Wspomagane adaptacyjne pobieranie próbek | Obsługiwane próbkowanie o stałej szybkości | Obsługiwane pobieranie próbek połkniętych |
|-|-|-|-|
| ASP.NET | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-aspnet-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-applications) | Tylko wtedy, gdy nie obowiązuje żadne inne pobieranie próbek |
| ASP.NET Core | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Tylko wtedy, gdy nie obowiązuje żadne inne pobieranie próbek |
| Azure Functions | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-azure-functions) | Nie | Tylko wtedy, gdy nie obowiązuje żadne inne pobieranie próbek |
| Java | Nie | [Tak](#configuring-fixed-rate-sampling-for-java-applications) | Tylko wtedy, gdy nie obowiązuje żadne inne pobieranie próbek |
| Python | Nie | [Tak](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Tylko wtedy, gdy nie obowiązuje żadne inne pobieranie próbek |
| Wszystkie inne | Nie | Nie | [Tak](#ingestion-sampling) |

> [!NOTE]
> Informacje na większości tej strony dotyczą bieżących wersji składników SDK usługi Application Insights. Aby uzyskać informacje na temat starszych wersji SDK, [zobacz sekcję poniżej](#older-sdk-versions).

## <a name="types-of-sampling"></a>Rodzaje pobierania próbek

Istnieją trzy różne metody pobierania próbek:

* **Adaptacyjne próbkowanie** automatycznie dostosowuje ilość danych telemetrycznych wysyłanych z SDK w aplikacji ASP.NET/ASP.NET Core i z usługi Azure Functions. Jest to próbkowanie domyślne podczas korzystania z ASP.NET lub ASP.NET core SDK. Adaptacyjne próbkowanie jest obecnie dostępne tylko dla ASP.NET danych telemetrycznych po stronie serwera i dla usług Azure Functions.

* **Próbkowanie o stałej szybkości** zmniejsza ilość danych telemetrycznych wysyłanych zarówno z ASP.NET lub ASP.NET core lub java oraz z przeglądarek użytkowników. Ustawiasz stawkę. Klient i serwer zsynchronizuje ich próbkowania, tak aby w wyszukiwarce można nawigować między widokami strony pokrewnych i żądań.

* **Próbkowanie pozyskiwania** odbywa się w punkcie końcowym usługi usługi Application Insights. Odrzuca niektóre dane telemetryczne, które pochodzą z aplikacji, z częstotliwością próbkowania, które można ustawić. Nie zmniejsza ruchu telemetrycznego wysyłanego z aplikacji, ale pomaga utrzymać w ramach miesięcznego przydziału. Główną zaletą pobierania próbek pozyskiwania jest to, że można ustawić częstotliwość próbkowania bez ponownego rozmieszczania aplikacji. Pobieranie próbek działa jednolicie dla wszystkich serwerów i klientów, ale nie ma zastosowania, gdy działają inne typy próbkowania.

> [!IMPORTANT]
> Jeśli działają metody adaptacyjnego lub próbkowania o stałej szybkości, pobieranie próbek jest wyłączone.

## <a name="adaptive-sampling"></a>Adaptacyjne pobieranie próbek

Adaptacyjne próbkowanie wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera sieci web do punktu końcowego usługi Application Insights.

> [!TIP]
> Adaptacyjne próbkowanie jest domyślnie włączone podczas korzystania z ASP.NET SDK lub ASP.NET Core SDK, a także jest domyślnie włączone dla usługi Azure Functions.

Głośność jest regulowana automatycznie, aby utrzymać określoną maksymalną `MaxTelemetryItemsPerSecond`szybkość ruchu i jest kontrolowana przez ustawienie . Jeśli aplikacja generuje małą ilość danych telemetrycznych, na przykład podczas debugowania lub z powodu niskiego użycia, elementy `MaxTelemetryItemsPerSecond`nie zostaną usunięte przez procesor próbkowania, o ile wolumin jest niższy . Wraz ze wzrostem liczby danych telemetrycznych częstotliwość próbkowania jest dostosowywana w taki sposób, aby osiągnąć wolumin docelowy. Korekta jest przeliczana w regularnych odstępach czasu i opiera się na średniej ruchomej szybkości transmisji wychodzącej.

Aby osiągnąć wolumin docelowy, niektóre z wygenerowanych danych telemetrycznych są odrzucane. Ale podobnie jak inne typy próbkowania algorytm zachowuje powiązane elementy telemetryczne. Na przykład podczas sprawdzania danych telemetrycznych w wyszukiwarce, będzie można znaleźć żądanie związane z określonym wyjątkiem.

Liczba metryk, takich jak szybkość żądania i szybkość wyjątku są dostosowywane w celu skompensowania częstotliwości próbkowania, tak aby były wyświetlane w przybliżeniu poprawne wartości w Eksploratorze metryk.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurowanie adaptacyjnego pobierania próbek do ASP.NET aplikacji

> [!NOTE]
> Ta sekcja dotyczy aplikacji ASP.NET, a nie ASP.NET aplikacji Core. [Dowiedz się więcej o konfigurowaniu próbkowania adaptacyjnego dla aplikacji ASP.NET Core w dalszej części tego dokumentu.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

W [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)obszarze , można dostosować `AdaptiveSamplingTelemetryProcessor` kilka parametrów w węźle. Pokazane liczby są wartościami domyślnymi:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Szybkość docelowa, do jaką ma dążyć algorytm adaptacyjny **na każdym hoście serwera.** Jeśli aplikacja sieci web działa na wielu hostach, zmniejsz tę wartość, aby pozostała w docelowym tempie ruchu w portalu usługi Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał, w którym bieżąca szybkość telemetrii jest ponownie wyceniana. Ocena jest przeprowadzana jako średnia ruchoma. Można skrócić ten interwał, jeśli dane telemetryczne jest odpowiedzialny za nagłe wybuchy.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Kiedy wartość procentowa próbkowania zmienia się, jak szybko po możemy ponownie obniżyć procent próbkowania, aby przechwycić mniej danych?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Kiedy wartość procentowa próbkowania zmienia się, jak szybko po możemy ponownie zwiększyć procent próbkowania, aby przechwycić więcej danych?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Ponieważ procent próbkowania zmienia się, jaka jest minimalna wartość, którą możemy ustawić?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Ponieważ procent próbkowania zmienia się, jaka jest maksymalna wartość, którą możemy ustawić?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Przy obliczaniu średniej ruchomej określa ona wagę, która powinna być przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej niż 1. Mniejsze wartości sprawiają, że algorytm jest mniej reaktywny na nagłe zmiany.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Ilość danych telemetrycznych do próbkowania po rozpoczęciu aplikacji. Nie zmniejszaj tej wartości podczas debugowania.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Średnik rozdzielona lista typów, które nie mają być przedmiotem próbkowania. Rozpoznane typy `Event` `Exception`to: `Request` `Dependency` `Trace`, , , `PageView`, , . Przesyłane są wszystkie dane telemetryczne określonych typów; typy, które nie są określone będą próbkowania.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Średnik rozdzielona lista typów, które mają być przedmiotem próbkowania. Rozpoznane typy `Event` `Exception`to: `Request` `Dependency` `Trace`, , , `PageView`, , . Próbki będą pobierane przez określone typy; wszystkie dane telemetryczne innych typów będą zawsze przesyłane.

**Aby wyłączyć** próbkowanie adaptacyjne, usuń `AdaptiveSamplingTelemetryProcessor` `ApplicationInsights.config`z niego węzeł(-y).

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie adaptacyjnego próbkowania w kodzie

Zamiast ustawiać parametr próbkowania w `.config` pliku, można programowo ustawić te wartości.

1. Usuń wszystkie `AdaptiveSamplingTelemetryProcessor` węzły z `.config` pliku.
2. Użyj następującego fragmentu kodu, aby skonfigurować próbkowanie adaptacyjne:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Dowiedz[się więcej o procesorach telemetrycznych).](../../azure-monitor/app/api-filtering-sampling.md#filtering)

Można również dostosować częstotliwość próbkowania dla każdego typu telemetrii indywidualnie lub nawet wykluczyć niektóre typy z próbkowania w ogóle:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET Core

Nie ma `ApplicationInsights.config` dla ASP.NET podstawowych aplikacji, więc cała konfiguracja odbywa się za pomocą kodu.
Adaptacyjne próbkowanie jest domyślnie włączone dla wszystkich aplikacji ASP.NET Core. Można wyłączyć lub dostosować zachowanie próbkowania.

#### <a name="turning-off-adaptive-sampling"></a>Wyłączanie adaptacyjnego pobierania próbek

Domyślną funkcję próbkowania można wyłączyć podczas dodawania `ApplicationInsightsServiceOptions` usługi `Startup.cs` Usługi Application Insights w metodzie `ConfigureServices`, używając w pliku:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

Powyższy kod wyłączy próbkowanie adaptacyjne. Wykonaj poniższe czynności, aby dodać próbkowanie z większą liczeniem opcji dostosowywania.

#### <a name="configure-sampling-settings"></a>Konfigurowanie ustawień próbkowania

Użyj metod `TelemetryProcessorChainBuilder` rozszerzenia, jak pokazano poniżej, aby dostosować zachowanie próbkowania.

> [!IMPORTANT]
> Jeśli ta metoda jest używana do konfigurowania próbkowania, upewnij się, że właściwość jest ustawiona `aiOptions.EnableAdaptiveSampling` na `false` podczas wywoływania `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurowanie adaptacyjnego próbkowania dla funkcji platformy Azure

Postępuj zgodnie z instrukcjami z [tej strony,](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) aby skonfigurować adaptacyjne próbkowanie dla aplikacji uruchomionych w usłudze Azure Functions.

## <a name="fixed-rate-sampling"></a>Próbkowanie o stałej szybkości

Próbkowanie o stałej szybkości zmniejsza ruch wysyłany z serwera sieci Web i przeglądarek internetowych. W przeciwieństwie do adaptacyjnego próbkowania zmniejsza dane telemetryczne o ustalonej przez Ciebie stałej szybkości. Próbkowanie o stałej szybkości jest dostępne dla aplikacji ASP.NET, ASP.NET Core, Java i Python.

Podobnie jak inne techniki pobierania próbek, zachowuje to również powiązane elementy. Synchronizuje również próbkowanie klienta i serwera, dzięki czemu powiązane elementy są zachowywane — na przykład, gdy spojrzysz na widok strony w wyszukiwarce, można znaleźć jego powiązane żądania serwera. 

W Eksploratorze metryk stawki, takie jak liczba żądań i wyjątków są mnożone przez współczynnik, aby skompensować częstotliwość próbkowania, tak aby były w przybliżeniu poprawne.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji ASP.NET

1. **Wyłącz próbkowanie adaptacyjne**: [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)W `AdaptiveSamplingTelemetryProcessor` , usuń lub skomentuj węzeł.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Włącz moduł próbkowania o stałej szybkości.** Dodaj ten fragment kodu [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)do:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternatywnie zamiast ustawiać parametr próbkowania w `ApplicationInsights.config` pliku, można programowo ustawić następujące wartości:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Dowiedz[się więcej o procesorach telemetrycznych).](../../azure-monitor/app/api-filtering-sampling.md#filtering)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji ASP.NET Core

1. **Wyłączenie próbkowania adaptacyjnego**: `ConfigureServices` Zmiany `ApplicationInsightsServiceOptions`można wywczynić w metodzie, za pomocą:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Włącz moduł próbkowania o stałej szybkości.** Zmiany można wywczynić w metodzie, `Configure` jak pokazano w poniższym urywek:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji Java

Domyślnie w agencie Java i SDK nie jest włączone próbkowanie. Obecnie obsługuje tylko próbkowanie o stałej szybkości. Adaptacyjne próbkowanie nie jest obsługiwane w języku Java.

#### <a name="configuring-java-agent"></a>Konfigurowanie agenta Java

1. Pobierz [aplikacjęinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

1. Aby włączyć próbkowanie, `ApplicationInsights.json` dodaj do pliku następujące elementy:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurowanie sdk Java

1. Pobierz i skonfiguruj aplikację internetową za pomocą najnowszego [sdk Java Application Insights](../../azure-monitor/app/java-get-started.md).

2. **Włącz moduł próbkowania o stałej szybkości,** `ApplicationInsights.xml` dodając do pliku następujący fragment kodu:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Można dołączyć lub wykluczyć określone typy danych telemetrycznych `Processor` z próbkowania przy użyciu następujących znaczników wewnątrz `FixedRateSamplingTelemetryProcessor`tagu:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Typy telemetrii, które mogą być `Dependency`uwzględnione `Event` `Exception`lub `PageView` `Request`wykluczone `Trace`z próbkowania to: , , , , , i .

> [!NOTE]
> Dla wartości procentowej próbkowania wybierz wartość procentową, która jest bliska 100/N, gdzie N jest całkowitej liczby.  Obecnie próbkowanie nie obsługuje innych wartości.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji OpenCensus Python

Zaaranżuj swoją aplikację z [najnowszymi eksporterami OpenCensus Azure Monitor](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Pobieranie próbek o stałej szybkości nie jest dostępne dla eksportera metryk. Oznacza to, że metryki niestandardowe są jedynymi typami danych telemetrycznych, w których nie można skonfigurować próbkowania. Eksporter metryki wyśle wszystkie dane telemetryczne, które śledzi.

#### <a name="fixed-rate-sampling-for-tracing"></a>Pobieranie próbek o stałej szybkości do śledzenia ####
Możesz określić element `sampler` w ramach konfiguracji elementu `Tracer`. Jeśli nie ma jawnego `ProbabilitySampler` samplera, będzie używany domyślnie. Będzie `ProbabilitySampler` używać stawki 1/10000 domyślnie, co oznacza, że jeden na 10000 żądań zostaną wysłane do usługi Application Insights. Jeśli chcesz określić częstotliwość próbkowania, zobacz instrukcje poniżej.

Aby określić częstotliwość próbkowania, upewnij się, że określa `Tracer` próbnik z częstotliwością próbkowania między 0,0 i 1,0 włącznie. Częstotliwość próbkowania 1.0 reprezentuje 100%, co oznacza, że wszystkie żądania zostaną wysłane jako dane telemetryczne do usługi Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Próbkowanie o stałej szybkości dla dzienników ####
Próbkowanie o stałej `AzureLogHandler` szybkości można `logging_sampling_rate` skonfigurować, modyfikując opcjonalny argument. Jeśli nie zostanie dostarczony żaden argument, zostanie użyta częstotliwość próbkowania 1.0. Częstotliwość próbkowania 1.0 reprezentuje 100%, co oznacza, że wszystkie żądania zostaną wysłane jako dane telemetryczne do usługi Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurowanie próbkowania o stałej szybkości dla stron internetowych za pomocą języka JavaScript

Strony internetowe oparte na języku JavaScript można skonfigurować do korzystania ze usługi Application Insights. Dane telemetryczne są wysyłane z aplikacji klienckiej uruchomionej w przeglądarce użytkownika, a strony mogą być hostowane z dowolnego serwera.

Podczas [konfigurowania stron sieci Web opartych na języku JavaScript dla aplikacji Usługi Insights](javascript.md)należy modyfikować fragment kodu JavaScript, który można uzyskać z portalu usługi Application Insights.

> [!TIP]
> W ASP.NET aplikacji z dołączonym javascriptem fragment kodu zazwyczaj `_Layout.cshtml`przechodzi do pliku .

Wstawić linię `samplingPercentage: 10,` podobną do klucza oprzyrządowania:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

Dla wartości procentowej próbkowania wybierz wartość procentową, która jest bliska 100/N, gdzie N jest całkowitej liczby. Obecnie próbkowanie nie obsługuje innych wartości.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordynowanie próbkowania po stronie serwera i klienta

SDK JavaScript po stronie klienta uczestniczy w próbkowaniu o stałej szybkości w połączeniu z sdk po stronie serwera. Instrumentowane strony będą wysyłać dane telemetryczne po stronie klienta tylko od tego samego użytkownika, dla którego zestaw SDK po stronie serwera podjął decyzję o uwzględnieniu w próbkowaniu. Ta logika jest przeznaczona do zachowania integralności sesji użytkownika w aplikacjach po stronie klienta i serwera. W rezultacie z dowolnego określonego elementu telemetrii w usłudze Application Insights można znaleźć wszystkie inne elementy telemetrii dla tego użytkownika lub sesji, a w wyszukiwarce można nawigować między powiązanymi widokami strony i żądaniami.

Jeśli dane telemetryczne po stronie klienta i serwera nie są wyświetlane skoordynowane próbki:

* Sprawdź, czy włączono próbkowanie zarówno na serwerze, jak i na kliencie.
* Sprawdź, czy ustawiono ten sam procent próbkowania zarówno w kliencie, jak i na serwerze.
* Upewnij się, że wersja SDK jest 2.0 lub wyższa.

## <a name="ingestion-sampling"></a>Pobieranie próbek połkowych

Próbkowanie pozyskiwania działa w miejscu, w którym dane telemetryczne z serwera sieci web, przeglądarek i urządzeń docierają do punktu końcowego usługi application insights. Mimo że nie zmniejsza ruchu telemetryczne wysyłane z aplikacji, to zmniejsza ilość przetworzonych i zachowanych (i opłata za) przez usługa Application Insights.

Użyj tego typu próbkowania, jeśli aplikacja często przechodzi przez jego miesięczny przydział i nie masz opcji korzystania z jednego z typów próbkowania opartych na zestawie SDK. 

Ustaw częstotliwość próbkowania na stronie Użycie i koszty szacunkowe:

![W bloku Przegląd aplikacji kliknij pozycję Ustawienia, Przydział, Próbki, a następnie wybierz częstotliwość próbkowania i kliknij pozycję Aktualizuj.](./media/sampling/data-sampling.png)

Podobnie jak inne typy próbkowania algorytm zachowuje powiązane elementy telemetryczne. Na przykład podczas sprawdzania danych telemetrycznych w wyszukiwarce, będzie można znaleźć żądanie związane z określonym wyjątkiem. Metryki, takie jak szybkość żądania i współczynnik wyjątków są poprawnie zachowywane.

Punkty danych, które są odrzucane przez próbkowanie, nie są dostępne w żadnej funkcji usługi Application Insights, takiej jak [eksport ciągły.](../../azure-monitor/app/export-telemetry.md)

Pobieranie próbek połkliwe nie działa podczas pobierania próbek adaptacyjnych lub o stałej szybkości. Adaptacyjne próbkowanie jest domyślnie włączone, gdy używany jest ASP.NET zestaw SDK lub ASP.NET Core SDK lub gdy usługa Application Insights jest włączona w [usłudze Azure App Service](azure-web-apps.md) lub za pomocą Monitora stanu. Po odebraniu danych telemetrycznych przez punkt końcowy usługi Usługi Application Insights sprawdza dane telemetryczne i jeśli częstotliwość próbkowania jest zgłaszane do mniej niż 100% (co wskazuje, że dane telemetryczne są próbkowane), a następnie szybkość próbkowania pozyskiwania, który można ustawić jest ignorowana.

> [!WARNING]
> Wartość wyświetlana na kafelku portalu wskazuje wartość ustawioną dla próbkowania pozyskiwania. Nie reprezentuje rzeczywistej częstotliwości próbkowania, jeśli działa jakikolwiek rodzaj pobierania próbek SDK (próbkowanie adaptacyjne lub próbkowanie o stałej szybkości).

## <a name="when-to-use-sampling"></a>Kiedy używać pobierania próbek

Ogólnie rzecz biorąc, dla większości małych i średnich zastosowań nie trzeba próbkowania. Najbardziej przydatne informacje diagnostyczne i najdokładniejsze statystyki są uzyskiwane przez zbieranie danych na temat wszystkich działań użytkownika. 

Główne zalety pobierania próbek to:

* Usługa application insights spada ("ograniczania") punktów danych, gdy aplikacja wysyła bardzo wysoką szybkość telemetrii w krótkim odstępie czasu. Próbkowanie zmniejsza prawdopodobieństwo, że aplikacja będzie widzieć ograniczania występuje.
* Aby zachować w [ramach przydziału](pricing.md) punktów danych dla warstwy cenowej. 
* Aby zmniejszyć ruch sieciowy z kolekcji danych telemetrycznych. 

### <a name="which-type-of-sampling-should-i-use"></a>Jakiego rodzaju pobierania próbek należy używać?

**Należy użyć pobierania próbek połkowych, jeśli:**

* Często używasz miesięcznego przydziału danych telemetrycznych.
* Otrzymujesz zbyt dużo danych telemetrycznych z przeglądarek internetowych użytkowników.
* Używasz wersji SDK, która nie obsługuje próbkowania — na przykład ASP.NET wersje wcześniejsze niż 2.

**Należy użyć pobierania próbek o stałej szybkości, jeżeli:**

* Chcesz zsynchronizować próbkowanie między klientem a serwerem, tak aby podczas badania zdarzeń w [wyszukiwarce](../../azure-monitor/app/diagnostic-search.md)można nawigować między powiązanymi zdarzeniami na kliencie i serwerze, takimi jak widoki strony i żądania HTTP.
* Masz pewność, że odpowiedni procent próbkowania dla aplikacji. Powinien być wystarczająco wysoki, aby uzyskać dokładne metryki, ale poniżej stawki, która przekracza przydział cen i limity ograniczania przepustowości.

**Użyj adaptacyjnego pobierania próbek:**

Jeżeli warunki stosowania innych form pobierania próbek nie mają zastosowania, zalecamy pobieranie próbek adaptacyjnych. To ustawienie jest domyślnie włączone w ASP.NET/ASP.NET Core SDK. Nie zmniejszy to ruchu do czasu osiągnięcia określonej stawki minimalnej, w związku z czym witryny o niskim zużyciu prawdopodobnie nie będą w ogóle pobierane.

## <a name="knowing-whether-sampling-is-in-operation"></a>Wiedza o tym, czy pobieranie próbek jest w eksploatacji

Aby odnajdować rzeczywistą częstotliwość próbkowania bez względu na to, gdzie została zastosowana, użyj [zapytania Analytics](../../azure-monitor/app/analytics.md) w następujący sposób:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Jeśli widzisz, `RetainedPercentage` że dla dowolnego typu jest mniejsza niż 100, a następnie tego typu danych telemetrycznych jest próbkowanie.

> [!IMPORTANT]
> Usługa Application Insights nie przykłada sesji, metryk (w tym metryk niestandardowych) ani typów telemetrii licznika wydajności w żadnej z technik próbkowania. Te typy są zawsze wykluczone z pobierania próbek, ponieważ zmniejszenie precyzji może być wysoce niepożądane dla tych typów telemetrii.

## <a name="how-sampling-works"></a>Jak działa pobieranie próbek

Algorytm próbkowania decyduje, które elementy telemetryczne do upuszczenia i które z nich zachować. Jest to prawdą, czy próbkowanie odbywa się przez SDK lub w usłudze Usługi Application Insights. Decyzja o próbkowaniu opiera się na kilku regułach, które mają na celu zachowanie wszystkich powiązanych ze sobą punktów danych w stanie nienaruszonym, utrzymując środowisko diagnostyczne w usłudze Application Insights, które jest wykonalne i niezawodne nawet przy zmniejszonym zestawie danych. Na przykład jeśli aplikacja ma żądanie nie powiodło się zawarte w przykładzie, dodatkowe elementy telemetryczne (takie jak wyjątek i ślady rejestrowane dla tego żądania) zostaną zachowane. Pobieranie próbek utrzymuje je wszystkie razem. W rezultacie, gdy spojrzeć na szczegóły żądania w usłudze Application Insights, zawsze można zobaczyć żądanie wraz z jego elementów telemetrycznych skojarzone.

Decyzja o próbkowaniu jest oparta na identyfikatorze operacji żądania, co oznacza, że wszystkie elementy telemetryczne należące do określonej operacji są zachowywane lub usuwane. Dla elementów telemetrycznych, które nie mają zestawu identyfikatorów operacji (takich jak elementy telemetryczne zgłaszane z wątków asynchronicznych bez kontekstu HTTP) próbkowanie po prostu przechwytuje procent elementów telemetrycznych każdego typu.

Podczas prezentacji danych telemetrycznych z powrotem do Ciebie, usługa usługi Application Insights dostosowuje metryki o ten sam procent próbkowania, który został użyty w czasie zbierania, aby zrekompensować brakujące punkty danych. W związku z tym, patrząc na dane telemetryczne w usłudze Application Insights, użytkownicy widzą statystycznie poprawne przybliżenia, które są bardzo zbliżone do liczb rzeczywistych.

Dokładność przybliżenia w dużej mierze zależy od skonfigurowanej wartości procentowej próbkowania. Ponadto zwiększa dokładność dla aplikacji, które obsługują dużą ilość ogólnie podobnych żądań od wielu użytkowników. Z drugiej strony dla aplikacji, które nie działają ze znacznym obciążeniem, próbkowanie nie jest potrzebne, ponieważ te aplikacje zwykle mogą wysyłać wszystkie swoje dane telemetryczne podczas przebywania w ramach przydziału, bez powodowania utraty danych z ograniczania przepustowości. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*Jakie jest domyślne zachowanie próbkowania w ASP.NET i ASP.NET core SDK?*

* Jeśli używasz jednej z najnowszych wersji powyższego sdk, Adaptive Sampling jest domyślnie włączona z pięciu elementów telemetrycznych na sekundę.
  Istnieją dwa `AdaptiveSamplingTelemetryProcessor` węzły dodane domyślnie, `Event` a jeden zawiera typ w `Event` próbkowaniu, podczas gdy drugi wyklucza typ z próbkowania. Ta konfiguracja oznacza, że zestaw SDK spróbuje ograniczyć `Event` elementy telemetryczne do pięciu elementów telemetrycznych `Events` typów i pięciu elementów telemetrycznych wszystkich innych typów połączonych, zapewniając w ten sposób, że są próbkowane oddzielnie od innych typów telemetrii. Zdarzenia są zazwyczaj używane do telemetrii biznesowej i najprawdopodobniej nie powinny mieć wpływu woluminów telemetrycznych diagnostycznych.
  
  Poniżej przedstawiono `ApplicationInsights.config` wygenerowany plik domyślny. W ASP.NET Core to samo domyślne zachowanie jest włączone w kodzie. Użyj [przykładów we wcześniejszej sekcji tej strony,](#configuring-adaptive-sampling-for-aspnet-core-applications) aby zmienić to domyślne zachowanie.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Czy można pobierać próbki danych telemetrycznych więcej niż raz?*

* Nie. SamplingTelemetryProcessors ignorują elementy z rozważań próbkowania, jeśli element jest już próbkował. To samo dotyczy pobierania próbek pozyskiwania, jak również, który nie będzie stosować próbkowania do tych elementów już próbki w samym zestawem SDK.

*Dlaczego próbkowanie nie jest proste "zbieranie X procent każdego typu telemetrii"?*

* Takie podejście do próbkowania zapewni wysoki poziom precyzji w przybliżeniach metryk, ale spowoduje przerwanie możliwości skorelowania danych diagnostycznych na użytkownika, sesję i żądanie, co ma kluczowe znaczenie dla diagnostyki. W związku z tym próbkowanie działa lepiej z zasad, takich jak "zbieraj wszystkie elementy telemetryczne dla X procent użytkowników aplikacji" lub "zbieraj wszystkie dane telemetryczne dla X procent żądań aplikacji". Dla elementów telemetrycznych nie skojarzone z żądaniami (takich jak tło przetwarzania asynchronicznym), rezerwowy jest "zbieranie X procent wszystkich elementów dla każdego typu telemetrii." 

*Czy procent próbkowania może się zmieniać w czasie?*

* Tak, adaptacyjne próbkowanie stopniowo zmienia procent próbkowania, na podstawie aktualnie obserwowanej objętości telemetrii.

*Jeśli używam próbkowania o stałej szybkości, skąd mam wiedzieć, który procent próbkowania będzie najlepszy dla mojej aplikacji?*

* Jednym ze sposobów jest rozpoczęcie pobierania próbek adaptacyjnych, sprawdzenie, na jakiej szybkości się osiada (patrz powyższe pytanie), a następnie przejście na pobieranie próbek o stałym tempie przy użyciu tej szybkości. 
  
    W przeciwnym razie musisz odgadnąć. Analizuj bieżące użycie danych telemetrycznych w usłudze Application Insights, obserwuj wszelkie występujące ograniczanie przepustowości i oszacuj ilość zebranych danych telemetrycznych. Te trzy dane wejściowe, wraz z wybranej warstwy cenowej, sugerują, ile można zmniejszyć ilość zebranych danych telemetrycznych. Jednak zwiększenie liczby użytkowników lub inne przesunięcie w ilości danych telemetrycznych może unieważnić oszacowanie.

*Co się stanie, jeśli skonfiguruję procent próbkowania jako zbyt niski?*

* Zbyt niskie wartości procentowe próbkowania powodują zbyt agresywne próbkowanie i zmniejszają dokładność przybliżeń, gdy usługa Application Insights próbuje skompensować wizualizację danych dla zmniejszenia ilości danych. Również doświadczenie diagnostyczne może mieć negatywny wpływ, ponieważ niektóre z rzadko nieudanych lub powolnych żądań mogą być próbkowane.

*Co się stanie, jeśli skonfiguruję procent próbkowania jako zbyt wysoki?*

* Konfigurowanie zbyt wysoki procent próbkowania (nie wystarczająco agresywne) powoduje niewystarczające zmniejszenie ilości zebranych danych telemetrycznych. Nadal może wystąpić utrata danych telemetrycznych związane z ograniczaniem przepustowości, a koszt korzystania z usługi Application Insights może być wyższy niż planowano z powodu opłat za przeładowanie.

*Na jakich platformach mogę korzystać z próbkowania?*

* Pobieranie próbkowania może wystąpić automatycznie dla dowolnej telemetrii powyżej określonego woluminu, jeśli sdk nie wykonuje próbkowania. Ta konfiguracja będzie działać, na przykład, jeśli używasz starszej wersji ASP.NET SDK lub Java SDK.
* Jeśli używasz bieżącego ASP.NET lub ASP.NET podstawowych zestawów SDK (hostowanych na platformie Azure lub na własnym serwerze), domyślnie otrzymujesz adaptacyjne próbkowanie, ale możesz przełączyć się na stałe, jak opisano powyżej. W przypadku próbkowania o stałej szybkości zestaw SDK przeglądarki automatycznie synchronizuje się z przykładowymi zdarzeniami powiązanymi. 
* Jeśli używasz bieżącego agenta Java, `ApplicationInsights.json` możesz skonfigurować (dla java `ApplicationInsights.xml`SDK, configure ) aby włączyć próbkowanie o stałej szybkości. Próbkowanie jest domyślnie wyłączone. Dzięki próbkowaniu o stałej szybkości zestaw SDK przeglądarki i serwer automatycznie synchronizują się z przykładowymi zdarzeniami powiązanymi.

*Są pewne rzadkie wydarzenia, które zawsze chcę zobaczyć. Jak mogę je ominąć moduł pobierania próbek?*

* Najlepszym sposobem osiągnięcia tego celu jest napisanie niestandardowego [telemetryinitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), który ustawia `SamplingPercentage` do 100 na element telemetrii, które mają zachowane, jak pokazano poniżej. Ponieważ inicjatorzy są gwarantowane do uruchomienia przed procesorami telemetrii (w tym próbkowania), gwarantuje to, że wszystkie techniki próbkowania będzie ignorować ten element z wszelkich rozważań próbkowania. Niestandardowe inicjatory telemetrii są dostępne w ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK i Java SDK. Na przykład można skonfigurować inicjatora telemetrii przy użyciu ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Starsze wersje SDK

Adaptacyjne próbkowanie jest dostępne dla SDK usługi Application Insights dla ASP.NET wersji 2.0.0-beta3 i nowszych, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 i nowszych i jest domyślnie włączona.

Próbkowanie o stałej szybkości jest funkcją sdk w ASP.NET wersjach od wersji 2.0.0 i Java SDK w wersji 2.0.1 i nowszych.

Przed v2.5.0-beta2 ASP.NET SDK i v2.2.0-beta3 ASP.NET Core SDK, decyzja próbkowania została oparta na mieszaniu identyfikatora użytkownika dla aplikacji definiujących "użytkownika" (czyli najbardziej typowych aplikacji sieci web). Dla typów aplikacji, które nie zdefiniowały użytkowników (takich jak usługi sieci web) decyzja o próbkowaniu została oparta na identyfikatorze operacji żądania. Najnowsze wersje ASP.NET i ASP.NET core SDK używają identyfikatora operacji dla decyzji o próbkowaniu.

## <a name="next-steps"></a>Następne kroki

* [Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md) może zapewnić bardziej ścisłą kontrolę nad tym, co wysyła moduł SDK.
* Przeczytaj artykuł o sieci deweloperów [Optymalizuj dane telemetryczne za pomocą aplikacji Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
