---
title: Metryki usługi Azure Application Insights Standard | Microsoft Docs
description: W tym artykule przedstawiono metryki usługi Azure Application Insights z obsługiwanymi agregacjami i wymiarami.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572359"
---
# <a name="application-insights-standard-metrics"></a>Application Insights metryki standardowych

Metryki standardowe są wstępnie agregowane podczas zbierania, ale mają lepszą wydajność w czasie wykonywania zapytania. Dzięki temu najlepszym wyborem dla pulpitów nawigacyjnych i alertów w czasie rzeczywistym.

## <a name="availability-metrics"></a>Metryki dostępności

Metryki w kategorii dostępność umożliwiają sprawdzenie kondycji aplikacji sieci Web zaobserwowanej z punktów na całym świecie. [Skonfiguruj testy dostępności,](../app/monitor-web-app-availability.md) aby rozpocząć korzystanie z dowolnych metryk z tej kategorii.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostępność (availabilityResults/availabilityPercentage)
Metryka *dostępności* przedstawia wartość procentową przebiegów testów sieci Web, które nie wykryły żadnych problemów. Najmniejsza możliwa wartość to 0, co oznacza, że wszystkie uruchomienia testu sieci Web nie powiodły się. Wartość 100 oznacza, że wszystkie uruchomienia testu sieci Web przeszły kryteria walidacji.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Procent|Średnia|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Czas trwania testu dostępności (availabilityResults/Duration)

Metryka *czas trwania testu dostępności* pokazuje, ile czasu zajęło uruchomienie testu sieci Web. W przypadku [wieloetapowych testów sieci Web](../app/availability-multistep.md)Metryka wskazuje całkowity czas wykonywania wszystkich kroków.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|)|Średnia, minimum, maksimum|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Testy dostępności (availabilityResults/Count)

Metryki *testów dostępności* odzwierciedlają liczbę testów sieci Web wykonywanych przez Azure monitor.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Liczba|Liczba|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Metryki przeglądarki

Metryki przeglądarki są zbierane przez zestaw Application Insights JavaScript SDK z rzeczywistych przeglądarek użytkowników końcowych. Zapewniają doskonałe informacje o doświadczeniu użytkowników w Twojej aplikacji sieci Web. Metryki przeglądarki zazwyczaj nie są próbkowane, co oznacza, że zapewniają większą precyzję numerów użycia w porównaniu z metrykami po stronie serwera, które mogą być pochylone przez próbkowanie.

> [!NOTE]
> Aby zbierać metryki przeglądarki, aplikacja musi być Instrumentacja przy użyciu [zestawu SDK języka JavaScript Application Insights](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Czas ładowania strony w przeglądarce (browserTimings/totalDuration)

|Jednostka miary|Obsługiwane agregacje| Obsługiwane wymiary|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Czas przetwarzania klienta (browserTiming/processingDuration)

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Czas połączenia sieciowego ładowania strony (browserTimings/networkDuration)

|Jednostka miary|Obsługiwane agregacje| Obsługiwane wymiary|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Czas odpowiedzi na odebranie (browserTimings/receiveDuration)

|Jednostka miary|Obsługiwane agregacje| Obsługiwane wymiary|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

### <a name="send-request-time-browsertimingssendduration"></a>Czas żądania wysłania (browserTimings/sendDuration)

|Jednostka miary|Obsługiwane agregacje| Obsługiwane wymiary|
|---|---|---|
|)|Średnia, minimum, maksimum|Brak|

## <a name="failure-metrics"></a>Metryki błędów

Metryki w **błędach** pokazują problemy związane z przetwarzaniem żądań, wywołaniami zależności i zgłoszonymi wyjątkami.

### <a name="browser-exceptions-exceptionsbrowser"></a>Wyjątki przeglądarki (wyjątki/przeglądarka)

Ta Metryka odzwierciedla liczbę zgłoszonych wyjątków z kodu aplikacji działającego w przeglądarce. W metryce są uwzględniane tylko wyjątki, które są śledzone za pomocą ```trackException()``` wywołania interfejsu API Application Insights.

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary|
|---|---|---|---|
| Liczba | Liczba | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Błędy wywołań zależności (zależności/niepowodzenie)

Liczba wywołań zależności zakończonych niepowodzeniem.

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
|Liczba|Liczba| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Wyjątki (wyjątki/liczba)

Za każdym razem, gdy rejestrujesz wyjątek do Application Insights, istnieje wywołanie [metody trackexception ()](../app/api-custom-events-metrics.md#trackexception) zestawu SDK. Metryka wyjątków pokazuje liczbę zarejestrowanych wyjątków.

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
|Liczba|Liczba|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Nieudane żądania (żądania/niepowodzenie)

Liczba śledzonych żądań serwera, które zostały oznaczone jako *zakończone niepowodzeniem*. Domyślnie zestaw SDK Application Insights automatycznie oznacza każde żądanie serwera, które zwróciło kod odpowiedzi HTTP 5xx lub 4xx jako żądanie zakończone niepowodzeniem. Tę logikę można dostosować, modyfikując właściwość  *Success* elementu telemetrii żądania w [niestandardowym inicjatorze telemetrii](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
|Liczba|Liczba|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Wyjątki serwera (wyjątki/serwer)

Ta Metryka przedstawia liczbę wyjątków serwera.

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
|Liczba|Liczba|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Liczniki wydajności

Użyj metryk w kategorii **liczniki wydajności** , aby uzyskać dostęp do [liczników wydajności systemu zbieranych przez Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostępna pamięć (liczniki wydajności/availableMemory)

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
|Zależne od danych: megabajty, gigabajty|Average, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Częstotliwość wyjątków (liczniki wydajności/exceptionRate)

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
| Liczba | Average, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Czas wykonywania żądania HTTP (liczniki wydajności/requestExecutionTime)

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
| ) | Average, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Częstotliwość żądań HTTP (liczniki wydajności/requestsPerSecond)

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
| Żądania na sekundę | Average, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Żądania HTTP w kolejce aplikacji (liczniki wydajności/requestsInQueue)

|Jednostka miary|Obsługiwane agregacje | Obsługiwane wymiary |
|---|---|---|---|
| Liczba | Average, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Procesor CPU procesu (liczniki wydajności/processCpuPercentage)

Metryka pokazuje, ile całkowitej pojemności procesora jest zużywanych przez proces obsługujący monitorowaną aplikację.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procent|Average, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Współczynnik operacji we/wy procesu (liczniki wydajności/processIOBytesPerSecond)

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty na sekundę|Średnia, minimum, maksimum|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Prywatne bajty procesu (liczniki wydajności/processPrivateBytes)

Ilość pamięci nieudostępnionej, którą proces monitorowania przydzielił dla danych.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty|Średnia, minimum, maksimum|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Czas procesora (liczniki wydajności/processorCpuPercentage)

Użycie procesora CPU przez *wszystkie* procesy uruchomione w monitorowanym wystąpieniu serwera.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procent|Średnia, minimum, maksimum|`Cloud role instance` |

>[!NOTE]
> Metryka czasu procesora nie jest dostępna dla aplikacji hostowanych w usłudze Azure App Services. Metryka  [procesora CPU procesu](#process-cpu-performancecountersprocesscpupercentage) służy do śledzenia użycia procesora CPU przez aplikacje sieci Web hostowane w App Services.

## <a name="server-metrics"></a>Metryki serwera

### <a name="dependency-calls-dependenciescount"></a>Wywołania zależności (zależności/liczba)

Ta Metryka jest w odniesieniu do liczby wywołań zależności.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Liczba | Liczba | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Czas trwania zależności (zależności/czas trwania)

Ta Metryka odnosi się do czasu trwania wywołań zależności.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Godzina | Średnia, minimum, maksimum | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Liczba żądań serwera (żądania/liczba)

Ta Metryka odzwierciedla liczbę przychodzących żądań serwera, które zostały odebrane przez aplikację sieci Web.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Liczba | Średnia | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Żądania serwera (żądania/liczba)

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Liczba | Liczba | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Czas odpowiedzi serwera (żądania/czas trwania)

Ta Metryka przedstawia czas przetwarzania żądań przychodzących przez serwery.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Godzina | Średnia, minimum, maksimum | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Metryki użycia

### <a name="page-view-load-time-pageviewsduration"></a>Czas ładowania widoku strony (pageViews/czas trwania)

Ta Metryka odnosi się do czasu, jaki zajęło załadowanie zdarzeń PageView.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Godzina | Średnia, minimum, maksimum | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Wyświetlenia stron (pageViews/Count)

Liczba zdarzeń PageView zarejestrowanych przy użyciu interfejsu API Application Insights TrackPageView ().

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Liczba | Liczba | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Ślady (ślady/liczba)

Liczba instrukcji Trace zarejestrowanych przy użyciu wywołania interfejsu API Application Insights TrackTrace ().

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
| Liczba | Liczba | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [metrykach opartych na dzienniku i wstępnie agregowanych](./pre-aggregated-metrics-log-metrics.md).
* [Zapytania i definicje metryk opartych na dziennikach](../essentials/app-insights-metrics.md).