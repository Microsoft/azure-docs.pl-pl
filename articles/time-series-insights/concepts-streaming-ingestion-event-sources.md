---
title: Źródła zdarzeń pozyskiwania strumieniowego — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o przesyłanych strumieniowo danych do Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 499cb3c978a67f9ef71e6ad9dd03be9f05b45729
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726974"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights Źródła zdarzeń 2. generacji

Środowisko Azure Time Series Insights Gen2 może mieć maksymalnie dwa źródła zdarzeń przesyłania strumieniowego. Dwa typy zasobów platformy Azure są obsługiwane jako dane wejściowe:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Zdarzenia muszą być wysyłane w formacie JSON zakodowanym w formacie UTF-8.

## <a name="create-or-edit-event-sources"></a>Tworzenie lub edytowanie źródeł zdarzeń

Źródło zdarzenia to połączenie między centrum a środowiskiem usługi Azure Time Series Insights Gen2, a w grupie zasobów jest tworzony `Time Series Insights event source` oddzielny zasób typu . Zasoby IoT Hub lub centrum zdarzeń mogą być na żywo w tej samej subskrypcji platformy Azure, w Azure Time Series Insights Gen2 lub w innej subskrypcji. Jednak najlepszym rozwiązaniem jest, aby w tym samym regionie świadczenia usługi Azure Azure Time Series Insights i centrum zdarzeń IoT Hub lub centrum zdarzeń.

Do [Azure Portal](./tutorials-set-up-tsi-environment.md#create-an-azure-time-series-insights-gen2-environment)tworzenia, edytowania lub usuwania źródeł zdarzeń środowiska możesz użyć szablonów interfejsu wiersza polecenia platformy [Azure,](https://docs.microsoft.com/cli/azure/ext/timeseriesinsights/tsi/event-source)interfejsu wiersza polecenia platformy Azure, interfejsu wiersza polecenia platformy [Azure Resource Manager](time-series-insights-manage-resources-using-azure-resource-manager-template.md)Azure oraz interfejsu [API REST.](/rest/api/time-series-insights/management(gen1/gen2)/eventsources)

> [!WARNING]
> Nie należy ograniczać publicznego dostępu do Internetu do centrum lub źródła zdarzeń używanego przez Time Series Insights lub wymagane połączenie zostanie przerwane.

## <a name="start-options"></a>Opcje uruchamiania

Podczas tworzenia źródła zdarzeń można określić, jakie dane mają być zbierane wcześniej. To ustawienie jest opcjonalne. Dostępne są następujące opcje:

| Nazwa   |  Opis  |  Azure Resource Manager przykład szablonu |
|----------|-------------|------|
| EarliestAvailable | Pozysuj wszystkie wcześniej istniejące dane przechowywane w centrum zdarzeń lub IoT | `"ingressStartAt": {"type": "EarliestAvailable"}` |
| EventSourceCreationTime |  Rozpocznij pozysłowanie danych, które docierają po utworzeniu źródła zdarzeń. Wszystkie wcześniej istniejące dane, które zostały strumieniowo przesyłane przed utworzeniem źródła zdarzeń, zostaną zignorowane. Jest to ustawienie domyślne w Azure Portal   |   `"ingressStartAt": {"type": "EventSourceCreationTime"}` |
| CustomEnqueuedTime | Środowisko pozyska dane z niestandardowego czasu w kolejkach (UTC) do przodu. Wszystkie zdarzenia, które zostały zapisane w kolejkach do usługi IoT lub Centrum zdarzeń o godzinie lub po niestandardowym czasie w kolejkach, zostaną pozyskane i zapisane. Wszystkie zdarzenia, które dotarły przed niestandardowym czasem w kolejkach, zostaną zignorowane. Należy pamiętać, że termin "czas w kolejkach" odnosi się do czasu (w czasie UTC), kiedy zdarzenie dotarło do usługi IoT lub centrum zdarzeń. Różni się to od niestandardowej [właściwości znacznika czasu,](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp) która znajduje się w treści zdarzenia. |     `"ingressStartAt": {"type": "CustomEnqueuedTime", "time": "2021-03-01T17:00:00.20Z"}` |

> [!IMPORTANT]
>
> - Jeśli wybierzesz pozycję EarliestAvailable i masz dużo wcześniej istniejących danych, może wystąpić duże początkowe opóźnienie, ponieważ środowisko Azure Time Series Insights Gen2 przetwarza wszystkie dane.
> - To duże opóźnienie powinno ostatecznie zostać zerowane w momencie indeksowania danych. Prześlij bilet pomocy technicznej za pośrednictwem Azure Portal, jeśli wystąpią duże opóźnienia.

- EarliestAvailable

![EarliestAvailable Diagram](media/concepts-streaming-event-sources/event-source-earliest-available.png)

- EventSourceCreationTime

![EventSourceCreationTime Diagram](media/concepts-streaming-event-sources/event-source-creation-time.png)

- CustomEnqueuedTime

![CustomEnqueuedTime Diagram](media/concepts-streaming-event-sources/event-source-custom-enqueued-time.png)

## <a name="streaming-ingestion-best-practices"></a>Najlepsze rozwiązania dotyczące pozyskiwania danych przesyłanych strumieniowo

- Zawsze twórz unikatową grupę odbiorców dla środowiska Azure Time Series Insights Gen2, aby korzystać z danych ze źródła zdarzeń. Ponowne użyć grup odbiorców może spowodować losowe rozłączenie i może spowodować utratę danych.

- Skonfiguruj środowisko Azure Time Series Insights Gen2 oraz środowisko IoT Hub i/lub Event Hubs w tym samym regionie świadczenia usługi Azure. Chociaż istnieje możliwość skonfigurowania źródła zdarzeń w osobnym regionie, ten scenariusz nie jest obsługiwany i nie możemy zagwarantować wysokiej dostępności.

- Nie wykraczaj poza limit przepływności środowiska ani [limit](./concepts-streaming-ingress-throughput-limits.md) liczby partycji.

- Skonfiguruj alert o [opóźnień,](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) aby był powiadamiany, jeśli w środowisku występują problemy z przetwarzaniem danych. Aby [uzyskać sugerowane](./concepts-streaming-ingestion-event-sources.md#production-workloads) warunki alertu, zobacz Production workloads below (Obciążenia produkcyjne poniżej).

- Pozyskiwanie danych przesyłanych strumieniowo dotyczy tylko danych niemal w czasie rzeczywistym i ostatnich. Przesyłanie strumieniowe danych historycznych nie jest obsługiwane.

- Dowiedz się, jak właściwości będą ucieczki, a dane JSON [spłaszczone i przechowywane.](./concepts-json-flattening-escaping-rules.md)

- Podczas dostarczania ciągów połączenia źródła zdarzeń postępuj zgodnie z zasadą najmniejszych uprawnień. Na Event Hubs zasad dostępu współdzielonych tylko  za pomocą oświadczenia wysyłania i IoT Hub używać *tylko* uprawnień połączenia usługi.

> [!CAUTION]
> Jeśli usuniesz IoT Hub lub centrum zdarzeń i ponownie utworzysz nowy zasób o tej samej nazwie, musisz utworzyć nowe źródło zdarzeń i dołączyć nowe IoT Hub lub Centrum zdarzeń. Dane nie zostaną pozyowane do czasu ukończenia tego kroku.

## <a name="production-workloads"></a>Obciążenia produkcyjne

Oprócz powyższych najlepszych rozwiązań zalecamy zaimplementowanie następujących rozwiązań w przypadku obciążeń o krytycznym znaczeniu dla działania firmy.

- Zwiększ IoT Hub przechowywania danych w centrum zdarzeń do maksymalnie siedmiu dni.

- Utwórz alerty środowiska w Azure Portal. Alerty oparte na [metrykach platformy](./how-to-monitor-tsi-reference.md#metrics) umożliwiają weryfikowanie zachowania potoku na wszystkich platformach. Instrukcje dotyczące tworzenia alertów i zarządzania nimi znajdują [się tutaj.](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) Sugerowane warunki alertu:

  - Wartość IngressReceivedMessagesTimeLag jest większa niż 5 minut
  - IngressReceivedBytes to 0
- Utrzymuj równoważenie obciążenia pozyskiwania między partycjami IoT Hub lub centrum zdarzeń.

### <a name="historical-data-ingestion"></a>Pozyskiwanie danych historycznych

Importowanie danych historycznych przy użyciu potoku przesyłania strumieniowego nie jest obecnie obsługiwane w Azure Time Series Insights Gen2. Jeśli musisz zaimportować wcześniejsze dane do środowiska, postępuj zgodnie z poniższymi wytycznymi:

- Nie przesyłaj równolegle danych na żywo i danych historycznych. Pozysowanie danych poza kolejnością spowoduje obniżenie wydajności zapytań.
- Pozyskaj dane historyczne w sposób uporządkowany w czasie, aby uzyskać najlepszą wydajność.
- Pozostań w ramach limitów przepływności pozyskiwania poniżej.
- Wyłącz ciepły magazyn, jeśli dane są starsze niż okres przechowywania magazynu ciepłego.

## <a name="event-source-timestamp"></a>Znacznik czasu źródła zdarzenia

Podczas konfigurowania źródła zdarzeń zostanie poproszony o podanie właściwości identyfikatora sygnatury czasowej. Właściwość sygnatury czasowej służy do śledzenia zdarzeń w czasie. Jest to czas, który będzie używany jako znacznik czasu w interfejsach API zapytań i do kreślenia serii w eksploratorze `$ts` Azure Time Series Insights. [](/rest/api/time-series-insights/dataaccessgen2/query/execute) Jeśli w czasie tworzenia nie podano żadnej właściwości lub jeśli w zdarzeniu brakuje właściwości sygnatury czasowej, jako domyślny zostanie użyty czas w kolejkach usługi IoT Hub lub Event Hubs. Wartości właściwości znacznika czasu są przechowywane w czasie UTC.

Ogólnie rzecz biorąc, użytkownicy zdecydują się na dostosowanie właściwości znacznika czasu i użyją czasu wygenerowania odczytu przez czujnik lub tag, zamiast używać domyślnego czasu w kolejkach centrum. Jest to szczególnie konieczne, gdy urządzenia mają sporadyczne utraty łączności i partii komunikatów opóźnione są przekazywane do Azure Time Series Insights Gen2.

Jeśli niestandardowy znacznik czasu znajduje się w zagnieżdżonym obiekcie JSON lub tablicy, musisz podać poprawną nazwę właściwości zgodnie z naszymi konwencjami spłaszczania i ucieczki [nazewnictwa.](concepts-json-flattening-escaping-rules.md) Na przykład znacznik czasu źródła zdarzenia dla pokazanego tutaj ładunku JSON [należy](concepts-json-flattening-escaping-rules.md#example-a) wprowadzić jako `"values.time"` .

### <a name="time-zone-offsets"></a>Przesunięcia strefy czasowej

Znaczniki czasu muszą być wysyłane w formacie ISO 8601 i będą przechowywane w formacie UTC. Jeśli zostanie podane przesunięcie strefy czasowej, zostanie zastosowane przesunięcie, a następnie czas przechowywany i zwrócony w formacie UTC. Jeśli przesunięcie jest nieprawidłowo sformatowane, zostanie zignorowane. W sytuacjach, w których rozwiązanie może nie mieć kontekstu oryginalnego przesunięcia, możesz wysłać dane przesunięcia w dodatkowej oddzielnej właściwości zdarzenia, aby upewnić się, że są one zachowane i że aplikacja może odwoływać się w odpowiedzi na zapytanie.

Przesunięcie strefy czasowej powinno być sformatowane jako jedno z następujących:

±HHMMZ<br />
±HH:MM<br />
±HH:MMZ

## <a name="next-steps"></a>Następne kroki

- Przeczytaj reguły spłaszczania i ucieczki w danych [JSON,](./concepts-json-flattening-escaping-rules.md) aby dowiedzieć się, jak będą przechowywane zdarzenia.

- Understand your environment's throughput limitations (Opis ograniczeń [przepływności środowiska)](./concepts-streaming-ingress-throughput-limits.md)
