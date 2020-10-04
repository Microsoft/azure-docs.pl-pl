---
title: Analiza zmian w Azure Monitor dla maszyn wirtualnych
description: Azure Monitor dla maszyn wirtualnych integracja z integracją zmiany aplikacji umożliwia wyświetlanie wszelkich zmian wprowadzonych w maszynie wirtualnej, które mogą mieć na to wydajność.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711553"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Analiza zmian w Azure Monitor dla maszyn wirtualnych
Azure Monitor dla maszyn wirtualnych integracja z integracją [zmiany aplikacji](../app/change-analysis.md) umożliwia wyświetlanie wszelkich zmian wprowadzonych w maszynie wirtualnej, które mogą mieć na to wydajność.

## <a name="overview"></a>Omówienie
Załóżmy, że maszyna wirtualna działa wolno i chcesz sprawdzić, czy ostatnie zmiany w jej konfiguracji mogły mieć zaatakowaną wydajność. Możesz wyświetlić wydajność maszyny wirtualnej przy użyciu Azure Monitor dla maszyn wirtualnych i sprawdzić, czy w ostatniej godzinie wystąpiła wzrost użycia pamięci. Analiza zmian może pomóc w ustaleniu, czy wszystkie zmiany konfiguracji dokonane w tym czasie były przyczyną tego wzrostu.

Usługa analizy zmian aplikacji agreguje zmiany z [grafu zasobów platformy Azure](../../governance/resource-graph/how-to/get-resource-changes.md) , a także zmiany właściwości zagnieżdżonych, takie jak reguły zabezpieczeń sieci z Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Włączanie analizy zmian
Aby dołączyć analizę zmian w Azure Monitor dla maszyn wirtualnych, należy zarejestrować dostawcę zasobów *Microsoft. ChangeAnalysis* . Przy pierwszym uruchomieniu Azure Monitor dla maszyn wirtualnych lub analizie zmian aplikacji w Azure Portal dostawca zasobów zostanie automatycznie zarejestrowany. Analiza zmian aplikacji to bezpłatna usługa, która nie ma dodatkowych kosztów związanych z wydajnością zasobów.

## <a name="view-change-analysis"></a>Wyświetl analizę zmian
Analiza zmian jest dostępna na karcie **wydajność** lub **Mapa** Azure monitor dla maszyn wirtualnych przez wybranie opcji **Zmień** . 

[![Zbadaj zmiany](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kliknij przycisk **Zbadaj zmiany** , aby uruchomić stronę analizy zmian aplikacji przefiltrowaną dla maszyny wirtualnej. Możesz przejrzeć wymienione zmiany, aby sprawdzić, czy istnieją jakieś problemy. Jeśli nie masz pewności dotyczącej określonej zmiany, możesz odwołać się do kolumny **zmiany według** , aby określić osobę, która wprowadziła zmianę.

[![Szczegóły zmiany](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [analizie zmian aplikacji](../app/change-analysis.md).
- Dowiedz się więcej o [usłudze Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

