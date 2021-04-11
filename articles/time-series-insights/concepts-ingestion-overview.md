---
title: Przegląd pozyskiwania — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej na temat pozyskiwania danych w Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: b688bbf454b3df9f6ae368c66a62657a5522d720
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505454"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Azure Time Series Insights Gen2 pozyskiwania danych

Środowisko Azure Time Series Insights Gen2 zawiera *aparat* pozyskiwania, który zbiera, przetwarza i przechowuje strumieniowo dane szeregów czasowych. Po nadejściu danych do źródeł zdarzeń Azure Time Series Insights Gen2 będzie zużywać dane i przechowywać je niemal w czasie rzeczywistym.

[![Omówienie pozyskiwania danych](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Tematy dotyczące pozyskiwania

W poniższych artykułach szczegółowo omówiono przetwarzanie danych, w tym najlepsze rozwiązania, które należy wykonać:

* Przeczytaj o [źródłach zdarzeń](./concepts-streaming-ingestion-event-sources.md) i wskazówkach dotyczących wybierania sygnatury czasowej źródła zdarzeń.

* Przejrzyj obsługiwane [typy danych](./concepts-supported-data-types.md)

* Dowiedz się, jak aparat pozyskiwania zastosuje zestaw [reguł](./concepts-json-flattening-escaping-rules.md) do właściwości JSON, aby utworzyć kolumny konta magazynu.

* Przejrzyj [ograniczenia przepływności](./concepts-streaming-ingress-throughput-limits.md) środowiska, aby zaplanować potrzeby skalowania.

## <a name="next-steps"></a>Następne kroki

* Kontynuuj, aby dowiedzieć się więcej na temat [źródeł zdarzeń](./concepts-streaming-ingestion-event-sources.md) dla środowiska Azure Time Series Insights Gen2.
