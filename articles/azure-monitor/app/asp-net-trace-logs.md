---
title: Eksplorowanie dzienników śledzenia .NET w Application Insights
description: Wyszukaj dzienniki wygenerowane przez Trace, NLog lub Log4Net.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: 083ddbd06561550f89e414d6c679cdc6433fa338
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937552"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Poznaj dzienniki śledzenia .NET/.NET Core i Python w Application Insights

Wyślij dzienniki śledzenia diagnostyki dla aplikacji ASP.NET/ASP.NET Core z ILogger, NLog, log4Net lub system. Diagnostics. Trace do [platformy Azure Application Insights][start]. W przypadku aplikacji języka Python Wyślij dzienniki śledzenia diagnostycznego za pomocą AzureLogHandler w OpenCensus Python dla Azure Monitor. Następnie możesz eksplorować i wyszukiwać. Te dzienniki są scalane z innymi plikami dziennika z Twojej aplikacji, dzięki czemu można identyfikować ślady skojarzone z poszczególnymi żądaniami użytkowników i skorelować je z innymi zdarzeniami i raportami o wyjątkach.

> [!NOTE]
> Potrzebujesz modułu przechwytywania dziennika? Jest to przydatna karta dla rejestratorów innych firm. Ale jeśli nie korzystasz już z NLog, log4Net lub system. Diagnostics. Trace, rozważ jedynie wywołanie [**Application Insights TrackTrace ()**](./api-custom-events-metrics.md#tracktrace) bezpośrednio.
>
>
## <a name="install-logging-on-your-app"></a>Instalowanie rejestrowania w aplikacji
Zainstaluj wybraną platformę rejestrowania w projekcie, co powinno skutkować wpisem w app.config lub web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurowanie Application Insights do zbierania dzienników
[Dodaj Application Insights do projektu](./asp-net.md) , jeśli jeszcze tego nie zrobiono. Zobaczysz opcję obejmującą moduł zbierający dzienniki.

Lub kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań, aby **skonfigurować Application Insights**. Wybierz opcję **Konfiguruj zbieranie śladów** .

> [!NOTE]
> Nie Application Insights menu ani opcji modułu zbierającego dzienniki? Wypróbuj [Rozwiązywanie problemów](#troubleshooting).

## <a name="manual-installation"></a>Instalacja ręczna
Użyj tej metody, jeśli typ projektu nie jest obsługiwany przez Instalatora Application Insights (na przykład projekt klasyczny systemu Windows).

1. Jeśli planujesz używać log4Net lub NLog, zainstaluj ją w projekcie.
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
3. Wyszukaj ciąg "Application Insights".
4. Wybierz jeden z następujących pakietów:

   - W przypadku ILogger: [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger transparent](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - W przypadku nLOG: [Microsoft. ApplicationInsights. NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet nLOG transparent](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - W przypadku Log4Net: [Microsoft. ApplicationInsights. Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net transparent](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Dla elementu System. Diagnostics: [Microsoft. ApplicationInsights. TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet system. Diagnostics transparent](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft. ApplicationInsights. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
 [ ![ Transparent odbiornika źródła diagnostyki NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft. ApplicationInsights. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
 [ ![ Transparent modułu zbierającego ETW NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft. ApplicationInsights. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
 [ ![ Transparent odbiornika źródła zdarzeń NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Pakiet NuGet instaluje niezbędne zestawy i modyfikuje web.config lub app.config, jeśli ma to zastosowanie.

## <a name="ilogger"></a>ILogger

Przykłady użycia Application Insights implementacji ILogger z aplikacjami konsolowymi i ASP.NET Core można znaleźć w temacie [ApplicationInsightsLoggerProvider for .NET Core ILogger Logs](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Wstawianie wywołań dzienników diagnostycznych
Jeśli używasz elementu System. Diagnostics. Trace, typowym wywołaniem będzie:

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

Jeśli wolisz log4net lub NLog, użyj:

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>Korzystanie z zdarzeń EventSource
Zdarzenia [System. Diagnostics. Trace. EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) można skonfigurować do wysyłania do Application Insights jako śladów. Najpierw zainstaluj `Microsoft.ApplicationInsights.EventSourceListener` pakiet NuGet. Następnie Edytuj `TelemetryModules` sekcję pliku [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **Nazwa** określa nazwę elementu EventSource do zebrania.
 * **Poziom** określa poziom rejestrowania do zebrania: *krytyczne*, *Error*, *informacyjne*, *LogAlways*, *verbose* lub *Warning*.
 * **Słowa kluczowe** (opcjonalnie) określają wartość całkowitą kombinacji słów kluczowych do użycia.

## <a name="use-diagnosticsource-events"></a>Korzystanie z zdarzeń DiagnosticSource
Można skonfigurować zdarzenia [System. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) , które mają być wysyłane do Application Insights jako dane śledzenia. Najpierw zainstaluj [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakiet NuGet. Następnie przeprowadź edycję sekcji "TelemetryModules" pliku [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Dla każdej DiagnosticSource, którą chcesz śledzić, Dodaj wpis z atrybutem **name** ustawionym na nazwę DiagnosticSource.

## <a name="use-etw-events"></a>Korzystanie z zdarzeń ETW
Można skonfigurować zdarzenia śledzenia zdarzeń systemu Windows (ETW), które mają być wysyłane do Application Insights jako dane śledzenia. Najpierw zainstaluj `Microsoft.ApplicationInsights.EtwCollector` pakiet NuGet. Następnie przeprowadź edycję sekcji "TelemetryModules" pliku [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) .

> [!NOTE] 
> Zdarzenia ETW można zbierać tylko wtedy, gdy proces obsługujący zestaw SDK działa w ramach tożsamości będącej członkiem użytkowników dzienników wydajności lub administratorów.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **ProviderName** jest nazwą dostawcy ETW do zebrania.
 * **ProviderGuid** określa identyfikator GUID dostawcy ETW do zebrania. Można go użyć zamiast `ProviderName` .
 * **Poziom** ustawia poziom rejestrowania do zebrania. Może to być *krytyczne*, *Error*, *informacyjne*, *LogAlways*, *verbose* lub *Warning*.
 * **Słowa kluczowe** (opcjonalnie) ustawiają wartość całkowitą kombinacji słów kluczowych do użycia.

## <a name="use-the-trace-api-directly"></a>Bezpośrednie używanie interfejsu API śledzenia
Interfejs API śledzenia Application Insights można wywołać bezpośrednio. Karty rejestrowania używają tego interfejsu API.

Na przykład:

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
var telemetryClient = new TelemetryClient(configuration);
telemetry.TrackTrace("Slow response - database01");
```

Zaletą TrackTrace jest możliwość umieszczania stosunkowo długich danych w komunikacie. Na przykład możesz kodować dane POST w tym miejscu.

Do wiadomości można także dodać poziom ważności. Podobnie jak w przypadku innych telemetrii, można dodać wartości właściwości, aby ułatwić filtrowanie lub wyszukiwanie różnych zestawów śladów. Na przykład:

  ```csharp
  TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
  var telemetryClient = new TelemetryClient(configuration);
  telemetryClient.TrackTrace("Slow database response",
                              SeverityLevel.Warning,
                              new Dictionary<string, string> { { "database", "db.ID" } });
  ```

Dzięki temu można łatwo odfiltrować [wszystkie komunikaty][diagnostic] o określonym poziomie ważności powiązane z określoną bazą danych.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler for OpenCensus Python
Program obsługi dzienników Azure Monitor umożliwia eksportowanie dzienników języka Python do Azure Monitor.

Instrumentacja aplikacji za pomocą [zestawu OpenCensus Python SDK](./opencensus-python.md) dla Azure monitor.

Ten przykład pokazuje, jak wysłać dziennik poziomu ostrzeżeń do Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Eksplorowanie dzienników
Uruchom aplikację w trybie debugowania lub Wdróż ją na żywo.

W okienku Przegląd aplikacji w [portalu Application Insights][portal]wybierz pozycję [Wyszukaj][diagnostic].

Możesz na przykład:

* Filtrowanie danych śledzenia dzienników lub elementów mających określone właściwości.
* Sprawdź szczegóły określonego elementu.
* Znajdź inne dane dziennika systemu, które odnoszą się do tego samego żądania użytkownika (ma ten sam OperationId).
* Zapisz konfigurację strony jako ulubioną.

> [!NOTE]
>Jeśli aplikacja wysyła dużo danych i używasz zestawu Application Insights SDK dla ASP.NET w wersji 2.0.0-beta3 lub nowszej, funkcja *próbkowania adaptacyjnego* może działać i wysyłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](./sampling.md)
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="how-do-i-do-this-for-java"></a>Jak mogę zrobić to w języku Java?
W instrumentacji bez kodu Java (zalecane) dzienniki są zbierane z pola, użyj programu [Java 3,0 Agent](./java-in-process-agent.md).

Jeśli używasz zestawu SDK języka Java, użyj [kart dzienników języka Java](./java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>W menu kontekstowym projektu nie ma opcji Application Insights
* Upewnij się, że Developer Analytics Tools jest zainstalowana na komputerze deweloperskim. W obszarze   >  **rozszerzenia i aktualizacje** narzędzi programu Visual Studio poszukaj **Developer Analytics Tools**. Jeśli nie ma go na karcie **zainstalowane** , Otwórz kartę **online** i zainstaluj ją.
* Może to być typ projektu, który Developer Analytics Tools nie obsługuje. Użyj [instalacji ręcznej](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>W narzędziu konfiguracji nie ma opcji karty dziennika
* Najpierw zainstaluj platformę rejestrowania.
* Jeśli używasz funkcji system. Diagnostics. Trace, upewnij się, że [skonfigurowano ją w *web.config*](/dotnet/api/system.diagnostics.eventlogtracelistener).
* Upewnij się, że masz najnowszą wersję Application Insights. W programie Visual Studio przejdź do pozycji **Narzędzia**  >  **rozszerzenia i aktualizacje**, a następnie otwórz kartę **aktualizacje** . Jeśli **Developer Analytics Tools** , wybierz ją, aby ją zaktualizować.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucz instrumentacji nie może być pusty"
Prawdopodobnie zainstalowano pakiet NuGet karty rejestrowania bez instalowania Application Insights. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy pozycję *ApplicationInsights.config*, a następnie wybierz polecenie **Aktualizuj Application Insights**. Zostanie wyświetlony monit o zalogowanie się do platformy Azure i utworzenie zasobu Application Insights lub ponowne użycie istniejącego. Który powinien rozwiązać ten problem.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Mogę zobaczyć ślady, ale nie inne zdarzenia w przeszukiwaniu diagnostycznym
Może upłynąć trochę czasu dla wszystkich zdarzeń i żądań, aby przejść przez potok.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Ile danych jest przechowywanych?
Niektóre czynniki wpływają na ilość przechowywanych danych. Aby uzyskać więcej informacji, zobacz sekcję [limity](./api-custom-events-metrics.md#limits) na stronie metryki zdarzeń klienta.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nie widzę niektórych wpisów dziennika, których oczekuje
Jeśli aplikacja wysyła Voluminous ilość danych i korzysta z zestawu SDK Application Insights dla ASP.NET w wersji 2.0.0-beta3 lub nowszej, funkcja próbkowania adaptacyjnego może działać i wysyłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>Następne kroki

* [Diagnozuj błędy i wyjątki w ASP.NET][exceptions]
* [Dowiedz się więcej o wyszukiwaniu][diagnostic]
* [Konfigurowanie testów dostępności i czasu odpowiedzi][availability]
* [Rozwiązywanie problemów][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

