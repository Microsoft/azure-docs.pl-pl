---
title: Odłączanie dysku danych z maszyny wirtualnej z systemem Windows — Azure
description: Odłączanie dysku danych z maszyny wirtualnej na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: cae75c88b4803912565e010f744a7757a3b98f04
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201556"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>How to detach a data disk from a Windows virtual machine (Jak odłączyć dysk od maszyny wirtualnej systemu Windows)

Gdy już nie potrzebujesz dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Spowoduje to usunięcie dysku z maszyny wirtualnej, ale nie powoduje usunięcia go z magazynu.

> [!WARNING]
> Jeśli dysk zostanie odłączony, nie jest automatycznie usuwany. Jeśli subskrybujesz usługę Premium Storage, nadal będą naliczane opłaty za magazyn dla tego dysku. Aby uzyskać więcej informacji, zobacz [Cennik i rozliczenia w przypadku korzystania z Premium Storage](../disks-types.md#billing).

Jeśli chcesz użyć danych znajdujących się na tym dysku, możesz dołączyć go ponownie do tej samej lub innej maszyny wirtualnej.

 

## <a name="detach-a-data-disk-using-powershell"></a>Odłączanie dysku danych przy użyciu programu PowerShell

Możesz *gorąco* usuwanie dysku danych przy użyciu programu PowerShell, ale przed odłączeniem go od maszyny wirtualnej nie trzeba aktywnie używać dysku.

W tym przykładzie usuniemy **dysk o nazwie** **myVM** z maszyny wirtualnej z **grupy zasobów.** Najpierw należy usunąć dysk przy użyciu polecenia cmdlet [Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) . Następnie należy zaktualizować stan maszyny wirtualnej za pomocą polecenia cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) , aby ukończyć proces usuwania dysku z danymi.

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

Można *gorąco* usuwanie dysku danych, ale nie należy aktywnie używać dysku przed odłączeniem go od maszyny wirtualnej.

1. W menu po lewej stronie wybierz pozycję **Virtual Machines**.
1. Wybierz maszynę wirtualną zawierającą dysk danych, który chcesz odłączyć.
1. W obszarze **Ustawienia** wybierz pozycję **Dyski**.
1. W okienku **dyski** z prawej strony dysku z danymi, które chcesz odłączyć, kliknij przycisk Usuń **X** .
1. Wybierz pozycję **Zapisz** w górnej części strony, aby zapisać zmiany.

Dysk pozostaje w magazynie, ale nie jest już dołączony do maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz ponownie użyć dysku z danymi, możesz po prostu [dołączyć go do innej maszyny wirtualnej](attach-managed-disk-portal.md)
