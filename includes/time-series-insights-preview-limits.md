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
ms.openlocfilehash: cd6ea6d4967e024ddf88fb9572d5efae8b7a7815
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495344"
---
### <a name="property-limits"></a>Limity właściwości

Limity właściwości Azure Time Series Insights wzrosły do 1 000 z maksymalnie 800 w Gen1. Podane właściwości zdarzenia mają odpowiednie kolumny JSON, CSV i wykresu, które można wyświetlić w [eksploratorze Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Jednostka SKU | Właściwości maksymalne |
| --- | --- |
| Gen2 (L1) | Właściwości 1 000 (kolumny) |
| Gen1 (S1) | Właściwości 600 (kolumny) |
| Gen1 (S2) | Właściwości 800 (kolumny) |

### <a name="event-sources"></a>Źródła zdarzeń

Obsługiwane są maksymalnie dwa źródła zdarzeń na wystąpienie.

* Dowiedz się, jak [dodać Źródło centrum zdarzeń](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Skonfiguruj [Źródło Centrum IoT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Domyślnie [środowiska Gen2 obsługują stawki](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-throughput-limitations) za transfer danych przychodzących do **1 megabajtów na sekundę (MB/s) na środowisko**. W razie potrzeby klienci mogą skalować swoje środowiska do **16 MB/s** . Istnieje również limit partycji wynoszący **0,5 MB/s**.

### <a name="api-limits"></a>Limity interfejsu API

Limity interfejsu API REST dla Azure Time Series Insights Gen2 są określone w [dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
