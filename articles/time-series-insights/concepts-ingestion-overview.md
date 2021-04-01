---
title: Przegląd pozyskiwania — Azure Time Series Insights Gen2 | Microsoft Docs
description: Dowiedz się więcej na temat pozyskiwania danych w Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 2b76490ab23cb14755f6f90a27ec8b176bb30a11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96780029"
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
