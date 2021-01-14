---
title: Korzystanie z maszyny wirtualnej Rozwiązywanie problemów z systemem Windows przy użyciu Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy dotyczące maszyn wirtualnych z systemem Windows na platformie Azure, łącząc dysk systemu operacyjnego z maszyną wirtualną odzyskiwania przy użyciu Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: fbdccc2efb72626d1974f4cd81be9d2ef4c522d4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201590"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure PowerShell
Jeśli maszyna wirtualna z systemem Windows na platformie Azure napotyka błąd rozruchowy lub dyskowy, może być konieczne wykonanie kroków rozwiązywania problemów na dysku. Typowym przykładem może być niepowodzenie aktualizacji aplikacji, która uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano, jak za pomocą Azure PowerShell połączyć dysk z inną maszyną wirtualną z systemem Windows w celu usunięcia błędów, a następnie naprawić oryginalną maszynę wirtualną. 

> [!Important]
> Skrypty w tym artykule mają zastosowanie tylko do maszyn wirtualnych, które korzystają z [dysku zarządzanego](../managed-disks-overview.md). 

 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Teraz można użyć Azure PowerShell, aby zmienić dysk systemu operacyjnego dla maszyny wirtualnej. Nie jest już konieczne usuwanie i Tworzenie maszyny wirtualnej.

Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
2. Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej.
3. Utwórz dysk na podstawie migawki dysku systemu operacyjnego.
4. Dołącz dysk jako dysk danych do maszyny wirtualnej odzyskiwania.
5. Nawiąż połączenie z maszyną wirtualną odzyskiwania. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy na skopiowanym dysku systemu operacyjnego.
6. Odinstalowanie i odłączanie dysku od maszyny wirtualnej odzyskiwania.
7. Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy.

Za pomocą poleceń naprawy maszyny wirtualnej można zautomatyzować kroki 1, 2, 3, 4, 6 i 7. Aby uzyskać więcej dokumentacji i instrukcje, zobacz [Naprawa maszyny wirtualnej z systemem Windows za pomocą poleceń naprawy maszyny wirtualnej platformy Azure](repair-windows-vm-using-azure-virtual-machine-repair-commands.md).

Upewnij się, że masz zainstalowaną [najnowszą Azure PowerShell](/powershell/azure/) i zalogowano się do subskrypcji:

```powershell
Connect-AzAccount
```

W poniższych przykładach Zastąp nazwy parametrów własnymi wartościami. 

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Możesz wyświetlić zrzut ekranu maszyny wirtualnej na platformie Azure, aby pomóc w rozwiązywaniu problemów z rozruchem. Ten zrzut ekranu może pomóc w ustaleniu, dlaczego rozruch maszyny wirtualnej nie powiedzie się. Poniższy przykład pobiera zrzut ekranu z maszyny wirtualnej z systemem Windows o nazwie `myVM` w grupie zasobów o nazwie `myResourceGroup` :

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Przejrzyj zrzut ekranu, aby określić, dlaczego rozruch maszyny wirtualnej kończy się niepowodzeniem. Zwróć uwagę na wszystkie określone komunikaty o błędach lub podane kody błędów.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Poniższy przykład powoduje zatrzymanie maszyny wirtualnej o nazwie `myVM` z grupy zasobów o nazwie `myResourceGroup` :

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Przed przetworzeniem do następnego kroku poczekaj na zakończenie usuwania maszyny wirtualnej.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Utwórz migawkę z dysku systemu operacyjnego maszyny wirtualnej

Poniższy przykład tworzy migawkę o nazwie `mySnapshot` z dysku systemu operacyjnego maszyny wirtualnej o nazwie "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Migawka to pełna kopia tylko do odczytu dysku VHD. Nie można go dołączyć do maszyny wirtualnej. W następnym kroku utworzymy dysk na podstawie tej migawki.

## <a name="create-a-disk-from-the-snapshot"></a>Utwórz dysk na podstawie migawki

Ten skrypt tworzy dysk zarządzany o nazwie `newOSDisk` z migawki o nazwie `mysnapshot` .  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
Teraz masz kopię oryginalnego dysku systemu operacyjnego. Ten dysk można zainstalować na innej maszynie wirtualnej z systemem Windows w celu rozwiązywania problemów.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Dołączanie dysku do innej maszyny wirtualnej z systemem Windows w celu rozwiązywania problemów

Teraz dołączymy kopię oryginalnego dysku systemu operacyjnego do maszyny wirtualnej jako dysk danych. Ten proces umożliwia poprawienie błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu na dysku. Poniższy przykład dołącza dysk o nazwie `newOSDisk` do maszyny wirtualnej o nazwie `RecoveryVM` .

> [!NOTE]
> Aby dołączyć dysk, kopia oryginalnego dysku systemu operacyjnego i maszyny wirtualnej odzyskiwania musi znajdować się w tej samej lokalizacji.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Nawiązywanie połączenia z maszyną wirtualną odzyskiwania i rozwiązywanie problemów na dołączonym dysku

1. Połącz protokół RDP z maszyną wirtualną odzyskiwania przy użyciu odpowiednich poświadczeń. Poniższy przykład pobiera plik połączenia RDP dla maszyny wirtualnej o nazwie `RecoveryVM` w grupie zasobów o nazwie `myResourceGroup` i pobiera ją do `C:\Users\ops\Documents` "

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Dysk danych powinien zostać automatycznie wykryty i dołączony. Wyświetl listę dołączonych woluminów, aby określić literę dysku w następujący sposób:

    ```powershell
    Get-Disk
    ```

    Poniższe przykładowe dane wyjściowe przedstawiają dysk połączony z dyskiem **2**. (Można również użyć, `Get-Volume` Aby wyświetlić literę dysku):

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Po zainstalowaniu oryginalnego dysku systemu operacyjnego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-os-disk"></a>Odinstalowywanie i odłączanie oryginalnego dysku systemu operacyjnego
Po rozwiązaniu problemów zostanie odinstalowany i odłączony istniejący dysk od maszyny wirtualnej odzyskiwania. Nie można używać dysku z żadną inną maszyną wirtualną, dopóki nie zostanie wydana dzierżawa dołączania dysku do maszyny wirtualnej odzyskiwania.

1. W ramach sesji RDP Odinstaluj dysk z danymi na maszynie wirtualnej odzyskiwania. Wymagany jest numer dysku z poprzedniego `Get-Disk` polecenia cmdlet. Następnie użyj, `Set-Disk` Aby ustawić dysk jako w trybie offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Potwierdź, że dysk jest teraz ustawiony jako używany ponownie w trybie offline `Get-Disk` . Następujące przykładowe dane wyjściowe pokazują, że dysk jest teraz ustawiony jako offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Zakończ sesję RDP. Z poziomu sesji Azure PowerShell Usuń dysk o nazwie `newOSDisk` z maszyny wirtualnej o nazwie "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Zmiana dysku systemu operacyjnego dla maszyny wirtualnej, której to dotyczy

Aby zamienić dyski systemu operacyjnego, można użyć Azure PowerShell. Nie trzeba usuwać ani tworzyć ponownie maszyny wirtualnej.

Ten przykład powoduje zatrzymanie maszyny wirtualnej o nazwie `myVM` i przypisanie dysku o nazwie `newOSDisk` jako nowego dysku systemu operacyjnego. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Weryfikowanie i włączanie diagnostyki rozruchu

Poniższy przykład włącza rozszerzenie diagnostyki na maszynie wirtualnej o nazwie `myVMDeployed` w grupie zasobów o nazwie `myResourceGroup` :

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). Problemy dotyczące uzyskiwania dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć [w temacie Rozwiązywanie problemów z łącznością aplikacji na maszynie wirtualnej z systemem Windows](troubleshoot-app-connection.md).

Aby uzyskać więcej informacji o korzystaniu z Menedżer zasobów, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md).
