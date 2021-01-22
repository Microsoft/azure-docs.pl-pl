---
title: Diagnozowanie za pomocą Live Metrics Stream platformy Azure Application Insights
description: Monitoruj swoją aplikację sieci Web w czasie rzeczywistym za pomocą metryk niestandardowych i Diagnozuj problemy z dynamicznym źródłem błędów, śladów i zdarzeń.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679470"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorowanie & diagnozowanie przy użyciu 1-sekundowego opóźnienia

Monitoruj działającą w środowisku produkcyjną aplikację sieci Web przy użyciu Live Metrics Stream (znanego również jako QuickPulse) z [Application Insights](./app-insights-overview.md). Wybieranie i filtrowanie metryk i liczników wydajności w czasie rzeczywistym, bez żadnych zakłóceń usługi. Sprawdzanie śladów stosu z przykładowych nieudanych żądań i wyjątków. Wraz z narzędziem [Profiler](./profiler.md) i [debuger migawek](./snapshot-debugger.md)program Live Metrics Stream zapewnia zaawansowane i nieinwazyjne narzędzie diagnostyczne dla działającej witryny sieci Web.

Za pomocą Live Metrics Stream można:

* Sprawdź poprawność poprawki, gdy zostanie ona wydana, obserwując liczniki wydajności i niepowodzeń.
* Obserwuj efekt ładowania testów i Diagnozuj problemy na żywo.
* Skup się na konkretnych sesjach testowych lub odfiltruj znane problemy, wybierając i filtrując metryki, które mają być obserwowane.
* Pobierz ślady wyjątków w miarę ich występowania.
* Eksperymentuj z filtrami, aby znaleźć najbardziej odpowiednie wskaźniki KPI.
* Monitoruj dowolny licznik wydajności systemu Windows na żywo.
* Łatwo Zidentyfikuj serwer, który ma problemy, i przefiltruj wszystkie wskaźniki KPI/kanał Aktualności na tylko ten serwer.

![Karta metryki na żywo](./media/live-stream/live-metric.png)

Metryki na żywo są obecnie obsługiwane w aplikacjach ASP.NET, ASP.NET Core, Azure Functions, Java i Node.js.

## <a name="get-started"></a>Rozpoczęcie pracy

1. Aby włączyć metryki na żywo, należy przestrzegać wytycznych dotyczących języka.
   * Metryki na żywo [ASP.NET](./asp-net.md) są domyślnie włączone.
   * Metryki na żywo [ASP.NET Core](./asp-net-core.md)są domyślnie włączone.
   * Metryki dla [konsoli/procesu roboczego platformy .NET Core](./worker-service.md)są domyślnie włączone.
   * [Aplikacje .NET — Włączanie przy użyciu kodu](#enable-livemetrics-using-code-for-any-net-application).
    * Funkcja [Java](./java-in-process-agent.md) -Live Metrics jest domyślnie włączona.
   * [Node.js](./nodejs.md#live-metrics)

2. W [Azure Portal](https://portal.azure.com)otwórz zasób Application Insights dla aplikacji, a następnie otwórz Live Stream.

3. [Zabezpiecz kanał kontroli,](#secure-the-control-channel) Jeśli możesz użyć poufnych danych, takich jak nazwy klientów w filtrach.

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>Włącz LiveMetrics przy użyciu kodu dla dowolnej aplikacji platformy .NET

Mimo że usługa LiveMetrics jest domyślnie włączona podczas dołączania przy użyciu zalecanych instrukcji dla aplikacji .NET, poniżej przedstawiono sposób ręcznego konfigurowania metryk na żywo.

1. Zainstaluj pakiet NuGet [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)
2. Poniższy przykładowy kod aplikacji konsoli pokazuje Konfigurowanie aktywnych metryk.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

Chociaż powyższy przykład dotyczy aplikacji konsolowej, ten sam kod może być używany w dowolnych aplikacjach .NET. Jeśli jakiekolwiek inne TelemetryModules są włączone, które automatycznej zbiera dane telemetryczne, ważne jest, aby upewnić się, że ta sama konfiguracja używana do inicjowania modułów jest również używana dla modułu metryki na żywo.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak Live Metrics Stream różnić się od Eksplorator metryk i analiz?

| |Transmisja strumieniowa na żywo | Eksplorator metryk i analiza |
|---|---|---|
|**Opóźnienie**|Dane wyświetlane w jednej sekundzie|Zagregowane w ciągu minut|
|**Brak przechowywania**|Dane są przechowywane na wykresie, a następnie są odrzucane|[Dane przechowywane przez 90 dni](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**Na żądanie**|Dane są przesyłane strumieniowo, gdy okienko metryki na żywo jest otwarte |Dane są wysyłane za każdym razem, gdy zestaw SDK jest zainstalowany i włączony|
|**Bezpłatna**|Za dane Live Stream nie są naliczane opłaty|Podlega [cennikowi](./pricing.md)
|**Próbkowanie**|Wszystkie wybrane metryki i liczniki są przesyłane. Błędy i ślady stosu są próbkowane. |Zdarzenia mogą być [próbkowane](./api-filtering-sampling.md)|
|**Kanał kontrolny**|Sygnały kontroli filtru są wysyłane do zestawu SDK. Zalecamy zabezpieczenie tego kanału.|Komunikacja jest jednym ze sposobów, w portalu|

## <a name="select-and-filter-your-metrics"></a>Wybieranie i filtrowanie metryk

(Dostępne w ASP.NET, ASP.NET Core i Azure Functions (v2).)

Możesz monitorować niestandardowy kluczowy wskaźnik wydajności, stosując dowolne filtry dla dowolnej Application Insights telemetrii w portalu. Kliknij kontrolkę filtr, która jest wyświetlana po umieszczeniu wskaźnika myszy na dowolnym z wykresów. Poniższy wykres przedstawia niestandardowy wskaźnik KPI liczby żądań z filtrami dla atrybutów adresu URL i czasu trwania. Sprawdź poprawność filtrów za pomocą sekcji Podgląd strumienia, która zawiera dynamiczne źródło danych telemetrycznych spełniające kryteria określone w dowolnym momencie.

![Częstotliwość żądań filtrowania](./media/live-stream/filter-request.png)

Można monitorować wartość różną od Count. Opcje są zależne od typu strumienia, który może być dowolną Application Insights telemetrii: żądania, zależności, wyjątki, ślady, zdarzenia lub metryki. Może to być własny [niestandardowy pomiar](./api-custom-events-metrics.md#properties):

![Konstruktor zapytań dla częstotliwości żądań z metryką niestandardową](./media/live-stream/query-builder-request.png)

Oprócz Application Insights telemetrii można także monitorować dowolny licznik wydajności systemu Windows, wybierając go z opcji strumienia i podając nazwę licznika wydajności.

Metryki na żywo są agregowane w dwóch punktach: lokalnie na każdym serwerze, a następnie na wszystkich serwerach. Wartość domyślną można zmienić, wybierając opcję inne opcje z odpowiednich list rozwijanych.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Przykładowa Telemetria: niestandardowe zdarzenia diagnostyczne na żywo
Domyślnie dynamiczne źródło zdarzeń zawiera przykłady żądań zakończonych niepowodzeniem oraz wywołania zależności, wyjątki, zdarzenia i ślady. Kliknij ikonę filtru, aby wyświetlić zastosowane kryteria w dowolnym momencie.

![Przycisk filtrowania](./media/live-stream/filter.png)

Podobnie jak w przypadku metryk, można określić dowolne dowolne kryterium dla dowolnych typów Application Insights telemetrii. W tym przykładzie wybieramy konkretne błędy żądań i zdarzenia.

![Konstruktor zapytań](./media/live-stream/query-builder.png)

> [!NOTE]
> Obecnie w przypadku kryteriów opartych na komunikatach o wyjątkach należy użyć najbardziej zewnętrznego komunikatu o wyjątku. W poprzednim przykładzie, aby odfiltrować wyjątek niegroźny z wewnętrznym komunikatem wyjątku (następuje "<--" ogranicznik) "klient został odłączony". Użyj komunikatu niezawierającego kryterium "błąd podczas odczytu zawartości żądania".

Zobacz szczegóły elementu w kanale dynamicznym, klikając go. Możesz wstrzymać kanał informacyjny przez kliknięcie przycisku **Wstrzymaj** lub po prostu przewinięcie lub kliknięcie elementu. Kanały informacyjne na żywo zostaną wznowione po przeprowadzeniu przewijania do góry lub przez kliknięcie licznika zebranych elementów podczas wstrzymania.

![Zrzut ekranu przedstawia okno przykładowej telemetrii z wybranym wyjątkem, a szczegóły wyjątku wyświetlane w dolnej części okna.](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtruj według wystąpienia serwera

Jeśli chcesz monitorować konkretne wystąpienie roli serwera, możesz filtrować według serwera. Aby filtrować, wybierz nazwę serwera w obszarze *serwery*.

![Próbkowane błędy na żywo](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Zabezpieczanie kanału kontroli

> [!NOTE]
> Obecnie można skonfigurować kanał uwierzytelniony tylko przy użyciu monitorowania opartego na kodzie i nie można uwierzytelnić serwerów przy użyciu dołączania bez kodu.

Kryteria filtrów niestandardowych określone w portalu metryk na żywo są wysyłane z powrotem do składnika metryki na żywo w zestawie Application Insights SDK. Filtry mogą potencjalnie zawierać informacje poufne, takie jak customerID. Kanał można zabezpieczyć przy użyciu klucza tajnego interfejsu API oprócz klucza Instrumentacji.

### <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API

![Klucz interfejsu API > Utwórz klucz interfejsu API ](./media/live-stream/api-key.png)
 ![ Utwórz klucz interfejsu API. Wybierz pozycję "Uwierzytelnij kanał kontroli zestawu SDK" i "Generuj klucz"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Dodaj klucz interfejsu API do konfiguracji

### <a name="aspnet"></a>ASP.NET

W pliku applicationinsights.config Dodaj AuthenticationApiKey do QuickPulseTelemetryModule:

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

W przypadku aplikacji [ASP.NET Core](./asp-net-core.md) postępuj zgodnie z poniższymi instrukcjami.

Zmodyfikuj `ConfigureServices` plik Startup.cs w następujący sposób:

Dodaj następującą przestrzeń nazw.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie zmodyfikuj `ConfigureServices` metodę jako poniżej.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

Więcej informacji na temat konfigurowania aplikacji ASP.NET Core można znaleźć w naszych wskazówkach dotyczących [konfigurowania modułów telemetrycznych w programie ASP.NET Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules).

### <a name="workerservice"></a>WorkerService

W przypadku aplikacji [WorkerService](./worker-service.md) postępuj zgodnie z poniższymi instrukcjami.

Dodaj następującą przestrzeń nazw.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie Dodaj następujący wiersz przed wywołaniem `services.AddApplicationInsightsTelemetryWorkerService` .

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Więcej informacji na temat konfigurowania aplikacji WorkerService można znaleźć w naszych wskazówkach dotyczących [konfigurowania modułów telemetrycznych w WorkerServices](./worker-service.md#configuring-or-removing-default-telemetrymodules).

### <a name="azure-function-apps"></a>Aplikacje funkcji platformy Azure

W przypadku aplikacji funkcji platformy Azure (v2) Zabezpieczanie kanału za pomocą klucza interfejsu API można wykonać przy użyciu zmiennej środowiskowej.

Utwórz klucz interfejsu API z poziomu zasobu Application Insights i przejdź do pozycji **ustawienia > konfiguracja** aplikacja funkcji. Wybierz pozycję **nowe ustawienie aplikacji** , wprowadź nazwę `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` i wartość odpowiadającą kluczowi interfejsu API.

Jeśli jednak rozpoznasz i ufasz wszystkim połączonym serwerom, możesz wypróbować filtry niestandardowe bez uwierzytelnionego kanału. Ta opcja jest dostępna przez sześć miesięcy. To przesłonięcie jest wymagane po każdej nowej sesji lub gdy nowy serwer przejdzie w tryb online.

![Opcje uwierzytelniania metryk na żywo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zalecamy skonfigurowanie kanału uwierzytelnionego przed wprowadzeniem potencjalnie poufnych informacji, takich jak CustomerID w kryteriach filtrowania.
>

## <a name="supported-features-table"></a>Tabela obsługiwanych funkcji

| Język                         | Metryki podstawowe       | Metryki wydajności | Filtrowanie niestandardowe    | Przykładowa Telemetria    | Dzielenie procesora CPU przez proces |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +)  |
| .NET Core (target =. NET Framework)| Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +)  |
| .NET Core (target =. NET Core)     | Obsługiwane (V 2.4.1 +) | Obsługiwane*          | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | **Nieobsługiwane**    |
| Azure Functions v2               | Obsługiwane           | Obsługiwane           | Obsługiwane           | Obsługiwane           | **Nieobsługiwane**    |
| Java                             | Obsługiwane (V 2.0.0 +) | Obsługiwane (V 2.0.0 +) | **Nieobsługiwane**   | **Nieobsługiwane**   | **Nieobsługiwane**    |
| Node.js                          | Obsługiwane (V 1.3.0 +) | Obsługiwane (V 1.3.0 +) | **Nieobsługiwane**   | Obsługiwane (V 1.3.0 +) | **Nieobsługiwane**    |

Podstawowe metryki obejmują żądanie, zależność i częstotliwość wyjątków. Metryki wydajności (liczniki wydajności) obejmują pamięć i procesor CPU. Przykładowa Telemetria przedstawia strumień szczegółowych informacji dotyczących żądań zakończonych niepowodzeniem i zależności, wyjątków, zdarzeń i śladów.

 \* Obsługa PerfCounters różni się nieco między wersjami programu .NET Core, które nie są przeznaczone dla .NET Framework:

- Metryki PerfCounters są obsługiwane w przypadku uruchamiania w Azure App Service dla systemu Windows. (AspNetCore SDK w wersji 2.4.1 lub nowszej)
- PerfCounters są obsługiwane, gdy aplikacja jest uruchomiona na wszystkich maszynach z systemem Windows (maszynie wirtualnej lub w chmurze lub w Premium itp.). (AspNetCore SDK w wersji 2.7.1 lub nowszej), ale dla aplikacji przeznaczonych dla platformy .NET Core 2,0 lub nowszej.
- PerfCounters są obsługiwane, gdy aplikacja działa w dowolnym miejscu (Linux, Windows, App Service for Linux, Containers itp.) w najnowszych wersjach (tj. AspNetCore SDK w wersji 2.8.0 lub nowszej), ale tylko w przypadku aplikacji przeznaczonych dla platformy .NET Core 2,0 lub nowszej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Live Metrics Stream używa różnych adresów IP niż inne Application Insights dane telemetryczne. Upewnij się, że [te adresy IP](./ip-addresses.md) są otwarte w zaporze. Sprawdź również [porty wychodzące dla Live Metrics Stream](./ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie użycia za pomocą Application Insights](./usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [Debuger migawek](./snapshot-debugger.md)