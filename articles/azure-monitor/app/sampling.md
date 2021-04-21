---
title: Próbkowanie danych telemetrycznych w usłudze Azure Application Insights | Microsoft Docs
description: Jak kontrolować ilość danych telemetrycznych.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: ba7892c8afbe8e557c7dcf9aa3bd663f53a5728f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834754"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights

Próbkowanie to funkcja w [usłudze Azure Application Insights.](./app-insights-overview.md) Jest to zalecany sposób zmniejszenia ruchu danych telemetrycznych, kosztów danych i kosztów magazynowania przy jednoczesnym zachowaniu statystycznie poprawnej analizy danych aplikacji. Próbkowanie pomaga również uniknąć Application Insights ograniczania telemetrii. Filtr próbkowania wybiera elementy powiązane, aby można było przechodzić między elementami podczas badania diagnostycznego.

Gdy liczby metryk są prezentowane w portalu, są one ponownie nieznormalizowane w celu uwzględnienia próbkowania. Pozwala to zminimalizować wpływ na statystyki.

## <a name="brief-summary"></a>Krótkie podsumowanie

* Istnieją trzy różne typy próbkowania: próbkowanie adaptacyjne, próbkowanie o stałej szybkości i próbkowanie pozyskiwania.
* Próbkowanie adaptacyjne jest domyślnie włączone we wszystkich najnowszych wersjach Application Insights ASP.NET i ASP.NET Core Software Development Kit (SDK). Jest on również używany przez [Azure Functions](../../azure-functions/functions-overview.md).
* Próbkowanie o stałej szybkości jest dostępne w najnowszych wersjach zestawów APPLICATION INSIGHTS SDK dla języków ASP.NET, ASP.NET Core, Java (zarówno agenta, jak i zestawu SDK) oraz języka Python.
* Próbkowanie pozyskiwania działa w punkcie Application Insights końcowego usługi. Ma zastosowanie tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie. Jeśli zestaw SDK próbkuje dane telemetryczne, próbkowanie pozyskiwania jest wyłączone.
* W przypadku aplikacji internetowych rejestrowanie zdarzeń niestandardowych i zapewnienie, że zestaw zdarzeń jest zachowywany lub odrzucany razem, zdarzenia muszą mieć taką samą `OperationId` wartość.
* W przypadku pisania zapytań analitycznych należy [wziąć pod uwagę próbkowanie](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations). W szczególności zamiast po prostu zliczać rekordy, należy `summarize sum(itemCount)` użyć .
* Niektóre typy danych telemetrycznych, w tym metryki wydajności i metryki niestandardowe, są zawsze zachowywane niezależnie od tego, czy próbkowanie jest włączone, czy nie.

W poniższej tabeli podsumowano typy próbkowania dostępne dla każdego zestawu SDK i typ aplikacji:

| Application Insights SDK | Obsługiwane próbkowanie adaptacyjne | Obsługiwane próbkowanie o stałej szybkości | Obsługiwane próbkowanie pozyskiwania |
|-|-|-|-|
| ASP.NET | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-aspnet-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-applications) | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie |
| ASP.NET Core | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie |
| Azure Functions | [Tak (domyślnie włączone)](#configuring-adaptive-sampling-for-azure-functions) | Nie | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie |
| Java | Nie | [Tak](#configuring-fixed-rate-sampling-for-java-applications) | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie |
| Node.JS | Nie | [Tak](./nodejs.md#sampling) | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie
| Python | Nie | [Tak](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Tylko wtedy, gdy nie jest dostępne żadne inne próbkowanie |
| Wszystkie inne | Nie | Nie | [Tak](#ingestion-sampling) |

> [!NOTE]
> Informacje na większości tej strony dotyczą bieżących wersji zestawów APPLICATION INSIGHTS SDK. Aby uzyskać informacje o starszych wersjach zestawów SDK, [zobacz sekcję poniżej.](#older-sdk-versions)

## <a name="types-of-sampling"></a>Typy próbkowania

Istnieją trzy różne metody próbkowania:

* **Próbkowanie adaptacyjne** automatycznie dostosowuje ilość danych telemetrycznych wysyłanych z zestawu SDK w ASP.NET/ASP.NET Core i z Azure Functions. Jest to próbkowanie domyślne w przypadku korzystania z ASP.NET lub ASP.NET Core SDK. Próbkowanie adaptacyjne jest obecnie dostępne tylko ASP.NET danych telemetrycznych po stronie serwera i dla Azure Functions.

* **Próbkowanie o stałej** szybkości zmniejsza ilość danych telemetrycznych wysyłanych z serwera ASP.NET lub ASP.NET Core lub Java oraz z przeglądarek użytkowników. Możesz ustawić stawkę. Klient i serwer zsynchronizują próbkowanie, aby w polu Wyszukaj można było przechodzić między powiązanymi wyświetleniami stron i żądaniami.

* **Próbkowanie pozyskiwania** odbywa się w punkcie Application Insights końcowego usługi. Odrzuca niektóre dane telemetryczne, które docierają z aplikacji, z ustawioną częstotliwością próbkowania. Nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, ale pomaga zachować miesięczny limit przydziału. Główną zaletą próbkowania pozyskiwania jest możliwość ustawienia częstotliwości próbkowania bez ponownego wdychania aplikacji. Próbkowanie pozyskiwania działa jednolicie dla wszystkich serwerów i klientów, ale nie ma zastosowania, gdy wszystkie inne typy próbkowania są w działaniu.

> [!IMPORTANT]
> Jeśli metody próbkowania z adaptacyjną lub stałą szybkością są włączone dla typu telemetrii, próbkowanie pozyskiwania jest wyłączone dla tej telemetrii. Jednak typy telemetrii, które są wykluczone z próbkowania na poziomie zestawu SDK, nadal będą podlegać próbkowaniu pozyskiwania z szybkością ustawioną w portalu.

## <a name="adaptive-sampling"></a>Próbkowanie adaptacyjne

Próbkowanie adaptacyjne wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera internetowego do punktu Application Insights końcowego usługi.

> [!TIP]
> Próbkowanie adaptacyjne jest domyślnie włączone w przypadku korzystania z zestawu ASP.NET SDK lub zestawu ASP.NET Core SDK, a także jest domyślnie włączone dla Azure Functions.

Wolumin jest dostosowywany automatycznie w celu utrzymania określonej maksymalnej szybkości ruchu i jest kontrolowany za pośrednictwem ustawienia `MaxTelemetryItemsPerSecond` . Jeśli aplikacja generuje małą ilość danych telemetrycznych, na przykład podczas debugowania lub z powodu niskiego użycia, elementy nie będą porzucane przez procesor próbkowania, o ile ilość jest poniżej `MaxTelemetryItemsPerSecond` . W przypadku wzrostu ilości danych telemetrycznych częstotliwość próbkowania jest dostosowywana w celu osiągnięcia woluminu docelowego. Korekta jest ponownie obliczania w regularnych odstępach czasu i jest oparta na średniej ruchomej szybkości transmisji wychodzącej.

Aby osiągnąć wolumin docelowy, niektóre wygenerowane dane telemetryczne są odrzucane. Jednak podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas inspekcji telemetrii w wyszukiwaniu można znaleźć żądanie związane z określonym wyjątkiem.

Liczniki metryk, takie jak częstotliwość żądań i częstotliwość wyjątków, są dostosowywane w celu kompensowania częstotliwości próbkowania, dzięki czemu w Eksploratorze metryk są wyświetlane w przybliżeniu poprawne wartości.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla ASP.NET aplikacji

> [!NOTE]
> Ta sekcja dotyczy ASP.NET, a nie aplikacji ASP.NET Core. [Dowiedz się więcej na temat konfigurowania próbkowania adaptacyjnego dla ASP.NET Core w dalszej części tego dokumentu.](#configuring-adaptive-sampling-for-aspnet-core-applications)

W [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) programie można dostosować kilka parametrów w `AdaptiveSamplingTelemetryProcessor` węźle. Pokazane wartości są wartościami domyślnymi:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Docelowa szybkość operacji [logicznych, które](./correlation.md#data-model-for-telemetry-correlation) algorytm adaptacyjny ma zbierać **na każdym hoście serwera.** Jeśli aplikacja internetowa działa na wielu hostach, zmniejsz tę wartość, aby zachować docelową szybkość ruchu w Application Insights portal.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał ponownego oceniania bieżącej szybkości telemetrii. Ocena jest wykonywana jako średnia ruchoma. Możesz skrócić ten interwał, jeśli telemetria jest odpowiedzialna za nagłe skoki.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Jak szybko po zmianie wartości procentowej próbkowania możemy ponownie obniżyć wartość procentową próbkowania w celu przechwycenia mniejszej wartości danych?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Jak szybko po zmianie wartości procentowej próbkowania możemy ponownie zwiększyć wartość procentową próbkowania w celu przechwycenia większej liczby danych?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Jaka jest minimalna wartość, jaką możemy ustawić w zależności od tego, jaka jest wartość procentowa próbkowania?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jaka jest maksymalna wartość, jaką możemy ustawić w zależności od tego, jaka jest wartość procentowa próbkowania?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    W obliczeniu średniej ruchomej określa ona wagę, która powinna zostać przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej niż 1. Mniejsze wartości sprawiają, że algorytm jest mniej reaktywny na nagłe zmiany.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Ilość danych telemetrycznych do próbkowania, gdy aplikacja została właśnie uruchomiona. Nie zmniejszaj tej wartości podczas debugowania.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Rozdzielana średnikami lista typów, które nie mają być próbkowane. Rozpoznawane typy to: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . Przesyłane są wszystkie dane telemetryczne określonych typów. Typy, które nie są określone, zostaną próbkowane.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być próbkowane. Rozpoznawane typy to: `Dependency` , , , , , `Event` `Exception` `PageView` `Request` `Trace` . Określone typy zostaną próbkowane. wszystkie dane telemetryczne innych typów będą zawsze przesyłane.

**Aby wyłączyć próbkowanie** adaptacyjne, usuń `AdaptiveSamplingTelemetryProcessor` węzła z . `ApplicationInsights.config`

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie próbkowania adaptacyjnego w kodzie

Zamiast ustawiać parametr próbkowania w `.config` pliku, można programowo ustawić te wartości.

1. Usuń wszystkie `AdaptiveSamplingTelemetryProcessor` węzła z `.config` pliku.
2. Użyj poniższego fragmentu kodu, aby skonfigurować próbkowanie adaptacyjne:

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

    (Dowiedz[się więcej o procesorach telemetrii).](./api-filtering-sampling.md#filtering)

Możesz również dostosować częstotliwość próbkowania dla każdego typu telemetrii indywidualnie lub nawet wykluczyć niektóre typy z próbkowania:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla ASP.NET Core

Nie ma żadnej `ApplicationInsights.config` ASP.NET Core, więc cała konfiguracja jest wykonywana za pośrednictwem kodu.
Próbkowanie adaptacyjne jest domyślnie włączone dla wszystkich ASP.NET Core. Możesz wyłączyć lub dostosować zachowanie próbkowania.

#### <a name="turning-off-adaptive-sampling"></a>Wyłączanie próbkowania adaptacyjnego

Domyślną funkcję próbkowania można wyłączyć podczas dodawania Application Insights, w metodzie `ConfigureServices` , przy użyciu w pliku `ApplicationInsightsServiceOptions` `Startup.cs` :

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

Powyższy kod wyłączy próbkowanie adaptacyjne. Wykonaj poniższe kroki, aby dodać próbkowanie z większej liczby opcji dostosowywania.

#### <a name="configure-sampling-settings"></a>Konfigurowanie ustawień próbkowania

Użyj metod rozszerzenia , `TelemetryProcessorChainBuilder` jak pokazano poniżej, aby dostosować zachowanie próbkowania.

> [!IMPORTANT]
> Jeśli używasz tej metody do konfigurowania próbkowania, upewnij się, że właściwość została ustawiona `aiOptions.EnableAdaptiveSampling` na `false` podczas wywoływania `AddApplicationInsightsTelemetry()` metody . Po w wprowadzeniem tej zmiany należy dokładnie postępować  zgodnie z instrukcjami w bloku kodu poniżej, aby ponownie włączyć próbkowanie adaptacyjne z dostosowaniami. Niewykonanie tej pracy może spowodować pozyskiwanie nadmiarowych danych. Zawsze testuj po zmianie ustawień próbkowania i ustaw odpowiedni dzienny limit [danych,](pricing.md#set-the-daily-cap) aby ułatwić kontrolę kosztów.

```csharp
using Microsoft.ApplicationInsights.Extensibility

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

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurowanie próbkowania adaptacyjnego dla Azure Functions

Postępuj zgodnie z [instrukcjami na tej stronie,](../../azure-functions/configure-monitoring.md#configure-sampling) aby skonfigurować próbkowanie adaptacyjne dla aplikacji uruchomionych w Azure Functions.

## <a name="fixed-rate-sampling"></a>Próbkowanie o stałej szybkości

Próbkowanie o stałej szybkości zmniejsza ruch wysyłany z serwera internetowego i przeglądarek internetowych. W przeciwieństwie do próbkowania adaptacyjnego zmniejsza ono telemetrię z ustalonym szybkością, o których decydujesz. Próbkowanie o stałej szybkości jest dostępne dla ASP.NET, ASP.NET Core, Java i Python.

Podobnie jak w przypadku innych technik próbkowania, zachowuje to również powiązane elementy. Synchronizuje również próbkowanie klienta i serwera w taki sposób, aby powiązane elementy zostały zachowane — na przykład podczas wyświetlania widoku strony w wyszukiwaniu można znaleźć powiązane żądania serwera. 

W Eksplorator metryk współczynniki, takie jak liczba żądań i wyjątków, są mnożone przez współczynnik kompensowania częstotliwości próbkowania, aby były w przybliżeniu poprawne.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla ASP.NET aplikacji

1. **Wyłącz próbkowanie adaptacyjne:** w [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) programie usuń węzeł lub usuń jego `AdaptiveSamplingTelemetryProcessor` komentarz.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Włącz moduł próbkowania o stałej szybkości.** Dodaj ten fragment kodu [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) do :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternatywnie zamiast ustawiać parametr próbkowania w pliku, można `ApplicationInsights.config` programowo ustawić następujące wartości:

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

    (Dowiedz[się więcej o procesorach telemetrii).](./api-filtering-sampling.md#filtering)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji ASP.NET Core

1. **Wyłącz próbkowanie adaptacyjne:** zmiany można wprowadzać w `ConfigureServices` metodzie przy użyciu metody `ApplicationInsightsServiceOptions` :

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

2. **Włącz moduł próbkowania o stałej szybkości.** Zmiany można wprowadzać w `Configure` metodzie , jak pokazano w poniższym fragmencie kodu:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = configuration.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji Java

Domyślnie w agencie języka Java i zestawie SDK nie jest włączone próbkowanie. Obecnie obsługuje tylko próbkowanie o stałej częstotliwości. Próbkowanie adaptacyjne nie jest obsługiwane w języku Java.

#### <a name="configuring-java-agent"></a>Konfigurowanie agenta Java

1. Pobierz [plik applicationinsights-agent-3.0.0-PREVIEW.5.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Aby włączyć próbkowanie, dodaj do pliku następujące `applicationinsights.json` elementy:

```json
{
  "sampling": {
    "percentage": 10 //this is just an example that shows you how to enable only 10% of transaction 
  }
}
```

#### <a name="configuring-java-sdk"></a>Konfigurowanie zestawu Java SDK

1. Pobierz i skonfiguruj aplikację internetową przy użyciu najnowszego [zestawu SDK Application Insights Java.](./java-get-started.md)

2. **Włącz moduł próbkowania o stałej szybkości,** dodając następujący fragment kodu do `ApplicationInsights.xml` pliku:

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

3. Możesz dołączyć lub wykluczyć określone typy danych telemetrycznych z próbkowania przy użyciu następujących tagów wewnątrz `Processor` tagu `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Typy telemetrii, które mogą być uwzględniane lub wykluczane z próbkowania, to: `Dependency` , , , , i `Event` `Exception` `PageView` `Request` `Trace` .

> [!NOTE]
> Dla wartości procentowej próbkowania wybierz wartość procentową, która jest bliska 100/N, gdzie N jest liczbą całkowitą.  Obecnie próbkowanie nie obsługuje innych wartości.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji OpenCensus w języku Python

Instrumentować aplikację przy użyciu [najnowszego standardu OpenCensus Azure Monitor eksporterów](./opencensus-python.md).

> [!NOTE]
> Próbkowanie o stałej szybkości nie jest dostępne dla eksportera metryk. Oznacza to, że metryki niestandardowe są jedynymi typami danych telemetrycznych, w przypadku których NIE można skonfigurować próbkowania. Eksporter metryk wyśle wszystkie dane telemetryczne, które śledzi.

#### <a name="fixed-rate-sampling-for-tracing"></a>Próbkowanie o stałej częstotliwości na przykład dla śledzenia ####
Możesz określić element `sampler` w ramach konfiguracji elementu `Tracer`. Jeśli nie zostanie podany jawny próbnik, `ProbabilitySampler` domyślnie zostanie użyta wartość . Wartość będzie domyślnie używać szybkości `ProbabilitySampler` 1/10000, co oznacza, że jedno na każde 10000 żądań będzie wysyłane do Application Insights. Jeśli chcesz określić częstotliwość próbkowania, zobacz instrukcje poniżej.

Aby określić częstotliwość próbkowania, upewnij się, że określono próbkator z częstotliwością próbkowania od `Tracer` 0,0 do 1,0 włącznie. Częstotliwość próbkowania 1,0 reprezentuje 100%, co oznacza, że wszystkie żądania będą wysyłane jako dane telemetryczne do Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Próbkowanie o stałej szybkości dla dzienników ####
Próbkowanie o stałej szybkości można skonfigurować `AzureLogHandler` dla parametru , modyfikując `logging_sampling_rate` opcjonalny argument . Jeśli argument nie zostanie podany, zostanie użyta częstotliwość próbkowania 1,0. Częstotliwość próbkowania 1,0 reprezentuje 100%, co oznacza, że wszystkie żądania będą wysyłane jako dane telemetryczne do Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurowanie próbkowania o stałej szybkości dla stron internetowych za pomocą języka JavaScript

Strony internetowe oparte na języku JavaScript można skonfigurować do używania Application Insights. Dane telemetryczne są wysyłane z aplikacji klienckiej uruchomionej w przeglądarce użytkownika, a strony mogą być hostowane z dowolnego serwera.

Podczas [konfigurowania stron internetowych opartych](javascript.md)na języku JavaScript dla usługi Application Insights zmodyfikuj fragment kodu JavaScript, który można pobrać z Application Insights portal.

> [!TIP]
> W ASP.NET z dołączonym kodem JavaScript fragment kodu zazwyczaj znajduje się w pliku `_Layout.cshtml` .

Wstaw wiersz, taki `samplingPercentage: 10,` jak przed kluczem instrumentacji:

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

Dla wartości procentowej próbkowania wybierz wartość procentową, która jest bliska 100/N, gdzie N jest liczbą całkowitą. Obecnie próbkowanie nie obsługuje innych wartości.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordynowanie próbkowania po stronie serwera i po stronie klienta

Zestaw SDK języka JavaScript po stronie klienta uczestniczy w próbkowaniu o stałej szybkości w połączeniu z zestawem SDK po stronie serwera. Strony z instrumentami będą wysyłać tylko dane telemetryczne po stronie klienta od tego samego użytkownika, dla którego zestaw SDK po stronie serwera podjął decyzję o dołączyć dane do próbkowania. Ta logika została zaprojektowana w celu zachowania integralności sesji użytkowników w aplikacjach po stronie klienta i serwera. W związku z tym z dowolnego elementu telemetrii w programie Application Insights można znaleźć wszystkie inne elementy telemetrii dla tego użytkownika lub sesji, a w wyszukiwaniu można przechodzić między powiązanymi wyświetleniami stron i żądaniami.

Jeśli dane telemetryczne po stronie klienta i serwera nie pokazują skoordynowanych przykładów:

* Sprawdź, czy włączono próbkowanie zarówno na serwerze, jak i na kliencie.
* Sprawdź, czy ustawiono tę samą wartość procentową próbkowania zarówno dla klienta, jak i serwera.
* Upewnij się, że wersja zestawu SDK to 2.0 lub nowsza.

## <a name="ingestion-sampling"></a>Próbkowanie pozyskiwania

Próbkowanie pozyskiwania działa w punkcie, w którym dane telemetryczne z serwera internetowego, przeglądarek i urządzeń docierają do Application Insights końcowego usługi. Chociaż nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, zmniejsza ilość przetwarzanych i zachowywanych danych (i opłat za nie) dzięki Application Insights.

Tego typu próbkowania należy używać, jeśli aplikacja często przekroczy miesięczny limit przydziału i nie masz możliwości użycia jednego z typów próbkowania opartych na zestawie SDK. 

Ustaw częstotliwość próbkowania na stronie Użycie i szacowane koszty:

![W bloku Przegląd aplikacji kliknij pozycję Ustawienia, Limit przydziału, Przykłady, a następnie wybierz częstotliwość próbkowania i kliknij przycisk Aktualizuj.](./media/sampling/data-sampling.png)

Podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas inspekcji telemetrii w wyszukiwaniu można znaleźć żądanie związane z określonym wyjątkiem. Liczniki metryk, takie jak liczba żądań i liczba wyjątków, są poprawnie zachowywane.

Punkty danych, które są odrzucane przez próbkowanie, nie są dostępne w żadnej Application Insights funkcji, takiej jak [eksport ciągły.](./export-telemetry.md)

Próbkowanie pozyskiwania nie działa, gdy próbkowanie adaptacyjne lub próbkowanie o stałej szybkości jest w działaniu. Próbkowanie adaptacyjne jest domyślnie włączone w przypadku używania zestawu ASP.NET SDK lub zestawu ASP.NET Core SDK albo gdy Application Insights jest włączone w [programie Azure App Service ](azure-web-apps.md) lub przy użyciu monitor stanu. Gdy dane telemetryczne są odbierane przez punkt końcowy usługi Application Insights, sprawdza ona telemetrię i czy częstotliwość próbkowania jest zgłaszana jako mniejsza niż 100% (co oznacza, że dane telemetryczne są próbkowane), ustawiona częstotliwość próbkowania pozyskiwania jest ignorowana.

> [!WARNING]
> Wartość wyświetlana na kafelku portalu wskazuje wartość ustawioną na próbkowanie pozyskiwania. Nie reprezentuje rzeczywistej częstotliwości próbkowania, jeśli istnieje jakikolwiek rodzaj próbkowania zestawu SDK (próbkowanie adaptacyjne lub próbkowanie o stałej szybkości).

## <a name="when-to-use-sampling&quot;></a>Kiedy należy używać próbkowania

Ogólnie rzecz biorąc, w przypadku większości małych i średnich aplikacji próbkowanie nie jest konieczne. Najbardziej przydatne informacje diagnostyczne i najdokładniejsze statystyki są uzyskiwane przez zbieranie danych dotyczących wszystkich działań użytkownika. 

Główne zalety próbkowania to:

* Application Insights usługa porzuca (&quot;ogranicza") punkty danych, gdy aplikacja wysyła bardzo dużą szybkość telemetrii w krótkim przedziale czasu. Próbkowanie zmniejsza prawdopodobieństwo wystąpienia ograniczenia przepustowości w aplikacji.
* Aby zachować limit [przydziału](pricing.md) punktów danych dla warstwy cenowej. 
* Aby zmniejszyć ruch sieciowy z kolekcji danych telemetrycznych. 

### <a name="which-type-of-sampling-should-i-use"></a>Jakiego typu próbkowania należy użyć?

**Użyj próbkowania pozyskiwania, jeśli:**

* Często używasz miesięcznego limitu przydziału danych telemetrycznych.
* Z przeglądarek internetowych użytkowników jest chylisz się zbyt wiele danych telemetrycznych.
* Używasz wersji zestawu SDK, która nie obsługuje próbkowania — na przykład w ASP.NET starszych niż 2.

**Użyj próbkowania o stałej szybkości, jeśli:**

* Chcesz zsynchronizować próbkowanie między klientem a serwerem, aby podczas [](./diagnostic-search.md)badania zdarzeń w wyszukiwaniu można było nawigować między powiązanymi zdarzeniami na kliencie i serwerze, takimi jak wyświetlenia stron i żądania HTTP.
* Masz pewność, że masz odpowiednią wartość procentową próbkowania dla twojej aplikacji. Powinna ona być wystarczająco wysoka, aby uzyskać dokładne metryki, ale poniżej stawki przekraczającej limit przydziału cen i limity ograniczania przepustowości.

**Użyj próbkowania adaptacyjnego:**

Jeśli warunki korzystania z innych form próbkowania nie mają zastosowania, zalecamy próbkowanie adaptacyjne. To ustawienie jest domyślnie włączone w zestawie SDK ASP.NET/ASP.NET Core. Nie zmniejszy to ruchu, dopóki nie zostanie osiągnięta pewna minimalna stawka, w związku z tym witryny o niskim użyciu prawdopodobnie nie będą w ogóle próbkowane.

## <a name="knowing-whether-sampling-is-in-operation"></a>Jak sprawdzić, czy próbkowanie jest w toku

Aby wykryć rzeczywistą częstotliwość próbkowania niezależnie od tego, gdzie została zastosowana, użyj [zapytania](../logs/log-query-overview.md) analizy, takiego jak:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Jeśli zobaczysz, że `RetainedPercentage` dla dowolnego typu jest mniejsza niż 100, ten typ telemetrii jest próbkowany.

> [!IMPORTANT]
> Application Insights nie próbkuje sesji, metryk (w tym metryk niestandardowych) ani typów telemetrii liczników wydajności w żadnej z technik próbkowania. Te typy są zawsze wykluczone z próbkowania, ponieważ zmniejszenie dokładności może być wysoce niepożądane dla tych typów telemetrii.

## <a name="how-sampling-works"></a>Jak działa próbkowanie

Algorytm próbkowania decyduje o tym, które elementy telemetrii należy usunąć i które z nich zachować. Jest to prawdziwe niezależnie od tego, czy próbkowanie jest wykonywane przez zestaw SDK, czy w Application Insights usługi. Decyzja o próbkowaniu jest oparta na kilku zasadach, które mają na celu zachowanie wszystkich powiązanych punktów danych bez zmian, zachowując środowisko diagnostyczne w Application Insights, które jest z możliwością działania i niezawodnością nawet w przypadku ograniczonego zestawu danych. Na przykład jeśli w przykładzie aplikacja ma żądanie, które zakończyło się niepowodzeniem, zostaną zachowane dodatkowe elementy telemetrii (takie jak wyjątek i ślady zarejestrowane dla tego żądania). Próbkowanie zachowuje lub porzuca je wszystkie razem. W związku z tym, gdy przyjrzysz się szczegółom żądania w Application Insights, zawsze możesz zobaczyć żądanie wraz ze skojarzonymi z nim elementami telemetrii.

Decyzja o próbkowaniu jest oparta na identyfikatorze operacji żądania, co oznacza, że wszystkie elementy telemetrii należące do określonej operacji są zachowywane lub porzucane. W przypadku elementów telemetrii, które nie mają zestawu identyfikatorów operacji (takich jak elementy telemetrii zgłaszane z wątków asynchronicznych bez kontekstu HTTP), próbkowanie po prostu przechwytuje procent elementów telemetrii każdego typu.

Podczas prezentowania telemetrii usługa Application Insights dostosowuje metryki o tę samą wartość procentową próbkowania, która była używana w czasie zbierania, aby skompensować brakujące punkty danych. W związku z tym podczas przyglądania się telemetrii w Application Insights, użytkownicy widzą statystycznie poprawne przybliżenia, które są bardzo zbliżone do liczb rzeczywistych.

Dokładność przybliżenia w dużej mierze zależy od skonfigurowanej wartości procentowej próbkowania. Ponadto dokładność zwiększa się w przypadku aplikacji, które obsługują dużą liczbę zwykle podobnych żądań od wielu użytkowników. Z drugiej strony w przypadku aplikacji, które nie pracują ze znacznym obciążeniem, próbkowanie nie jest wymagane, ponieważ te aplikacje zwykle wysyłają wszystkie swoje dane telemetryczne, pozostając w ramach limitu przydziału, bez powodowania utraty danych przed ograniczaniem. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*Jakie jest domyślne zachowanie próbkowania w zestawach ASP.NET i ASP.NET Core?*

* Jeśli używasz jednej z najnowszych wersji powyższego zestawu SDK, próbkowanie adaptacyjne jest domyślnie włączone z pięcioma elementami telemetrii na sekundę.
  Domyślnie dodawane są dwa węzły, a jeden zawiera typ próbkowania, a drugi wyklucza `AdaptiveSamplingTelemetryProcessor` `Event` typ z `Event` próbkowania. Ta konfiguracja oznacza, że zestaw SDK spróbuje ograniczyć elementy telemetrii do pięciu elementów typu telemetrii i pięć połączonych elementów telemetrii wszystkich innych typów, zapewniając, że próbki są próbkowane niezależnie od innych typów `Event` `Events` telemetrii. Zdarzenia są zwykle używane na potrzeby telemetrii biznesowej i najprawdopodobniej nie powinny mieć na nie wpływu woluminy telemetrii diagnostycznej.
  
  Poniżej przedstawiono wygenerowany `ApplicationInsights.config` plik domyślny. W ASP.NET Core to samo domyślne zachowanie jest włączone w kodzie. Użyj [przykładów we wcześniejszej sekcji tej strony,](#configuring-adaptive-sampling-for-aspnet-core-applications) aby zmienić to domyślne zachowanie.

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

*Czy dane telemetryczne można próbkować więcej niż raz?*

* Nie. Metody SamplingTelemetryProcessors ignorują elementy z rozważań próbkowania, jeśli element jest już próbkowany. Dotyczy to również próbkowania pozyskiwania, które nie zastosuje próbkowania do elementów, które zostały już próbkowane w samym zestawie SDK.

*Dlaczego próbkowanie nie jest prostym "zbieraniem X procentu każdego typu telemetrii"?*

* Takie podejście do próbkowania zapewniałoby wysoki poziom dokładności w przybliżeniach metryk, jednak skorelowałoby możliwość korelowania danych diagnostycznych na użytkownika, sesję i żądanie, co ma kluczowe znaczenie dla diagnostyki. W związku z tym próbkowanie działa lepiej w przypadku zasad, takich jak "zbieranie wszystkich elementów telemetrii dla X procent użytkowników aplikacji" lub "zbieranie wszystkich danych telemetrycznych dla X procent żądań aplikacji". W przypadku elementów telemetrii, które nie są skojarzone z żądaniami (takich jak przetwarzanie asynchroniczne w tle), rezerwowym jest "zbieranie X procent wszystkich elementów dla każdego typu telemetrii". 

*Czy procent próbkowania może zmieniać się w czasie?*

* Tak, próbkowanie adaptacyjne stopniowo zmienia wartość procentową próbkowania na podstawie aktualnie zaobserwowanej ilości danych telemetrycznych.

*Jeśli używam próbkowania o stałej szybkości, jak mogę sprawdzić, która wartość procentowa próbkowania będzie najlepsza dla mojej aplikacji?*

* Jednym ze sposobów jest rozpoczęcie od próbkowania adaptacyjnego, dowiedz się, na jakiej częstotliwości się rozliczy (zobacz powyższe pytanie), a następnie przełączyć się na próbkowanie o stałej częstotliwości przy użyciu tej częstotliwości. 
  
    W przeciwnym razie musisz odgadnąć. Przeanalizuj bieżące użycie telemetrii w Application Insights, obserwuj występujące ograniczanie przepustowości i os szacuj ilość zebranych danych telemetrycznych. Te trzy dane wejściowe wraz z wybraną warstwą cenową sugerują, o ile chcesz zmniejszyć ilość zebranych danych telemetrycznych. Jednak zwiększenie liczby użytkowników lub inna zmiana ilości danych telemetrycznych może unieważnić oszacowanie.

*Co się stanie, jeśli skonfiguruję zbyt małą wartość procentową próbkowania?*

* Zbyt niskie wartości procentowe próbkowania powodują zbyt agresywne próbkowanie i zmniejszają dokładność przybliżeń, gdy Application Insights próbuje skompensować wizualizację danych w celu zmniejszenia ilości danych. Może to mieć również negatywny wpływ na środowisko diagnostyczne, ponieważ niektóre rzadko awarii lub powolne żądania mogą być próbkowane.

*Co się stanie, jeśli skonfiguruję zbyt wysoką wartość procentową próbkowania?*

* Skonfigurowanie zbyt dużej wartości procentowej próbkowania (nie wystarczająco agresywnej) skutkuje niewystarczającym zmniejszeniem ilości zebranych danych telemetrycznych. Nadal może wystąpić utrata danych telemetrycznych związanych z ograniczaniem przepustowości, a koszt korzystania z usługi Application Insights może być wyższy, niż zaplanowano z powodu opłat za użycie overage.

*Na jakich platformach można używać próbkowania?*

* Próbkowanie pozyskiwania może nastąpić automatycznie dla każdej telemetrii powyżej określonego woluminu, jeśli zestaw SDK nie wykonuje próbkowania. Ta konfiguracja będzie działać na przykład w przypadku używania starszej wersji zestawu ASP.NET SDK lub zestawu JAVA SDK.
* Jeśli używasz bieżących zestawów SDK ASP.NET lub ASP.NET Core (hostowanych na platformie Azure lub na własnym serwerze), domyślnie pobierasz próbkowanie adaptacyjne, ale możesz przełączyć się na stałą stawkę, jak opisano powyżej. Dzięki próbkowaniu o stałej szybkości zestaw SDK przeglądarki automatycznie synchronizuje się z przykładami powiązanych zdarzeń. 
* Jeśli używasz bieżącego agenta języka Java, możesz skonfigurować (dla zestawu Java SDK, skonfiguruj ) włączanie próbkowania `applicationinsights.json` `ApplicationInsights.xml` o stałej szybkości. Próbkowanie jest domyślnie wyłączone. W przypadku próbkowania o stałej szybkości zestaw SDK przeglądarki i serwer automatycznie synchronizują się z przykładami powiązanych zdarzeń.

*Istnieją pewne rzadkie zdarzenia, które zawsze chcę zobaczyć. Jak mogę je pobrać po module próbkowania?*

* Najlepszym sposobem osiągnięcia tego celu jest napisanie niestandardowego elementu [TelemetryInitializer,](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)który ustawia dla elementu telemetrii 100, który ma zostać zachowany, jak pokazano `SamplingPercentage` poniżej. Ponieważ gwarantowane jest uruchamianie inicjatorów przed procesorami telemetrii (w tym próbkowania), gwarantuje to, że wszystkie techniki próbkowania zignorują ten element z wszelkich kwestii do rozważenia podczas próbkowania. Inicjatory telemetrii niestandardowej są dostępne w zestawie SDK języka ASP.NET, zestawie SDK ASP.NET Core, zestawie SDK języka JavaScript i zestawie JAVA SDK. Na przykład możesz skonfigurować inicjator telemetrii przy użyciu ASP.NET SDK:

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

## <a name="older-sdk-versions"></a>Starsze wersje zestawu SDK

Próbkowanie adaptacyjne jest dostępne dla zestawu APPLICATION INSIGHTS SDK dla wersji 2.0.0-beta3 i nowszych wersji ASP.NET, zestawu SDK Microsoft.ApplicationInsights.AspNetCore w wersji 2.2.0-beta1 lub nowszej i jest domyślnie włączone.

Próbkowanie o stałej szybkości to funkcja zestawu SDK w wersjach ASP.NET 2.0.0 i Java SDK w wersji 2.0.1 lub starszej.

Przed wersjami 2.5.0-beta2 zestawu ASP.NET SDK i 2.2.0-beta3 zestawu ASP.NET Core SDK decyzja o próbkowaniu opierała się na skrótie identyfikatora użytkownika dla aplikacji, które definiują "użytkownika" (czyli najbardziej typowe aplikacje internetowe). W przypadku typów aplikacji, które nie zdefiniowały użytkowników (takich jak usługi internetowe), decyzja o próbkowaniu została podjęta na podstawie identyfikatora operacji żądania. Ostatnie wersje zestawów SDK ASP.NET i ASP.NET Core używają identyfikatora operacji do podjęcia decyzji dotyczącej próbkowania.

## <a name="next-steps"></a>Następne kroki

* [Filtrowanie](./api-filtering-sampling.md) może zapewnić bardziej ścisłą kontrolę nad tym, co wysyła zestaw SDK.
* Przeczytaj artykuł Sieć deweloperów Optimize Telemetry with Application Insights (Optymalizowanie [telemetrii za pomocą Application Insights).](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights)

