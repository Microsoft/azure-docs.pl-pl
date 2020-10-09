---
title: Przygotuj miejsce docelowe replikacji maszyny wirtualnej VMware w Azure Site Recovery
description: W tym artykule opisano sposób przygotowania docelowego środowiska platformy Azure na potrzeby replikacji maszyny wirtualnej VMware na platformę Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73693170"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Przygotuj środowisko docelowe na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure

W tym artykule opisano, jak przygotować docelowe środowisko platformy Azure, aby rozpocząć replikowanie maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto:
- Utworzono magazyn Recovery Services na [Azure Portal](https://portal.azure.com "Azure Portal") , aby chronić maszyny źródłowe
- Środowisko lokalne zostało skonfigurowane w celu replikowania źródłowych [maszyn wirtualnych VMware](vmware-azure-set-up-source.md) lub [serwerów fizycznych](physical-azure-set-up-source.md) na platformę Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po wykonaniu **kroku 1: Wybierz cel ochrony** i **krok 2: przygotowanie źródła**, nastąpi **przejście do kroku 3: cel**

![Przygotowywanie celu](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego wybierz subskrypcję, do której chcesz replikować maszyny wirtualne lub serwery fizyczne.
2. **Model wdrażania:** Wybierz model wdrażania (klasyczny lub Menedżer zasobów)

W oparciu o wybrany model wdrożenia zostanie uruchomiona Walidacja, aby upewnić się, że w subskrypcji docelowej istnieje co najmniej jedna sieć wirtualna do replikacji i przejścia do trybu failover maszyny wirtualnej lub serwera fizycznego do programu.

Po pomyślnym ukończeniu walidacji kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, klikając przycisk **+ Sieć** w górnej części strony.

## <a name="next-steps"></a>Następne kroki
[Skonfiguruj ustawienia replikacji](vmware-azure-set-up-replication.md).
