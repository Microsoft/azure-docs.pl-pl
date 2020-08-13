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
ms.date: 08/12/2020
ms.openlocfilehash: e6fd405d1969a2f40a5f0c3466a57fbec60723e9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141163"
---
# <a name="supported-data-types"></a>Obsługiwane typy danych

Poniższa tabela zawiera listę typów danych obsługiwanych przez Azure Time Series Insights Gen2

| Typ danych | Opis | Przykład | Nazwa kolumny właściwości w Parquet
|---|---|---|---|
| **bool** | Typ danych, który ma jeden z dwóch stanów: `true` lub `false` . | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Wyrażony w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Właściwości DateTime są zawsze przechowywane w formacie UTC. Przesunięcia strefy czasowej (w przypadku prawidłowego formatowania) zostaną zastosowane, a następnie wartości przechowywane w formacie UTC. Zapoznaj się z [tą](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sekcją, aby uzyskać więcej informacji na temat właściwości sygnatury czasowej środowiska i przesunięcia DateTime | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **liczba o podwójnej precyzji** | Numer 64-bitowy o podwójnej precyzji  | `"value": 31.0482941` | value_double
| **liczba długa** | 64-bitowa liczba całkowita ze znakiem  | `"value" : 31` | value_long
| **parametry** | Wartości tekstowe muszą zawierać prawidłowy format UTF-8. Ciągi o wartości null i puste są traktowane jako takie same. |  `"site": "DIM_MLGGG"` | site_string
| **dynamiczna** | Złożony (niepierwotny) typ składający się z tablicy lub zbioru właściwości (Dictionary). Obecnie tylko skonwertowanej tablice JSON elementów podstawowych lub tablic obiektów niezawierających identyfikatora TS lub właściwości sygnatur czasowych będą przechowywane jako dynamiczne. Przeczytaj ten [artykuł](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak obiekty będą spłaszczane, a tablice mogą być nierzutowane. Właściwości ładunku przechowywane w ramach tego typu są dostępne za pomocą Eksploratora Azure Time Series Insights Gen2 i `GetEvents`   interfejsu API zapytań. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

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
