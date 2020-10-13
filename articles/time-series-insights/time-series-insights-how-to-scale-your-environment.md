---
title: Jak skalować środowisko — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak skalować środowisko Azure Time Series Insights przy użyciu Azure Portal.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 0dadf523c5d17ffb91f4fefa71b52d1d1855c978
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570247"
---
# <a name="how-to-scale-your-azure-time-series-insights-gen1-environment"></a>Jak skalować Azure Time Series Insights środowisko Gen1

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano sposób zmiany pojemności środowiska Azure Time Series Insights przy użyciu [Azure Portal](https://portal.azure.com). Pojemność to mnożnik stosowany do szybkości transferu danych przychodzących, pojemności magazynu i kosztu związanego z wybraną jednostką SKU.

Za pomocą Azure Portal można zwiększyć lub zmniejszyć pojemność w ramach danej jednostki SKU cenowej.

Jednak zmiana jednostki SKU warstwy cenowej jest niedozwolona. Na przykład środowisko z jednostką SKU cen S1 nie może być konwertowane na S2 lub odwrotnie.

## <a name="ga-limits"></a>Limity GA

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>Zmień pojemność środowiska

1. W Azure Portal Znajdź i wybierz środowisko Azure Time Series Insights.

1. W menu środowiska Azure Time Series Insights wybierz pozycję **Konfiguracja magazynu**.

   [![Konfigurowanie pojemności Azure Time Series Insights](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Dostosuj suwak **pojemności** , aby wybrać pojemność, która spełnia wymagania dotyczące stawek za transfer danych przychodzących i pojemności magazynu. Zwróć uwagę, że **szybkość**transferu danych przychodzących, **pojemność magazynu**i **Szacowana aktualizacja kosztu** są dynamiczne, aby pokazać wpływ zmiany.

   [![Konfigurowanie środowiska przy użyciu suwaka pojemności](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Alternatywnie można wpisać liczbę mnożnika pojemności do pola tekstowego po prawej stronie suwaka.

1. Wybierz pozycję **Zapisz** , aby skalować środowisko. Wskaźnik postępu jest wyświetlany do momentu, aż zmiana zostanie zatwierdzona.

1. Sprawdź, czy nowa pojemność jest [wystarczająca, aby zapobiec ograniczaniu wydajności](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zapoznaj się [z tematem przechowywanie w Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Dowiedz się więcej o [konfigurowaniu przechowywania danych w usłudze Azure Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej [na temat planowania środowiska](time-series-insights-environment-planning.md).
