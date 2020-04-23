---
title: Konwertowanie maszyny wirtualnej z systemem Windows z dysków niezarządzanych na dyski zarządzane
description: Jak przekonwertować maszynę wirtualną systemu Windows z dysków niezarządzanych na dyski zarządzane przy użyciu programu PowerShell w modelu wdrażania Menedżera zasobów
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: d8069b174b7a69cc2e6c47171159569c56a15563
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081952"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertowanie maszyny wirtualnej z systemem Windows z dysków niezarządzanych na dyski zarządzane

Jeśli masz istniejące maszyny wirtualne systemu Windows (VM), które używają dysków niezarządzanych, możesz przekonwertować maszyny wirtualne na dyski zarządzane za pośrednictwem usługi [Azure Managed Disks.](managed-disks-overview.md) Ten proces konwertuje zarówno dysk systemu operacyjnego, jak i wszystkie dołączone dyski danych.

 

## <a name="before-you-begin"></a>Przed rozpoczęciem


* Przejrzyj [plan migracji do dysków zarządzanych](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Przejrzyj [często zadawane pytania dotyczące migracji do dysków zarządzanych](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona. Jeśli chcesz znaleźć te dyski nieprzyłączone, aby je usunąć, zobacz nasz artykuł [Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure.](find-unattached-disks.md)


## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z pojedynczym wystąpieniem
W tej sekcji opisano sposób konwertowania maszyn wirtualnych platformy Azure z dysków niezarządzanych z dysków niezarządzanych na dyski zarządzane. (Jeśli maszyny wirtualne znajdują się w zestawie dostępności, zobacz następną sekcję). 

1. Przydziel alokację maszyny Wirtualnej przy użyciu polecenia cmdlet [Stop-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) Poniższy przykład przydziela maszynę wirtualną o nazwie `myVM` w grupie zasobów o nazwie: `myResourceGroup` 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konwertuj maszynę wirtualną na dyski zarządzane przy użyciu polecenia cmdlet [ConvertTo-AzVMManagedDisk.](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk) Następujący proces konwertuje poprzednią maszynę wirtualną, w tym dysk systemu operacyjnego i wszystkie dyski z danymi, i uruchamia maszynę wirtualną:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które chcesz przekonwertować na dyski zarządzane, są ustawione w zestawie dostępności, należy najpierw przekonwertować zestaw dostępności na zestaw dostępności zarządzanej.

1. Konwertuj zestaw dostępności przy użyciu polecenia cmdlet [Update-AzAvailabilitySet.](https://docs.microsoft.com/powershell/module/az.compute/update-azavailabilityset) Poniższy przykład aktualizuje zestaw `myAvailabilitySet` dostępności nazwany `myResourceGroup`w grupie zasobów o nazwie:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Jeśli region, w którym znajduje się zestaw dostępności, ma tylko 2 domeny błędów zarządzanych, ale liczba niezarządzanych domen błędów wynosi 3, to polecenie pokazuje błąd podobny do "Określona liczba domen błędów 3 musi leżyć w zakresie od 1 do 2". Aby rozwiązać ten problem, zaktualizuj `Sku` `Aligned` domenę błędów do 2 i zaktualizuj do następujących czynności:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Przydziel alokację i przekonwertuj maszyny wirtualne w zestawie dostępności. Następujący skrypt przydziela każdą maszynę wirtualną przy użyciu polecenia cmdlet [Stop-AzVM,](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) konwertuje ją za pomocą [funkcji ConvertTo-AzVMManagedDisk](https://docs.microsoft.com/powershell/module/az.compute/convertto-azvmmanageddisk)i automatycznie uruchamia ją automatycznie, ponieważ jest ona oddzielona od procesu konwersji:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wystąpi błąd podczas konwersji lub jeśli maszyna wirtualna jest w stanie awarii z `ConvertTo-AzVMManagedDisk` powodu problemów w poprzedniej konwersji, uruchom polecenie cmdlet ponownie. Prosta ponowna próby zwykle odblokowuje sytuację.
Przed konwersją upewnij się, że wszystkie rozszerzenia maszyny Wirtualnej są w stanie "Inicjowanie obsługi administracyjnej powiodło się" lub konwersja zakończy się niepowodzeniem z kodem błędu 409.

## <a name="convert-using-the-azure-portal"></a>Konwertowanie przy użyciu portalu Azure

Można również przekonwertować dyski niezarządzane na dyski zarządzane za pomocą witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny Wirtualnej wybierz opcję **Dyski** z menu.
4. U góry bloku **Dyski** wybierz pozycję **Migruj do dysków zarządzanych**.
5. Jeśli maszyna wirtualna jest w zestawie dostępności, będzie ostrzeżenie na **migracji do dysków zarządzanych** bloku, które należy najpierw przekonwertować zestaw dostępności. Ostrzeżenie powinno zawierać łącze, które można kliknąć, aby przekonwertować zestaw dostępności. Po przekonwertowaniu zestawu dostępności lub jeśli maszyna wirtualna nie jest w zestawie dostępności, kliknij przycisk **Migracja,** aby rozpocząć proces migracji dysków na dyski zarządzane.

Maszyna wirtualna zostanie zatrzymana i ponownie uruchomiona po zakończeniu migracji.

## <a name="next-steps"></a>Następne kroki

[Konwertowanie standardowych dysków zarządzanych na premium](convert-disk-storage.md)

Zrób kopię maszyny wirtualnej tylko do odczytu przy użyciu [migawek](snapshot-copy-managed-disk.md).

