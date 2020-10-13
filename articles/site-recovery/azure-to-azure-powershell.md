---
title: Odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu Azure PowerShell i Azure Site Recovery
description: Dowiedz się, jak skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 6a272294ca602e3f482156a7334084bf041f683e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91307555"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Skonfiguruj odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu programu Azure PowerShell

Ten artykuł zawiera instrukcje dotyczące konfigurowania i testowania odzyskiwania po awarii dla maszyn wirtualnych platformy Azure przy użyciu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn usługi Recovery Services.
> - Ustaw kontekst magazynu dla sesji programu PowerShell.
> - Przygotuj magazyn, aby rozpocząć replikację maszyn wirtualnych platformy Azure.
> - Utwórz mapowania sieci.
> - Utwórz konta magazynu, aby replikować maszyny wirtualne.
> - Replikowanie maszyn wirtualnych platformy Azure do regionu odzyskiwania na potrzeby odzyskiwania po awarii.
> - Wykonaj test pracy w trybie failover, zweryfikuj i oczyść test pracy w trybie failover.
> - Przełączenie w tryb failover do regionu odzyskiwania.

> [!NOTE]
> Nie wszystkie możliwości scenariusza dostępne w portalu mogą być dostępne za pomocą Azure PowerShell. Niektóre funkcje scenariusza nie są obecnie obsługiwane przez Azure PowerShell są następujące:
> - Możliwość określenia, że wszystkie dyski na maszynie wirtualnej mają być replikowane bez konieczności jawnego określania poszczególnych dysków maszyny wirtualnej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](azure-to-azure-support-matrix.md) wszystkich składników.
- Masz `Az` moduł Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić Azure PowerShell, postępuj zgodnie [z tym przewodnikiem, aby zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji Microsoft Azure

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia cmdlet.

```azurepowershell
Connect-AzAccount
```

Wybierz swoją subskrypcję platformy Azure. Użyj `Get-AzSubscription` polecenia cmdlet, aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure, z którą chcesz korzystać przy użyciu `Set-AzContext` polecenia cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Pobierz szczegóły maszyny wirtualnej, która ma zostać zreplikowana

W tym artykule maszyna wirtualna w regionie Wschodnie stany USA jest replikowana do i odzyskiwana w regionie zachodnie stany USA 2. Replikowana maszyna wirtualna ma dysk systemu operacyjnego i jeden dysk z danymi. Nazwa maszyny wirtualnej używanej w tym przykładzie to `AzureDemoVM` .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Pobierz szczegóły dysku dla dysków maszyny wirtualnej. Szczegóły dysku będą później używane podczas uruchamiania replikacji maszyny wirtualnej.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Utwórz grupę zasobów, w której ma zostać utworzony magazyn Recovery Services.

> [!IMPORTANT]
> * Magazyn usługi Recovery Services i chronione maszyny wirtualne muszą znajdować się w różnych lokalizacjach platformy Azure.
> * Grupa zasobów magazynu usługi Recovery Services i chronione maszyny wirtualne muszą znajdować się w różnych lokalizacjach platformy Azure.
> * Magazyn usługi Recovery Services i Grupa zasobów, do której ona należy, mogą znajdować się w tej samej lokalizacji platformy Azure.

W przykładzie w tym artykule chroniona maszyna wirtualna znajduje się w regionie Wschodnie stany USA. Region odzyskiwania wybrany na potrzeby odzyskiwania po awarii jest regionem zachodnie stany USA 2. Magazyn usługi Recovery Services i Grupa zasobów magazynu znajdują się w regionie odzyskiwania, zachodnie stany USA 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Utwórz magazyn usługi Recovery Services. W tym przykładzie w `a2aDemoRecoveryVault` regionie zachodnie stany USA 2 jest tworzony Recovery Services magazyn o nazwie.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Ustaw kontekst magazynu do użycia w sesji programu PowerShell. Po ustawieniu kontekstu magazynu operacje Azure Site Recovery w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Aby przeprowadzić migrację z platformy Azure na platformę Azure, można ustawić kontekst magazynu dla nowo utworzonego magazynu:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Przygotuj magazyn, aby rozpocząć replikację maszyn wirtualnych platformy Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Utwórz obiekt sieci szkieletowej Site Recovery, aby reprezentować region podstawowy (źródłowy)

Obiekt sieci szkieletowej w magazynie reprezentuje region świadczenia usługi Azure. Obiekt podstawowej sieci szkieletowej jest tworzony w celu reprezentowania regionu platformy Azure, do którego należą maszyny wirtualne chronione magazynem. W przykładzie w tym artykule chroniona maszyna wirtualna znajduje się w regionie Wschodnie stany USA.

- Na region można utworzyć tylko jeden obiekt sieci szkieletowej.
- Jeśli wcześniej włączono Site Recovery replikację maszyny wirtualnej w Azure Portal, Site Recovery automatycznie tworzy obiekt sieci szkieletowej. Jeśli obiekt sieci szkieletowej istnieje dla regionu, nie można utworzyć nowego.

Przed rozpoczęciem należy zrozumieć, że operacje Site Recovery są wykonywane asynchronicznie. Po zainicjowaniu operacji zostanie przesłane zadanie Azure Site Recovery i zostanie zwrócony obiekt śledzenia zadań. Użyj obiektu śledzenia zadań, aby uzyskać najnowszy stan zadania ( `Get-AzRecoveryServicesAsrJob` ) oraz monitorować stan operacji.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Jeśli maszyny wirtualne z wielu regionów platformy Azure są chronione w tym samym magazynie, Utwórz jeden obiekt sieci szkieletowej dla każdego źródłowego regionu platformy Azure.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Utwórz obiekt sieci szkieletowej Site Recovery, aby reprezentować region odzyskiwania

Obiekt sieci szkieletowej odzyskiwania reprezentuje lokalizację odzyskiwania na platformie Azure. W przypadku przejścia w tryb failover maszyny wirtualne są replikowane i odzyskiwane do regionu odzyskiwania reprezentowanego przez sieć szkieletową odzyskiwania. Region odzyskiwania systemu Azure używany w tym przykładzie to zachodnie stany USA 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Tworzenie kontenera ochrony Site Recovery w podstawowej sieci szkieletowej

Kontener ochrony jest kontenerem używanym do grupowania replikowanych elementów w sieci szkieletowej.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Tworzenie kontenera ochrony Site Recovery w sieci szkieletowej odzyskiwania

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Tworzenie mapowania kontenera ochrony między podstawowym i głównym kontenerem ochrony

Mapowanie kontenera ochrony mapuje podstawowy kontener ochrony z kontenerem ochrony odzyskiwania i zasadami replikacji. Utwórz jedno mapowanie dla każdej zasady replikacji, która będzie używana do replikowania maszyn wirtualnych między parą kontenera ochrony.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Tworzenie mapowania kontenera ochrony na potrzeby powrotu po awarii (replikacja odwrotna po przejściu w tryb failover)

Po przejściu w tryb failover, gdy wszystko będzie gotowe do przełączenia maszyny wirtualnej z systemem do stanu z powrotem do oryginalnego regionu platformy Azure, należy przeprowadzić powrót po awarii. Aby powrócić po awarii, maszyna wirtualna przełączona w tryb failover zostanie odtworzona z regionu przełączenia w tryb failover do oryginalnego regionu. W przypadku replikacji odwrotnej role oryginalnego regionu i przełączenia regionu odzyskiwania. Oryginalny region zostanie teraz nowym regionem odzyskiwania, a początkowo region odzyskiwania stał się regionem podstawowym. Mapowanie kontenera ochrony na potrzeby replikacji odwrotnej reprezentuje role przełączane w regionach oryginalnego i odzyskiwania.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Utwórz konto magazynu pamięci podręcznej i docelowe konto magazynu

Konto magazynu pamięci podręcznej to standardowe konto magazynu w tym samym regionie świadczenia usługi Azure, w którym jest replikowana maszyna wirtualna. Konto magazynu pamięci podręcznej służy do tymczasowego przechowywania zmian replikacji przed przeniesieniem zmian do regionu odzyskiwania usługi Azure. Można wybrać opcję, ale nie jest to konieczne, aby określić różne konta magazynu pamięci podręcznej dla różnych dysków maszyny wirtualnej.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

W przypadku maszyn wirtualnych, które **nie korzystają z dysków zarządzanych**, docelowe konto magazynu jest kontem magazynu w regionie odzyskiwania, do którego są replikowane dyski maszyny wirtualnej. Docelowe konto magazynu może być kontem magazynu w warstwie Standardowa lub kontem magazynu w warstwie Premium. Wybierz rodzaj konta magazynu wymagane na podstawie współczynnika zmian danych (współczynnik zapisu we/wy) dla dysków i Azure Site Recovery obsługiwanych limitów dla typu magazynu.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Utwórz mapowania sieci

Mapowanie sieci mapuje sieci wirtualne w regionie podstawowym do sieci wirtualnych w regionie odzyskiwania. Mapowanie sieci określa sieć wirtualną platformy Azure w regionie odzyskiwania, w której maszyna wirtualna w podstawowej sieci wirtualnej powinna działać w trybie failover. Jedną sieć wirtualną platformy Azure można zamapować do pojedynczej sieci wirtualnej platformy Azure w regionie odzyskiwania.

- Utwórz sieć wirtualną platformy Azure w regionie odzyskiwania w celu przełączenia w tryb failover:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Pobierz podstawową sieć wirtualną. Sieci wirtualnej, z którą jest połączona maszyna wirtualna:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Utwórz mapowanie sieci między podstawową siecią wirtualną a siecią wirtualną odzyskiwania:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Utwórz mapowanie sieci dla kierunku odwrotnego (powrót po awarii):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replikowanie maszyny wirtualnej platformy Azure

Replikuj maszynę wirtualną platformy Azure z **dyskami zarządzanymi**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replikuj maszynę wirtualną platformy Azure z **dyskami niezarządzanymi**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Po pomyślnym zakończeniu operacji uruchamiania replikacji dane maszyny wirtualnej są replikowane do regionu odzyskiwania.

Proces replikacji rozpoczyna się od wstępnego wypełniania kopii dysków replikowanych maszyny wirtualnej w regionie odzyskiwania. Ta faza jest nazywana fazą replikacji początkowej.

Po zakończeniu replikacji początkowej replikacja przechodzi do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona i można na niej wykonać operację testowania pracy w trybie failover. Stan replikacji replikowanego elementu reprezentujący maszynę wirtualną przechodzi do stanu **chronionego** po zakończeniu replikacji początkowej.

Monitoruj stan replikacji i kondycję replikacji maszyny wirtualnej, pobierając szczegóły dotyczące chronionego elementu replikacji.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Wykonaj test pracy w trybie failover, zweryfikuj i oczyść test pracy w trybie failover

Po osiągnięciu chronionego stanu przez replikację maszyny wirtualnej można wykonać operację testowej pracy w trybie failover na maszynie wirtualnej (na chronionym elemencie replikacji maszyny wirtualnej).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Wykonaj test pracy w trybie failover.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Poczekaj na zakończenie operacji testowej pracy w trybie failover.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Po pomyślnym ukończeniu zadania testowego przejścia w tryb failover można nawiązać połączenie z testem zakończonym niepowodzeniem za pomocą maszyny wirtualnej i zweryfikować test pracy w trybie failover.

Po zakończeniu testowania na maszynie wirtualnej z testem zakończonym niepowodzeniem Wyczyść kopię testową, uruchamiając operację oczyszczania testowego trybu failover. Ta operacja usuwa kopię testową maszyny wirtualnej, która została utworzona przez test pracy w trybie failover.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

Przełączenie maszyny wirtualnej w tryb failover do określonego punktu odzyskiwania.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Po pomyślnym zakończeniu zadania trybu failover można zatwierdzić operację trybu failover.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Ponowne włączanie ochrony i powrót po awarii do regionu źródłowego

Po przejściu w tryb failover, gdy wszystko jest gotowe do powrotu do oryginalnego regionu, uruchom replikację odwrotną dla elementu chronionego przez replikację przy użyciu `Update-AzRecoveryServicesAsrProtectionDirection` polecenia cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Po ponownym włączeniu ponownej ochrony możesz przejść do trybu failover w odwrotnym kierunku, zachodnie stany USA, Wschodnie stany USA, a następnie powrócić do regionu źródłowego.

## <a name="disable-replication"></a>Wyłączanie replikacji

Replikację można wyłączyć za pomocą `Remove-AzRecoveryServicesAsrReplicationProtectedItem` polecenia cmdlet.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak można wykonać inne zadania, takie jak tworzenie planów odzyskiwania i testowanie pracy w trybie failover planów odzyskiwania przy użyciu programu PowerShell, zobacz [informacje dotyczące Azure Site Recovery programu PowerShell](/powershell/module/az.RecoveryServices) .
