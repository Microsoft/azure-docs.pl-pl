---
title: Analiza zmian w usłudze VM Insights
description: Integracja usługi VM Insights z integracją analizy zmian aplikacji umożliwia wyświetlanie wszelkich zmian wprowadzonych w maszynie wirtualnej, które mogą mieć na to wydajność.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046775"
---
# <a name="change-analysis-in-vm-insights"></a>Analiza zmian w usłudze VM Insights
Integracja usługi VM Insights z integracją [analizy zmian aplikacji](../app/change-analysis.md) umożliwia wyświetlanie wszelkich zmian wprowadzonych w maszynie wirtualnej, które mogą mieć na to wydajność.

## <a name="overview"></a>Omówienie
Załóżmy, że maszyna wirtualna działa wolno i chcesz sprawdzić, czy ostatnie zmiany w jej konfiguracji mogły mieć zaatakowaną wydajność. Możesz wyświetlić wydajność maszyny wirtualnej za pomocą szczegółowych informacji o maszynach wirtualnych i sprawdzić, czy w ostatniej godzinie wystąpiła wzrost użycia pamięci. Analiza zmian może pomóc w ustaleniu, czy wszystkie zmiany konfiguracji dokonane w tym czasie były przyczyną tego wzrostu.

Usługa analizy zmian aplikacji agreguje zmiany z [grafu zasobów platformy Azure](../../governance/resource-graph/how-to/get-resource-changes.md) , a także zmiany właściwości zagnieżdżonych, takie jak reguły zabezpieczeń sieci z Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Włączanie analizy zmian
Aby dołączyć analizę zmian w usłudze VM Insights, należy zarejestrować dostawcę zasobów *Microsoft. ChangeAnalysis* . Przy pierwszym uruchomieniu analizy zmian w usłudze VM lub w Azure Portal ten dostawca zasobów zostanie automatycznie zarejestrowany. Analiza zmian aplikacji to bezpłatna usługa, która nie ma dodatkowych kosztów związanych z wydajnością zasobów.

## <a name="view-change-analysis"></a>Wyświetl analizę zmian
Analiza zmian jest dostępna na karcie **wydajność** lub **Mapa** usługi VM Insights, wybierając opcję **Zmień** . 

[![Zbadaj zmiany](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kliknij przycisk **Zbadaj zmiany** , aby uruchomić stronę analizy zmian aplikacji przefiltrowaną dla maszyny wirtualnej. Możesz przejrzeć wymienione zmiany, aby sprawdzić, czy istnieją jakieś problemy. Jeśli nie masz pewności dotyczącej określonej zmiany, możesz odwołać się do kolumny **zmiany według** , aby określić osobę, która wprowadziła zmianę.

[![Szczegóły zmiany](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [analizie zmian aplikacji](../app/change-analysis.md).
- Dowiedz się więcej o [usłudze Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

