---
title: Model danych usługi Azure Application Insights | Microsoft Docs
description: Opisuje właściwości eksportowane z eksportu ciągłego w formacie JSON i używane jako filtry.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 29ad999c307d1c11e7a584b61d85ed73b9448cb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324390"
---
# <a name="application-insights-export-data-model"></a>Application Insights eksportowanie modelu danych
Ta tabela zawiera listę właściwości telemetrii wysyłanych z zestawów SDK [Application Insights](./app-insights-overview.md) do portalu.
Te właściwości będą widoczne w danych wyjściowych z [eksportu ciągłego](export-telemetry.md).
Są one również wyświetlane w filtrach właściwości w [Eksploratorze metryk](../platform/metrics-charts.md) i w [przeszukiwaniu diagnostycznym](./diagnostic-search.md).

Punkty do uwagi:

* `[0]` w tych tabelach wskazuje punkt w ścieżce, w którym trzeba wstawić indeks; ale nie zawsze jest równa 0.
* Czas trwania jest częścią mikrosekundowych, więc 10000000 = = 1 sekunda.
* Daty i godziny są UTC i są podawane w formacie ISO `yyyy-MM-DDThh:mm:ss.sssZ`

## <a name="example"></a>Przykład

```json
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
```

## <a name="context"></a>Kontekst
Wszystkie typy danych telemetrycznych są dołączone do sekcji kontekstowej. Nie wszystkie z tych pól są przesyłane do każdego punktu danych.

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |Obiekt [] |Pary ciągów klucz-wartość ustawione przez parametr właściwości niestandardowych. Maksymalna długość klucza 100, Maksymalna długość wartości 1024. Więcej niż 100 wartości unikatowych, właściwość można wyszukać, ale nie można jej użyć do segmentacji. Maksymalna 200 kluczy na iKey. |
| Context. Custom. Metrics [0] |Obiekt [] |Pary klucz-wartość są ustawiane za pomocą parametru pomiarów niestandardowych i TrackMetrics. Maksymalna długość klucza 100, wartość może być wartością numeryczną. |
| Context. Data. eventTime |ciąg |UTC |
| Context. Data. issyntetyczne |boolean |Żądanie prawdopodobnie pochodzi z testu bot lub sieci Web. |
| Context. Data. samplingRate |liczba |Procent danych telemetrycznych generowanych przez zestaw SDK, który jest wysyłany do portalu. Zakres 0.0-100,0. |
| Context. Device |object |Urządzenie klienckie |
| Context. Device. Browser |ciąg |IE, Chrome,... |
| Context. Device. browserVersion |ciąg |Chrome 48,0,... |
| Context. Device. deviceModel |ciąg | |
| Context. Device. DeviceName |ciąg | |
| context.device.id |ciąg | |
| Context. Device. locale |ciąg |pl GB, de-DE,... |
| Context. Device. Network |ciąg | |
| Context. Device. oemName |ciąg | |
| Context. Device. OS |ciąg | |
| Context. Device. osVersion |ciąg |System operacyjny hosta |
| Context. Device. roleInstance |ciąg |Identyfikator hosta serwera |
| Context. Device. rolename |ciąg | |
| Context. Device. screenResolution |ciąg | |
| Context. Device. Type |ciąg |KOMPUTER, przeglądarka,... |
| Context. Location |object |Pochodny od `clientip` . |
| Context. Location. City |ciąg |Pochodny od `clientip` , jeśli jest znany |
| Context. Location. ClientIP |ciąg |Ostatni ośmiokątem jest anonimowe na 0. |
| Context. Location. kontynent |ciąg | |
| Context. Location. Country |ciąg | |
| Context. Location. Prowincja |ciąg |Województwo |
| context.operation.id |ciąg |Elementy, które mają takie same `operation id` są wyświetlane jako elementy pokrewne w portalu. Zwykle `request id` . |
| context.operation.name |ciąg |adres URL lub nazwa żądania |
| Context. Operation. parentId |ciąg |Zezwala na zagnieżdżone elementy pokrewne. |
| context.session.id |ciąg |`Id` grupy operacji z tego samego źródła. Okres 30 minut bez operacji sygnalizuje zakończenie sesji. |
| Context. Session. isfirst |boolean | |
| Context. User. accountAcquisitionDate |ciąg | |
| Context. User. accountId |ciąg | |
| Context. User. anonAcquisitionDate |ciąg | |
| Context. User. anonId |ciąg | |
| Context. User. authAcquisitionDate |ciąg |[Uwierzytelniony użytkownik](./api-custom-events-metrics.md#authenticated-users) |
| Context. User. authId |ciąg | |
| Context. User. IsAuthenticated |boolean | |
| Context. User. storeRegion |ciąg | |
| internal.data.documentVersion |ciąg | |
| internal.data.id |ciąg | `Unique id` jest przypisywany, gdy element zostanie pozyskany do Application Insights |

## <a name="events"></a>Zdarzenia
Zdarzenia niestandardowe wygenerowane przez [poleceń trackEvent ()](./api-custom-events-metrics.md#trackevent).

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| liczba zdarzeń [0] |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład 4 = &gt; 25%. |
| Nazwa zdarzenia [0] |ciąg |Nazwa zdarzenia.  Maksymalna długość 250. |
| adres URL zdarzenia [0] |ciąg | |
| zdarzenie [0] urlData. Base |ciąg | |
| zdarzenie [0] urlData. host |ciąg | |

## <a name="exceptions"></a>Wyjątki
Zgłasza [wyjątki](./asp-net-exceptions.md) na serwerze i w przeglądarce.

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| zestaw basicexception [0] |ciąg | |
| Licznik basicexception [0] |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład 4 = &gt; 25%. |
| wyjątek basicexception [0] |ciąg | |
| wyjątek basicexception [0] |ciąg | |
| podstawowaexception [0] failedUserCodeMethod |ciąg | |
| podstawowaexception [0] failedUserCodeAssembly |ciąg | |
| podstawowaexception [0] handledAt |ciąg | |
| podstawowaexception [0] hasFullStack |boolean | |
| podstawowaexception [0] `id` |ciąg | |
| Metoda basicexception [0] |ciąg | |
| komunikat podstawowyexception [0] |ciąg |Komunikat o wyjątku. Maksymalna długość 10 znaków. |
| podstawowaexception [0] outerExceptionMessage |ciąg | |
| podstawowaexception [0] outerExceptionThrownAtAssembly |ciąg | |
| podstawowaexception [0] outerExceptionThrownAtMethod |ciąg | |
| podstawowaexception [0] outerExceptionType |ciąg | |
| podstawowaexception [0] outerId |ciąg | |
| zestaw basicexception [0] parsedStack [0] |ciąg | |
| podstawowaexception [0] parsedStack [0] fileName |ciąg | |
| podstawowyexception [0] parsedStack [0] |liczba całkowita | |
| Linia podstawowa [0] parsedStack [0] |liczba całkowita | |
| Podstawowa metoda [0] parsedStack [0] |ciąg | |
| stosexception [0] |ciąg |Maksymalna długość 10 tys. |
| podstawowaexception [0], typeName |ciąg | |

## <a name="trace-messages"></a>Komunikaty śledzenia
Wysyłane przez [TrackTrace](./api-custom-events-metrics.md#tracktrace)i [karty rejestrowania](./asp-net-trace-logs.md).

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| komunikat [0] rejestratorname |ciąg | |
| komunikat [0] parametry |ciąg | |
| komunikat [0] RAW |ciąg |Komunikat dziennika, Maksymalna długość 10 tys. |
| komunikat [0] severityLevel |ciąg | |

## <a name="remote-dependency"></a>Zależność zdalna
Wysłane przez TrackDependency. Służy do zgłaszania wydajności i użycia [wywołań do zależności](./asp-net-dependencies.md) na serwerze i wywołań AJAX w przeglądarce.

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| remoteDependency [0] Async |boolean | |
| remoteDependency [0] basename |ciąg | |
| remoteDependency [0] CommandName |ciąg |Na przykład "Strona główna/Indeks" |
| remoteDependency [0] liczba |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |ciąg |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |liczba |Czas od wywołania do ukończenia odpowiedzi według zależności |
| remoteDependency [0] `id` |ciąg | |
| remoteDependency [0] nazwa |ciąg |Adres URL. Maksymalna długość 250. |
| remoteDependency [0] resultCode |ciąg |z zależności HTTP |
| remoteDependency [0] sukces |boolean | |
| remoteDependency [0] typ |ciąg |Http,,... SQL |
| adres URL remoteDependency [0] |ciąg |Maksymalna długość 2000 |
| remoteDependency [0] urlData. Base |ciąg |Maksymalna długość 2000 |
| remoteDependency [0] urlData. hasztagów |ciąg | |
| remoteDependency [0] urlData. host |ciąg |Maksymalna długość 200 |

## <a name="requests"></a>Żądania
Wysłane przez [TrackRequest](./api-custom-events-metrics.md#trackrequest). Moduły standardowe używają tego do raportowania czasu odpowiedzi serwera, mierzoną na serwerze.

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| Liczba żądań [0] |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład: 4 = &gt; 25%. |
| żądanie [0] durationMetric. Value |liczba |Czas od żądania do odpowiedzi. 1e7 = = 1S |
| żądanie [0] `id` |ciąg |`Operation id` |
| Nazwa żądania [0] |ciąg |GET/POST + URL Base.  Maksymalna długość 250 |
| żądanie [0] responseCode |liczba całkowita |Odpowiedź HTTP wysłana do klienta |
| żądanie [0] zakończyło się pomyślnie |boolean |Wartość domyślna = = (responseCode &lt; 400) |
| adres URL żądania [0] |ciąg |Nie dołączaj hosta |
| żądanie [0] urlData. Base |ciąg | |
| żądanie [0] urlData. hasztagów |ciąg | |
| żądanie [0] urlData. host |ciąg | |

## <a name="page-view-performance"></a>Wydajność wyświetlania stron
Wysyłane przez przeglądarkę. Mierzy czas przetwarzania strony od użytkownika inicjującego żądanie wyświetlenia zakończone (z wyłączeniem asynchronicznych wywołań AJAX).

Wartości kontekstu pokazują system operacyjny klienta i wersję przeglądarki.

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |liczba całkowita |Czas od końca odebrania kodu HTML w celu wyświetlenia strony. |
| clientPerformance [0] nazwa |ciąg | |
| clientPerformance [0] networkConnection. Value |liczba całkowita |Czas nawiązywania połączenia sieciowego. |
| clientPerformance [0] receiveRequest. Value |liczba całkowita |Czas od końca wysłania żądania do odebrania kodu HTML w odpowiedzi. |
| clientPerformance [0] sendRequest. Value |liczba całkowita |Czas od wykonania w celu wysłania żądania HTTP. |
| clientPerformance [0] suma. wartość |liczba całkowita |Czas od rozpoczęcia wysłania żądania w celu wyświetlenia strony. |
| adres URL clientPerformance [0] |ciąg |Adres URL tego żądania |
| clientPerformance [0] urlData. Base |ciąg | |
| clientPerformance [0] urlData. hasztagów |ciąg | |
| clientPerformance [0] urlData. host |ciąg | |
| clientPerformance [0] urlData. Protocol |ciąg | |

## <a name="page-views"></a>Wyświetlenia strony
Wysłane przez trackPageView () lub [stopTrackPage](./api-custom-events-metrics.md#page-views)

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| Widok [0] |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład 4 = &gt; 25%. |
| Widok [0] durationMetric. Value |liczba całkowita |Wartość Opcjonalnie ustawiona w trackPageView () lub przez startTrackPage ()-stopTrackPage (). Nie są takie same jak wartości clientPerformance. |
| Widok [0] nazwa |ciąg |Tytuł strony.  Maksymalna długość 250 |
| Wyświetl [0] URL |ciąg | |
| Widok [0] urlData. Base |ciąg | |
| Widok [0] urlData. hasztagów |ciąg | |
| Widok [0] urlData. host |ciąg | |

## <a name="availability"></a>Dostępność
Raportuje [testy sieci Web dostępności](./monitor-web-app-availability.md).

| Ścieżka | Type | Uwagi |
| --- | --- | --- |
| Dostępność [0] availabilityMetric.name |ciąg |availability |
| Dostępność [0] availabilityMetric. Value |liczba |1,0 lub 0,0 |
| Dostępność [0] |liczba całkowita |100/(częstotliwość[próbkowania](./sampling.md) ). Na przykład 4 = &gt; 25%. |
| Dostępność [0] dataSizeMetric.name |ciąg | |
| Dostępność [0] dataSizeMetric. Value |liczba całkowita | |
| Dostępność [0] durationMetric.name |ciąg | |
| Dostępność [0] durationMetric. Value |liczba |Czas trwania testu. 1e7 = = 1S |
| komunikat o dostępności [0] |ciąg |Diagnostyka niepowodzeń |
| Dostępność [0] wynik |ciąg |Powodzenie/niepowodzenie |
| Dostępność [0] runLocation |ciąg |Źródło geograficzne żądania http |
| Dostępność [0] TestName |ciąg | |
| Dostępność [0] testRunId |ciąg | |
| Dostępność [0] testTimestamp |ciąg | |

## <a name="metrics"></a>Metryki
Wygenerowano przez TrackMetric ().

Wartość metryki znajduje się w kontekście. Custom. Metrics [0]

Na przykład:

```json
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
      ]  
    }
  }
}
```

## <a name="about-metric-values"></a>Informacje o wartościach metryk
Wartości metryk, zarówno w raportach metryk, jak i w innym miejscu, są raportowane przy użyciu standardowej struktury obiektów. Na przykład:

```json
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
```

Obecnie ta sytuacja może ulec zmianie w przyszłości — wszystkie wartości raportowane przez standardowe moduły SDK `count==1` i są przydatne tylko w przypadku `name` `value` pól i. Jedyną sytuacją, w której byłyby różne, byłoby napisanie własnych wywołań TrackMetric, w których są ustawiane inne parametry.

Celem innych pól jest umożliwienie agregowania metryk w zestawie SDK w celu zmniejszenia ruchu do portalu. Na przykład można obliczyć kilka kolejnych odczytów przed wysłaniem poszczególnych raportów metryk. Następnie można obliczyć wartości minimalną, maksymalną, odchylenie standardowe i wartość zagregowaną (sum lub Average) i ustawić liczbę odczytów reprezentowanych przez raport.

W powyższych tabelach pominięto rzadko używane pola Count, min, Max, stdDev i sampledValue.

Zamiast wstępnie agregowanych metryk można użyć [próbkowania](./sampling.md) , jeśli zachodzi potrzeba zmniejszenia ilości danych telemetrycznych.

### <a name="durations"></a>Czasów trwania
O ile nie zaznaczono inaczej, czasy trwania są reprezentowane w dziesiątce mikrosekundowych, więc 10000000,0 oznacza 1 sekundę.

## <a name="see-also"></a>Zobacz też
* [Application Insights](./app-insights-overview.md)
* [Eksport ciągły](export-telemetry.md)
* [Przykłady kodu](export-telemetry.md#code-samples)

