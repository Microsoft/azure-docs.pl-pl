---
title: Jak skalować środowisko — usługa Azure Time Series Insights| Dokumenty firmy Microsoft
description: Dowiedz się, jak skalować środowisko usługi Azure Time Series Insights przy użyciu witryny Azure portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/17/2020
ms.custom: seodec18
ms.openlocfilehash: 9604f0c6eeb9d0ac4b7d07f1ab1ba03285fd60fe
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640064"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Jak skalować środowisko usługi Time Series Insights

W tym artykule opisano, jak zmienić pojemność środowiska usługi Time Series Insights przy użyciu [witryny Azure portal.](https://portal.azure.com) Pojemność to mnożnik zastosowany do szybkości transferu danych przychodzących, pojemności magazynu i kosztów skojarzonych z wybraną jednostką SKU.

Za pomocą witryny Azure portal można zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cenowej.

Jednak zmiana jednostki SKU warstwy cenowej jest niedozwolona. Na przykład środowiska z jednostką SKU cen S1 nie można przekonwertować na S2 lub odwrotnie.

## <a name="ga-limits"></a>Limity GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Zmiana pojemności środowiska

1. W witrynie Azure portal znajdź i wybierz środowisko usługi Time Series Insights.

1. W menu dla środowiska usługi Time Series Insights wybierz pozycję **Konfiguracja magazynu**.

   [![Konfigurowanie możliwości usługi Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Dostosuj **suwak Pojemność,** aby wybrać pojemność, która spełnia wymagania dotyczące szybkości transferu danych przychodzących i pojemności. Zwróć uwagę na **szybkość transferu danych przychodzących,** **pojemność magazynu**i szacowaną aktualizację **kosztu** dynamicznie, aby pokazać wpływ zmiany.

   [![Konfigurowanie środowiska za pomocą suwaka pojemności](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Alternatywnie można wpisać numer mnożnika pojemności w polu tekstowym po prawej stronie suwaka.

1. Wybierz **pozycję Zapisz,** aby skalować środowisko. Wskaźnik postępu jest wyświetlany do momentu, gdy zmiana zostanie zatwierdzona, na chwilę.

1. Sprawdź, czy nowa pojemność jest [wystarczająca, aby zapobiec ograniczaniu przepustowości](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj [się z opisem przechowywania w aplikacji Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz się więcej o [konfigurowaniu przechowywania danych w usłudze Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej o [planowaniu środowiska](time-series-insights-environment-planning.md).
