---
title: Źródła zdarzeń pozyskiwania strumieniowego — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o przesyłaniu strumieniowym danych do Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4e22d93d3037c190193f53b7cfdbc87cff2da6ed
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504400"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights źródła zdarzeń Gen2

Środowisko Azure Time Series Insights Gen2 może mieć do dwóch źródeł zdarzeń przesyłania strumieniowego. Jako dane wejściowe są obsługiwane dwa typy zasobów platformy Azure:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Zdarzenia muszą być wysyłane jako zakodowany w formacie UTF-8 kod JSON.

## <a name="create-or-edit-event-sources"></a>Tworzenie lub edytowanie źródeł zdarzeń

Źródłem zdarzenia jest link między centrum a Azure Time Series Insights środowiskiem Gen2 i `Time Series Insights event source` w grupie zasobów jest tworzony oddzielny zasób typu. IoT Hub lub zasoby centrum zdarzeń mogą znajdować się w tej samej subskrypcji platformy Azure co środowisko Azure Time Series Insights Gen2 lub inną subskrypcję. Jednak najlepszym rozwiązaniem jest otoczenie środowiska Azure Time Series Insights i IoT Hub lub centrum zdarzeń w tym samym regionie świadczenia usługi Azure.

Możesz użyć [Azure Portal](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source), [szablonów Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md)i [interfejsu API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) do tworzenia, edytowania lub usuwania źródeł zdarzeń środowiska.

## <a name="start-options"></a>Opcje uruchamiania

Podczas tworzenia źródła zdarzeń można określić, jakie istniejące dane mają być zbierane. To ustawienie jest opcjonalne. Dostępne są następujące opcje:

| Nazwa   |  Opis  |  Przykład szablonu Azure Resource Manager |
|----------|-------------|------|
| EarliestAvailable | Pozyskiwanie wszystkich wstępnie istniejących danych przechowywanych w usłudze IoT lub centrum zdarzeń | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Rozpocznij pozyskiwanie danych, które docierają po utworzeniu źródła zdarzenia. Wszystkie istniejące wcześniej dane, które były przesyłane strumieniowo przed utworzeniem źródła zdarzenia, zostaną zignorowane. Jest to ustawienie domyślne w Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Twoje środowisko pozyskuje dane z niestandardowego czasu w kolejce (UTC) do przodu. Wszystkie zdarzenia, które zostały dodane do kolejki IoT lub centrum zdarzeń w lub po niestandardowym czasie zapamiętania, zostaną przechowane i zapisane. Wszystkie zdarzenia, które dotarły przed czasem niestandardowego, zostaną zignorowane. Należy pamiętać, że "czas w kolejce" odnosi się do czasu (w formacie UTC), który zdarzenie dotarło do centrum IoT lub centrów zdarzeń. Różni się to od niestandardowej [Właściwości sygnatury czasowej](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) , która znajduje się w treści zdarzenia. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Jeśli wybierzesz pozycję EarliestAvailable i masz wiele istniejących danych, może wystąpić wysokie początkowe opóźnienie, ponieważ środowisko Azure Time Series Insights Gen2 przetwarza wszystkie dane.
> - To duże opóźnienie powinno być ostatecznie umieszczone jako dane są indeksowane. Prześlij bilet pomocy technicznej za pomocą Azure Portal, jeśli wystąpią duże opóźnienia.

* EarliestAvailable

![Diagram EarliestAvailable](media/concepts-streaming-event-sources/event-source-earliest-available.png)

* EventSourceCreationTime

![Diagram EventSourceCreationTime](media/concepts-streaming-event-sources/event-source-creation-time.png)

* CustomEnqueuedTime

![Diagram CustomEnqueuedTime](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)


## <a name="streaming-ingestion-best-practices"></a>Najlepsze rozwiązania pozyskiwania strumieniowego

- Zawsze należy utworzyć unikatową grupę odbiorców dla środowiska Azure Time Series Insights Gen2, aby używać danych ze źródła zdarzeń. Ponowne użycie grup odbiorców może spowodować losowe odłączenie i może spowodować utratę danych.

- Skonfiguruj środowisko Azure Time Series Insights Gen2 i IoT Hub i/lub Event Hubs w tym samym regionie świadczenia usługi Azure. Chociaż istnieje możliwość skonfigurowania źródła zdarzeń w osobnym regionie, ten scenariusz nie jest obsługiwany, a firma Microsoft nie może zagwarantować wysokiej dostępności.

- Nie należy przekroczyć [limitu szybkości przepływności](./concepts-streaming-ingress-throughput-limits.md) dla środowiska lub limitu partycji.

- Skonfiguruj [alert](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) z opóźnieniem, aby otrzymywać powiadomienia o problemach z przetwarzaniem danych w środowisku. Zobacz poniższe [obciążenia produkcyjne](./concepts-streaming-ingestion-event-sources.md#production-workloads) w przypadku sugerowanych warunków alertów.

- Używaj pozyskiwania strumieniowego w przypadku niemal czasu rzeczywistego i ostatnich danych, ale dane historyczne przesyłania strumieniowego nie są obsługiwane.

- Dowiedz się, w jaki sposób właściwości będą wyprowadzane, a dane JSON są [spłaszczone i przechowywane.](./concepts-json-flattening-escaping-rules.md)

- Stosuj zasadę najniższych uprawnień podczas udostępniania parametrów połączenia źródła zdarzeń. W przypadku Event Hubs Skonfiguruj zasady dostępu współdzielonego tylko przy użyciu tylko żądania *wysyłania* , a dla IoT Hub Użyj tylko uprawnienia *usługi Connect* .

> [!CAUTION]
> Jeśli usuniesz IoT Hub lub centrum zdarzeń i ponownie utworzysz nowy zasób o tej samej nazwie, musisz utworzyć nowe źródło zdarzenia i dołączyć nowe IoT Hub lub centrum zdarzeń. Dane nie zostaną pozyskane do momentu ukończenia tego kroku.

## <a name="production-workloads"></a>Obciążenia produkcyjne

Oprócz najlepszych praktyk zaleca się wdrożenie następujących rozwiązań dla obciążeń krytycznych dla działalności biznesowej.

- Zwiększ czas przechowywania danych IoT Hub lub centrum zdarzeń do maksymalnie siedmiu dni.

- Utwórz alerty środowiska w Azure Portal. Alerty oparte na [metrykach](./how-to-monitor-tsi-reference.md#metrics) platformy umożliwiają Weryfikowanie kompleksowego zachowania potoku. Instrukcje dotyczące tworzenia alertów i zarządzania nimi znajdują się [tutaj](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts). Sugerowane warunki alertu:

  - IngressReceivedMessagesTimeLag jest większa niż 5 minut
  - IngressReceivedBytes jest 0
- Utrzymuj zrównoważenie obciążenia pozyskiwania między IoT Hubami i partycjami centrum zdarzeń.

### <a name="historical-data-ingestion"></a>Pozyskiwanie danych historycznych

Korzystanie z potoku przesyłania strumieniowego do importowania danych historycznych nie jest obecnie obsługiwane w Azure Time Series Insights Gen2. Jeśli zachodzi potrzeba zaimportowania wcześniejszych danych do środowiska, postępuj zgodnie z poniższymi wskazówkami:

- Nie przesyłaj jednocześnie danych na żywo i historyczne. Pozyskanie danych z kolejności nie spowoduje obniżenia wydajności zapytań.
- Pozyskiwanie danych historycznych w uporządkowany czas w celu uzyskania najlepszej wydajności.
- Poniżej znajdują się limity przepływności pozyskiwania.
- Wyłącz sklep ciepły, jeśli dane są starsze niż okres przechowywania w sklepie eksploatacyjnym.

## <a name="event-source-timestamp"></a>Sygnatura czasowa źródła zdarzeń

Podczas konfigurowania źródła zdarzeń zostanie wyświetlony monit o podanie właściwości identyfikatora znacznika czasu. Właściwość timestamp służy do śledzenia zdarzeń w czasie, czyli czasu, który będzie używany jako sygnatura czasowa `$ts` w [interfejsach API zapytań](/rest/api/time-series-insights/dataaccessgen2/query/execute) oraz do wykreślania serii w Eksploratorze Azure Time Series Insights. Jeśli w czasie tworzenia nie podano żadnej właściwości, lub jeśli w zdarzeniu brakuje właściwości timestamp, zostanie użyta wartość domyślna "IoT Hub zdarzeń" i centra zdarzeń w kolejce. Wartości właściwości timestamp są przechowywane w formacie UTC.

Ogólnie rzecz biorąc, użytkownicy będą dostosowywać Właściwość sygnatur czasowych i używać czasu, gdy czujnik lub tag wygenerował odczyt, zamiast korzystać z domyślnego, wbudowanego czasu centrum. Jest to szczególnie konieczne, gdy urządzenia mają nieprzerwaną utratę łączności, a partia opóźnionych komunikatów jest przekazywana do Azure Time Series Insights Gen2.

Jeśli niestandardowa sygnatura czasowa znajduje się w zagnieżdżonym obiekcie JSON lub tablicy, należy podać poprawną nazwę właściwości zgodnie z naszymi [konwencjami nazewnictwa i ucieczki](concepts-json-flattening-escaping-rules.md). Na przykład sygnatura czasowa źródła zdarzeń dla ładunku JSON pokazana [tutaj](concepts-json-flattening-escaping-rules.md#example-a) powinna zostać wprowadzona jako `"values.time"` .

### <a name="time-zone-offsets"></a>Przesunięcia strefy czasowej

Sygnatury czasowe muszą być wysyłane w formacie ISO 8601 i będą przechowywane w czasie UTC. Jeśli zostanie podane przesunięcie strefy czasowej, zostanie zastosowane przesunięcie, a następnie czas przechowywania i zwracania w formacie UTC. Jeśli przesunięcie jest nieprawidłowo sformatowane, zostanie zignorowane. W sytuacjach, w których rozwiązanie może nie mieć kontekstu oryginalnego przesunięcia, można wysłać dane przesunięcia w dodatkowej osobnej właściwości zdarzenia, aby upewnić się, że jest zachowywana i czy aplikacja może odwoływać się do odpowiedzi na zapytanie.

Przesunięcie strefy czasowej powinno być sformatowane jako jedno z następujących:

HHMMZ</br>
± HH: MM</br>
± GG: MMZ</br>

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [reguły spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak będą przechowywane zdarzenia.

- Zapoznaj się z [ograniczeniami przepływności](./concepts-streaming-ingress-throughput-limits.md) środowiska
