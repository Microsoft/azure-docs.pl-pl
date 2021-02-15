---
title: Replikowanie maszyn wirtualnych platformy Azure działających w grupach umieszczania w sąsiedztwie
description: Dowiedz się, jak replikować maszyny wirtualne platformy Azure działające w grupach umieszczania zbliżeniowe przy użyciu Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 681b635099d450f061e0bcdb5b2c5d60d56c20a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380760"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replikowanie maszyn wirtualnych platformy Azure działających w grupach umieszczania w pobliżu w innym regionie

W tym artykule opisano sposób replikowania maszyn wirtualnych, trybu failover i powrotu po awarii działających w grupie umieszczania sąsiedztwa do regionu pomocniczego.

[Grupy umieszczania w sąsiedztwie](../virtual-machines/windows/proximity-placement-groups-portal.md) to funkcja logicznego grupowania maszyn wirtualnych platformy Azure, której można użyć w celu zmniejszenia opóźnień sieci między maszynami wirtualnymi związanymi z aplikacjami. Gdy maszyny wirtualne są wdrażane w ramach tej samej grupy położenia bliskości, są fizycznie zlokalizowane jak najbliżej siebie. Grupy umieszczania zbliżeniowe są szczególnie przydatne do rozwiązywania wymagań dotyczących obciążeń zależnych od opóźnienia.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Odzyskiwanie po awarii przy użyciu grup umieszczania w sąsiedztwie

W typowym scenariuszu maszyny wirtualne mogą działać w grupie umieszczania bliskości, aby uniknąć opóźnień sieci między różnymi warstwami aplikacji. Chociaż może to zapewnić optymalne opóźnienia sieci aplikacji, należy chronić te aplikacje przy użyciu Site Recovery dla dowolnego błędu poziomu regionu. Site Recovery replikuje dane z jednego regionu do innego regionu platformy Azure i łączy maszyny w regionie odzyskiwania po awarii w przypadku przejścia w tryb failover.

## <a name="considerations"></a>Zagadnienia do rozważenia

- Najlepszym nakładem pracy jest przełączenie w tryb failover i powrót po awarii maszyn wirtualnych do grupy umieszczania sąsiedztwa. Jeśli jednak maszyna wirtualna nie może zostać przełączona w tryb failover lub powrotu po awarii, nastąpi przejście do trybu failover/powrotu po awarii, a maszyny wirtualne zostaną utworzone poza grupą położenia sąsiedztwa.
- Jeśli zestaw dostępności jest przypięty do grupy umieszczania bliskości, a podczas pracy w trybie failover/powrotu po awarii w zestawie dostępności istnieją ograniczenia alokacji, maszyny wirtualne zostaną utworzone poza zestaw dostępności i grupę umieszczania sąsiedztwa.
- Site Recovery dla grup umieszczania zbliżeniowe nie jest obsługiwana w przypadku dysków niezarządzanych.

> [!NOTE]
> Azure Site Recovery nie obsługuje powrotu po awarii z dysków zarządzanych dla scenariuszy funkcji Hyper-V do platformy Azure. Z tego powodu powrót po awarii z grupy umieszczania sąsiedztwa na platformie Azure do funkcji Hyper-V nie jest obsługiwany.

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-portal"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych w grupach umieszczania w sąsiedztwie za pośrednictwem portalu

### <a name="azure-to-azure-via-portal"></a>Azure do platformy Azure za pośrednictwem portalu

Można włączyć replikację dla maszyny wirtualnej za pośrednictwem strony odzyskiwania po awarii maszyny wirtualnej lub przechodząc do wstępnie utworzonego magazynu, a następnie przejść do sekcji Site Recovery, a następnie włączyć replikację. Przyjrzyjmy się, jak Site Recovery można skonfigurować dla maszyn wirtualnych w PPG za pomocą obu metod:

- Jak wybrać PPG w regionie odzyskiwania po włączeniu replikacji za pomocą bloku DR IaaS VM:
  1. Przejdź do maszyny wirtualnej. W bloku z lewej strony w obszarze "operacje" Wybierz pozycję "odzyskiwanie po awarii"
  2. Na karcie "podstawowe" Wybierz region DR, do którego chcesz replikować maszynę wirtualną. Przejdź do pozycji "Ustawienia zaawansowane"
  3. W tym miejscu można zobaczyć grupę umieszczania sąsiedztwa maszyny wirtualnej oraz opcję wybierania PPG w regionie odzyskiwania po awarii. Site Recovery udostępnia również opcję użycia nowej grupy położenia zbliżeniowe, która jest tworzona dla Ciebie, jeśli zdecydujesz się użyć tej opcji domyślnej. Możesz wybrać żądaną grupę umieszczania, a następnie przejść do pozycji "przegląd + uruchamianie replikacji", a następnie włączyć replikację.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-1.png" alt-text="Włącz replikację.":::

- Jak wybrać PPG w regionie odzyskiwania po włączeniu replikacji za pomocą bloku magazynu:
  1. Przejdź do magazynu Recovery Services i przejdź do karty Site Recovery
  2. Kliknij pozycję "+ Włącz Site Recovery", a następnie wybierz pozycję "1: Włącz replikację" w obszarze Azure Virtual Machines (w przypadku przeprowadzania replikacji maszyny wirtualnej platformy Azure)
  3. Wypełnij pola wymagane na karcie "Źródło" i kliknij przycisk "dalej"
  4. Wybierz listę maszyn wirtualnych, dla których chcesz włączyć replikację na karcie maszyny wirtualne, a następnie kliknij przycisk Dalej.
  5. W tym miejscu możesz zobaczyć opcję wyboru PPG w regionie odzyskiwania po awarii. Site Recovery udostępnia również opcję użycia nowego PPG, który tworzy dla Ciebie, jeśli zdecydujesz się użyć tej opcji domyślnej. Możesz wybrać PPG, a następnie włączyć replikację.

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-group-a2a-2.png" alt-text="Włącz replikację za pośrednictwem magazynu.":::

Należy pamiętać, że można łatwo zaktualizować wybór PPG w regionie odzyskiwania po włączeniu replikacji dla maszyny wirtualnej.

1. Przejdź do maszyny wirtualnej i w bloku po lewej stronie w obszarze "operacje" Wybierz pozycję "odzyskiwanie awaryjne".
2. Przejdź do bloku "obliczenia i sieć" i kliknij pozycję "Edytuj" w górnej części strony
3. Można wyświetlić opcje edycji wielu ustawień docelowych, w tym docelowa PPG. Wybierz PPG, do których maszyna wirtualna ma zostać przejściu w tryb failover, a następnie kliknij pozycję "Zapisz".

### <a name="vmware-to-azure-via-portal"></a>VMware do platformy Azure za pośrednictwem portalu

Grupę umieszczania dla docelowej maszyny wirtualnej można skonfigurować po włączeniu replikacji dla maszyny wirtualnej. Upewnij się, że oddzielnie utworzysz PPG w regionie docelowym zgodnie z wymaganiami. Następnie możesz łatwo zaktualizować wybór PPG w regionie odzyskiwania po włączeniu replikacji dla maszyny wirtualnej.

1. Wybierz maszynę wirtualną z magazynu i w bloku po lewej stronie w obszarze "operacje" Wybierz pozycję "odzyskiwanie awaryjne".
2. Przejdź do bloku "obliczenia i sieć" i kliknij pozycję "Edytuj" w górnej części strony
3. Można wyświetlić opcje edycji wielu ustawień docelowych, w tym docelowa PPG. Wybierz PPG, do których maszyna wirtualna ma zostać przejściu w tryb failover, a następnie kliknij pozycję "Zapisz".

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-v2a.png" alt-text="Aktualizacja PPG V2A":::

### <a name="hyper-v-to-azure-via-portal"></a>Funkcja Hyper-V do platformy Azure za pośrednictwem portalu

Grupę umieszczania dla docelowej maszyny wirtualnej można skonfigurować po włączeniu replikacji dla maszyny wirtualnej. Upewnij się, że oddzielnie utworzysz PPG w regionie docelowym zgodnie z wymaganiami. Następnie możesz łatwo zaktualizować wybór PPG w regionie odzyskiwania po włączeniu replikacji dla maszyny wirtualnej.

1. Wybierz maszynę wirtualną z magazynu i w bloku po lewej stronie w obszarze "operacje" Wybierz pozycję "odzyskiwanie awaryjne".
2. Przejdź do bloku "obliczenia i sieć" i kliknij pozycję "Edytuj" w górnej części strony
3. Można wyświetlić opcje edycji wielu ustawień docelowych, w tym docelowa PPG. Wybierz PPG, do których maszyna wirtualna ma zostać przejściu w tryb failover, a następnie kliknij pozycję "Zapisz".

   :::image type="content" source="media/how-to-enable-replication-proximity-placement-groups/proximity-placement-groups-update-h2a.png" alt-text="Aktualizacja PPG H2A":::

## <a name="set-up-disaster-recovery-for-vms-in-proximity-placement-groups-via-powershell"></a>Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych w grupach umieszczania sąsiedztwa za pośrednictwem programu PowerShell

### <a name="prerequisites"></a>Wymagania wstępne 

1. Upewnij się, że masz Azure PowerShell AZ module. Jeśli musisz zainstalować lub uaktualnić Azure PowerShell, postępuj zgodnie [z tym przewodnikiem, aby zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).
2. Minimalna Azure PowerShell AZ Version powinna być 4.1.0. Aby sprawdzić bieżącą wersję, użyj poniższego polecenia.

    ```
    Get-InstalledModule -Name Az
    ```

### <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Skonfiguruj Site Recovery Virtual Machines w grupie umieszczania sąsiedztwa

> [!NOTE]
> Upewnij się, że masz unikatowy identyfikator docelowej grupy umieszczania sąsiedztwa. Jeśli tworzysz nową grupę umieszczania bliskości, sprawdź to polecenie w [tym miejscu](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) i jeśli używasz istniejącej grupy umieszczania bliskości, a następnie użyj tego polecenia w [tym miejscu](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups).

### <a name="azure-to-azure"></a>Azure–Azure

1. [Zaloguj](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription) się do swojego konta i ustaw swoją subskrypcję.
2. Pobierz szczegóły maszyny wirtualnej, którą chcesz replikować, jak opisano w [tym miejscu](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated).
3. [Utwórz](./azure-to-azure-powershell.md#create-a-recovery-services-vault) magazyn usługi Recovery Services i [Ustaw](./azure-to-azure-powershell.md#set-the-vault-context) kontekst magazynu.
4. Przygotuj magazyn, aby rozpocząć replikację maszyny wirtualnej. Obejmuje to utworzenie [obiektu usługi Service Fabric](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) dla regionów głównych i odzyskiwania.
5. [Utwórz](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric) kontener ochrony Site Recovery dla sieci szkieletowej podstawowej i odzyskiwania.
6. [Utwórz](./azure-to-azure-powershell.md#create-a-replication-policy) zasady replikacji.
7. Utwórz mapowanie kontenera ochrony między podstawowym i głównym kontenerem ochrony przy użyciu [tych](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) kroków oraz mapowania kontenera ochrony dla powrotu po awarii, jak [opisano tutaj](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Utwórz konto magazynu pamięci podręcznej [, wykonując następujące kroki.](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account)
9. Utwórz wymagane mapowania sieci zgodnie z opisem w [tym miejscu](./azure-to-azure-powershell.md#create-network-mappings).
10. Aby replikować maszynę wirtualną platformy Azure z dyskami zarządzanymi, użyj poniższego polecenia cmdlet programu PowerShell —

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Podczas włączania replikacji dla wielu dysków danych Użyj poniższego polecenia cmdlet programu PowerShell —

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

$diskconfigs = @()
$diskconfigs.Add($OSDiskReplicationConfig)

#Data disk

# Add data disks
Foreach( $disk in $VM.StorageProfile.DataDisks)
{
    $datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
    $dataDiskId1 = $datadisk[0].Id
    $RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
    $RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name
    $DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $dataDiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType
    $diskconfigs.Add($DataDisk1ReplicationConfig)
}

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

Podczas włączania replikacji strefy do strefy przy użyciu PPG polecenie uruchomienia replikacji zostanie nastąpić przy użyciu polecenia cmdlet programu PowerShell —

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

Po pomyślnym zakończeniu operacji uruchamiania replikacji dane maszyny wirtualnej są replikowane do regionu odzyskiwania.

Proces replikacji rozpoczyna się od wstępnego wypełniania kopii dysków replikowanych maszyny wirtualnej w regionie odzyskiwania. Ta faza jest nazywana fazą replikacji początkowej.

Po zakończeniu replikacji początkowej replikacja przechodzi do fazy synchronizacji różnicowej. W tym momencie maszyna wirtualna jest chroniona i można na niej wykonać operację testowania pracy w trybie failover. Stan replikacji replikowanego elementu reprezentujący maszynę wirtualną przechodzi do stanu chronionego po zakończeniu replikacji początkowej.

Monitoruj stan replikacji i kondycję replikacji maszyny wirtualnej, pobierając szczegóły dotyczące chronionego elementu replikacji.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Aby przeprowadzić test pracy w trybie failover, zweryfikuj i oczyść test pracy w trybie failover, wykonaj [następujące](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) kroki.
12. Aby przełączyć się do trybu failover, wykonaj kroki opisane [tutaj](./azure-to-azure-powershell.md#fail-over-to-azure).
13. Aby ponownie włączyć ochronę i powrót po awarii do regionu źródłowego, użyj poniższego polecenia cmdlet programu PowerShell —

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. Aby wyłączyć replikację, wykonaj kroki opisane [tutaj](./azure-to-azure-powershell.md#disable-replication).

### <a name="vmware-to-azure-via-powershell"></a>Program VMware do platformy Azure za pośrednictwem programu PowerShell

1. Upewnij się, że [przygotowano lokalne serwery VMware](./vmware-azure-tutorial-prepare-on-premises.md) do odzyskiwania po awarii na platformie Azure.
2. Zaloguj się do swojego konta i ustaw subskrypcję zgodnie z opisem w [tym miejscu](./vmware-azure-disaster-recovery-powershell.md#log-into-azure).
3. [Skonfiguruj](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault) magazyn Recovery Services i [Ustaw kontekst magazynu](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context).
4. [Sprawdź poprawność](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration) rejestracji magazynu.
5. [Utwórz](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy) zasady replikacji.
6. [Dodaj](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms) serwer vCenter i odnajdź maszyny wirtualne i [Utwórz](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication) konta magazynu na potrzeby replikacji.
7. Aby replikować oprogramowanie VMware Virtual Machines, zapoznaj się z informacjami w tym miejscu i wykonaj następujące polecenie cmdlet programu PowerShell —

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. Można sprawdzić stan replikacji i kondycję replikacji maszyny wirtualnej za pomocą polecenia cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. Skonfiguruj ustawienia trybu failover, wykonując kroki opisane [tutaj](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings).
10. [Uruchom](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover) test pracy w trybie failover.
11. Przełączenie do trybu failover na platformie Azure za pomocą [tych](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) kroków.

### <a name="hyper-v-to-azure-via-powershell"></a>Funkcja Hyper-V do platformy Azure za pośrednictwem programu PowerShell

1. Upewnij się, że [przygotowano lokalne serwery funkcji Hyper-V](./hyper-v-prepare-on-premises-tutorial.md) na potrzeby odzyskiwania po awarii na platformie Azure.
2. [Zaloguj](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account) się do platformy Azure.
3. [Skonfiguruj](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault) magazyn i [Ustaw](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context) Recovery Services kontekstu magazynu.
4. [Utwórz](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site) lokację funkcji Hyper-V.
5. [Zainstaluj](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent) dostawcę i agenta.
6. [Utwórz](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy) zasady replikacji.
7. Włącz replikację, wykonując poniższe kroki: 
    
    a. Pobierz element objęty ochroną, który odnosi się do maszyny wirtualnej, która ma być chroniona, w następujący sposób:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Ochrona maszyny wirtualnej. Jeśli do chronionej maszyny wirtualnej jest dołączony więcej niż jeden dysk, określ dysk systemu operacyjnego za pomocą parametru OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    c. Poczekaj, aż maszyny wirtualne osiągną stan chroniony po replikacji początkowej. Może to chwilę potrwać, w zależności od takich czynników, jak ilość danych, które mają być replikowane, oraz dostępną przepustowość w strumieniu do platformy Azure. W przypadku stanu chronionego stan zadania i StateDescription są aktualizowane w następujący sposób: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Zaktualizuj właściwości odzyskiwania (takie jak rozmiar roli maszyny wirtualnej) i sieć platformy Azure, do której ma zostać dołączona karta sieciowa maszyny wirtualnej po przejściu w tryb failover.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Uruchom test [pracy w trybie failover](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover).


## <a name="next-steps"></a>Następne kroki

Aby przeprowadzić ponowne włączenie ochrony i powrotu po awarii dla oprogramowania VMware na platformę Azure, wykonaj kroki opisane [tutaj](./vmware-azure-prepare-failback.md).

Aby przeprowadzić przejście w tryb failover dla funkcji Hyper-V do platformy Azure, wykonaj kroki opisane [tutaj](./site-recovery-failover.md) i aby przeprowadzić powrót po awarii, wykonaj kroki opisane [tutaj](./hyper-v-azure-failback.md).

Aby uzyskać więcej informacji, zobacz [tryb failover w Site Recovery](site-recovery-failover.md).