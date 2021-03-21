---
title: Użyj portalu, aby wdrożyć Virtual Machines platformy Azure
description: Jak używać Azure PowerShell do wdrożenia Virtual Machines w celu oszczędności kosztów.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 879a3e9b3d3f651a1dea17e76dba503cd2816b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098574"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Wdróż Virtual Machines w miejscu na platformie Azure przy użyciu Azure Portal

Korzystanie z [usługi Azure Spot Virtual Machines](spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines na platformie Azure. Z tego względu Virtual Machines na platformie Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Cennik usługi Azure Virtual Machines w miejscu to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Virtual Machines platformy Azure — cennik](spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej usługi Azure Spot można ustawić w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.05701` Cena maksymalna $0,05701 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.

Po wykluczeniu maszyny wirtualnej można usunąć maszynę wirtualną i dysk podstawowy lub cofnąć jej alokację, aby można było ją ponownie uruchomić później.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Podczas wdrażania maszyny wirtualnej możesz użyć wystąpienia usługi Azure spot.


Na karcie **podstawowe** w sekcji **szczegóły wystąpienia** **nie** jest domyślnie używana usługa Azure Spot instance.

![Przechwytywanie ekranu w celu wybrania opcji nie, nie używaj wystąpienia usługi Azure spot](./media/spot-portal/no.png)

Jeśli wybierzesz opcję **tak**, sekcja zostanie rozwinięta i będzie można wybrać [Typ wykluczenia i zasady wykluczania](spot-vms.md#eviction-policy). 

![Przechwytywanie ekranu w celu wybrania opcji tak, użyj wystąpienia usługi Azure spot](./media/spot-portal/yes.png)

Możesz również porównać ceny i stawki wykluczenia z innymi podobnymi regionami, wybierając pozycję **Wyświetl historię cen i porównać ceny w pobliżu regionów**.

W tym przykładzie region Kanada Środkowa jest tańszy i ma niższą stawkę wykluczenia niż region Wschodnie stany USA.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Zrzut ekranu przedstawiający opcje regionu z różnicą cen i stawek wykluczania.":::

Region można zmienić, wybierając wybór, który działa najlepiej dla Ciebie, a następnie wybierając **przycisk OK**.

## <a name="simulate-an-eviction"></a>Symulowanie wykluczenia

Możesz [symulować wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej platformy Azure w celu przetestowania, jak dobrze aplikacja będzie reagować na nagłe wykluczenie. 

Zastąp następujące informacje następującymi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` oznacza, że symulowane wykluczenie zakończyło się pomyślnie. 

## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć Virtual Machines w miejscu na platformie Azure przy użyciu [programu PowerShell](./windows/spot-powershell.md), [interfejsu wiersza polecenia](./linux/spot-cli.md)lub [szablonu](./linux/spot-template.md).
