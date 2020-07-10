---
title: Diagnozowanie za pomocą Live Metrics Stream platformy Azure Application Insights
description: Monitoruj swoją aplikację sieci Web w czasie rzeczywistym za pomocą metryk niestandardowych i Diagnozuj problemy z dynamicznym źródłem błędów, śladów i zdarzeń.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: e554595a7a88e1455f7426636dc69db99a7d3e94
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86166488"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: monitorowanie & diagnozowanie przy użyciu 1-sekundowego opóźnienia

Monitoruj działającą w środowisku produkcyjną aplikację sieci Web przy użyciu Live Metrics Stream z [Application Insights](../../azure-monitor/app/app-insights-overview.md). Wybieranie i filtrowanie metryk i liczników wydajności w czasie rzeczywistym, bez żadnych zakłóceń usługi. Sprawdzanie śladów stosu z przykładowych nieudanych żądań i wyjątków. Wraz z narzędziem [Profiler](../../azure-monitor/app/profiler.md) i [debuger migawek](../../azure-monitor/app/snapshot-debugger.md)program Live Metrics Stream zapewnia zaawansowane i nieinwazyjne narzędzie diagnostyczne dla działającej witryny sieci Web.

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

1. [Zainstaluj Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) w aplikacji.
2. Oprócz standardowych pakietów Application Insights [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) jest wymagany do włączenia strumienia metryk na żywo.
3. **Zaktualizuj do najnowszej wersji** pakietu Application Insights. W programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Otwórz kartę **aktualizacje** i wybierz wszystkie pakiety Microsoft. ApplicationInsights. *.

    Ponownie wdróż aplikację.

3. W [Azure Portal](https://portal.azure.com)otwórz zasób Application Insights dla aplikacji, a następnie otwórz Live Stream.

4. [Zabezpiecz kanał kontroli,](#secure-the-control-channel) Jeśli możesz użyć poufnych danych, takich jak nazwy klientów w filtrach.

### <a name="no-data-check-your-server-firewall"></a>Brak danych? Sprawdź zaporę serwera

Sprawdź, czy [porty wychodzące Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak Live Metrics Stream różnić się od Eksplorator metryk i analiz?

| |Transmisja strumieniowa na żywo | Eksplorator metryk i analiza |
|---|---|---|
|**Opóźnienie**|Dane wyświetlane w jednej sekundzie|Zagregowane w ciągu minut|
|**Brak przechowywania**|Dane są przechowywane na wykresie, a następnie są odrzucane|[Dane przechowywane przez 90 dni](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|**Na żądanie**|Dane są przesyłane strumieniowo, gdy okienko metryki na żywo jest otwarte |Dane są wysyłane za każdym razem, gdy zestaw SDK jest zainstalowany i włączony|
|**Bezpłatna**|Za dane Live Stream nie są naliczane opłaty|Podlega [cennikowi](../../azure-monitor/app/pricing.md)
|**Próbkowanie**|Wszystkie wybrane metryki i liczniki są przesyłane. Błędy i ślady stosu są próbkowane. TelemetryProcessors nie są stosowane.|Zdarzenia mogą być [próbkowane](../../azure-monitor/app/api-filtering-sampling.md)|
|**Kanał kontrolny**|Sygnały kontroli filtru są wysyłane do zestawu SDK. Zalecamy zabezpieczenie tego kanału.|Komunikacja jest jednym ze sposobów, w portalu|

## <a name="select-and-filter-your-metrics"></a>Wybieranie i filtrowanie metryk

(Dostępne w ASP.NET, ASP.NET Core i Azure Functions (v2).)

Możesz monitorować niestandardowy kluczowy wskaźnik wydajności, stosując dowolne filtry dla dowolnej Application Insights telemetrii w portalu. Kliknij kontrolkę filtr, która jest wyświetlana po umieszczeniu wskaźnika myszy na dowolnym z wykresów. Poniższy wykres przedstawia niestandardowy wskaźnik KPI liczby żądań z filtrami dla atrybutów adresu URL i czasu trwania. Sprawdź poprawność filtrów za pomocą sekcji Podgląd strumienia, która zawiera dynamiczne źródło danych telemetrycznych spełniające kryteria określone w dowolnym momencie.

![Częstotliwość żądań filtrowania](./media/live-stream/filter-request.png)

Można monitorować wartość różną od Count. Opcje są zależne od typu strumienia, który może być dowolną Application Insights telemetrii: żądania, zależności, wyjątki, ślady, zdarzenia lub metryki. Może to być własny [niestandardowy pomiar](../../azure-monitor/app/api-custom-events-metrics.md#properties):

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

![Próbkowane błędy na żywo](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>Filtruj według wystąpienia serwera

Jeśli chcesz monitorować konkretne wystąpienie roli serwera, możesz filtrować według serwera. Aby filtrować, wybierz nazwę serwera w obszarze *serwery*.

![Próbkowane błędy na żywo](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>Zabezpieczanie kanału kontroli

> [!NOTE]
> Obecnie można skonfigurować kanał uwierzytelniony tylko przy użyciu monitorowania podstawowego kodu i nie można uwierzytelnić serwerów przy użyciu dołączania bez kodu.

Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika metryki na żywo w zestawie Application Insights SDK. Filtry mogą potencjalnie zawierać informacje poufne, takie jak customerID. Kanał można zabezpieczyć przy użyciu klucza tajnego interfejsu API oprócz klucza Instrumentacji.
### <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API

![Klucz interfejsu API > Utwórz klucz interfejsu API ](./media/live-stream/api-key.png)
 ![ Utwórz klucz interfejsu API. Wybierz pozycję "Uwierzytelnij kanał kontroli zestawu SDK" i "Generuj klucz"](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>Dodaj klucz interfejsu API do konfiguracji

### <a name="classic-aspnet"></a>Klasyczny ASP.NET

W pliku applicationinsights.config Dodaj AuthenticationApiKey do QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Lub w kodzie, ustaw go na QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplikacje funkcji platformy Azure

W przypadku aplikacji funkcji platformy Azure (v2) Zabezpieczanie kanału za pomocą klucza interfejsu API można wykonać przy użyciu zmiennej środowiskowej.

Utwórz klucz interfejsu API z poziomu zasobu Application Insights i przejdź do pozycji **Ustawienia aplikacji** dla aplikacja funkcji. Wybierz pozycję **Dodaj nowe ustawienie** i wprowadź nazwę `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` i wartość odpowiadającą kluczowi interfejsu API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (wymaga Application Insights ASP.NET Core SDK 2.3.0 lub nowszego)

Zmodyfikuj plik startup.cs w następujący sposób:

Najpierw Dodaj

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie w ramach metody ConfigureServices Dodaj:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Jeśli jednak rozpoznasz i ufasz wszystkim połączonym serwerom, możesz wypróbować filtry niestandardowe bez uwierzytelnionego kanału. Ta opcja jest dostępna przez sześć miesięcy. To przesłonięcie jest wymagane po każdej nowej sesji lub gdy nowy serwer przejdzie w tryb online.

![Opcje uwierzytelniania metryk na żywo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zalecamy skonfigurowanie kanału uwierzytelnionego przed wprowadzeniem potencjalnie poufnych informacji, takich jak CustomerID w kryteriach filtrowania.
>

## <a name="supported-features-table"></a>Tabela obsługiwanych funkcji

| Language                         | Metryki podstawowe       | Metryki wydajności | Filtrowanie niestandardowe    | Przykładowa Telemetria    | Dzielenie procesora CPU przez proces |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +) | Obsługiwane (V 2.7.2 +)  |
| .NET Core (target =. NET Framework)| Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +)  |
| .NET Core (target =. NET Core)     | Obsługiwane (V 2.4.1 +) | Obsługiwane*          | Obsługiwane (V 2.4.1 +) | Obsługiwane (V 2.4.1 +) | **Nieobsługiwane**    |
| Azure Functions v2               | Obsługiwane           | Obsługiwane           | Obsługiwane           | Obsługiwane           | **Nieobsługiwane**    |
| Java                             | Obsługiwane (V 2.0.0 +) | Obsługiwane (V 2.0.0 +) | **Nieobsługiwane**   | **Nieobsługiwane**   | **Nieobsługiwane**    |
| Node.js                          | Obsługiwane (V 1.3.0 +) | Obsługiwane (V 1.3.0 +) | **Nieobsługiwane**   | Obsługiwane (V 1.3.0 +) | **Nieobsługiwane**    |

Podstawowe metryki obejmują żądanie, zależność i częstotliwość wyjątków. Metryki wydajności (liczniki wydajności) obejmują pamięć i procesor CPU. Przykładowa Telemetria przedstawia strumień szczegółowych informacji dotyczących żądań zakończonych niepowodzeniem i zależności, wyjątków, zdarzeń i śladów.

 \*Obsługa PerfCounters różni się nieco między wersjami programu .NET Core, które nie są przeznaczone dla .NET Framework:

- Metryki PerfCounters są obsługiwane w przypadku uruchamiania w Azure App Service dla systemu Windows. (AspNetCore SDK w wersji 2.4.1 lub nowszej)
- PerfCounters są obsługiwane, gdy aplikacja jest uruchomiona na wszystkich maszynach z systemem Windows (maszynie wirtualnej lub w chmurze lub w Premium itp.). (AspNetCore SDK w wersji 2.7.1 lub nowszej), ale dla aplikacji przeznaczonych dla platformy .NET Core 2,0 lub nowszej.
- PerfCounters są obsługiwane, gdy aplikacja działa w dowolnym miejscu (Linux, Windows, App Service for Linux, Containers itp.) w najnowszej wersji beta (tj. AspNetCore SDK w wersji 2.8.0-beta1 lub nowszej), ale dla aplikacji przeznaczonych dla platformy .NET Core 2,0 lub nowszej.

Domyślnie metryki na żywo są wyłączone w zestawie SDK Node.js. Aby włączyć metryki na żywo, Dodaj `setSendLiveMetrics(true)` do [metod konfiguracji](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) w miarę inicjowania zestawu SDK.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Brak danych? Jeśli aplikacja znajduje się w sieci chronionej: Live Metrics Stream używa różnych adresów IP niż inne Application Insights telemetrii. Upewnij się, że [te adresy IP](../../azure-monitor/app/ip-addresses.md) są otwarte w zaporze.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie użycia za pomocą Application Insights](../../azure-monitor/app/usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Debuger migawek](../../azure-monitor/app/snapshot-debugger.md)
