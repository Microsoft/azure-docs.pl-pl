---
title: Kody błędów dla wystąpień Virtual Machines i zestawów skalowania platformy Azure
description: Dowiedz się więcej o kodach błędów, które mogą być widoczne podczas korzystania z usługi Azure Spot Virtual Machines i wystąpień zestawów skalowania.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 9bea9978f1755e5a40b5fb3ff967eb7f32384d19
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557743"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Komunikaty o błędach dla Virtual Machines i zestawów skalowania na platformie Azure

Poniżej przedstawiono niektóre możliwe kody błędów, które można odbierać podczas korzystania z usługi Azure Virtual Machines i zestawów skalowania.


| Klucz | Komunikat | Opis |
|-----|---------|-------------|
| SkuNotAvailable | Żądana warstwa zasobu " \<resource\> " jest obecnie niedostępna w lokalizacji " \<location\> " dla subskrypcji " \<subscriptionID\> ". Wypróbuj inną warstwę lub Wdróż ją w innej lokalizacji. | Brak wystarczającej pojemności maszyny wirtualnej platformy Azure w tej lokalizacji, aby można było utworzyć maszynę wirtualną lub wystąpienie zestawu skalowania. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Zasady wykluczania można ustawić tylko w Virtual Machines w miejscu na platformie Azure. | Ta maszyna wirtualna nie jest maszyną wirtualną platformy Azure, dlatego nie można ustawić zasad wykluczania. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Maszyna wirtualna platformy Azure w miejscu nie jest obsługiwana w zestawie dostępności. | Musisz wybrać jedną z maszyn wirtualnych platformy Azure lub użyć maszyny wirtualnej w zestawie dostępności, ale nie możesz wybrać obu tych opcji. |
| AzureSpotFeatureNotEnabledForSubscription  |  Subskrypcja nie jest włączona przy użyciu funkcji maszyny wirtualnej platformy Azure. | Użyj subskrypcji, która obsługuje Virtual Machines na platformie Azure. |
| VMPriorityCannotBeApplied  |  Nie można zastosować określonej wartości priorytetu " {0} " do maszyny wirtualnej " {1} ", ponieważ nie określono priorytetu podczas tworzenia maszyny wirtualnej. | Określ priorytet podczas tworzenia maszyny wirtualnej. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Nie można wykonać operacji " {0} ", ponieważ podana cena maksymalna " {1} USD" jest niższa niż bieżąca cena punktowa " {2} USD" dla rozmiaru maszyny wirtualnej platformy Azure " {3} ". | Wybierz wyższą cenę maksymalną. Aby uzyskać więcej informacji, zobacz informacje o cenach dla [systemu](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub Windows.|
| MaxPriceValueInvalid  |  Nieprawidłowa maksymalna wartość ceny. Jedyne obsługiwane wartości w polu Maksymalna cena to-1 lub liczba dziesiętna większa od zera. Cena maksymalna wartość-1 oznacza, że maszyna wirtualna platformy Azure nie zostanie wykluczona ze względu na cenę. | Wprowadź prawidłową maksymalną cenę. Aby uzyskać więcej informacji, zobacz cennik dla systemu [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) lub [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Zmiana ceny maksymalnej nie jest dozwolona, gdy maszyna wirtualna " {0} " jest aktualnie przypisana. Cofnij przydział i spróbuj ponownie. | Stop\Deallocate maszynę wirtualną, aby można było zmienić maksymalną cenę. |
| MaxPriceChangeNotAllowed | Maksymalna zmiana ceny jest niedozwolona. | Nie można zmienić maksymalnej ceny dla tej maszyny wirtualnej. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Maszyna wirtualna platformy Azure w miejscu nie jest obsługiwana w tej wersji interfejsu API. | Wersja interfejsu API musi mieć wartość 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Maszyna wirtualna platformy Azure w miejscu nie jest obsługiwana w przypadku tego rozmiaru maszyny wirtualnej {0} . | Wybierz inny rozmiar maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Virtual Machines na platformie Azure](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Cena maksymalna jest obsługiwana tylko w przypadku Virtual Machines na platformie Azure. | Aby uzyskać więcej informacji, zobacz [Virtual Machines na platformie Azure](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Żądanie Move Resources zawiera maszynę wirtualną platformy Azure. Nieobsługiwane. Sprawdź szczegóły błędu dotyczące identyfikatorów maszyn wirtualnych. | Nie można przenieść Virtual Machines w miejscu na platformie Azure. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Żądanie Move Resources zawiera zestaw skalowania maszyn wirtualnych platformy Azure. Nieobsługiwane. Sprawdź szczegóły błędu dotyczące identyfikatorów zestawu skalowania maszyn wirtualnych. | Nie można przenieść wystąpień zestawu skalowania maszyn wirtualnych platformy Azure. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Maszyna wirtualna platformy Azure nie jest obsługiwana w zestawie skalowania maszyn wirtualnych z trybem aranżacji maszyny wirtualnej. | Ustaw tryb aranżacji na zestaw skalowania maszyn wirtualnych, aby korzystać z wystąpień maszyn wirtualnych platformy Azure. |


**Następne kroki** Aby uzyskać więcej informacji, zobacz [Virtual Machines](./spot-vms.md).