---
title: Korzystanie z portalu do wdrażania maszyn wirtualnych usługi Azure spot
description: Jak używać Azure PowerShell do wdrażania maszyn wirtualnych w celu oszczędzania kosztów.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 0da650646c35a9a663dd29589f963d23cbe552cf
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828487"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Wdrażanie maszyn wirtualnych przy użyciu Azure Portal

Korzystanie z [maszyn wirtualnych na miejscu](spot-vms.md) pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.05701` Cena maksymalna $0,05701 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.

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

Możesz [symulować wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) maszyny wirtualnej na miejscu, aby przetestować, w jaki sposób aplikacja będzie odistnieć do nagłego wykluczenia. 

Zastąp następujące informacje następującymi informacjami: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Następne kroki

Możesz również tworzyć maszyny wirtualne za pomocą [programu PowerShell](./windows/spot-powershell.md), [interfejsu wiersza polecenia](./linux/spot-cli.md)lub [szablonu](./linux/spot-template.md).
