---
title: Źródła zdarzeń pozyskiwania strumieniowego — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej o przesyłaniu strumieniowym danych do Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 6bc238389ac470e6127a582eb174ec7bc438e36b
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650872"
---
# <a name="azure-time-series-insights-gen2-event-sources"></a>Azure Time Series Insights źródła zdarzeń Gen2

 Środowisko Azure Time Series Insights Gen2 może mieć do dwóch źródeł zdarzeń przesyłania strumieniowego. Jako dane wejściowe są obsługiwane dwa typy zasobów platformy Azure:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Zdarzenia muszą być wysyłane jako zakodowany w formacie UTF-8 kod JSON.

## <a name="create-or-edit-event-sources"></a>Tworzenie lub edytowanie źródeł zdarzeń

Zasoby źródłowe zdarzeń mogą się znajdować w tej samej subskrypcji platformy Azure co środowisko Azure Time Series Insights Gen2 lub inną subskrypcję. Możesz użyć [Azure Portal](time-series-insights-update-create-environment.md#create-a-preview-payg-environment), [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli-extensions/tree/master/src/timeseriesinsights), [szablonów ARM](time-series-insights-manage-resources-using-azure-resource-manager-template.md)i [interfejsu API REST](/rest/api/time-series-insights/management(gen1/gen2)/eventsources) do tworzenia, edytowania lub usuwania źródeł zdarzeń środowiska.

Po nawiązaniu połączenia ze źródłem zdarzeń środowisko Azure Time Series Insights Gen2 odczytuje wszystkie zdarzenia przechowywane obecnie w usłudze IoT lub centrum zdarzeń, rozpoczynając od najstarszego zdarzenia.

> [!IMPORTANT]
>
> - Podczas dołączania źródła zdarzeń do środowiska Azure Time Series Insights Gen2 może wystąpić wysokie początkowe opóźnienie.
> - Opóźnienie źródła zdarzenia zależy od liczby zdarzeń znajdujących się obecnie w IoT Hub lub centrum zdarzeń.
> - Duże opóźnienie zostanie umieszczone po pierwszym pozyskaniu danych źródła zdarzenia. Prześlij bilet pomocy technicznej za pomocą Azure Portal, jeśli wystąpią duże opóźnienia.

## <a name="streaming-ingestion-best-practices"></a>Najlepsze rozwiązania pozyskiwania strumieniowego

- Zawsze należy utworzyć unikatową grupę odbiorców dla środowiska Azure Time Series Insights Gen2, aby używać danych ze źródła zdarzeń. Ponowne używanie grup odbiorców może spowodować losowe odłączenie i może skutkować utratą danych.

- Skonfiguruj środowisko Azure Time Series Insights Gen2 i IoT Hub i/lub Event Hubs w tym samym regionie świadczenia usługi Azure. Chociaż istnieje możliwość skonfigurowania źródła zdarzeń w osobnym regionie, ten scenariusz nie jest obsługiwany, a firma Microsoft nie może zagwarantować wysokiej dostępności.

- Nie należy przekroczyć [limitu szybkości przepływności](./concepts-streaming-ingress-throughput-limits.md) dla środowiska lub limitu partycji.

- Skonfiguruj [alert](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts) z opóźnieniem, aby otrzymywać powiadomienia o problemach z przetwarzaniem danych w środowisku.

- Używaj pozyskiwania strumieniowego w przypadku niemal czasu rzeczywistego i ostatnich danych, ale dane historyczne przesyłania strumieniowego nie są obsługiwane.

- Dowiedz się, w jaki sposób właściwości będą wyprowadzane, a dane JSON są [spłaszczone i przechowywane.](./concepts-json-flattening-escaping-rules.md)

- Stosuj zasadę najniższych uprawnień podczas udostępniania parametrów połączenia źródła zdarzeń. W przypadku Event Hubs Skonfiguruj zasady dostępu współdzielonego tylko przy użyciu tylko żądania *wysyłania* , a dla IoT Hub Użyj tylko uprawnienia *usługi Connect* .

### <a name="historical-data-ingestion"></a>Pozyskiwanie danych historycznych

Korzystanie z potoku przesyłania strumieniowego do importowania danych historycznych nie jest obecnie obsługiwane w Azure Time Series Insights Gen2. Jeśli zachodzi potrzeba zaimportowania wcześniejszych danych do środowiska, postępuj zgodnie z poniższymi wskazówkami:

- Nie przesyłaj jednocześnie danych na żywo i historyczne. Pozyskanie danych z kolejności nie spowoduje obniżenia wydajności zapytań.
- Pozyskiwanie danych historycznych w uporządkowany czas w celu uzyskania najlepszej wydajności.
- Poniżej znajdują się limity przepływności pozyskiwania.
- Wyłącz sklep ciepły, jeśli dane są starsze niż okres przechowywania w sklepie eksploatacyjnym.

## <a name="event-source-timestamp"></a>Sygnatura czasowa źródła zdarzeń

Podczas konfigurowania źródła zdarzeń zostanie wyświetlony monit o podanie właściwości identyfikatora znacznika czasu. Właściwość timestamp służy do śledzenia zdarzeń w czasie. jest to czas, który będzie używany jako $event. $ts w [interfejsach API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute) oraz do wykreślania serii w Eksploratorze Azure Time Series Insights. Jeśli w czasie tworzenia nie podano żadnej właściwości, lub jeśli w zdarzeniu brakuje właściwości timestamp, zostanie użyta wartość domyślna "IoT Hub zdarzeń" i centra zdarzeń w kolejce. Wartości właściwości timestamp są przechowywane w formacie UTC.

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
