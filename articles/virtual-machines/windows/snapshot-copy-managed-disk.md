---
title: Tworzenie migawki wirtualnego dysku twardego przy użyciu portalu lub programu PowerShell
description: Dowiedz się, jak utworzyć kopię maszyny wirtualnej platformy Azure, która ma być używana jako kopia zapasowa lub w celu rozwiązywania problemów przy użyciu portalu lub programu PowerShell.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.openlocfilehash: 9070b69ac4c6b85791ff3dd4662273e75a3cd22c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556064"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Tworzenie migawki przy użyciu portalu lub programu PowerShell

Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego (VHD). Możesz utworzyć migawkę dysku VHD systemu operacyjnego lub danych, aby użyć go jako kopii zapasowej, lub rozwiązać problemy z maszynami wirtualnymi.

Jeśli zamierzasz utworzyć nową maszynę wirtualną przy użyciu migawki, zalecamy wyczyszczenie maszyny wirtualnej przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal 

Aby utworzyć migawkę, wykonaj następujące czynności: 
1.  Na [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**.
2. Wyszukaj i wybierz pozycję **migawka**.
3. W oknie **migawki** wybierz pozycję **Utwórz**. Zostanie wyświetlone okno **Utwórz migawkę** .
4. Wprowadź **nazwę** migawki.
5. Wybierz istniejącą [grupę zasobów](../../azure-resource-manager/management/overview.md#resource-groups) lub wprowadź nazwę nowej. 
6. Wybierz **lokalizację** centrum danych Azure.  
7. W polu **dysk źródłowy** wybierz dysk zarządzany do utworzenia migawki.
8. Wybierz **Typ konta** , który ma być używany do przechowywania migawki. Wybierz pozycję **Standard_HDD**, chyba że potrzebujesz, aby migawka była przechowywana na dysku o wysokiej wydajności.
9. Wybierz przycisk **Utwórz**.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell

Poniższe kroki pokazują, jak skopiować dysk VHD i utworzyć konfigurację migawki. Następnie można wykonać migawkę dysku za pomocą polecenia cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

 

1. Ustaw niektóre parametry: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Pobierz maszynę wirtualną:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Utwórz konfigurację migawki. W tym przykładzie migawka jest dyskiem systemu operacyjnego:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Jeśli chcesz przechowywać migawkę w magazynie odpornym na strefy, utwórz ją w regionie, który obsługuje [strefy dostępności](../../availability-zones/az-overview.md) i Uwzględnij `-SkuName Standard_ZRS` parametr.   
   
4. Zrób migawkę:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Następne kroki

Tworzenie maszyny wirtualnej na podstawie migawki przez utworzenie dysku zarządzanego na podstawie migawki, a następnie dołączenie nowego dysku zarządzanego jako dysku systemu operacyjnego. Aby uzyskać więcej informacji, zobacz przykład w temacie [Tworzenie maszyny wirtualnej na podstawie migawki przy użyciu programu PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot).