---
title: Wybierz narzędzie do przemieszczania zasobów platformy Azure między regionami
description: Przegląd opcji i narzędzi do przemieszczania zasobów platformy Azure między regionami
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90603360"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Wybierz narzędzie do przeniesienia zasobów platformy Azure

Zasoby można przenieść na platformę Azure w następujący sposób:

- **Przenoszenie zasobów między regionami**: Przenieś zasób z poziomu centrum przenoszenia zasobów lub grupy zasobów. 
- **Przenoszenie zasobów między grupami zasobów/subskrypcjami**: przenoszenie z grupy zasobów. 
- **Przenoszenie zasobów między chmurami platformy Azure**: usługa Azure Site Recovery służy do przenoszenia zasobów między chmurami publicznymi i administracyjnymi.
- **Przenoszenie zasobów między strefami dostępności w tym samym regionie**: użyj usługi Azure Site Recovery do przenoszenia zasobów między strefami dostępności w tym samym regionie świadczenia usługi Azure.


## <a name="compare-move-tools"></a>Porównanie narzędzi przenoszenia

**Narzędzie** | **Kiedy stosować** | **Dowiedz się więcej**
--- | --- | ---
**Przenieś w grupie zasobów** | Przenoszenie zasobów do innej grupy zasobów/subskrypcji lub między regionami.<br/><br/> W przypadku przechodzenia między regionami w grupie zasobów wybierasz zasoby, które chcesz przenieść, a następnie przejdź do centrum przenoszenia zasobów, aby sprawdzić zależności i przenieść zasoby do regionu docelowego. | [Przenoszenie zasobów do innej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Przenoszenie zasobów do innego regionu z grupy zasobów](move-region-within-resource-group.md).
**Przenoszenie z centrum przenoszenia zasobów** | Przenoszenie zasobów między regionami. <br/><br/> W regionie docelowym można przenieść do regionu docelowego lub do określonej strefy dostępności lub zestawu dostępności. | [Przenoszenie zasobów między regionami w centrum przenoszenia zasobów]().
**Przenoszenie maszyn wirtualnych za pomocą Site Recovery** | Służy do przemieszczania maszyn wirtualnych platformy Azure między administracją rządową i chmurą publiczną.<br/><br/> Użyj, jeśli chcesz przenieść maszyny wirtualne między strefami dostępności w tym samym regionie. |[Przenoszenie zasobów między chmurami rządowymi i publicznymi](../site-recovery/region-move-cross-geos.md), [przenoszenie zasobów do stref dostępności w tym samym regionie](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](about-move-process.md) na temat składników przenoszenia zasobów i procesu przenoszenia.
