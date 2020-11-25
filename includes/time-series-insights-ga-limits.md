---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027697"
---
Poniżej zestawiono limity kluczy w Azure Time Series Insights Gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Stawki i pojemności jednostki SKU

Stawki za transfery danych w warstwie S1 i S2 zapewniają elastyczność podczas konfigurowania nowego środowiska Azure Time Series Insights. Pojemność jednostki SKU wskazuje dzienny wskaźnik ruchu przychodzącego na podstawie liczby zdarzeń lub bajtów przechowywanych, zależnie od tego, co nastąpi wcześniej. Należy pamiętać, że ruch przychodzący jest mierzony *na minutę*, a **ograniczenie** jest stosowane przy użyciu algorytmu zasobnika. Ruch przychodzący jest mierzony w blokach o wymiarach 1 KB. Na przykład rzeczywiste zdarzenie 0,8-KB byłoby mierzone jako jedno zdarzenie, a zdarzenie 2,6 KB jest traktowane jako trzy zdarzenia.

| Pojemność jednostki SKU S1 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 1 GB (1 000 000 zdarzeń) dziennie | 30 GB (30 000 000 zdarzeń) miesięcznie |
| 10 | 10 GB (10 000 000 zdarzeń) dziennie | 300 GB (zdarzenia 300 000 000) miesięcznie |

| Pojemność jednostki SKU S2 | Szybkość transferu danych przychodzących | Maksymalna pojemność magazynu
| --- | --- | --- |
| 1 | 10 GB (10 000 000 zdarzeń) dziennie | 300 GB (zdarzenia 300 000 000) miesięcznie |
| 10 | 100 GB (zdarzenia 100 000 000) dziennie | 3 TB (3 000 000 000 zdarzeń) miesięcznie |

> [!NOTE]
> Pojemności są skalowane liniowie, więc jednostka SKU S1 z pojemnością 2 obsługuje 2 GB (2 000 000) zdarzeń na dzień i 60 GB (60 000 000 zdarzeń) miesięcznie.

Środowiska jednostki SKU S2 obsługują znacznie więcej zdarzeń miesięcznie i mają znacznie wyższą wydajność transferu danych przychodzących.

| SKU  | Liczba zdarzeń miesięcznie  | Liczba zdarzeń na minutę | Rozmiar zdarzenia na minutę  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000   |  720    |  720 KB   |
 |S2     |   300 000 000   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Limity właściwości

Limity właściwości Gen1 zależą od wybranego środowiska jednostki SKU. Podane właściwości zdarzenia mają odpowiednie kolumny JSON, CSV i wykresu, które mogą być wyświetlane w [eksploratorze Azure Time Series Insights](../articles/time-series-insights/time-series-quickstart.md).

| SKU | Właściwości maksymalne |
| --- | --- |
| S1 | Właściwości 600 (kolumny) |
| S2 | Właściwości 800 (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie.

* Dowiedz się, jak [dodać Źródło centrum zdarzeń](../articles/time-series-insights/how-to-ingest-data-event-hub.md).
* Skonfiguruj [Źródło Centrum IoT](../articles/time-series-insights/how-to-ingest-data-iot-hub.md).

### <a name="api-limits"></a>Limity interfejsu API

Limity interfejsu API REST dla Azure Time Series Insights Gen1 są określone w [dokumentacji interfejsu API REST](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).