---
title: Kody błędów dla maszyn wirtualnych i wystąpień zestawów skalowania platformy Azure
description: Informacje o kodach błędów, które mogą być widoczne podczas korzystania z maszyn wirtualnych na miejscu i wystąpień zestawów skalowania.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547819"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Komunikaty o błędach dotyczące dodatkowych maszyn wirtualnych i zestawów skalowania

Poniżej przedstawiono niektóre możliwe kody błędów, które można odbierać podczas korzystania z maszyn wirtualnych i zestawów skalowania.


| Key | Komunikat | Opis |
|-----|---------|-------------|
| SkuNotAvailable | Żądana warstwa zasobu\<"Resource\>" jest obecnie niedostępna w lokalizacji\<\>"% Subscription" subskrypcji\<\>". Wypróbuj inną warstwę lub Wdróż ją w innej lokalizacji. | W tej lokalizacji nie ma wystarczającej pojemności platformy Azure, aby utworzyć maszynę wirtualną lub wystąpienie zestawu skalowania. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zasady wykluczania można ustawić tylko w Virtual Machines w miejscu na platformie Azure. | Ta maszyna wirtualna nie jest maszyną wirtualną, dlatego nie można ustawić zasad wykluczania. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Maszyna wirtualna platformy Azure w miejscu nie jest obsługiwana w zestawie dostępności. | Musisz wybrać lub użyć maszyny wirtualnej na miejscu lub użyć maszyny wirtualnej w zestawie dostępności, ale nie możesz wybrać obu tych opcji. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subskrypcja nie jest włączona z funkcją Azure spot. | Użyj subskrypcji, która obsługuje dodatkowe maszyny wirtualne. |
| VMPriorityCannotBeApplied  |  Nie można zastosować określonej wartości{0}priorytetu "" do maszyny wirtualnej "{1}", ponieważ nie określono priorytetu podczas tworzenia maszyny wirtualnej. | Określ priorytet podczas tworzenia maszyny wirtualnej. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Nie można wykonać operacji "{0}", ponieważ podana cena maksymalna "{1} USD" jest niższa niż bieżąca cena spot "{2} USD" dla rozmiaru maszyny wirtualnej usługi Azure Spot{3}"". | Wybierz wyższą cenę maksymalną. Aby uzyskać więcej informacji, zobacz informacje o cenach dla [systemu](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub Windows.|
| MaxPriceValueInvalid  |  Nieprawidłowa maksymalna wartość ceny. Jedyne obsługiwane wartości w polu Maksymalna cena to-1 lub liczba dziesiętna większa od zera. Cena maksymalna wartość-1 oznacza, że maszyna wirtualna platformy Azure nie zostanie wykluczona ze względu na cenę. | Wprowadź prawidłową maksymalną cenę. Aby uzyskać więcej informacji, zobacz cennik dla systemu [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Zmiana ceny maksymalnej nie jest dozwolona, gdy maszyna wirtualna{0}"" jest aktualnie przypisana. Cofnij przydział i spróbuj ponownie. | Stop\Deallocate maszynę wirtualną, aby można było zmienić maksymalną cenę. |
| MaxPriceChangeNotAllowed | Maksymalna zmiana ceny jest niedozwolona. | Nie można zmienić maksymalnej ceny dla tej maszyny wirtualnej. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Usługa Azure Spot nie jest obsługiwana w przypadku tej wersji interfejsu API. | Wersja interfejsu API musi mieć wartość 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Usługa Azure Spot nie jest obsługiwana w przypadku tego {0}rozmiaru maszyny wirtualnej. | Wybierz inny rozmiar maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Cena maksymalna jest obsługiwana tylko w przypadku Virtual Machines na platformie Azure. | Aby uzyskać więcej informacji, zobacz [Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Żądanie Move Resources zawiera maszynę wirtualną platformy Azure. Nie jest to obecnie obsługiwane. Sprawdź szczegóły błędu dotyczące identyfikatorów maszyn wirtualnych. | Nie można przenosić maszyn wirtualnych. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Żądanie Move Resources zawiera zestaw skalowania maszyn wirtualnych platformy Azure. Nie jest to obecnie obsługiwane. Sprawdź szczegóły błędu dotyczące identyfikatorów zestawu skalowania maszyn wirtualnych. | Nie można przenieść wystąpień zestawów skalowania dodatkowego. |
| EphemeralOSDisksNotSupportedForSpotVMs | Tymczasowe dyski systemu operacyjnego nie są obsługiwane w przypadku maszyn wirtualnych. | Użyj zwykłego dysku systemu operacyjnego dla maszyny wirtualnej. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Maszyna wirtualna platformy Azure nie jest obsługiwana w zestawie skalowania maszyn wirtualnych z trybem aranżacji maszyny wirtualnej. | Ustaw tryb aranżacji na zestaw skalowania maszyn wirtualnych, aby korzystać z wystąpień dodatkowych. |


**Następne kroki** Aby uzyskać więcej informacji, zobacz [Virtual Machines](./linux/spot-vms.md).