---
title: Odłączaj dysk danych z maszyny Wirtualnej systemu Windows — Azure
description: Odłączaj dysk danych z maszyny wirtualnej na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: c93bb5fd3e92c6a947fe997b58207b87b2717fd5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082768"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>How to detach a data disk from a Windows virtual machine (Jak odłączyć dysk od maszyny wirtualnej systemu Windows)

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Spowoduje to usunięcie dysku z maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu.

> [!WARNING]
> W przypadku odłączenia dysku nie jest on automatycznie usuwany. Jeśli zasubskrybujesz magazyn w wersji Premium, nadal będziesz ponosić opłaty za magazyn dysku. Aby uzyskać więcej informacji, zobacz [Ceny i rozliczenia podczas korzystania z magazynu w wersji Premium](disks-types.md#billing).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odłączaj dysk danych przy użyciu programu PowerShell

Dysk z danymi za pomocą programu PowerShell można usunąć na *gorąco,* ale upewnij się, że nic nie używa aktywnie dysku przed odłączeniem go od maszyny Wirtualnej.

W tym przykładzie usuwamy dysk o nazwie **myDisk** z maszyny wirtualnej **myVM** w grupie zasobów **myResourceGroup.** Najpierw należy usunąć dysk za pomocą polecenia cmdlet [Remove-AzVMDataDisk.](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) Następnie należy zaktualizować stan maszyny wirtualnej przy użyciu polecenia cmdlet [Update-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) aby zakończyć proces usuwania dysku danych.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="detach-a-data-disk-using-the-portal"></a>Odłączanie dysku danych przy użyciu portalu

Możesz usunąć dysk danych na *gorąco,* ale upewnij się, że nic nie używa aktywnie dysku przed odłączeniem go od maszyny Wirtualnej.

1. W menu po lewej stronie wybierz polecenie **Maszyny wirtualne**.
1. Wybierz maszynę wirtualną, która ma dysk danych, który chcesz odłączyć.
1. W obszarze **Ustawienia** wybierz pozycję **Dyski**.
1. U góry okienka **Dyski** wybierz pozycję **Edytuj**.
1. W okienku **Dyski** po prawej stronie dysku danych, który chcesz odłączyć, wybierz pozycję **Odłącz**.
1. Wybierz **pozycję Zapisz** u góry strony, aby zapisać zmiany.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz ponownie użyć dysku danych, możesz po prostu [dołączyć go do innej maszyny Wirtualnej](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
