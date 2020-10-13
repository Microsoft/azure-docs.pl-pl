---
title: Konwertowanie dysków niezarządzanych maszyny wirtualnej z systemem Windows na dyski zarządzane
description: Jak skonwertować maszynę wirtualną z systemem Windows z dysków niezarządzanych do dysków zarządzanych przy użyciu programu PowerShell w modelu wdrażania Menedżer zasobów
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 2168b3e3c55eaf06b93358f4623b181f134b4f9e
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974198"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konwertowanie dysków niezarządzanych maszyny wirtualnej z systemem Windows na dyski zarządzane

Jeśli masz istniejące maszyny wirtualne z systemem Windows, które korzystają z dysków niezarządzanych, Możesz skonwertować maszyny wirtualne do korzystania z dysków zarządzanych za pośrednictwem usługi [Azure Managed disks](../managed-disks-overview.md) . Ten proces powoduje przekonwertowanie dysku systemu operacyjnego i dołączonych dysków danych.

 

## <a name="before-you-begin"></a>Przed rozpoczęciem


* Przejrzyj [plan migracji do Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Przejrzyj [często zadawane pytania dotyczące migracji do Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Oryginalne wirtualne dyski twarde i konto magazynu używane przez maszynę wirtualną przed konwersją nie są usuwane. Nadal będą za nie naliczane opłaty. Aby uniknąć naliczania opłat za te artefakty, usuń oryginalne obiekty blob dysków VHD po upewnieniu się, że konwersja została zakończona. Jeśli chcesz znaleźć niedołączone dyski, aby je usunąć, zobacz artykuł [Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Konwertowanie maszyn wirtualnych z pojedynczym wystąpieniem
W tej sekcji opisano sposób konwersji maszyn wirtualnych platformy Azure z jednym wystąpieniem z dysków niezarządzanych na dyski zarządzane. (Jeśli maszyny wirtualne znajdują się w zestawie dostępności, zobacz następną sekcję). 

1. Cofnij przydział maszyny wirtualnej za pomocą polecenia cmdlet [stop-AzVM](/powershell/module/az.compute/stop-azvm) . Poniższy przykład powoduje cofnięcie przydziału maszyny wirtualnej o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Przekonwertuj maszynę wirtualną na dyski zarządzane za pomocą polecenia cmdlet [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) . Poniższy proces konwertuje poprzednią maszynę wirtualną, w tym dysk systemu operacyjnego i wszystkie dyski danych, i uruchamia maszynę wirtualną:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konwertowanie maszyn wirtualnych w zestawie dostępności

Jeśli maszyny wirtualne, które mają zostać przekonwertowane na dyski zarządzane, znajdują się w zestawie dostępności, należy najpierw skonwertować zestaw dostępności na zarządzany zestaw dostępności.

1. Przekonwertuj zestaw dostępności za pomocą polecenia cmdlet [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) . Poniższy przykład aktualizuje zestaw dostępności o nazwie `myAvailabilitySet` w grupie zasobów o nazwie `myResourceGroup` :

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Jeśli region, w którym znajduje się zestaw dostępności, ma tylko dwie zarządzane domeny błędów, ale liczba niezarządzanych domen błędów to 3, to polecenie wyświetla błąd podobny do "określona liczba domen błędów 3 musi należeć do zakresu od 1 do 2". Aby rozwiązać ten problem, należy zaktualizować domenę błędów do 2 i zaktualizować `Sku` ją do programu `Aligned` w następujący sposób:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Cofnij przydział i przekonwertuj maszyny wirtualne w zestawie dostępności. Poniższy skrypt cofa alokację każdej maszyny wirtualnej za pomocą polecenia cmdlet [stop-AzVM](/powershell/module/az.compute/stop-azvm) , konwertuje je za pomocą [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk)i ponownie uruchamia je w ramach procesu konwersji:

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

Jeśli wystąpi błąd podczas konwersji lub jeśli maszyna wirtualna jest w stanie niepowodzenia z powodu problemów w poprzedniej konwersji, `ConvertTo-AzVMManagedDisk` ponownie uruchom polecenie cmdlet. Proste ponowienie próby zwykle odblokowuje sytuację.
Przed przekonwertowaniem upewnij się, że wszystkie rozszerzenia maszyn wirtualnych znajdują się w stanie "Inicjowanie obsługi" zakończyło się pomyślnie, lub konwersja zakończy się niepowodzeniem z kodem błędu 409.

## <a name="convert-using-the-azure-portal"></a>Konwertuj przy użyciu Azure Portal

Możesz również skonwertować dyski niezarządzane na dyski zarządzane przy użyciu Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny wirtualnej wybierz pozycję **dyski** z menu.
4. W górnej części bloku **dyski** wybierz pozycję **Migruj do dysków zarządzanych**.
5. Jeśli maszyna wirtualna znajduje się w zestawie dostępności, pojawi się ostrzeżenie w bloku **Migrowanie do dysków zarządzanych** , aby najpierw skonwertować zestaw dostępności. Ostrzeżenie powinno mieć link, który można kliknąć, aby przekonwertować zestaw dostępności. Po przekonwertowaniu zestawu dostępności lub jeśli maszyna wirtualna nie znajduje się w zestawie dostępności, kliknij pozycję **Migruj** , aby rozpocząć proces migrowania dysków do dysków zarządzanych.

Maszyna wirtualna zostanie zatrzymana i uruchomiona ponownie po zakończeniu migracji.

## <a name="next-steps"></a>Następne kroki

[Konwertuj dyski zarządzane w warstwie Standardowa na Premium](convert-disk-storage.md)

Użyj kopii tylko do odczytu maszyny wirtualnej za pomocą [migawek](snapshot-copy-managed-disk.md).