---
title: Model danych usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Zawiera opis właściwości eksportowanych z ciągłego eksportu w JSON i używanych jako filtry.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 9891bea1d52c61197fa32fa5c0764df5450b563c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536849"
---
# <a name="application-insights-export-data-model"></a>Model danych eksportu aplikacji Insights
W tej tabeli wymieniono właściwości danych telemetrycznych wysyłanych z [modułów](../../azure-monitor/app/app-insights-overview.md) SDK usługi Application Insights do portalu.
Te właściwości zostaną wyświetlone w danych wyjściowych z [ciągłego eksportu](export-telemetry.md).
Pojawiają się one również w filtrach właściwości w [Eksploratorze metryk](../../azure-monitor/platform/metrics-charts.md) i [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md).

Uwagi do uwag:

* `[0]`w tych tabelach oznacza punkt w ścieżce, w którym trzeba wstawić indeks; ale nie zawsze jest 0.
* Czas trwania jest w dziesiątych sekund mikrosekundy, więc 10000000 == 1 sekunda.
* Daty i godziny są UTC i są podane w formacie ISO`yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Przykład
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Kontekst
Wszystkim typom danych telemetrycznych towarzyszy sekcja kontekstu. Nie wszystkie z tych pól są przesyłane z każdym punktem danych.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| context.custom.dimensions [0] |obiekt [ ] |Pary ciągów klucz-wartość ustawione przez parametr właściwości niestandardowych. Klucz maksymalna długość 100, wartości max długość 1024. Więcej niż 100 unikatowych wartości, właściwość może być przeszukiwana, ale nie może być używana do segmentacji. Maksymalnie 200 klawiszy na ikey. |
| context.custom.metrics [0] |obiekt [ ] |Pary klucz-wartość ustawione przez parametr pomiarów niestandardowych i trackmetrics. Klawisz maksymalna długość 100, wartości mogą być liczbowe. |
| context.data.eventCzas |ciąg |UTC |
| context.data.isSyntezy |wartość logiczna |Żądanie wydaje się pochodzić z bota lub testu sieci web. |
| context.data.samplingAtRate |numer |Procent danych telemetrycznych generowanych przez sdk, który jest wysyłany do portalu. Zakres 0,0-100,0. |
| context.device |obiekt |Urządzenie klienckie |
| context.device.browser |ciąg |IE, Chrome, ... |
| context.device.browserVersion |ciąg |Chrome 48.0, ... |
| context.device.deviceModel |ciąg | |
| context.device.deviceName |ciąg | |
| context.device.id |ciąg | |
| context.device.locale |ciąg |pl-GB, de-DE, ... |
| context.device.network |ciąg | |
| context.device.oemName |ciąg | |
| context.device.os |ciąg | |
| context.device.osVersion |ciąg |System operacyjny hosta |
| context.device.roleInstance |ciąg |Identyfikator hosta serwera |
| context.device.roleName |ciąg | |
| rozwiązanie context.device.screenRozrozmienie |ciąg | |
| context.device.type |ciąg |PC, Przeglądarka, ... |
| context.location |obiekt |Pochodzi z `clientip`. |
| context.location.city |ciąg |Pochodna `clientip`, jeśli znana |
| context.location.clientip |ciąg |Ostatni ośmiokąt jest anonimizowany do 0. |
| context.location.continent |ciąg | |
| context.location.country |ciąg | |
| context.location.province |ciąg |Województwo |
| context.operation.id |ciąg |Elementy, które `operation id` mają takie same są wyświetlane jako elementy pokrewne w portalu. Zazwyczaj `request id`. |
| context.operation.name |ciąg |nazwa adresu url lub żądania |
| element context.operation.parentId |ciąg |Zezwala na zagnieżdżone powiązane elementy. |
| context.session.id |ciąg |`Id`grupy operacji z tego samego źródła. Okres 30 minut bez operacji sygnalizuje koniec sesji. |
| context.session.isPieramy |wartość logiczna | |
| context.user.accountAcquisitionDate |ciąg | |
| identyfikator context.user.accountId |ciąg | |
| context.user.anonAcquisitionDate |ciąg | |
| identyfikator context.user.anonId |ciąg | |
| context.user.authAcquisitionDate |ciąg |[Uwierzytelniony użytkownik](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| identyfikator context.user.authId |ciąg | |
| context.user.isAuthenticated |wartość logiczna | |
| context.user.storeRegion |ciąg | |
| internal.data.documentWersja |ciąg | |
| internal.data.id |ciąg | `Unique id`który jest przypisywany, gdy element jest połajany do usługi Application Insights |

## <a name="events"></a>Zdarzenia
Zdarzenia niestandardowe generowane przez [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| zdarzenie [0] liczyć |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład 4 =&gt; 25%. |
| nazwa zdarzenia [0] |ciąg |Nazwa zdarzenia.  Maksymalna długość 250. |
| zdarzenie [0] adres URL |ciąg | |
| zdarzenie [0] urlData.base |ciąg | |
| zdarzenie [0] urlData.host |ciąg | |

## <a name="exceptions"></a>Wyjątki
Zgłasza [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) na serwerze i w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| basicException [0] montaż |ciąg | |
| podstawowaWyjęcie [0] |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład 4 =&gt; 25%. |
| basicException [0] exceptionGroup |ciąg | |
| basicException [0] exceptionType |ciąg | |
| basicException [0] nie powiodło sięUżycielodowy |ciąg | |
| basicException [0] nie powiodło sięUserCodeAssembly |ciąg | |
| basicException [0] handledAt |ciąg | |
| basicException [0] maFullStack |wartość logiczna | |
| basicException [0]`id` |ciąg | |
| metoda podstawowaEkstawa [0] |ciąg | |
| basicException [0] wiadomość |ciąg |Komunikat o wyjątku. Maksymalna długość 10k. |
| basicException [0] outerExceptionMessage |ciąg | |
| basicException [0] outerExceptionThrownAtAssembly basic |ciąg | |
| basicException [0] outerExceptionThrownAtMetoda |ciąg | |
| basicException [0] outerExceptionType |ciąg | |
| basicException [0] outerId |ciąg | |
| basicException [0] parsedStack [0] montaż |ciąg | |
| basicException [0] parsedStack [0] fileName |ciąg | |
| basicException [0] parsedStack [0] poziom |liczba całkowita | |
| basicException [0] parsedStack [0] linia |liczba całkowita | |
| basicException [0] parsedStack [0] metoda |ciąg | |
| basicException [0] stos |ciąg |Maksymalna długość 10k |
| basicException [0] typeName |ciąg | |

## <a name="trace-messages"></a>Śledzenie komunikatów
Wysyłane przez [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)i [przez karty rejestrowania](../../azure-monitor/app/asp-net-trace-logs.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| komunikat [0] loggerName |ciąg | |
| parametry komunikatu [0] |ciąg | |
| wiadomość [0] nieprzetworzona |ciąg |Komunikat dziennika, maksymalna długość 10k. |
| wiadomość [0] severityPoziom |ciąg | |

## <a name="remote-dependency"></a>Zależność zdalna
Wysyłane przez TrackDependency. Służy do raportowania wydajności i użycia [wywołań zależności](../../azure-monitor/app/asp-net-dependencies.md) na serwerze i wywołania AJAX w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| remoteDependency [0] asynchronii |wartość logiczna | |
| remoteDependency [0] baseName |ciąg | |
| remoteDependency [0] commandName |ciąg |Na przykład "home/index" |
| liczba remoteDependency [0] |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład 4 =&gt; 25%. |
| zależność remoteDependency [0]TypeName |ciąg |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |numer |Czas od wywołania do zakończenia odpowiedzi według zależności |
| remoteDependency [0]`id` |ciąg | |
| nazwa remoteDependency [0] |ciąg |Adres url. Maksymalna długość 250. |
| remoteDependency [0] wynikKodko |ciąg |od zależności HTTP |
| remoteDependency [0] sukces |wartość logiczna | |
| typ remoteDependency [0] |ciąg |Http, Sql,... |
| remoteDependency [0] url |ciąg |Maksymalna długość 2000 |
| remoteDependency [0] urlData.base |ciąg |Maksymalna długość 2000 |
| remoteDependency [0] urlData.hashTag |ciąg | |
| remoteDependency [0] urlData.host |ciąg |Maksymalna długość 200 |

## <a name="requests"></a>Żądania
Wysyłane przez [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Moduły standardowe używają tego do raportów czas odpowiedzi serwera, mierzony na serwerze.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| żądanie [0] zliczanie |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład: 4 =&gt; 25%. |
| żądanie [0] durationMetric.value |numer |Czas od przybycia żądania do odpowiedzi. 1e7 == 1s |
| wniosek [0]`id` |ciąg |`Operation id` |
| nazwa żądania [0] |ciąg |GET/POST + baza adresów URL.  Maksymalna długość 250 |
| żądanie [0] responseCode |liczba całkowita |Odpowiedź HTTP wysłana do klienta |
| prośba o sukces [0] |wartość logiczna |Domyślnie == &lt; (responseCode 400) |
| adres URL żądania [0] |ciąg |Nie wliczając hosta |
| żądanie [0] urlData.base |ciąg | |
| żądanie [0] urlData.hashTag |ciąg | |
| żądanie [0] urlData.host |ciąg | |

## <a name="page-view-performance"></a>Wydajność widoku strony
Wysyłane przez przeglądarkę. Mierzy czas przetwarzania strony, od użytkownika inicjującego żądanie do wyświetlania kompletne (z wyłączeniem asynchronicznego wywołania AJAX).

Wartości kontekstu pokazują system operacyjny klienta i wersję przeglądarki.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |liczba całkowita |Czas od zakończenia odbierania kodu HTML do wyświetlania strony. |
| nazwa clientPerformance [0] |ciąg | |
| clientPerformance [0] networkConnection.value |liczba całkowita |Czas na nawiązanie połączenia sieciowego. |
| clientPerformance [0] receiveRequest.value |liczba całkowita |Czas od zakończenia wysyłania żądania do odbierania kodu HTML w odpowiedzi. |
| clientPerformance [0] sendRequest.value |liczba całkowita |Czas od wysłania żądania HTTP. |
| clientPerformance [0] całkowita.wartość |liczba całkowita |Czas od rozpoczęcia wysyłania żądania do wyświetlania strony. |
| adres URL clientPerformance [0] |ciąg |Adres URL tego żądania |
| clientPerformance [0] urlData.base |ciąg | |
| clientPerformance [0] urlData.hashTag |ciąg | |
| clientPerformance [0] urlData.host |ciąg | |
| clientPerformance [0] urlData.protocol |ciąg | |

## <a name="page-views"></a>Wyświetlenia strony
Wysłane przez trackPageView() lub [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| wyświetl [0] zliczanie |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład 4 =&gt; 25%. |
| wyświetl [0] czas trwaniaMetric.value |liczba całkowita |Wartość opcjonalnie ustawiona w trackPageView() lub startTrackPage() - stopTrackPage(). Nie takie same jak clientPerformance wartości. |
| zobacz [0] nazwa |ciąg |Tytuł strony.  Maksymalna długość 250 |
| wyświetl [0] url |ciąg | |
| widok [0] urlData.base |ciąg | |
| view [0] urlData.hashTag |ciąg | |
| wyświetl [0] urlData.host |ciąg | |

## <a name="availability"></a>Dostępność
Raporty [dostępności testów sieci web](../../azure-monitor/app/monitor-web-app-availability.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| dostępność [0] availabilityMetric.name |ciąg |availability |
| dostępność [0] availabilityMetric.value |numer |1,0 lub 0,0 |
| liczba dostępności [0] |liczba całkowita |100/([częstotliwość pobierania próbek).](../../azure-monitor/app/sampling.md) Na przykład 4 =&gt; 25%. |
| dostępność [0] dataSizeMetric.name |ciąg | |
| dostępność [0] dataSizeMetric.value |liczba całkowita | |
| dostępność [0] durationMetric.name |ciąg | |
| dostępność [0] czas trwaniaMetric.value |numer |Czas trwania badania. 1e7==1 s |
| komunikat o dostępności [0] |ciąg |Diagnostyka awarii |
| dostępność [0] wynik |ciąg |Powodzenie/niepowodzenie |
| dostępność [0] runLocation |ciąg |Źródło geograficzne http req |
| dostępność [0] testName |ciąg | |
| dostępność [0] testRunId |ciąg | |
| dostępność [0] testTimestamp |ciąg | |

## <a name="metrics"></a>Metryki
Generowane przez TrackMetric().

Wartość metryki znajduje się w pliku context.custom.metrics[0]

Przykład:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Informacje o wartościach metrycznych
Wartości metryki, zarówno w raportach metryk, jak i w innych miejscach, są zgłaszane ze standardową strukturą obiektów. Przykład:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Obecnie - choć może się to zmienić w przyszłości - we wszystkich `count==1` wartościach `name` `value` zgłoszonych ze standardowych modułów SDK i tylko pola i są przydatne. Jedynym przypadkiem, w którym byłyby one różne byłoby, jeśli piszesz własne wywołania TrackMetric, w którym można ustawić inne parametry.

Celem innych pól jest umożliwienie metryki mają być agregowane w zestawie SDK, aby zmniejszyć ruch do portalu. Na przykład można uśrednić kilka kolejnych odczytów przed wysłaniem każdego raportu metryki. Następnie należy obliczyć wartość min, max, odchylenia standardowego i łącznej (suma lub średnia) i ustawić liczbę odczytów reprezentowanych przez raport.

W powyższych tabelach pominięto rzadko używane pola count, min, max, stdDev i sampledValue.

Zamiast wstępnej agregacji metryki, można użyć [próbkowania,](../../azure-monitor/app/sampling.md) jeśli trzeba zmniejszyć ilość danych telemetrycznych.

### <a name="durations"></a>Czas trwania
O ile nie zaznaczono inaczej, czas trwania jest reprezentowany w dziesiątych sekundach mikrosekundy, tak że 10000000.0 oznacza 1 sekundę.

## <a name="see-also"></a>Zobacz też
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Ciągły eksport](export-telemetry.md)
* [Przykłady kodu](export-telemetry.md#code-samples)
