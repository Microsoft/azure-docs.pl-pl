---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions do monitorowania wykonywania funkcji.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: devx-track-csharp, fasttrack-edit
ms.openlocfilehash: 239d1da028a06d4272ed9b22b624413394aa142f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212999"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługą Azure Application Insights](../azure-monitor/app/app-insights-overview.md) do monitorowania funkcji. W tym artykule opisano sposób konfigurowania Azure Functions do wysyłania generowanych przez system plików dzienników do Application Insights.

Zalecamy używanie Application Insights, ponieważ zbiera dane dotyczące dzienników, wydajności i błędów. Automatycznie wykrywa anomalie wydajności i oferuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu korzystania z funkcji. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Możesz nawet używać Application Insights podczas tworzenia projektu aplikacji funkcji lokalnych. Aby uzyskać więcej informacji, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

Ponieważ wymagana Application Insights Instrumentacja jest wbudowana w Azure Functions, wystarczy, że jest to prawidłowy klucz instrumentacji, aby połączyć aplikację funkcji z Application Insights zasobem. Klucz Instrumentacji należy dodać do ustawień aplikacji podczas tworzenia zasobu aplikacji funkcji na platformie Azure. Jeśli aplikacja funkcji nie ma jeszcze tego klucza, możesz [ją ustawić ręcznie](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny i limity

Możesz wypróbować Application Insights integrację z usługą Azure Functions bezpłatnie. Istnieje dzienny limit ilości danych, które można przetworzyć bezpłatnie. Ten limit można napotkać podczas testowania. Platforma Azure udostępnia powiadomienia w portalu i wiadomości e-mail po zbliżaniu się dziennego limitu. Jeśli pominięto te alerty i osiągniesz limit, nowe dzienniki nie będą wyświetlane w Application Insights zapytaniach. Należy pamiętać o limicie, aby uniknąć niepotrzebnego czasu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cenami i ilością danych w Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights zawiera funkcję [próbkowania](../azure-monitor/app/sampling.md) , która umożliwia ochronę przed generowaniem zbyt dużej ilości danych telemetrycznych w przypadku zakończonych wykonań w czasie szczytowego ładowania. Próbkowanie jest domyślnie włączone. Jeśli wydaje się, że brakuje danych, może być konieczne dostosowanie ustawień próbkowania do określonego scenariusza monitorowania. Aby dowiedzieć się więcej, zobacz [Konfigurowanie próbkowania](#configure-sampling).

Pełna lista funkcji Application Insights dostępnych dla aplikacji funkcji została szczegółowo opisana w [Application Insights dla Azure Functions obsługiwanych funkcji](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Wyświetlanie telemetrii na karcie Monitor

Z [włączoną integracją Application Insights](#enable-application-insights-integration)można wyświetlić dane telemetryczne na karcie **monitorowanie** .

1. Na stronie aplikacja funkcji wybierz funkcję, która została uruchomiona co najmniej raz po skonfigurowaniu Application Insights. Następnie wybierz pozycję **monitor** w okienku po lewej stronie. Wybierz pozycję **Odśwież** okresowo, dopóki nie zostanie wyświetlona lista wywołań funkcji.

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Wyświetlenie listy w czasie, gdy klient telemetrii wysyła dane do serwera, może upłynąć do 5 minut. Opóźnienie nie ma zastosowania do [Live Metrics Stream](../azure-monitor/app/live-stream.md). Ta usługa nawiązuje połączenie z hostem funkcji podczas ładowania strony, dlatego dzienniki są przesyłane strumieniowo bezpośrednio do strony.

1. Aby wyświetlić dzienniki dla określonego wywołania funkcji, wybierz łącze kolumna **Data (UTC)** dla tego wywołania. Dane wyjściowe rejestrowania dla tego wywołania pojawiają się na nowej stronie.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

1. Wybierz pozycję **Uruchom w Application Insights** , aby wyświetlić Źródło zapytania pobierającego dane dziennika Azure monitor w usłudze Azure log. Jeśli używasz usługi Azure Log Analytics w ramach subskrypcji po raz pierwszy, zostanie wyświetlony monit o jej włączenie.

1. Po włączeniu Log Analytics zostanie wyświetlona następująca kwerenda. Można zobaczyć, że wyniki zapytania są ograniczone do 30 ostatnich dni ( `where timestamp > ago(30d)` ). Ponadto wyniki nie pokazują więcej niż 20 wierszy ( `take 20` ). W przeciwieństwie do listy szczegóły wywołania funkcji jest używany w ciągu ostatnich 30 dni bez limitu.

   ![Lista wywołań analizy Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Aby uzyskać więcej informacji, zobacz temat [wysyłanie danych telemetrycznych](#query-telemetry-data) w dalszej części tego artykułu.

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie danych telemetrycznych w Application Insights

Aby otworzyć Application Insights z aplikacji funkcji w Azure Portal, wybierz pozycję **Application Insights** w obszarze **Ustawienia** na lewej stronie. Jeśli po raz pierwszy używasz Application Insights z subskrypcją, zobaczysz monit o jego włączenie: wybierz pozycję **włącz Application Insights**, a następnie wybierz pozycję **Zastosuj** na następnej stronie.

![Otwórz Application Insights na stronie przeglądu aplikacji funkcji](media/functions-monitoring/ai-link.png)

Informacje o sposobach używania Application Insights można znaleźć w [dokumentacji Application Insights](/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w Application Insights. Jeśli znasz już Application Insights, możesz przejść bezpośrednio do [sekcji dotyczącej sposobu konfigurowania i dostosowywania danych telemetrycznych](#configure-categories-and-log-levels).

![Karta przegląd Application Insights](media/functions-monitoring/metrics-explorer.png)

Następujące obszary Application Insights mogą być pomocne podczas oceny zachowania, wydajności i błędów w funkcjach:

| Zbadaj | Opis |
| ---- | ----------- |
| **[Błędy](../azure-monitor/app/asp-net-exceptions.md)** |  Twórz wykresy i alerty na podstawie błędów funkcji i wyjątków serwera. **Nazwa operacji** to nazwa funkcji. Błędy w zależnościach nie są wyświetlane, chyba że zaimplementowano niestandardową telemetrię dla zależności. |
| **[Wydajność](../azure-monitor/app/performance-counters.md)** | Analizuj problemy z wydajnością, wyświetlając wykorzystanie zasobów i przepływność dla **wystąpień ról w chmurze**. Te dane mogą być przydatne w scenariuszach debugowania, w których funkcje boggingją bazowe zasoby. |
| **[Metryki](../azure-monitor/platform/metrics-charts.md)** | Tworzenie wykresów i alertów opartych na metrykach. Metryki obejmują liczbę wywołań funkcji, czas wykonywania i współczynnik sukcesu. |
| **[Metryki na żywo    ](../azure-monitor/app/live-stream.md)** | Wyświetlanie danych metryk w miarę ich tworzenia niemal w czasie rzeczywistym. |

## <a name="query-telemetry-data"></a>Zapytanie danych telemetrycznych

[Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md) zapewnia dostęp do wszystkich danych telemetrycznych w formie tabel w bazie danych. Analiza zawiera język zapytań służący do wyodrębniania, manipulowania i wizualizacji danych. 

Wybierz pozycję **dzienniki** , aby zbadać lub zbadać zarejestrowane zdarzenia.

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Oto przykład zapytania, który pokazuje rozkład żądań na proces roboczy w ciągu ostatnich 30 minut.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Dostępne tabele są wyświetlane na karcie **schemat** po lewej stronie. Dane generowane przez wywołania funkcji można znaleźć w następujących tabelach:

| Tabela | Opis |
| ----- | ----------- |
| **ścieżki** | Dzienniki utworzone przez środowisko uruchomieniowe i kod funkcji. |
| **żądania** | Jedno żądanie wywołania funkcji. |
| **wyłączenia** | Wszystkie wyjątki zgłoszone przez środowisko uruchomieniowe. |
| **customMetrics** | Liczba zakończonych powodzeniem i niepowodzeniem wywołań, współczynnik sukcesu i czas trwania. |
| **customEvents** | Zdarzenia śledzone przez środowisko uruchomieniowe, na przykład: żądania HTTP wyzwalające funkcję. |
| **Liczniki wydajności** | Informacje o wydajności serwerów, na których działają funkcje. |

Inne tabele są przeznaczone dla testów dostępności, a dane telemetryczne klienta i przeglądarki. Możesz zaimplementować niestandardową telemetrię, aby dodać do nich dane.

W każdej tabeli niektóre dane specyficzne dla funkcji znajdują się w `customDimensions` polu.  Na przykład następujące zapytanie pobiera wszystkie ślady mające poziom rejestrowania `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Środowisko uruchomieniowe udostępnia `customDimensions.LogLevel` `customDimensions.Category` pola i. W dziennikach można podać dodatkowe pola, które można napisać w kodzie funkcji. Zobacz [Rejestrowanie strukturalne](#structured-logging) w dalszej części tego artykułu.

## <a name="configure-categories-and-log-levels"></a>Konfigurowanie kategorii i poziomów dziennika

Application Insights można używać bez żadnej konfiguracji niestandardowej. Konfiguracja domyślna może powodować duże ilości danych. W przypadku korzystania z subskrypcji programu Visual Studio Azure można osiągnąć limit danych Application Insights. W dalszej części tego artykułu dowiesz się, jak skonfigurować i dostosować dane wysyłane przez funkcje do Application Insights. W przypadku aplikacji funkcji rejestrowanie jest konfigurowane w [host.js] pliku.

### <a name="categories"></a>Kategorie

Rejestrator Azure Functions obejmuje *kategorię* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska uruchomieniowego lub kod funkcji zapisał dziennik. Poniższy wykres zawiera opis głównych kategorii dzienników tworzonych przez środowisko uruchomieniowe. 

| Kategoria | Opis |
| ----- | ----- | 
| Host.Results (Kategoria Host.Results) | Te dzienniki są wyświetlane jako **żądania** w Application Insights. Wskazują one powodzenie lub Niepowodzenie funkcji. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |
| Host. agregator | Te dzienniki zapewniają liczniki i średnie wywołania funkcji w [konfigurowalnym](#configure-the-aggregator) okresie. Domyślny okres to 30 sekund lub 1 000 wyników, zależnie od tego, co nastąpi wcześniej. Dzienniki są dostępne w tabeli **customMetrics** w Application Insights. Przykłady to liczba przebiegów, Częstotliwość powodzeń i czas trwania. Wszystkie te dzienniki są zapisywane na `Information` poziomie. W przypadku filtrowania na poziomie `Warning` lub wyższym nie będą widoczne żadne z tych danych. |

Wszystkie dzienniki dla kategorii innych niż te są dostępne w tabeli **TRACES** w Application Insights.

Wszystkie dzienniki z kategoriami zaczynającymi się od programu `Host` są zapisywane przez środowisko uruchomieniowe funkcji. Dzienniki **funkcji uruchomiono** i **Zakończono funkcję** mają kategorię `Host.Executor` . W przypadku pomyślnych uruchomień te dzienniki są `Information` poziomami. Wyjątki są rejestrowane na `Error` poziomie. Środowisko uruchomieniowe tworzy również `Warning` dzienniki poziomów, na przykład: komunikaty w kolejce wysyłane do kolejki trującej.

Środowisko uruchomieniowe funkcji tworzy dzienniki z kategorią rozpoczynającą się od "host". W wersji 1. x,, `function started` `function executed` i `function completed` dzienniki mają kategorię `Host.Executor` . Począwszy od wersji 2. x, te dzienniki mają kategorię `Function.<YOUR_FUNCTION_NAME>` .

W przypadku pisania dzienników w kodzie funkcji kategoria jest `Function.<YOUR_FUNCTION_NAME>.User` i może być dowolnego poziomu dziennika. W wersji 1. x środowiska uruchomieniowego Functions kategoria ma wartość `Function` .

### <a name="log-levels"></a>Poziomy rejestrowania

Rejestrator Azure Functions obejmuje również *poziom dziennika* z każdym dziennikiem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje na ważność względną:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczne    | 5 |
|Brak        | 6 |

Poziom dziennika `None` został wyjaśniony w następnej sekcji. 

### <a name="log-configuration-in-hostjson"></a>Konfiguracja dziennika w host.jsna

[host.jsw] pliku konfiguruje, ile rejestrowania aplikacja funkcji wysyła do Application Insights. Dla każdej kategorii należy określić minimalny poziom rejestrowania do wysłania. Istnieją dwa przykłady: pierwszy przykład dotyczy [wersji 2. x i nowszych](functions-versions.md#version-2x) środowiska uruchomieniowego funkcji (z .NET Core), a drugi przykład jest przeznaczony dla środowiska uruchomieniowego w wersji 1. x.

### <a name="version-2x-and-higher"></a>Wersja 2. x lub nowsza

Wersja V2. x i nowsze wersje środowiska uruchomieniowego funkcji używają [hierarchii filtrów rejestrowania programu .NET Core](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1. x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Ten przykład konfiguruje następujące reguły:

* W przypadku dzienników z kategorią `Host.Results` lub `Function` Wyślij `Error` poziom tylko do Application Insights. Dzienniki na `Warning` poziomie i poniżej zostały zignorowane.
* W przypadku dzienników z kategorią `Host.Aggregator` Wyślij wszystkie dzienniki do Application Insights. `Trace`Poziom dziennika jest taki sam jak w przypadku niektórych rejestratorów `Verbose` , ale użyj `Trace` w [host.jsna] pliku.
* Dla wszystkich innych dzienników wysyłaj tylko `Information` poziom i powyżej do Application Insights.

Wartość kategorii w [host.jsna] kontrolkach rejestrowania dla wszystkich kategorii, które zaczynają się od tej samej wartości. `Host` w [host.jsna] kontrolach rejestrowania dla `Host.General` ,, `Host.Executor` `Host.Results` , i tak dalej.

Jeśli [host.json] zawiera wiele kategorii, które zaczynają się od tego samego ciągu, zostaną one dopasowane jako pierwsze. Załóżmy, że chcesz, aby wszystkie elementy uruchomieniowe były wykonywane z wyjątkiem `Host.Aggregator` rejestrowania na `Error` poziomie, ale chcesz `Host.Aggregator` zalogować się na `Information` poziomie:

### <a name="version-2x-and-later"></a>Wersja 2. x i nowsze

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1. x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Aby pominąć wszystkie dzienniki dla kategorii, można użyć poziomu dziennika `None` . Żadne dzienniki nie są zapisywane w tej kategorii i nie ma żadnego poziomu dziennika.

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wskazano w poprzedniej sekcji, środowisko uruchomieniowe agreguje dane dotyczące wykonywania funkcji w danym okresie czasu. Domyślny okres to 30 sekund lub 1 000 uruchomienia, w zależności od tego, co nastąpi wcześniej. To ustawienie można skonfigurować w [host.js] pliku.  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfiguruj próbkowanie

Application Insights zawiera funkcję [próbkowania](../azure-monitor/app/sampling.md) , która umożliwia ochronę przed generowaniem zbyt dużej ilości danych telemetrycznych w przypadku zakończonych wykonań w czasie szczytowego ładowania. Gdy częstotliwość wykonywania przychodzących przekracza określony próg, Application Insights zaczyna losowo ignorować niektóre wykonania przychodzące. Domyślne ustawienie maksymalnej liczby wykonań na sekundę to 20 (pięć w wersji 1. x). Można skonfigurować próbkowanie w [host.jsna](./functions-host-json.md#applicationinsights).  Oto przykład:

### <a name="version-2x-and-later"></a>Wersja 2. x i nowsze

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

W wersji 2. x można wykluczyć pewne typy danych telemetrycznych z próbkowania. W powyższym przykładzie dane typu `Request` są wykluczone z próbkowania. Gwarantuje to, że *wszystkie* wykonania funkcji (żądania) są rejestrowane, podczas gdy inne typy telemetrii nadal podlegają próbkowaniu.

### <a name="version-1x"></a>Wersja 1. x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Write logs in C# functions (Zapisywanie dzienników w funkcjach języka C#)

Dzienniki można napisać w kodzie funkcji, który jest wyświetlany jako ślady w Application Insights.

### <a name="ilogger"></a>ILogger

Użyj parametru [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) w funkcjach, a nie `TraceWriter` parametru. Dzienniki utworzone za pomocą polecenia `TraceWriter` Przejdź do Application Insights, ale `ILogger` umożliwiają [Rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Przy użyciu `ILogger` obiektu wywoływanie `Log<level>` [metod rozszerzających ILogger](/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) umożliwia tworzenie dzienników. Poniższy kod zapisuje `Information` dzienniki z kategorią "Function. <YOUR_FUNCTION_NAME>. Użytkownik ".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Rejestrowanie strukturalne

Kolejność symboli zastępczych, a nie ich nazw, określa parametry, które są używane w komunikacie dziennika. Załóżmy, że masz następujący kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

W przypadku zachowania tego samego ciągu komunikatu i odwrócenia kolejności parametrów, otrzymany tekst komunikatu będzie miał wartości w niewłaściwych miejscach.

Symbole zastępcze są obsługiwane w ten sposób, dzięki czemu można przeprowadzić rejestrowanie strukturalne. Application Insights przechowuje pary nazwa-wartość parametru i ciąg komunikatu. Wynikiem tego jest to, że argumenty komunikatów staną się polami, w których można wykonywać zapytania.

Jeśli wywołanie metody rejestratora wygląda jak w poprzednim przykładzie, można wysłać zapytanie do pola `customDimensions.prop__rowKey` . `prop__`Prefiks jest dodawany, aby upewnić się, że nie ma kolizji między polami, które dodaje i dodaje kod funkcji.

Możesz również wykonać zapytanie dotyczące oryginalnego ciągu wiadomości, odwołując się do pola `customDimensions.prop__{OriginalFormat}` .  

Poniżej przedstawiono przykładową reprezentację danych w formacie JSON `customDimensions` :

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryk niestandardowych

W funkcjach skryptu języka C# można użyć `LogMetric` metody rozszerzenia w `ILogger` programie, aby utworzyć metryki niestandardowe w Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywołania przy `TrackMetric` użyciu interfejsu API Application Insights dla platformy .NET.

## <a name="write-logs-in-javascript-functions"></a>Write logs in JavaScript functions (Zapisywanie dzienników w funkcjach języka JavaScript)

W funkcjach Node.js służy `context.log` do pisania dzienników. Rejestrowanie strukturalne nie jest włączone.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryk niestandardowych

Gdy korzystasz z [wersji 1. x](functions-versions.md#creating-1x-apps) środowiska uruchomieniowego funkcji, Node.js funkcje mogą używać `context.log.metric` metody do tworzenia niestandardowych metryk w Application Insights. Ta metoda nie jest obecnie obsługiwana w wersji 2. x i nowszych. Oto przykładowe wywołanie metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywoływania przy `trackMetric` użyciu zestawu SDK Node.js dla Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Log custom telemetry in C# functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka C#)

Istnieje oparta na funkcjach wersja zestawu SDK Application Insights, której można użyć do wysyłania niestandardowych danych telemetrycznych z funkcji do Application Insights: [Microsoft. Azure. WebJobs. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Użyj następującego polecenia w wierszu polecenia, aby zainstalować ten pakiet:

# <a name="command"></a>[Polecenie](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

W tym poleceniu Zastąp `<VERSION>` wartość wersją tego pakietu, która obsługuje zainstalowaną wersję programu [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Poniższe przykłady w języku C# używają [niestandardowego interfejsu API telemetrii](../azure-monitor/app/api-custom-events-metrics.md). Przykład dotyczy biblioteki klas .NET, ale kod Application Insights jest taki sam dla skryptu C#.

### <a name="version-2x-and-later"></a>Wersja 2. x i nowsze

Wersja 2. x i nowsze wersje środowiska uruchomieniowego używają nowszych funkcji w Application Insights do automatycznego skorelowania telemetrii z bieżącą operacją. Nie ma potrzeby ręcznego ustawiania operacji `Id` , `ParentId` lub `Name` pól.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

Metoda [GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) jest obecnie ZALECAnym interfejsem API do tworzenia metryki.

### <a name="version-1x"></a>Wersja 1. x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Nie wywołuj `TrackRequest` lub `StartOperation<RequestTelemetry>` ponieważ zobaczysz zduplikowane żądania wywołania funkcji.  Środowisko uruchomieniowe funkcji automatycznie śledzi żądania.

Nie ustawiono `telemetryClient.Context.Operation.Id` . To ustawienie globalne powoduje nieprawidłowe korelację, gdy wiele funkcji jest uruchomionych jednocześnie. Zamiast tego należy utworzyć nowe wystąpienie telemetrii ( `DependencyTelemetry` , `EventTelemetry` ) i zmodyfikować jego `Context` Właściwość. Następnie Przekaż wystąpienie telemetrii do odpowiedniej `Track` metody w `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Ta metoda zapewnia, że Telemetria ma poprawne szczegóły korelacji dla bieżącego wywołania funkcji.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Log custom telemetry in JavaScript functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka JavaScript)

Poniżej przedstawiono przykładowe fragmenty kodu, które wysyłają niestandardową telemetrię przy użyciu [zestawu SDK Application Insights Node.js](https://github.com/microsoft/applicationinsights-node.js):

### <a name="version-2x-and-later"></a>Wersja 2. x i nowsze

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>Wersja 1. x

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

`tagOverrides`Parametr ustawia wartość `operation_Id` na identyfikator wywołania funkcji. To ustawienie pozwala skorelować wszystkie automatycznie generowane i niestandardową telemetrię dla danego wywołania funkcji.

## <a name="dependencies"></a>Zależności

Funkcja v2 automatycznie zbiera zależności dla żądań HTTP, ServiceBus, EventHub i SQL.

Można napisać niestandardowy kod, aby wyświetlić zależności. Przykłady zawierają przykładowy kod w [sekcji niestandardowe telemetrię języka C#](#log-custom-telemetry-in-c-functions). Przykładowy kod powoduje, że *Mapa aplikacji* w Application Insights wygląda jak na poniższej ilustracji:

![Mapa aplikacji](./media/functions-monitoring/app-map.png)

> [!NOTE]
> Zależności są zapisywane na poziomie informacji. W przypadku filtrowania na ostrzeżenie lub powyżej nie będą wyświetlane żadne z tych danych. Ponadto Automatyczne zbieranie zależności odbywa się w zakresie nienależącym do użytkownika. Upewnij się, że poziom jest ustawiony na co najmniej **informacje** poza zakresem użytkownika w host.jsna (tj. poza funkcją. <YOUR_FUNCTION_NAME>. Klucz użytkownika), jeśli te zależności mają być przechwytywane.

## <a name="enable-application-insights-integration"></a>Enable Application Insights integration (Włączanie integracji z usługą Application Insights)

Aby aplikacja funkcji mogła wysyłać dane do Application Insights, musi znać klucz Instrumentacji zasobu Application Insights. Klucz musi być w ustawieniu aplikacji o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

Podczas tworzenia aplikacji funkcji [w Azure Portal](functions-create-first-azure-function.md)z wiersza polecenia przy użyciu [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md)lub przy użyciu [Visual Studio Code](functions-create-first-function-vs-code.md)integracja Application Insights jest włączona domyślnie. Zasób Application Insights ma taką samą nazwę jak aplikacja funkcji i jest tworzony w tym samym regionie lub w najbliższym regionie.

### <a name="new-function-app-in-the-portal"></a>Nowa aplikacja funkcji w portalu

Aby przejrzeć tworzony zasób Application Insights, zaznacz go, aby rozwinąć okno **Application Insights** . **Nową nazwę zasobu** można zmienić lub wybrać inną **lokalizację w lokalizacji** [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w której mają być przechowywane dane.

![Włącz Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

Po wybraniu opcji **Utwórz**zasób Application Insights zostanie utworzony za pomocą aplikacji funkcji, która ma `APPINSIGHTS_INSTRUMENTATIONKEY` ustawioną wartość ustawienia aplikacji. Wszystko jest gotowe do użycia.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Dodawanie do istniejącej aplikacji funkcji 

Podczas tworzenia aplikacji funkcji przy użyciu [programu Visual Studio](functions-create-your-first-function-visual-studio.md)należy utworzyć zasób Application Insights. Następnie można dodać klucz Instrumentacji z tego zasobu jako [Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) w aplikacji funkcji.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Wczesne wersje funkcji używały wbudowanego monitorowania, które nie jest już zalecane. Podczas włączania integracji Application Insights dla takiej aplikacji funkcji należy również [wyłączyć wbudowane funkcje rejestrowania](#disable-built-in-logging).  

## <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problem związany z integracją Application Insights w usłudze Functions lub uzyskać sugestię lub żądanie, [Utwórz problem w usłudze GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji często warto zobaczyć, co jest zapisywane w dziennikach niemal w czasie rzeczywistym podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia plików dziennika generowanych przez wykonania funkcji.

* **Wbudowane przesyłanie strumieniowe dzienników**: platforma App Service umożliwia wyświetlenie strumienia plików dziennika aplikacji. Jest to równoznaczne z danymi wyjściowymi wyświetlanymi podczas debugowania funkcji podczas [lokalnego tworzenia](functions-develop-local.md) i korzystania z karty **test** w portalu. Wyświetlane są wszystkie informacje oparte na dzienniku. Aby uzyskać więcej informacji, zobacz [dzienniki przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie może być używana z aplikacją działającą w systemie Linux w planie zużycia.

* **Live Metrics Stream**: gdy aplikacja funkcji jest [połączona z Application Insights](#enable-application-insights-integration), można wyświetlać dane dziennika i inne metryki niemal w czasie rzeczywistym w Azure Portal przy użyciu [Live Metrics Stream](../azure-monitor/app/live-stream.md). Użyj tej metody, gdy funkcje monitorowania działają w wielu wystąpieniach lub w systemie Linux w planie zużycia. Ta metoda używa [danych próbkowanych](#configure-sampling).

Strumienie dzienników można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistycznych. 

### <a name="portal"></a>Portal

Oba typy strumieni dzienników można wyświetlić w portalu.

#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dzienników

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz kartę **funkcje platformy** w aplikacji funkcji. Następnie w obszarze **monitorowanie**wybierz pozycję **Rejestruj strumieniowo**.

![Włączanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Spowoduje to połączenie aplikacji z usługą przesyłania strumieniowego dzienników, a Dzienniki aplikacji są wyświetlane w oknie. Można przełączać się między **dziennikami aplikacji** a **dziennikami serwera sieci Web**.  

![Wyświetlanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Aby wyświetlić Live Metrics Stream aplikacji, wybierz kartę **Przegląd** aplikacji funkcji. Gdy Application Insights włączasz, zobaczysz link **Application Insights** w obszarze **skonfigurowane funkcje**. Ten link prowadzi do strony Application Insights Twojej aplikacji.

W Application Insights wybierz pozycję **Live Metrics Stream**. [Przykładowe wpisy dziennika](#configure-sampling) są wyświetlane w obszarze **przykładowe dane telemetryczne**.

![Wyświetlanie Live Metrics Stream w portalu](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Narzędzia podstawowe

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Użyj następujących poleceń, aby się zalogować, wybierz swoją subskrypcję i pliki dziennika przesyłania strumieniowego:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć za pomocą [Azure PowerShell](/powershell/azure/). W przypadku programu PowerShell Użyj polecenia [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) , aby włączyć rejestrowanie w aplikacji funkcji, jak pokazano w poniższym fragmencie kodu: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

Aby uzyskać więcej informacji, zobacz [kompletny przykład kodu](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="scale-controller-logs-preview"></a>Dzienniki kontrolera skalowania (wersja zapoznawcza)

Ta funkcja jest dostępna w wersji zapoznawczej. 

[Kontroler Azure Functions skalowania](./functions-scale.md#runtime-scaling) monitoruje wystąpienia Azure Functions hosta, na którym działa aplikacja. Ten kontroler podejmuje decyzje dotyczące sytuacji, w których należy dodawać lub usuwać wystąpienia na podstawie bieżącej wydajności. Kontroler skalowania może wysyłać dzienniki do Application Insights lub do magazynu obiektów blob, aby lepiej zrozumieć decyzje podejmowane przez kontroler skalowania dla aplikacji funkcji.

Aby włączyć tę funkcję, Dodaj nowe ustawienie aplikacji o nazwie `SCALE_CONTROLLER_LOGGING_ENABLED` . Wartość tego ustawienia musi być w formacie `<DESTINATION>:<VERBOSITY>` , w oparciu o następujące:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Na przykład następujące polecenie interfejsu wiersza polecenia platformy Azure włącza pełne rejestrowanie w kontrolerze skalowania do Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

W tym przykładzie Zastąp `<FUNCTION_APP_NAME>` `<RESOURCE_GROUP_NAME>` wartości i nazwą aplikacji funkcji i nazwą grupy zasobów odpowiednio. 

Następujące polecenie interfejsu wiersza polecenia platformy Azure wyłącza rejestrowanie przez ustawienie szczegółowości `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Możesz również wyłączyć rejestrowanie, usuwając `SCALE_CONTROLLER_LOGGING_ENABLED` ustawienie za pomocą następującego polecenia interfejsu wiersza poleceń platformy Azure:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Wyłączanie wbudowanego rejestrowania

Po włączeniu Application Insights Wyłącz wbudowane rejestrowanie korzystające z usługi Azure Storage. Wbudowane rejestrowanie jest przydatne do testowania z lekkimi obciążeniami, ale nie jest przeznaczone do użycia w środowisku produkcyjnym. W celu monitorowania produkcji zalecamy Application Insights. Jeśli Wbudowana funkcja rejestrowania jest używana w środowisku produkcyjnym, rekord rejestrowania może być niekompletny z powodu ograniczania w usłudze Azure Storage.

Aby wyłączyć wbudowane rejestrowanie, Usuń `AzureWebJobsDashboard` ustawienie aplikacji. Aby uzyskać informacje dotyczące sposobu usuwania ustawień aplikacji w Azure Portal, zobacz sekcję **Ustawienia aplikacji** w temacie [jak zarządzać aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Przed usunięciem ustawienia aplikacji upewnij się, że żadne funkcje w tej samej aplikacji funkcji nie używają ustawienia dla wyzwalaczy lub powiązań usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Application Insights](/azure/application-insights/)
* [Rejestrowanie ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host.jsna]: functions-host-json.md
