---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98109309"
---
### <a name="property-limits"></a>Limity właściwości

Limity właściwości Azure Time Series Insights zostały zwiększone do 1 000 dla magazynu ciepłego i nie ma limitu właściwości dla magazynu chłodnego. Podane właściwości zdarzenia mają odpowiednie kolumny JSON, CSV i wykresu, które można wyświetlić w [eksploratorze Azure Time Series Insights Gen2](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Właściwości maksymalne |
| --- | --- |
| Gen2 (L1) | 1 000 właściwości (kolumny) dla magazynu ciepłego i bez ograniczeń dla magazynu zimnego|
| Gen1 (S1) | Właściwości 600 (kolumny) |
| Gen1 (S2) | Właściwości 800 (kolumny) |

### <a name="streaming-ingestion"></a>Pozyskiwanie danych za pośrednictwem przesyłania strumieniowego

* Istnieje maksymalnie dwa [źródła zdarzeń](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) na środowisko.

* Najlepsze rozwiązania i ogólne wskazówki dotyczące źródeł zdarzeń można znaleźć [tutaj](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Domyślnie Azure Time Series Insights Gen2 może pozyskać dane przychodzące z szybkością **do 1 megabajtów na sekundę (MB/s) na Azure Time Series Insights środowisku Gen2**. Istnieją dodatkowe ograniczenia [na partycję centrum](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Stawki do 2 MB/s można dostarczyć przez przesłanie biletu pomocy technicznej przez Azure Portal. Aby dowiedzieć się więcej, Przeczytaj [limity przepływności pozyskiwania strumieniowego](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Limity interfejsu API

Limity interfejsu API REST dla Azure Time Series Insights Gen2 są określone w [dokumentacji interfejsu API REST](/rest/api/time-series-insights/preview#limits-1).