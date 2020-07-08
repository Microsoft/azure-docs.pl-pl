---
title: Przegląd pozyskiwania — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej na temat pozyskiwania danych w Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050115"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Przegląd pozyskiwania danych Azure Time Series Insights

Środowisko Azure Time Series Insights zawiera *aparat* pozyskiwania, który zbiera, przetwarza i przechowuje strumieniowo dane szeregów czasowych. W miarę jak dane docierają do źródeł zdarzeń, Azure Time Series Insights będą zużywać dane i przechowywać je niemal w czasie rzeczywistym.

[![Przegląd pozyskiwania](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Tematy dotyczące pozyskiwania

W poniższych artykułach szczegółowo omówiono przetwarzanie danych, w tym najlepsze rozwiązania, które należy wykonać:

* Przeczytaj o [źródłach zdarzeń](concepts-streaming-ingestion-event-sources.md) i wskazówkach dotyczących wybierania sygnatury czasowej źródła zdarzeń.

* Przejrzyj obsługiwane [typy danych](concepts-supported-data-types.md)

* Dowiedz się, jak aparat pozyskiwania zastosuje zestaw [reguł](./concepts-json-flattening-escaping-rules.md) do właściwości JSON, aby utworzyć kolumny konta magazynu.

* Przejrzyj [ograniczenia przepływności](concepts-streaming-throughput-limitations.md) środowiska, aby zaplanować potrzeby skalowania.

## <a name="next-steps"></a>Następne kroki

* Kontynuuj, aby dowiedzieć się więcej na temat [źródeł zdarzeń](concepts-streaming-ingestion-event-sources.md) dla środowiska Azure Time Series Insights. 
