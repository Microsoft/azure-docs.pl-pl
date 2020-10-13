---
title: Obsługiwane typy danych — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej na temat obsługiwanych typów danych w Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 11199e5a283459d7d97f649322f9d41fc7b3e11d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650798"
---
# <a name="supported-data-types"></a>Obsługiwane typy danych

Poniższa tabela zawiera listę typów danych obsługiwanych przez Azure Time Series Insights Gen2

| Typ danych | Opis | Przykład | [Składnia wyrażenia szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nazwa kolumny właściwości w Parquet
|---|---|---|---|---|
| **bool** | Typ danych, który ma jeden z dwóch stanów: `true` lub `false` . | `"isQuestionable" : true` | `$event.isQuestionable.Bool` lub `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Wyrażony w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Właściwości DateTime są zawsze przechowywane w formacie UTC. Przesunięcia strefy czasowej (w przypadku prawidłowego formatowania) zostaną zastosowane, a następnie wartości przechowywane w formacie UTC. Zapoznaj się z [tą](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sekcją, aby uzyskać więcej informacji na temat właściwości sygnatury czasowej środowiska i przesunięcia DateTime | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Jeśli "eventProcessedLocalTime" jest sygnaturą czasową źródła zdarzeń: `$event.$ts` . Jeśli jest to inna Właściwość JSON: `$event.eventProcessedLocalTime.DateTime` lub `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **liczba o podwójnej precyzji** | Numer 64-bitowy o podwójnej precyzji  | `"value": 31.0482941` | `$event.value.Double` lub `$event['value'].Double` |  `value_double`
| **liczba długa** | 64-bitowa liczba całkowita ze znakiem  | `"value" : 31` | `$event.value.Long` lub `$event['value'].Long` |  `value_long`
| **parametry** | Wartości tekstowe muszą zawierać prawidłowy format UTF-8. Ciągi o wartości null i puste są traktowane jako takie same. |  `"site": "DIM_MLGGG"`| `$event.site.String` lub `$event['site'].String`| `site_string`
| **dynamiczna** | Złożony (niepierwotny) typ składający się z tablicy lub zbioru właściwości (Dictionary). Obecnie tylko skonwertowanej tablice JSON elementów podstawowych lub tablic obiektów niezawierających identyfikatora TS lub właściwości sygnatur czasowych będą przechowywane jako dynamiczne. Przeczytaj ten [artykuł](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak obiekty będą spłaszczane, a tablice mogą być nierzutowane. Właściwości ładunku przechowywane jako ten typ są dostępne tylko po wybraniu `Explore Events` w eksploratorze Time Series Insights, aby wyświetlić zdarzenia pierwotne lub za pomocą [`GetEvents`](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)   interfejsu API zapytania dla analizy po stronie klienta. |  `"values": "[197, 194, 189, 188]"` | Odwoływanie się do typów dynamicznych w wyrażeniu szeregów czasowych nie jest jeszcze obsługiwane. | `values_dynamic`

> [!NOTE]
> 64 bitowe wartości całkowite są obsługiwane, ale największą liczbę, którą program Azure Time Series Insights Explorer może bezpiecznie wyszukać, to 9 007 199 254 740 991 (2 ^ 53-1) z powodu ograniczeń języka JavaScript. Jeśli pracujesz z liczbami w modelu danych powyżej, możesz zmniejszyć rozmiar przez utworzenie [zmiennej modelu szeregów czasowych](/azure/time-series-insights/concepts-variables#numeric-variables) i [przekonwertowanie](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) wartości.

> [!NOTE]
> Typ **ciągu** nie dopuszcza wartości null:
>
> * [Wyrażenie szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) wyrażone w [zapytaniu szeregów czasowych](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis) porównujące wartość pustego ciągu (**""**) z **wartością null** , będzie w ten sam sposób zachowywać się w taki sam sposób: `$event.siteid.String = NULL` jest równoważne `$event.siteid.String = ''` .
> * Interfejs API może zwracać wartości **null** nawet wtedy, gdy oryginalne zdarzenia zawierają puste ciągi.
> * Nie należy podejmować zależności od wartości **null** w kolumnach **ciągów** do wykonywania porównań ani ocen, Traktuj je w taki sam sposób, jak puste ciągi.

## <a name="sending-mixed-data-types"></a>Wysyłanie mieszanych typów danych

Środowisko Gen2 Azure Time Series Insights jest silnie wpisane. Jeśli urządzenia lub Tagi wysyłają dane różnych typów dla właściwości urządzenia, wartości będą przechowywane w dwóch oddzielonych kolumnach, a [Funkcja łączenia ()](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) powinna być używana podczas definiowania wyrażeń zmiennych modelu szeregów czasowych w WYWOŁANIACH interfejsu API.

Eksplorator Azure Time Series Insights umożliwia szybkie łączenia oddzielnych kolumn tej samej właściwości urządzenia. W poniższym przykładzie czujnik wysyła `PresentValue` Właściwość, która może być zarówno typu Long, jak i Double. Aby wykonać zapytanie względem wszystkich przechowywanych wartości (niezależnie od typu danych) `PresentValue` właściwości, wybierz `PresentValue (Double | Long)` i kolumny zostaną połączone.

[![Eksplorator autołączenia](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Obiekty i tablice

Można wysyłać złożone typy, takie jak obiekty i tablice, w ramach ładunku zdarzenia. Obiekty zagnieżdżone zostaną spłaszczone i tablice będą przechowywane jako `dynamic` lub spłaszczone w celu utworzenia wielu zdarzeń w zależności od konfiguracji środowiska i kształtu JSON. Aby dowiedzieć się więcej na temat [reguł spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [reguły spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak będą przechowywane zdarzenia.

* Zapoznaj się z [ograniczeniami przepływności](./concepts-streaming-ingress-throughput-limits.md) środowiska

* Informacje o [źródłach zdarzeń](concepts-streaming-ingestion-event-sources.md) w celu pozyskiwania danych przesyłanych strumieniowo.
