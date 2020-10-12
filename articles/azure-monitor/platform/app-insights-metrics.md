---
title: Metryki oparte na dzienniku usługi Azure Application Insights | Microsoft Docs
description: W tym artykule przedstawiono metryki usługi Azure Application Insights z obsługiwanymi agregacjami i wymiarami. Szczegółowe informacje o metrykach opartych na dzienniku zawierają bazowe instrukcje zapytania Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 9ea98df4b6cd8572412e7082b451feac3736919c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327076"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights metryki oparte na dzienniku

Application Insights metryki oparte na dzienniku umożliwiają analizowanie kondycji monitorowanych aplikacji, tworzenie zaawansowanych pulpitów nawigacyjnych i Konfigurowanie alertów. Istnieją dwa rodzaje metryk:

* [Metryki oparte na dziennikach](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) są tłumaczone na [zapytania Kusto](/azure/kusto/query/) z przechowywanych zdarzeń.
* [Metryki standardowe](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) są przechowywane jako szeregi czasowe zagregowane.

Ze względu na to, że *metryki standardowe* są wstępnie agregowane podczas zbierania, mają lepszą wydajność w czasie wykonywania zapytania. Zapewnia to lepszy wybór dla pulpitów nawigacyjnych i alertów w czasie rzeczywistym. *Metryki oparte na dzienniku* mają więcej wymiarów, co sprawia, że jest to opcja najwyższej jakości do analizy danych i diagnostyki ad hoc. Użyj [selektora przestrzeni nazw](metrics-getting-started.md#create-your-first-metric-chart) , aby przełączać się między metrykami opartymi na dzienniku i standardowym w [Eksploratorze metryk](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretuj i używaj zapytań z tego artykułu

Ten artykuł zawiera listę metryk z obsługiwanymi agregacjami i wymiarami. Szczegółowe informacje o metrykach opartych na dzienniku zawierają bazowe instrukcje zapytania Kusto. Dla wygody każde zapytanie używa wartości domyślnych dla stopnia szczegółowości czasu, typu wykresu i czasami dzielenia wymiarem, które upraszcza użycie zapytania w Log Analytics bez konieczności modyfikowania.

Podczas wykreślania tej samej metryki w [Eksploratorze metryk](metrics-getting-started.md)nie ma żadnych wartości domyślnych — zapytanie jest dynamicznie dostosowywane na podstawie ustawień wykresu:

- Wybrany **zakres czasu** jest tłumaczony na dodatkową klauzulę *WHERE sygnatura* czasowa..., aby wybierać tylko zdarzenia z wybranego zakresu czasu. Na przykład wykres przedstawiający dane dla ostatnich 24 godzin zapytania zawiera *| gdzie sygnatura czasowa > temu (24 h)*.

- Wybrany **stopień szczegółowości czasu** jest wprowadzany do końcowego *podsumowania... według: bin (Sygnatura czasowa, [ziarno))* .

- Wszystkie wybrane wymiary **filtru** są tłumaczone na dodatkowe klauzule *WHERE* .

- Wybrany wymiar **podzielony wykres** jest tłumaczony na dodatkową właściwość podsumowującą. Na przykład w przypadku dzielenia wykresu według *lokalizacji*i kreślenia przy użyciu 5-minutowego stopnia szczegółowości zostanie podsumowana klauzula *podsumowania* *... według bin (Sygnatura czasowa, 5 m), lokalizacja*.

> [!NOTE]
> Jeśli dopiero zaczynasz pracę z językiem zapytań Kusto, możesz zacząć od skopiowania i wklejenia instrukcji Kusto do okienka zapytania Log Analytics bez wprowadzania żadnych modyfikacji. Kliknij przycisk **Uruchom** , aby wyświetlić wykres podstawowy. Po rozpoczęciu zrozumienia składni języka zapytań można zacząć wprowadzać małe modyfikacje i zobaczyć wpływ zmiany. Eksplorowanie własnych danych jest doskonałym sposobem na rozpoczęcie realizacji pełnej mocy [log Analytics](../log-query/get-started-portal.md) i [Azure monitor](../overview.md).

## <a name="availability-metrics"></a>Metryki dostępności

Metryki w kategorii dostępność umożliwiają sprawdzenie kondycji aplikacji sieci Web zaobserwowanej z punktów na całym świecie. [Skonfiguruj testy dostępności,](../app/monitor-web-app-availability.md) aby rozpocząć korzystanie z dowolnych metryk z tej kategorii.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostępność (availabilityResults/availabilityPercentage)
Metryka *dostępności* przedstawia wartość procentową przebiegów testów sieci Web, które nie wykryły żadnych problemów. Najmniejsza możliwa wartość to 0, co oznacza, że wszystkie uruchomienia testu sieci Web nie powiodły się. Wartość 100 oznacza, że wszystkie uruchomienia testu sieci Web przeszły kryteria walidacji.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Procent|Średnia|Lokalizacja przebiegu, nazwa testu|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Czas trwania testu dostępności (availabilityResults/Duration)

Metryka *czas trwania testu dostępności* pokazuje, ile czasu zajęło uruchomienie testu sieci Web. W przypadku [wieloetapowych testów sieci Web](../app/availability-multistep.md)Metryka wskazuje całkowity czas wykonywania wszystkich kroków.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|)|Średnia, minimum, maksimum|Lokalizacja przebiegu, nazwa testu, wynik testu

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testy dostępności (availabilityResults/Count)

Metryki *testów dostępności* odzwierciedlają liczbę testów sieci Web wykonywanych przez Azure monitor.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Liczba|Liczba|Lokalizacja przebiegu, nazwa testu, wynik testu|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Metryki przeglądarki

Metryki przeglądarki są zbierane przez zestaw Application Insights JavaScript SDK z rzeczywistych przeglądarek użytkowników końcowych. Zapewniają doskonałe informacje o doświadczeniu użytkowników w Twojej aplikacji sieci Web. Metryki przeglądarki zazwyczaj nie są próbkowane, co oznacza, że zapewniają większą precyzję numerów użycia w porównaniu z metrykami po stronie serwera, które mogą być pochylone przez próbkowanie.

> [!NOTE]
> Aby zbierać metryki przeglądarki, aplikacja musi być Instrumentacja przy użyciu [zestawu SDK języka JavaScript Application Insights](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Czas ładowania strony w przeglądarce (browserTimings/totalDuration)

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Czas przetwarzania klienta (browserTiming/processingDuration)

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Czas połączenia sieciowego ładowania strony (browserTimings/networkDuration)

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Czas odpowiedzi na odebranie (browserTimings/receiveDuration)

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Czas żądania wysłania (browserTimings/sendDuration)

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metryki błędów

Metryki w **błędach** pokazują problemy związane z przetwarzaniem żądań, wywołaniami zależności i zgłoszonymi wyjątkami.

### <a name="browser-exceptions-exceptionsbrowser"></a>Wyjątki przeglądarki (wyjątki/przeglądarka)

Ta Metryka odzwierciedla liczbę zgłoszonych wyjątków z kodu aplikacji działającego w przeglądarce. W metryce są uwzględniane tylko wyjątki, które są śledzone za pomocą ```trackException()``` wywołania interfejsu API Application Insights.

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Brak|Wersja oparta na dzienniku korzysta z agregacji **sum**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Błędy wywołań zależności (zależności/niepowodzenie)

Liczba wywołań zależności zakończonych niepowodzeniem.

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Brak|Wersja oparta na dzienniku korzysta z agregacji **sum**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Wyjątki (wyjątki/liczba)

Za każdym razem, gdy rejestrujesz wyjątek do Application Insights, istnieje wywołanie [metody trackexception ()](../app/api-custom-events-metrics.md#trackexception) zestawu SDK. Metryka wyjątków pokazuje liczbę zarejestrowanych wyjątków.

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Nazwa roli w chmurze, wystąpienie roli w chmurze, typ urządzenia|Wersja oparta na dzienniku korzysta z agregacji **sum**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Nieudane żądania (żądania/niepowodzenie)

Liczba śledzonych żądań serwera, które zostały oznaczone jako *zakończone niepowodzeniem*. Domyślnie zestaw SDK Application Insights automatycznie oznacza każde żądanie serwera, które zwróciło kod odpowiedzi HTTP 5xx lub 4xx jako żądanie zakończone niepowodzeniem. Tę logikę można dostosować, modyfikując właściwość  *Success* elementu telemetrii żądania w [niestandardowym inicjatorze telemetrii](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Wystąpienie roli w chmurze, nazwa roli chmury, ruch rzeczywisty lub syntetyczny, wydajność żądania, kod odpowiedzi|Wersja oparta na dzienniku korzysta z agregacji **sum**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Wyjątki serwera (wyjątki/serwer)

Ta Metryka przedstawia liczbę wyjątków serwera.

|Jednostka miary|Obsługiwane agregacje|Wymiary wstępnie zagregowane|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Nazwa roli w chmurze, wystąpienie roli w chmurze|Wersja oparta na dzienniku korzysta z agregacji **sum**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Liczniki wydajności

Użyj metryk w kategorii **liczniki wydajności** , aby uzyskać dostęp do [liczników wydajności systemu zbieranych przez Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostępna pamięć (liczniki wydajności/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Częstotliwość wyjątków (liczniki wydajności/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Czas wykonywania żądania HTTP (liczniki wydajności/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Częstotliwość żądań HTTP (liczniki wydajności/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Żądania HTTP w kolejce aplikacji (liczniki wydajności/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Procesor CPU procesu (liczniki wydajności/processCpuPercentage)

Metryka pokazuje, ile całkowitej pojemności procesora jest zużywanych przez proces obsługujący monitorowaną aplikację.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procent|Średnia, minimum, maksimum|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Współczynnik operacji we/wy procesu (liczniki wydajności/processIOBytesPerSecond)

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty na sekundę|Średnia, minimum, maksimum|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Prywatne bajty procesu (liczniki wydajności/processPrivateBytes)

Ilość pamięci nieudostępnionej, którą proces monitorowania przydzielił dla danych.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty|Średnia, minimum, maksimum|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Czas procesora (liczniki wydajności/processorCpuPercentage)

Użycie procesora CPU przez *wszystkie* procesy uruchomione w monitorowanym wystąpieniu serwera.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procent|Średnia, minimum, maksimum|Wystąpienie roli w chmurze

>[!NOTE]
> Metryka czasu procesora nie jest dostępna dla aplikacji hostowanych w usłudze Azure App Services. Metryka  [procesora CPU procesu](#process-cpu-performancecountersprocesscpupercentage) służy do śledzenia użycia procesora CPU przez aplikacje sieci Web hostowane w App Services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metryki serwera

### <a name="dependency-calls-dependenciescount"></a>Wywołania zależności (zależności/liczba)

Ta Metryka jest w odniesieniu do liczby wywołań zależności.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Czas trwania zależności (zależności/czas trwania)

Ta Metryka odnosi się do czasu trwania wywołań zależności.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Żądania serwera (żądania/liczba)

Ta Metryka odzwierciedla liczbę przychodzących żądań serwera, które zostały odebrane przez aplikację sieci Web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Czas odpowiedzi serwera (żądania/czas trwania)

Ta Metryka przedstawia czas przetwarzania żądań przychodzących przez serwery.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metryki użycia

### <a name="page-view-load-time-pageviewsduration"></a>Czas ładowania widoku strony (pageViews/czas trwania)

Ta Metryka odnosi się do czasu, jaki zajęło załadowanie zdarzeń PageView.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Wyświetlenia stron (pageViews/Count)

Liczba zdarzeń PageView zarejestrowanych przy użyciu interfejsu API Application Insights TrackPageView ().

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sesje (sesje/liczba)

Ta Metryka odnosi się do liczby unikatowych identyfikatorów sesji.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Ślady (ślady/liczba)

Liczba instrukcji Trace zarejestrowanych przy użyciu wywołania interfejsu API Application Insights TrackTrace ().

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Użytkownicy (Użytkownicy/liczba)

Liczba różnych użytkowników, którzy uzyskali dostęp do Twojej aplikacji. Dokładność tej metryki może mieć znaczny wpływ na próbkowanie i filtrowanie danych telemetrycznych.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Użytkownicy, uwierzytelnieni (Użytkownicy/uwierzytelnieni)

Liczba różnych użytkowników, którzy uwierzytelniali się w aplikacji.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```

