---
title: Konfigurowanie środowiska docelowego dla serwerów fizycznych w Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania docelowego środowiska platformy Azure na potrzeby odzyskiwania po awarii serwerów fizycznych przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73953900"
---
# <a name="prepare-target-vmware-to-azure"></a>Przygotowywanie celu (VMware do platformy Azure)

W tym artykule opisano, jak przygotować środowisko platformy Azure do uruchamiania replikacji serwerów fizycznych (x64) z systemem Windows lub Linux do platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto:
- Utworzono magazyn Recovery Services, aby chronić serwery fizyczne. Magazyn Recovery Services można utworzyć przy użyciu [Azure Portal](https://portal.azure.com "Azure Portal").
- [Środowisko lokalne zostało skonfigurowane](physical-azure-disaster-recovery.md) w celu replikowania serwerów fizycznych na platformę Azure.

## <a name="prepare-target"></a>Przygotowywanie celu

Po wykonaniu **kroku 1: Wybierz cel ochrony** i **krok 2: przygotowanie źródła**, nastąpi **przejście do kroku 3: cel**

![Przygotowywanie celu](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Subskrypcja:** Z menu rozwijanego wybierz subskrypcję, do której chcesz replikować serwery fizyczne.
2. **Model wdrażania:** Wybierz model wdrażania (klasyczny lub Menedżer zasobów)

W oparciu o wybrany model wdrażania zostanie uruchomiona Walidacja, aby upewnić się, że w subskrypcji docelowej istnieje co najmniej jedno zgodne konto magazynu i Sieć wirtualna do replikacji serwerów fizycznych do programu i przełączania ich w tryb failover.

Po pomyślnym ukończeniu walidacji kliknij przycisk OK, aby przejść do następnego kroku.

Jeśli nie masz zgodnego konta magazynu Menedżer zasobów lub sieci wirtualnej, możesz je utworzyć, klikając przycisk **+ konto magazynu** lub **+ sieci** w górnej części strony.

## <a name="next-steps"></a>Następne kroki
[Skonfiguruj ustawienia replikacji](vmware-azure-set-up-replication.md).
