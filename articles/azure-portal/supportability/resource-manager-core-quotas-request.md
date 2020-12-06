---
title: Azure Resource Manager żądania zwiększenia limitu przydziału vCPU
description: Azure Resource Manager żądania zwiększenia limitu przydziału vCPU
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745251"
---
# <a name="quota-increase-requests"></a>Żądania zwiększenia limitu przydziału

Menedżer zasobów przydziały vCPU dla maszyn wirtualnych i zestawów skalowania maszyn wirtualnych są wymuszane w dwóch warstwach dla każdej subskrypcji w każdym regionie.

Pierwsza warstwa to całkowity limit procesorów wirtualnych vCPU regionalnych dla wszystkich serii maszyn wirtualnych. Druga warstwa to limit procesorów wirtualnych vCPU na serię maszyn wirtualnych, taki jak procesorów wirtualnych vCPU serii D. Gdy zostanie wdrożona nowa maszyna wirtualna, suma nowych i istniejących procesorów wirtualnych vCPU użycie dla tej serii maszyn wirtualnych nie może przekroczyć limitu przydziału vCPU zatwierdzonego dla danej serii maszyn wirtualnych. Dodatkowo całkowita i istniejąca liczba vCPU wdrożona na wszystkich seriach maszyn wirtualnych nie powinna przekraczać całkowitego przydziału regionalnego procesorów wirtualnych vCPU zatwierdzonego dla subskrypcji. Jeśli jeden z tych przydziałów zostanie przekroczony, wdrożenie maszyny wirtualnej nie będzie dozwolone.
Można zażądać zwiększenia limitu przydziału procesorów wirtualnych vCPU dla serii maszyn wirtualnych z Azure Portal. Zwiększenie limitu przydziału serii maszyn wirtualnych powoduje automatyczne zwiększenie łącznej granicy procesorów wirtualnych vCPU w regionie o tej samej wartości.

Po utworzeniu nowej subskrypcji domyślna łączna liczba procesorów wirtualnych vCPU regionalnych może nie być równa sumie domyślnych przydziałów vCPU dla wszystkich poszczególnych serii maszyn wirtualnych. Ten fakt może skutkować subskrypcją z wystarczającym limitem przydziału dla każdej pojedynczej serii maszyn wirtualnych, która ma zostać wdrożona. Może brakować wystarczającego limitu przydziału dla procesorów wirtualnych vCPU regionalnych wszystkich wdrożeń. W takim przypadku musisz przesłać żądanie, aby w sposób jawny zwiększyć łączny limit procesorów wirtualnych vCPU regionalny. Łączny limit procesorów wirtualnych vCPU regionalnych nie może przekroczyć sumy zatwierdzonego przydziału dla całej serii maszyn wirtualnych dla regionu.

> [!NOTE]
> Jeśli chcesz podnieść limit lub przydział powyżej domyślnego limitu, [Otwórz żądanie obsługi klienta online bez dodatkowych opłat](../../azure-resource-manager/templates/error-resource-quota.md#solution).

Aby uzyskać więcej informacji na temat przydziałów, zobacz limity [przydziału vCPU maszyny wirtualnej](../../virtual-machines/windows/quotas.md) i [limitów subskrypcji i usług platformy Azure, przydziałów i ograniczeń](../../azure-resource-manager/management/azure-subscription-service-limits.md).