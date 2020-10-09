---
title: Próbkowanie danych telemetrycznych na platformie Azure Application Insights | Microsoft Docs
description: Jak zachować ilość danych telemetrycznych pod kontrolą.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 151bc87bd5674a61b8652adfa70634318c405240
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839609"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights

Próbkowanie to funkcja [platformy Azure Application Insights](./app-insights-overview.md). Zalecanym sposobem jest zmniejszenie ruchu telemetrii, kosztów danych i kosztów magazynowania przy zachowaniu statystycznej prawidłowej analizy danych aplikacji. Próbkowanie pomaga również uniknąć Application Insights ograniczania danych telemetrycznych. Filtr próbkowania wybiera elementy, które są powiązane, dzięki czemu można przechodzić między elementami podczas przeprowadzania badań diagnostycznych.

Gdy liczba metryk jest prezentowana w portalu, zostaną one rozznormalizowane w celu uwzględnienia próbkowania. W ten sposób minimalizuje wszystkie efekty dotyczące statystyk.

## <a name="brief-summary"></a>Krótkie podsumowanie

* Istnieją trzy różne typy próbkowania: próbkowanie adaptacyjne, próbkowanie o stałej szybkości i próbkowanie pozyskiwania.
* Próbkowanie adaptacyjne jest domyślnie włączone we wszystkich najnowszych wersjach Application Insights ASP.NET i ASP.NET Core Software Development Kit (SDK). Jest on również używany przez [Azure Functions](../../azure-functions/functions-overview.md).
* Próbkowanie z ustaloną szybkością jest dostępne w ostatnich wersjach Application Insights zestawów SDK dla ASP.NET, ASP.NET Core, Java (zarówno dla agenta, jak i zestawu SDK), jak i języka Python.
* Próbkowanie pozyskiwania działa w punkcie końcowym usługi Application Insights. Ma zastosowanie tylko wtedy, gdy nie są stosowane żadne inne próbki. Jeśli zestaw SDK próbkuje dane telemetryczne, pobieranie próbek pozyskiwania jest wyłączone.
* W przypadku aplikacji sieci Web, Jeśli rejestrujesz zdarzenia niestandardowe i chcesz upewnić się, że zestaw zdarzeń jest przechowywany lub odrzucony, zdarzenia muszą mieć tę samą `OperationId` wartość.
* W przypadku pisania zapytań analitycznych należy [wziąć pod uwagę próbkowanie](../log-query/aggregations.md). W szczególności, a nie do zwykłego zliczania rekordów, należy użyć `summarize sum(itemCount)` .
* Niektóre typy telemetrii, w tym metryki wydajności i metryki niestandardowe, są zawsze zachowywane niezależnie od tego, czy próbkowanie jest włączone, czy nie.

W poniższej tabeli zestawiono typy próbkowania dostępne dla każdego zestawu SDK i typu aplikacji:

| Zestaw SDK Application Insights | Obsługiwane próbkowanie adaptacyjne | Obsługiwane próbkowanie z ustaloną szybkością | Obsługiwane próbkowanie pozyskiwania |
|-|-|-|-|
| ASP.NET | [Tak (domyślnie włączona)](#configuring-adaptive-sampling-for-aspnet-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-applications) | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie |
| ASP.NET Core | [Tak (domyślnie włączona)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Tak](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie |
| Azure Functions | [Tak (domyślnie włączona)](#configuring-adaptive-sampling-for-azure-functions) | Nie | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie |
| Java | Nie | [Tak](#configuring-fixed-rate-sampling-for-java-applications) | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie |
| Node.JS | Nie | [Tak](./nodejs.md#sampling) | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie
| Python | Nie | [Tak](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Tylko wtedy, gdy nie jest stosowane żadne inne próbkowanie |
| Wszystkie pozostałe | Nie | Nie | [Tak](#ingestion-sampling) |

> [!NOTE]
> Informacje na większości tej strony dotyczą bieżących wersji zestawów SDK Application Insights. Aby uzyskać informacje na temat starszych wersji zestawów SDK, [Zobacz sekcję poniżej](#older-sdk-versions).

## <a name="types-of-sampling"></a>Typy próbkowania

Istnieją trzy różne metody próbkowania:

* **Próbkowanie adaptacyjne** automatycznie dostosowuje ilość danych telemetrycznych wysyłanych z zestawu SDK w aplikacji ASP.NET/ASP.NET Core oraz z Azure Functions. Jest to domyślne próbkowanie przy użyciu zestawu SDK ASP.NET lub ASP.NET Core. Próbkowanie adaptacyjne jest obecnie dostępne tylko dla telemetrii po stronie serwera ASP.NET i dla Azure Functions.

* **Próbkowanie o stałej szybkości** zmniejsza ilość danych telemetrycznych wysyłanych z serwera ASP.NET lub ASP.NET Core lub Java oraz z przeglądarek użytkowników. Należy ustawić stawkę. Klient i serwer zsynchronizują swoje próbkowanie, aby w programie Search można było nawigować między widokami i żądaniami stron pokrewnych.

* **Pobieranie próbek** odbywa się w punkcie końcowym usługi Application Insights. Odrzuci niektóre dane telemetryczne, które nadeszły z Twojej aplikacji, przy ustawionej częstotliwości próbkowania. Nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, ale ułatwia utrzymywanie się w ramach miesięcznego limitu przydziału. Główną zaletą pobierania próbek jest możliwość ustawienia częstotliwości próbkowania bez ponownego wdrażania aplikacji. Próbkowanie do pozyskiwania działa jednolicie dla wszystkich serwerów i klientów, ale nie ma zastosowania, gdy inne typy próbkowania nie są obsługiwane.

> [!IMPORTANT]
> Jeśli metody próbkowania lub naprawionej stawki są w działaniu, pobieranie próbek jest wyłączone.

## <a name="adaptive-sampling"></a>Adaptacyjne próbkowanie

Próbkowanie adaptacyjne wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera sieci Web do punktu końcowego usługi Application Insights.

> [!TIP]
> Próbkowanie adaptacyjne jest domyślnie włączone w przypadku korzystania z zestawu SDK ASP.NET lub zestawu SDK ASP.NET Core i jest również domyślnie włączone dla Azure Functions.

Wolumin jest dostosowywany automatycznie, aby zachować ruch w określonym maksymalnym natężeniu ruchu, i jest kontrolowany za pośrednictwem ustawienia `MaxTelemetryItemsPerSecond` . Jeśli aplikacja tworzy niską ilość danych telemetrycznych, na przykład w przypadku debugowania lub z powodu niskiego użycia, elementy nie zostaną porzucone przez procesor próbkowania, o ile ilość jest mniejsza niż poniżej `MaxTelemetryItemsPerSecond` . W miarę wzrostu ilości danych telemetrycznych częstotliwość próbkowania jest dostosowywana do osiągnięcia woluminu docelowego. Korekta jest przeliczana w regularnych odstępach czasu i opiera się na średniej liczbie wychodzącej szybkości transmisji.

Aby osiągnąć wolumin docelowy, niektóre z wygenerowanych danych telemetrycznych są odrzucane. Podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas przeprowadzania inspekcji danych telemetrycznych w programie Search będziesz mieć możliwość znalezienia żądania związanego z określonym wyjątkiem.

Liczby metryk, takie jak częstotliwość żądań i częstotliwość wyjątków, są dostosowywane w celu zrekompensowania współczynnika próbkowania, dzięki czemu pokazują one w Eksploratorze metryk w mniej prawidłowych wartościach.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET

> [!NOTE]
> Ta sekcja dotyczy aplikacji ASP.NET, a nie ASP.NET Core aplikacji. [Dowiedz się więcej o konfigurowaniu próbkowania adaptacyjnego dla aplikacji ASP.NET Core w dalszej części tego dokumentu.](#configuring-adaptive-sampling-for-aspnet-core-applications)

W programie [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) można dostosować kilka parametrów w `AdaptiveSamplingTelemetryProcessor` węźle. Wyświetlane wartości są wartościami domyślnymi:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Docelowa szybkość [operacji logicznych](./correlation.md#data-model-for-telemetry-correlation) , którą algorytm adaptacyjny ma zbierać **na każdym hoście serwera**. Jeśli aplikacja sieci Web jest uruchamiana na wielu hostach, Zmniejsz tę wartość tak, aby pozostawała w docelowym wskaźniku ruchu w portalu Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał, w którym bieżąca stawka danych telemetrycznych jest przeszacowana. Obliczanie jest wykonywane jako średnia przenoszona. Możesz chcieć skrócić ten interwał, jeśli dane telemetryczne są odpowiedzialne za nagłe rozerwania.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    W przypadku zmiany wartości procentowej próbkowania, jak wkrótce będzie można obniżyć procent próbkowania w celu przechwycenia mniejszej ilości danych?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    W przypadku zmiany wartości procentowej próbkowania, jak wkrótce będzie możliwe zwiększenie wartości procentowej próbkowania w celu przechwycenia większej ilości danych?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Czy w miarę jak procent próbkowania różni się, jaka jest minimalna wartość, którą można ustawić?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jak procent próbkowania różni się, jaka jest maksymalna wartość, którą można ustawić?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    W obliczeniach średniej przenoszonej określa ona wagę, która ma zostać przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej od 1. Mniejsze wartości sprawiają, że algorytm jest mniej aktywny w nagłych zmianach.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Ilość danych telemetrycznych do próbkowania, gdy aplikacja została właśnie uruchomiona. Nie należy zmniejszać tej wartości podczas debugowania.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Rozdzielana średnikami lista typów, które nie mają być objęte próbką. Rozpoznawane typy to: `Dependency` ,,,, `Event` `Exception` `PageView` `Request` , `Trace` . Wszystkie dane telemetryczne określonych typów są przesyłane; typy, które nie są określone, będą próbkowane.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być objęte próbką. Rozpoznawane typy to: `Dependency` ,,,, `Event` `Exception` `PageView` `Request` , `Trace` . Określone typy będą próbkowane; wszystkie dane telemetryczne innych typów będą zawsze przesyłane.

**Aby wyłączyć** próbkowanie adaptacyjne, Usuń `AdaptiveSamplingTelemetryProcessor` węzły z `ApplicationInsights.config` .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie próbkowania adaptacyjnego w kodzie

Zamiast ustawiać parametr próbkowania w `.config` pliku, można programowo ustawić te wartości.

1. Usuń wszystkie `AdaptiveSamplingTelemetryProcessor` węzły z `.config` pliku.
2. Skorzystaj z następującego fragmentu kodu, aby skonfigurować adaptacyjne próbkowanie:

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

    ([Dowiedz się więcej o procesorach telemetrii](./api-filtering-sampling.md#filtering)).

Możesz również dostosować częstotliwość próbkowania osobno dla każdego typu telemetrii lub nawet wykluczyć określone typy z próbkowania:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET Core

Nie ma `ApplicationInsights.config` dla aplikacji ASP.NET Core, więc cała konfiguracja odbywa się za pośrednictwem kodu.
Próbkowanie adaptacyjne jest domyślnie włączone dla wszystkich aplikacji ASP.NET Core. Można wyłączyć lub dostosować zachowanie pobierania próbek.

#### <a name="turning-off-adaptive-sampling"></a>Wyłączanie próbkowania adaptacyjnego

Domyślną funkcję próbkowania można wyłączyć podczas dodawania usługi Application Insights, w metodzie `ConfigureServices` , przy użyciu `ApplicationInsightsServiceOptions` `Startup.cs` pliku:

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

Powyższy kod spowoduje wyłączenie próbkowania adaptacyjnego. Wykonaj poniższe kroki, aby dodać próbkowanie z większą liczbą opcji dostosowywania.

#### <a name="configure-sampling-settings"></a>Konfigurowanie ustawień próbkowania

Użyj metod rozszerzających `TelemetryProcessorChainBuilder` , jak pokazano poniżej, aby dostosować zachowanie próbkowania.

> [!IMPORTANT]
> W przypadku korzystania z tej metody w celu skonfigurowania próbkowania upewnij się, że właściwość jest ustawiona `aiOptions.EnableAdaptiveSampling` na wartość `false` podczas wywoływania `AddApplicationInsightsTelemetry()` .

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

Postępuj zgodnie z instrukcjami wyświetlanymi na [tej stronie](../../azure-functions/functions-monitoring.md#configure-sampling) , aby skonfigurować próbkowanie adaptacyjne dla aplikacji działających w Azure Functions.

## <a name="fixed-rate-sampling"></a>Próbkowanie ustalonej stawki

Próbkowanie o stałej szybkości zmniejsza ruch wysyłany z serwera sieci Web i przeglądarek sieci Web. W przeciwieństwie do próbkowania adaptacyjnego, zmniejsza ona dane telemetryczne według stałej stawki ustalonej przez użytkownika. Próbkowanie o ustalonej szybkości jest dostępne dla aplikacji ASP.NET, ASP.NET Core, Java i Python.

Podobnie jak w przypadku innych technik próbkowania, zachowuje także powiązane elementy. Synchronizuje także próbkowanie klienta i serwera, aby powiązane elementy były zachowywane — na przykład podczas przeglądania widoku strony w usłudze Search można znaleźć powiązane z nim żądania serwera. 

W Eksplorator metryk stawki, takie jak żądania i liczby wyjątków, są mnożone przez współczynnik, aby zrekompensować częstotliwość próbkowania, dzięki czemu są one mniej poprawne.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji ASP.NET

1. **Wyłącz próbkowanie adaptacyjne**: w [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) , Usuń lub Dodaj komentarz do `AdaptiveSamplingTelemetryProcessor` węzła.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Włącz moduł próbkowania stałego.** Dodaj następujący fragment kodu do [`ApplicationInsights.config`](./configuration-with-applicationinsights-config.md) :
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Alternatywnie zamiast ustawiania parametru próbkowania w `ApplicationInsights.config` pliku można programowo ustawić następujące wartości:

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

    ([Dowiedz się więcej o procesorach telemetrii](./api-filtering-sampling.md#filtering)).

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji ASP.NET Core

1. **Wyłącz próbkowanie adaptacyjne**: zmiany można wprowadzać w `ConfigureServices` metodzie przy użyciu `ApplicationInsightsServiceOptions` :

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

2. **Włącz moduł próbkowania stałego.** Zmiany można wprowadzać w `Configure` metodzie, jak pokazano w poniższym fragmencie kodu:

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

Domyślnie żadne próbkowanie nie jest włączone w agencie Java i zestawie SDK. Obecnie obsługuje ona wyłącznie próbkowanie o stałej stawce. Próbkowanie adaptacyjne nie jest obsługiwane w języku Java.

#### <a name="configuring-java-agent"></a>Konfigurowanie agenta Java

1. Pobierz [ApplicationInsights-Agent-3.0.0-Preview. 5. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.5/applicationinsights-agent-3.0.0-PREVIEW.5.jar)

1. Aby włączyć próbkowanie, Dodaj następujący `ApplicationInsights.json` plik do pliku:

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

#### <a name="configuring-java-sdk"></a>Konfigurowanie zestawu Java SDK

1. Pobierz i skonfiguruj aplikację sieci Web przy użyciu najnowszego [Application Insights Java SDK](./java-get-started.md).

2. **Włącz moduł próbkowania ustalonego** przez dodanie następującego fragmentu kodu do `ApplicationInsights.xml` pliku:

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

3. Można dołączać lub wykluczać określone typy danych telemetrycznych z próbkowania przy użyciu następujących tagów w `Processor` tagu `FixedRateSamplingTelemetryProcessor` :
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

Typy telemetrii, które mogą być dołączone lub wykluczone z próbkowania, to:,,,, `Dependency` `Event` `Exception` `PageView` `Request` i `Trace` .

> [!NOTE]
> W polu procent próbkowania wybierz wartość procentową zbliżoną do 100/N, gdzie N jest liczbą całkowitą.  Obecnie próbkowanie nie obsługuje innych wartości.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurowanie próbkowania o stałej szybkości dla aplikacji OpenCensus Python

Instrumentacja aplikacji przy użyciu najnowszych [OpenCensusch eksportujących Azure monitor](./opencensus-python.md).

> [!NOTE]
> Próbkowanie ustalonej stawki nie jest dostępne dla eksportera metryk. Oznacza to, że metryki niestandardowe są jedynymi typami danych telemetrycznych, w przypadku których nie można skonfigurować pobierania próbek. Eksporter metryk wyśle wszystkie dane telemetryczne, które śledzi.

#### <a name="fixed-rate-sampling-for-tracing"></a>Próbkowanie z ustaloną szybkością dla śledzenia ####
Możesz określić element `sampler` w ramach konfiguracji elementu `Tracer`. Jeśli nie zostanie podany jawny próbnik, `ProbabilitySampler` będzie on używany domyślnie. `ProbabilitySampler`Użycie stawki 1/10000 będzie miało wartość domyślną, co oznacza, że jeden z żądań 10000 zostanie wysłany do Application Insights. Jeśli chcesz określić częstotliwość próbkowania, zobacz instrukcje poniżej.

Aby określić częstotliwość próbkowania, należy się upewnić, że `Tracer` określa próbkowanie z częstotliwością próbkowania z zakresu od 0,0 do 1,0 włącznie. Częstotliwość próbkowania 1,0 reprezentuje 100%, co oznacza, że wszystkie żądania będą wysyłane jako dane telemetryczne do Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Próbkowanie z ustaloną szybkością dla dzienników ####
Można skonfigurować próbkowanie o stałej szybkości `AzureLogHandler` przez zmodyfikowanie `logging_sampling_rate` opcjonalnego argumentu. Jeśli żaden argument nie zostanie podany, zostanie użyta częstotliwość próbkowania 1,0. Częstotliwość próbkowania 1,0 reprezentuje 100%, co oznacza, że wszystkie żądania będą wysyłane jako dane telemetryczne do Application Insights.

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurowanie stałego próbkowania dla stron sieci Web za pomocą języka JavaScript

Strony sieci Web oparte na języku JavaScript można skonfigurować do korzystania z Application Insights. Dane telemetryczne są wysyłane z aplikacji klienckiej uruchomionej w przeglądarce użytkownika, a strony mogą być hostowane na dowolnym serwerze.

Podczas [konfigurowania stron sieci Web opartych na języku JavaScript dla Application Insights](javascript.md)należy zmodyfikować fragment kodu JavaScript uzyskany w portalu Application Insights.

> [!TIP]
> W aplikacjach ASP.NET z obsługą języka JavaScript, fragment kodu zwykle znajduje się w `_Layout.cshtml` .

Wstaw wiersz, taki jak `samplingPercentage: 10,` przed kluczem Instrumentacji:

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

W polu procent próbkowania wybierz wartość procentową zbliżoną do 100/N, gdzie N jest liczbą całkowitą. Obecnie próbkowanie nie obsługuje innych wartości.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Koordynowanie próbkowania po stronie serwera i klienta

ZESTAW JavaScript po stronie klienta ma udział w próbkach o stałej szybkości w połączeniu z zestawem SDK po stronie serwera. Strony z instrumentami będą wysyłać dane telemetryczne po stronie klienta z tego samego użytkownika, dla którego zestaw SDK po stronie serwera wykonał decyzję o uwzględnieniu w próbie. Ta logika została zaprojektowana tak, aby zachować integralność sesji użytkowników między aplikacjami klienta i serwera. W związku z tym z dowolnego określonego elementu telemetrii w Application Insights można znaleźć wszystkie inne elementy telemetrii dla tego użytkownika lub sesji oraz w obszarze wyszukiwania, można nawigować między widokami i żądaniami stron pokrewnych.

Jeśli dane telemetryczne po stronie klienta i serwera nie są wyświetlane jako skoordynowane przykłady:

* Sprawdź, czy włączono próbkowanie zarówno na serwerze, jak i na komputerze klienckim.
* Upewnij się, że ustawiono ten sam procent próbkowania zarówno na kliencie, jak i na serwerze.
* Upewnij się, że wersja zestawu SDK to 2,0 lub nowsza.

## <a name="ingestion-sampling"></a>Pobieranie próbek

Próbkowanie do pozyskiwania działa w punkcie, w którym dane telemetryczne z serwera sieci Web, przeglądarek i urządzeń docierają do punktu końcowego usługi Application Insights. Chociaż nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, zmniejsza ilość przetworzonych i zachowywanych (i naliczanych przez program) przez Application Insights.

Użyj tego typu próbkowania, jeśli aplikacja często przechodzi przez swój miesięczny limit przydziału i nie masz możliwości użycia jednego z typów próbkowania opartych na zestawie SDK. 

Ustaw częstotliwość próbkowania na stronie użycie i szacowane koszty:

![W bloku przegląd aplikacji kliknij pozycję Ustawienia, przydziały, próbki, a następnie wybierz częstotliwość próbkowania, a następnie kliknij pozycję Aktualizuj.](./media/sampling/data-sampling.png)

Podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas przeprowadzania inspekcji danych telemetrycznych w programie Search będziesz mieć możliwość znalezienia żądania związanego z określonym wyjątkiem. Liczby metryk, takie jak częstotliwość żądań i częstotliwość wyjątków, są prawidłowo zachowywane.

Punkty danych, które są odrzucane przez próbkowanie, nie są dostępne w żadnej funkcji Application Insights, takiej jak [eksport ciągły](./export-telemetry.md).

Próbkowanie do pozyskiwania nie działa, gdy jest wykonywane próbkowanie adaptacyjne lub z ustaloną szybkością. Próbkowanie adaptacyjne jest domyślnie włączone w przypadku korzystania z zestawu SDK ASP.NET lub zestawu SDK ASP.NET Core lub gdy Application Insights jest włączona w [Azure App Service ](azure-web-apps.md) lub przy użyciu Monitor stanu. Gdy dane telemetryczne są odbierane przez punkt końcowy usługi Application Insights, badają dane telemetryczne i jeśli częstotliwość próbkowania jest raportowana jako mniejsza niż 100% (co oznacza, że dane telemetryczne są próbkowane), a następnie wybrana częstotliwość próbkowania pozyskiwania jest ignorowana.

> [!WARNING]
> Wartość wyświetlana na kafelku portalu wskazuje wartość ustawioną dla pobierania próbek. Nie reprezentuje rzeczywistej częstotliwości próbkowania w przypadku, gdy jest wykonywane jakiekolwiek Sortowanie próbek zestawu SDK (próbkowanie adaptacyjne lub stałe).

## <a name="when-to-use-sampling"></a>Kiedy należy używać próbkowania

Ogólnie rzecz biorąc, w przypadku aplikacji o małych i średnich rozmiarach nie jest wymagane próbkowanie. Najbardziej przydatne informacje diagnostyczne i najbardziej dokładne statystyki są uzyskiwane przez zbieranie danych na wszystkich działaniach użytkownika. 

Główne zalety pobierania próbek są następujące:

* Usługa Application Insights odrzuca punkty danych ("dławienia"), gdy aplikacja wysyła bardzo wysokie dane telemetryczne w krótkim czasie. Próbkowanie zmniejsza prawdopodobieństwo wystąpienia ograniczenia przez aplikację.
* Aby zachować [limit przydziału](pricing.md) punktów danych w warstwie cenowej. 
* Aby zmniejszyć ruch sieciowy z kolekcji telemetrii. 

### <a name="which-type-of-sampling-should-i-use"></a>Jakiego typu próbkowanie należy użyć?

**Użyj próbkowania pozyskiwania, jeśli:**

* Często używasz miesięcznego przydziału danych telemetrycznych.
* Używasz zbyt dużej ilości danych telemetrycznych z przeglądarek sieci Web użytkowników.
* Używasz wersji zestawu SDK, która nie obsługuje próbkowania — na przykład ASP.NET wersje wcześniejsze niż 2.

**Użyj próbkowania stałego, jeśli:**

* Należy synchronizować próbkowanie między klientem a serwerem, aby podczas badania zdarzeń w usłudze [Search](./diagnostic-search.md)można przechodzić między powiązanymi zdarzeniami na kliencie i serwerze, takimi jak widoki stron i żądania HTTP.
* Masz pewność, że dla aplikacji jest pobierana odpowiednia wartość procentowa. Powinien być wystarczająco duży, aby uzyskać dokładne metryki, ale poniżej stawki, która przekracza limit przydziału cen i limity ograniczania.

**Użyj próbkowania adaptacyjnego:**

Jeśli warunki używania innych form próbkowania nie mają zastosowania, zalecamy próbkowanie adaptacyjne. To ustawienie jest domyślnie włączone w ASP.NET/ASP.NET Core SDK. Nie zmniejszy to ruchu, dopóki nie zostanie osiągnięta określona stawka minimalna, w związku z czym w przypadku lokacji z niskim użyciem prawdopodobnie nie będą one próbkowane.

## <a name="knowing-whether-sampling-is-in-operation"></a>Jak sprawdzić, czy próbkowanie jest w toku

Aby wykryć rzeczywistą częstotliwość próbkowania niezależnie od tego, gdzie została ona zastosowana, należy użyć [zapytania analitycznego](../log-query/log-query-overview.md) , takiego jak:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Jeśli widzisz, że `RetainedPercentage` dla dowolnego typu jest mniejsza niż 100, ten typ telemetrii jest próbkowany.

> [!IMPORTANT]
> Application Insights nie przykłada sesji, metryk (w tym metryk niestandardowych) ani typów telemetrii licznika wydajności w żadnej z technik próbkowania. Te typy są zawsze wyłączone z próbkowania, ponieważ zmniejszenie dokładności może być wysoce niepożądane dla tych typów telemetrii.

## <a name="how-sampling-works"></a>Jak działa próbkowanie

Algorytm próbkowania decyduje o tym, które elementy telemetrii mają być porzucane i które mają być zachowane. Jest to prawdziwe, czy próbkowanie jest wykonywane przez zestaw SDK, czy też w usłudze Application Insights. Decyzja o próbkowaniu jest oparta na kilku regułach, które mają na celu zachowanie wszystkich powiązanych punktów danych bez zmian, dzięki objęciu diagnostyki w Application Insights, które są bardziej funkcjonalne i niezawodne nawet przy użyciu zredukowanego zestawu danych. Jeśli na przykład aplikacja zawiera żądanie zakończone niepowodzeniem, w przykładzie zostaną zachowane dodatkowe elementy telemetrii (takie jak wyjątek i ślady zarejestrowane dla tego żądania). Próbkowanie zachowuje lub porzuca wszystkie jednocześnie. W związku z tym, gdy zobaczysz szczegóły żądania w Application Insights, możesz zawsze zobaczyć żądanie wraz ze skojarzonymi z nim elementami telemetrii.

Decyzja o próbkowaniu jest określana na podstawie identyfikatora operacji żądania, co oznacza, że wszystkie elementy telemetrii należące do określonej operacji są zachowywane lub porzucane. W przypadku elementów telemetrycznych, które nie mają ustawionego identyfikatora operacji (takich jak elementy telemetrii zgłaszane z wątków asynchronicznych bez kontekstu HTTP), po prostu przechwytuje procent elementów telemetrii każdego typu.

Podczas przedstawiania danych telemetrycznych z powrotem do Ciebie Usługa Application Insights dostosowuje metryki o ten sam procent próbkowania, który był używany w czasie zbierania, aby skompensować brakujące punkty danych. W związku z tym podczas wyszukiwania danych telemetrycznych w Application Insights użytkownicy widzą statystycznie prawidłowe przybliżenia, które są blisko liczb rzeczywistych.

Dokładność przybliżania zależy od skonfigurowanej wartości procentowej próbkowania. Ponadto dokładność zwiększa się w przypadku aplikacji, które obsługują dużą liczbę zwykle podobnych żądań od wielu użytkowników. Z drugiej strony, w przypadku aplikacji, które nie współpracują z znaczącym obciążeniem, pobieranie próbek nie jest potrzebne, ponieważ te aplikacje zazwyczaj mogą wysyłać wszystkie dane telemetryczne w ramach limitu przydziału bez powodowania utraty danych. 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*Co to jest domyślne zachowanie podczas próbkowania w zestawach SDK ASP.NET i ASP.NET Core?*

* Jeśli używasz jednej z najnowszych wersji powyższego zestawu SDK, adaptacyjne próbkowanie jest domyślnie włączone z pięciu elementów telemetrii na sekundę.
  `AdaptiveSamplingTelemetryProcessor`Domyślnie są dodawane dwa węzły, a jeden zawiera `Event` Typ podczas próbkowania, podczas gdy inne wyklucza `Event` Typ z próbkowania. Ta konfiguracja oznacza, że zestaw SDK spróbuje ograniczyć liczbę elementów telemetrycznych do pięciu elementów telemetrii `Event` typów i pięć elementów telemetrii wszystkich innych typów połączonych, dzięki czemu `Events` są one próbkowane niezależnie od innych typów telemetrii. Zdarzenia są zwykle używane w przypadku telemetrii biznesowej i najprawdopodobniej nie powinny mieć wpływ na woluminy telemetrii diagnostyki.
  
  Poniżej przedstawiono domyślny `ApplicationInsights.config` wygenerowany plik. W ASP.NET Core to samo zachowanie domyślne jest włączone w kodzie. Użyj [przykładów z wcześniejszej części tej strony](#configuring-adaptive-sampling-for-aspnet-core-applications) , aby zmienić to zachowanie domyślne.

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

*Czy dane telemetryczne mogą być próbkowane więcej niż raz?*

* Nie. SamplingTelemetryProcessors Ignoruj elementy z przykładów próbkowania, jeśli element jest już próbkowany. To samo jest prawdziwe w przypadku pobierania próbek, które nie zastosują próbkowania do tych elementów, które są już próbkowane w samym zestawie SDK.

*Dlaczego próbkowanie jest proste "zbieranie X procent każdego typu telemetrii"?*

* Chociaż takie podejście do próbkowania zapewni wysoki poziom precyzji w przybliżeniu pomiarowym, może to spowodować uszkodzenie danych diagnostycznych dla poszczególnych użytkowników, sesji i żądań, co jest niezwykle ważne dla celów diagnostycznych. W związku z tym próbkowanie działa lepiej dzięki zasadom, takim jak "Zbierz wszystkie elementy telemetrii dla X procent użytkowników aplikacji", lub "Zbierz wszystkie dane telemetryczne dla X procent żądań aplikacji". W przypadku elementów telemetrycznych, które nie są skojarzone z żądaniami (na przykład asynchroniczne przetwarzanie w tle), rezerwa jest wartość "Zbierz X procent wszystkich elementów dla każdego typu telemetrii". 

*Czy wartość procentowa próbkowania może ulec zmianie z upływem czasu?*

* Tak, próbkowanie adaptacyjne stopniowo zmienia procent próbkowania na podstawie aktualnie zaobserwowanej ilości danych telemetrycznych.

*Jeśli korzystam z próbkowania o stałej szybkości, skąd mam wiedzieć, który procent próbkowania będzie działał najlepiej dla mojej aplikacji?*

* Jednym ze sposobów jest rozpoczęcie od adaptacyjnego próbkowania, Dowiedz się, jaka stawka jest naliczana (patrz powyższe pytanie), a następnie przejdź do próbkowania ustalonego przy użyciu tej stawki. 
  
    W przeciwnym razie musisz się przygadnąć. Analizuj bieżące użycie telemetrii w Application Insights, obserwuj wszystkie występujące ograniczenia przepustowości i Szacuj wolumen zebranych danych telemetrycznych. Te trzy dane wejściowe wraz z wybraną warstwą cenową sugerują, jak dużo można zmniejszyć wolumin zbieranych danych telemetrycznych. Jednak zwiększenie liczby użytkowników lub innych zmian w ilości danych telemetrycznych może spowodować unieważnienie oszacowania.

*Co się stanie, jeśli procent próbkowania zostanie skonfigurowany tak, aby był zbyt niski?*

* Nadmiernie niskie wartości procentowe próbkowania powodują zbyt intensywne próbkowanie i zmniejszają dokładność przybliżania, gdy Application Insights próbuje kompensować wizualizację danych w celu zmniejszenia ilości danych. Ponadto może to mieć negatywny wpływ na środowisko diagnostyczne, ponieważ niektóre z nietypowych lub powolnych żądań mogą być próbkowane.

*Co się stanie, jeśli procent próbkowania zostanie skonfigurowany jako zbyt wysoki?*

* Konfiguracja zbyt dużej wartości procentowej próbkowania (nie agresywnie wystarczającej) powoduje niewystarczającą redukcję woluminu zebranej telemetrii. Nadal może wystąpić utrata danych telemetrycznych związana z ograniczeniami, a koszt korzystania z Application Insights może być wyższy niż planowany ze względu na opłaty za nadwyżkowe użycie.

*Na jakich platformach można używać próbkowania?*

* Próbkowanie do pozyskiwania może odbywać się automatycznie dla każdej telemetrii powyżej określonego woluminu, jeśli zestaw SDK nie wykonuje próbkowania. Ta konfiguracja będzie działała, na przykład jeśli używasz starszej wersji zestawu SDK ASP.NET lub zestawu Java SDK.
* Jeśli używasz bieżących ASP.NET lub zestawów SDK ASP.NET Core (hostowanych na platformie Azure lub na własnym serwerze), domyślnie otrzymujesz próbkowanie adaptacyjne, ale możesz przełączyć się na stałą stawkę, jak opisano powyżej. W przypadku próbkowania o stałej szybkości zestaw SDK przeglądarki automatycznie synchronizuje się z przykładowymi zdarzeniami powiązanymi. 
* Jeśli używasz bieżącego agenta Java, możesz skonfigurować program `ApplicationInsights.json` (dla zestawu Java SDK, skonfigurować), `ApplicationInsights.xml` Aby włączyć próbkowanie o stałym tempie. Próbkowanie jest domyślnie wyłączone. W przypadku próbkowania o stałej szybkości zestaw SDK i serwer przeglądarki są automatycznie synchronizowane z przykładowymi zdarzeniami powiązanymi.

*Istnieją pewne rzadkie zdarzenia, które zawsze chcę zobaczyć. Jak mogę uzyskać dostęp do modułu próbkowania?*

* Najlepszym sposobem osiągnięcia tego celu jest napisanie niestandardowych [TelemetryInitializer](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), które ustawia wartość `SamplingPercentage` na 100 dla elementu telemetrii, który ma zostać zachowany, jak pokazano poniżej. Ponieważ inicjatory są gwarantowane do uruchomienia przed procesorami danych telemetrycznych (w tym próbkowanie), zapewnia to, że wszystkie techniki próbkowania zignorują ten element z wszelkich zagadnień związanych z próbkami. Niestandardowe inicjatory telemetrii są dostępne w zestawie SDK ASP.NET, zestaw SDK ASP.NET Core, zestaw SDK języka JavaScript i zestaw SDK języka Java. Na przykład można skonfigurować inicjatora telemetrii przy użyciu zestawu SDK ASP.NET:

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

Próbkowanie adaptacyjne jest dostępne dla Application Insights SDK dla ASP.NET v 2.0.0-beta3 i nowszych, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 i nowszych, i jest domyślnie włączone.

Próbkowanie z ustaloną szybkością jest funkcją zestawu SDK w wersjach ASP.NET z 2.0.0 i Java SDK w wersji 2.0.1 i nowszej.

Przed v 2.5.0 beta2 ASP.NET SDK i v 2.2.0-beta3 zestawu SDK ASP.NET Core, decyzja o próbkowaniu była oparta na skrócie identyfikatora użytkownika dla aplikacji, które definiują "użytkownika" (to znaczy najpopularniejsze aplikacje sieci Web). W przypadku typów aplikacji, które nie definiują użytkowników (takich jak usługi sieci Web), decyzja o próbkowaniu była oparta na IDENTYFIKATORze operacji żądania. Najnowsze wersje zestawów SDK ASP.NET i ASP.NET Core używają identyfikatora operacji dla decyzji o próbie.

## <a name="next-steps"></a>Następne kroki

* [Filtrowanie](./api-filtering-sampling.md) może zapewnić bardziej rygorystyczną kontrolę nad tym, co wysyła zestaw SDK.
* Zapoznaj się z artykułem dotyczącym sieci deweloperów [Optymalizacja danych telemetrycznych z Application Insights](/archive/msdn-magazine/2017/may/devops-optimize-telemetry-with-application-insights).

