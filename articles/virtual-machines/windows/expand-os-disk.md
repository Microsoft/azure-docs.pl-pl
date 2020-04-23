---
title: Rozszerzanie dysku systemu operacyjnego maszyny wirtualnej systemu Windows na platformie Azure
description: Rozwiń rozmiar dysku systemu operacyjnego maszyny wirtualnej przy użyciu programu Azure Powershell w modelu wdrażania usługi Resource Manager.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: e69b041a2e4c8a0715adb6ab126a3aede42f7dde
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869687"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Sposób rozszerzania dysku systemu operacyjnego maszyny wirtualnej

Podczas tworzenia nowej maszyny wirtualnej (VM) w grupie zasobów przez wdrożenie obrazu z [usługi Azure Marketplace,](https://azure.microsoft.com/marketplace/)domyślny dysk systemu operacyjnego jest często 127 GB (niektóre obrazy mają mniejsze rozmiary dysku systemu operacyjnego domyślnie). Mimo iż możliwe jest dodawanie dysków danych do maszyny wirtualnej (ich liczba zależy od wybranej jednostki magazynowej), a ponadto zaleca się instalowanie aplikacji i obciążeń intensywnie wykorzystujących procesor CPU na tych dodatkowych dyskach, klienci często muszą rozszerzać dysk systemu operacyjnego w celu obsługi niektórych scenariuszy, takich jak następujące:

- Obsługa starszych aplikacji, które instalują składniki na dysku systemu operacyjnego.
- Migrowanie fizycznego komputera lub maszyny wirtualnej ze środowiska lokalnego z większym dyskiem systemu operacyjnego.


> [!IMPORTANT]
> Zmiana rozmiaru dysku systemu operacyjnego maszyny wirtualnej platformy Azure wymaga cofniętego przydziału maszyny wirtualnej.
>
> Po rozwinięciu dysków należy [rozszerzyć wolumin w systemie operacyjnym,](#expand-the-volume-within-the-os) aby korzystać z większego dysku.
> 


 


## <a name="resize-a-managed-disk"></a>Ponowne rozmiary dysku zarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta platformy Microsoft Azure w trybie zarządzania zasobami i wybierz subskrypcję w następujący sposób:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Uzyskaj odwołanie do zarządzanego dysku systemu operacyjnego. Ustaw rozmiar zarządzanego dysku systemu operacyjnego na żądaną wartość i zaktualizuj dysk w następujący sposób:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Możliwe jest rozszerzenie obiektu blob VHD poza ten rozmiar, ale system operacyjny będzie mógł pracować tylko z pierwszym 2048 GB miejsca).
   > 
   > 
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

To wszystko! Teraz połącz protokół RDP z maszyną wirtualną, otwórz okno Zarządzanie komputerem (lub Zarządzanie dyskiem) i rozszerz dysk przy użyciu nowo przydzielonego miejsca.

## <a name="resize-an-unmanaged-disk"></a>Ponowne rozmiary dysku niezarządzanego

Otwórz okno programu Powershell ISE lub Powershell w trybie administracyjnym:

1. Zaloguj się do konta platformy Microsoft Azure w trybie zarządzania zasobami i wybierz subskrypcję w następujący sposób:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Ustaw nazwę swojej grupy zasobów i nazwę maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Uzyskaj odwołanie do maszyny wirtualnej w następujący sposób:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Zatrzymaj maszynę wirtualną przed zmianą rozmiaru dysku w następujący sposób:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Ustaw rozmiar niezarządzanego dysku systemu operacyjnego na żądaną wartość i zaktualizuj maszynę wirtualną w następujący sposób:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Nowy rozmiar powinien być większy niż istniejący rozmiar dysku. Maksymalna dozwolona wartość to 2048 GB dla dysków systemu operacyjnego. (Możliwe jest rozszerzenie obiektu blob VHD poza ten rozmiar, ale system operacyjny będzie mógł pracować tylko z pierwszym 2048 GB miejsca).
   > 
   > 
   
6. Zaktualizowanie maszyny wirtualnej może potrwać kilka sekund. Po zakończeniu wykonywania polecenia uruchom ponownie maszynę wirtualną w następujący sposób:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Skrypty dla dysku systemu operacyjnego

Poniżej znajduje się pełny skrypt dla odwołania zarówno dla dysków zarządzanych, jak i niezarządzanych:


**Dyski zarządzane**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Dyski niezarządzane**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Zmiana rozmiaru dysków danych

Ten artykuł koncentruje się przede wszystkim na rozwinięciu dysku systemu operacyjnego maszyny Wirtualnej, ale skrypt może być również używany do rozwijania dysków danych dołączonych do maszyny Wirtualnej. Aby na przykład rozszerzyć pierwszy dysk danych dołączony do maszyny wirtualnej, zamień obiekt `OSDisk` elementu `StorageProfile` na tablicę `DataDisks` i przy użyciu indeksu liczbowego uzyskaj odwołanie do pierwszego dołączonego dysku danych, jak pokazano poniżej:

**Dysk zarządzany**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Dysk niezarządzany**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Podobnie można odwoływać się do innych dysków danych dołączonych do maszyny Wirtualnej, przy użyciu indeksu, jak pokazano powyżej lub **Name** właściwości dysku:


**Dysk zarządzany**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Dysk niezarządzany**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Rozszerzanie głośności w systemach operacyjnych

Po rozwinięciu dysku dla maszyny Wirtualnej, należy przejść do systemu operacyjnego i rozszerzyć wolumin, aby objąć nowe miejsce. Istnieje kilka metod rozwijania partycji. W tej sekcji opisano podłączanie maszyny Wirtualnej przy użyciu połączenia RDP w celu rozszerzenia partycji za pomocą **programu DiskPart**.

1. Otwórz połączenie RDP z maszyną wirtualną.

2.  Otwórz wiersz polecenia i wpisz **diskpart**.

2.  W wierszu **DISKPART** wpisz polecenie `list volume`. Zanotuj wolumin, który chcesz rozszerzyć.

3.  W wierszu **DISKPART** wpisz polecenie `select volume <volumenumber>`. Spowoduje to wybranie *numeru woluminu woluminu,* który ma zostać rozszerzony na ciągłe, puste miejsce na tym samym dysku.

4.  W wierszu **DISKPART** wpisz polecenie `extend [size=<size>]`. Spowoduje to rozszerzenie wybranego woluminu o *rozmiar* w megabajtach (MB).


## <a name="next-steps"></a>Następne kroki

Dyski można również dołączać za pomocą [portalu Azure](attach-managed-disk-portal.md).
