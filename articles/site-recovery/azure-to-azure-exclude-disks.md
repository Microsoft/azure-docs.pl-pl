---
title: Wykluczanie dysków maszyny wirtualnej platformy Azure z replikacji przy użyciu Azure Site Recovery i Azure PowerShell
description: Dowiedz się, jak wykluczać dyski maszyn wirtualnych platformy Azure podczas Azure Site Recovery przy użyciu Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: a21460279420c46b11c43615ae5ecc7bfa81de4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135802"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Wykluczanie dysków z replikacji programu PowerShell na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób wykluczania dysków podczas replikowania maszyn wirtualnych platformy Azure. Możliwe jest wykluczenie dysków w celu zoptymalizowania zużywanej przepustowości replikacji lub zasobów po stronie docelowej używanych przez te dyski. Obecnie ta funkcja jest dostępna tylko za Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Upewnij się, że rozumiesz [architekturę i składniki odzyskiwania po awarii](azure-to-azure-architecture.md).
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Upewnij się, że masz moduł AzureRm programu PowerShell "AZ". Aby zainstalować lub zaktualizować program PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).
- Upewnij się, że utworzono magazyn usługi Recovery Services i chronione maszyny wirtualne co najmniej raz. Jeśli te czynności nie zostały wykonane, postępuj zgodnie z procesem w obszarze [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu Azure PowerShell](azure-to-azure-powershell.md).
- Jeśli szukasz informacji na temat dodawania dysków do maszyny wirtualnej platformy Azure z włączoną replikacją, [zapoznaj się z tym artykułem](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Dlaczego wykluczanie dysków z replikacji
Może być konieczne wykluczenie dysków z replikacji, ponieważ:

- Maszyna wirtualna osiągnęła [Azure Site Recovery limity, aby replikować szybkości zmian danych](./azure-to-azure-support-matrix.md).

- Dane, które zostały zmienione na wykluczonym dysku nie są istotne lub nie muszą być replikowane.

- Chcesz zaoszczędzić magazyn i zasoby sieciowe przez nie replikowanie danych.

## <a name="how-to-exclude-disks-from-replication"></a>Jak wykluczać dyski z replikacji

W naszym przykładzie replikujemy maszynę wirtualną, która ma jeden system operacyjny i trzy dyski danych znajdujące się w regionie Wschodnie stany USA do regionu zachodnie stany USA 2. Nazwa maszyny wirtualnej to *AzureDemoVM*. Wykluczamy dysk 1 i utrzymujemy dyski 2 i 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Pobierz szczegóły maszyn wirtualnych do replikacji

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Pobierz szczegóły dotyczące dysków maszyny wirtualnej. Te informacje będą później używane podczas uruchamiania replikacji maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replikowanie maszyny wirtualnej platformy Azure

W poniższym przykładzie przyjęto założenie, że masz już konto magazynu pamięci podręcznej, zasady replikacji i mapowania. Jeśli nie masz tych elementów, postępuj zgodnie z instrukcjami, [Aby skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu Azure PowerShell](azure-to-azure-powershell.md).

Replikowanie maszyny wirtualnej platformy Azure z *dyskami zarządzanymi*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Po pomyślnym wykonaniu operacji replikacji początkowej dane maszyny wirtualnej są replikowane do regionu odzyskiwania.

Możesz przejść do Azure Portal i wyświetlić zreplikowane maszyny wirtualne w obszarze "zreplikowane elementy".

Proces replikacji zaczyna się od wypełniania kopii dysków replikowanych maszyny wirtualnej w regionie odzyskiwania. Ta faza jest nazywana fazą replikacji początkowej.

Po zakończeniu replikacji początkowej replikacja jest przenoszona do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona. Wybierz chronioną maszynę wirtualną, aby sprawdzić, czy jakieś dyski są wykluczone.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [uruchamianiu testowej pracy w trybie failover](site-recovery-test-failover-to-azure.md).
