---
title: Przenoszenie zasobu maszyny wirtualnej z systemem Windows na platformie Azure
description: Przenieś maszynę wirtualną z systemem Windows do innej subskrypcji platformy Azure lub grupy zasobów w Menedżer zasobów model wdrażania.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: c5d3445144178ce855a5ce904b42d6e68a8e7d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555248"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Przenoszenie maszyny wirtualnej z systemem Windows do innej subskrypcji platformy Azure lub grupy zasobów
W tym artykule pokazano, jak przenieść maszynę wirtualną z systemem Windows między grupami zasobów lub subskrypcjami. Przenoszenie między subskrypcjami może być przydatne, jeśli pierwotnie utworzono maszynę wirtualną w prywatnej subskrypcji, a teraz chcesz przenieść ją do subskrypcji firmy, aby kontynuować pracę. Nie jest konieczne zatrzymanie maszyny wirtualnej w celu przeniesienia jej i kontynuowanie jej działania podczas przenoszenia.

> [!IMPORTANT]
>Nowe identyfikatory zasobów są tworzone w ramach przenoszenia. Po przeniesieniu maszyny wirtualnej należy zaktualizować narzędzia i skrypty w celu użycia nowych identyfikatorów zasobów.
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>Przenoszenie maszyny wirtualnej przy użyciu programu PowerShell

Aby przenieść maszynę wirtualną do innej grupy zasobów, należy upewnić się, że wszystkie zasoby zależne są również przeniesione. Aby uzyskać listę z IDENTYFIKATORem zasobu dla każdego z tych zasobów, należy użyć polecenia cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) .

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

Możesz użyć danych wyjściowych poprzedniego polecenia, aby utworzyć listę rozdzielonych przecinkami identyfikatorów zasobów do [przenoszenia-AzResource](/powershell/module/az.resources/move-azresource) , aby przenieść każdy zasób do miejsca docelowego.

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

Aby przenieść zasoby do innej subskrypcji, Dołącz parametr **-DestinationSubscriptionId** .

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


Gdy zostanie wyświetlony monit o potwierdzenie, że chcesz przenieść określone zasoby, wpisz **Y** , aby potwierdzić.

## <a name="next-steps"></a>Następne kroki
Wiele różnych typów zasobów można przenosić między grupami zasobów i subskrypcjami. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md).    
