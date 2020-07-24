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
ms.date: 07/07/2020
ms.openlocfilehash: c2e70a4f5cdbbc7a5a408138c3ec832cc831cf33
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046570"
---
# <a name="supported-data-types"></a>Obsługiwane typy danych

Poniższa tabela zawiera listę typów danych obsługiwanych przez Azure Time Series Insights Gen2

| Typ danych | Opis | Przykład | Nazwa kolumny właściwości w Parquet
|---|---|---|---|
| **bool** | Typ danych, który ma jeden z dwóch stanów: `true` lub `false` . | "iswątpliwy": prawda | isQuestionable_bool
| **datę** | Reprezentuje chwilę w czasie, zwykle wyrażoną jako datę i godzinę dnia. Wyrażony w formacie [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) . Właściwości DateTime są zawsze przechowywane w formacie UTC. Przesunięcia strefy czasowej (w przypadku prawidłowego formatowania) zostaną zastosowane, a następnie wartości przechowywane w formacie UTC. Zapoznaj się z [tą](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sekcją, aby uzyskać więcej informacji na temat właściwości sygnatury czasowej środowiska i przesunięcia DateTime | "eventProcessedLocalTime": "2020-03-20T09:03:32.8301668 Z" | eventProcessedLocalTime_datetime 
| **Double** | Numer 64-bitowy o podwójnej precyzji  | "wartość": 31,0482941 | value_double
| **długi** | 64-bitowa liczba całkowita ze znakiem  | "value": 31 | value_long
| **parametry** | Wartości tekstowe muszą zawierać prawidłowy format UTF-8. |  "Witryna": "DIM_MLGGG" | site_string
| **dynamic** | Złożony (niepierwotny) typ składający się z tablicy lub zbioru właściwości (Dictionary). Obecnie skonwertowanej tablice JSON elementów podstawowych lub tablic obiektów, które nie zawierają identyfikatora TS lub właściwych sygnatur czasowych, będą przechowywane jako dynamiczne. Przeczytaj ten [artykuł](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak obiekty zostaną spłaszczone, a tablice mogą zostać wycofane. Właściwości ładunku przechowywane w ramach tego typu są dostępne za pomocą Eksploratora Azure Time Series Insights Gen2 i interfejsu API zapytań GetEvents. |  "wartości": "[197, 194, 189, 188]" | values_dynamic

> [!IMPORTANT]
>
> * Środowisko Gen2 Azure Time Series Insights jest silnie wpisane. Jeśli urządzenia lub Tagi wysyłają zarówno dane całkowite, jak i niecałkowite, wartości właściwości urządzenia będą przechowywane w dwóch rozdzielonych podwójnie i długich kolumnach, a [Funkcja łączenia ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) powinna być używana podczas wykonywania wywołań interfejsu API i definiowania wyrażeń zmiennych modelu szeregów czasowych.

#### <a name="objects-and-arrays"></a>Obiekty i tablice

Można wysyłać złożone typy, takie jak obiekty i tablice, w ramach ładunku zdarzenia. Obiekty zagnieżdżone zostaną spłaszczone i tablice będą przechowywane jako `dynamic` lub spłaszczone w celu utworzenia wielu zdarzeń w zależności od konfiguracji środowiska i kształtu JSON. Aby dowiedzieć się więcej na temat [reguł spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [reguły spłaszczania i ucieczki JSON](./concepts-json-flattening-escaping-rules.md) , aby zrozumieć, jak będą przechowywane zdarzenia. 

* Zapoznaj się z [ograniczeniami przepływności](./concepts-streaming-ingress-throughput-limits.md) środowiska

* Informacje o [źródłach zdarzeń](concepts-streaming-ingestion-event-sources.md) w celu pozyskiwania danych przesyłanych strumieniowo.
