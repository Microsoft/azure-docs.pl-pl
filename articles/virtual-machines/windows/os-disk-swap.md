---
title: Wymiana dysku systemu operacyjnego dla maszyny wirtualnej platformy Azure przy użyciu programu PowerShell
description: Zmień dysk systemu operacyjnego używany przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 566347414ffe707b1d68a61b00ba21d19ff2b1eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869383"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Zmienianie dysku systemu operacyjnego używanego przez maszynę wirtualną platformy Azure przy użyciu programu PowerShell

Jeśli masz istniejącą maszynę wirtualną, ale chcesz zamienić dysk na dysk kopii zapasowej lub inny dysk systemu operacyjnego, możesz użyć Azure PowerShell do wymiany dysków systemu operacyjnego. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej. Można nawet użyć dysku zarządzanego w innej grupie zasobów, o ile nie jest on już używany.

 

Maszyna wirtualna musi być stopped\deallocated, a następnie identyfikator zasobu dysku zarządzanego można zastąpić IDENTYFIKATORem zasobu innego dysku zarządzanego.

Upewnij się, że rozmiar maszyny wirtualnej i typ magazynu są zgodne z dyskiem, który chcesz dołączyć. Na przykład jeśli dysk, który ma być używany, znajduje się w Premium Storage, maszyna wirtualna musi mieć możliwość Premium Storage (na przykład rozmiaru serii DS). Oba dyski muszą mieć taki sam rozmiar.

Pobierz listę dysków w grupie zasobów za pomocą polecenia [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Jeśli masz nazwę dysku, którego chcesz użyć, Ustaw jako dysk systemu operacyjnego dla maszyny wirtualnej. Ten przykład stop\deallocates maszynę wirtualną o nazwie *myVM* i przypisuje dysk o nazwie *newDisk* jako nowy dysk systemu operacyjnego. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Następne kroki**

Aby utworzyć kopię dysku, zobacz [migawka dysku](snapshot-copy-managed-disk.md).
