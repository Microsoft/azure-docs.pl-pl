---
title: Konfigurowanie odzyskiwania po awarii oprogramowania VMware przy użyciu programu PowerShell w usłudze Azure Site revoery
description: Dowiedz się, jak skonfigurować replikację i przejście w tryb failover na platformie Azure na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware przy użyciu programu PowerShell w Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129912"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Skonfiguruj odzyskiwanie po awarii maszyn wirtualnych VMware na platformę Azure za pomocą programu PowerShell

W tym artykule opisano sposób replikowania maszyn wirtualnych VMware i pracy w trybie failover na platformie Azure przy użyciu Azure PowerShell.

Omawiane kwestie:

> [!div class="checklist"]
> - Utwórz magazyn Recovery Services i ustaw kontekst magazynu.
> - Sprawdź poprawność rejestracji serwera w magazynie.
> - Skonfiguruj replikację, w tym zasady replikacji. Dodaj serwer vCenter i odnajdź maszyny wirtualne.
> - Dodawanie serwera vCenter i odnajdowanie
> - Utwórz konta magazynu, aby przechowywać dzienniki replikacji lub dane oraz replikować maszyny wirtualne.
> - Wykonaj przejście w tryb failover. Skonfiguruj ustawienia trybu failover, aby przeprowadzić replikację maszyn wirtualnych.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

- Przeanalizuj informacje o [składnikach i architekturze scenariusza](vmware-azure-architecture.md).
- Zapoznaj się z wymaganiami dotyczącymi [obsługi](./vmware-physical-azure-support-matrix.md) wszystkich składników.
- Masz `Az`  moduł Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić Azure PowerShell, postępuj zgodnie [z tym przewodnikiem, aby zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Logowanie się do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet Connect-AzAccount:

```azurepowershell
Connect-AzAccount
```
Wybierz subskrypcję platformy Azure, do której chcesz replikować maszyny wirtualne VMware. Użyj polecenia cmdlet Get-AzSubscription, aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure, z którą chcesz korzystać przy użyciu polecenia cmdlet Select-AzSubscription.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Konfigurowanie magazynu usługi Recovery Services

1. Utwórz grupę zasobów, w której ma zostać utworzony magazyn Recovery Services. W poniższym przykładzie grupa zasobów ma nazwę VMwareDRtoAzurePS i jest tworzona w regionie Azja Wschodnia.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Utwórz magazyn usługi Recovery Services. W poniższym przykładzie magazyn usługi Recovery Services nosi nazwę VMwareDRToAzurePs i jest tworzony w regionie Azja Wschodnia i w grupie zasobów utworzonej w poprzednim kroku.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Pobierz klucz rejestracji magazynu dla magazynu. Klucz rejestracji magazynu jest używany do rejestrowania lokalnego serwera konfiguracji w tym magazynie. Rejestracja jest częścią procesu instalacji oprogramowania serwera konfiguracji.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Użyj pobranego klucza rejestracji magazynu i postępuj zgodnie z instrukcjami podanym poniżej w celu przeprowadzenia instalacji i rejestracji serwera konfiguracji.
   - [Wybierz cele ochrony](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Konfigurowanie środowiska źródłowego](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Ustaw kontekst magazynu

Ustaw kontekst magazynu za pomocą polecenia cmdlet Set-ASRVaultContext. Po ustawieniu kolejne Azure Site Recovery operacje w sesji programu PowerShell są wykonywane w kontekście wybranego magazynu.

> [!TIP]
> Azure Site Recovery module programu PowerShell (AZ. RecoveryServices module) zawiera łatwe do użycia aliasy dla większości poleceń cmdlet. Polecenia cmdlet w module przyjmują postać *\<Operation> - **AzRecoveryServicesAsr** \<Object>* i mają równoważne aliasy, które przyjmują formularz usługi *\<Operation> - **ASR** \<Object>*. Aliasy poleceń cmdlet można zastąpić, aby ułatwić korzystanie z programu.

W poniższym przykładzie szczegóły magazynu ze zmiennej $vault są używane do określania kontekstu magazynu dla sesji programu PowerShell.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Alternatywą dla polecenia cmdlet Set-ASRVaultContext może być również użycie Import-AzRecoveryServicesAsrVaultSettingsFile polecenia cmdlet do ustawienia kontekstu magazynu. Określ ścieżkę, w której znajduje się plik klucza rejestracji magazynu jako parametr-path polecenia cmdlet Import-AzRecoveryServicesAsrVaultSettingsFile. Na przykład:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
W kolejnych sekcjach tego artykułu przyjęto założenie, że kontekst magazynu dla operacji Azure Site Recovery został ustawiony.

## <a name="validate-vault-registration"></a>Weryfikowanie rejestracji magazynu

W tym przykładzie mamy następujące elementy:

- Serwer konfiguracji (**ConfigurationServer**) został zarejestrowany w tym magazynie.
- Dodatkowy serwer przetwarzania (**skalowania-ProcessServer**) został zarejestrowany w usłudze *ConfigurationServer*
- Konta (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) zostały skonfigurowane na serwerze konfiguracji. Te konta są używane do dodawania serwera vCenter, do odnajdywania maszyn wirtualnych oraz do wypychania oprogramowania usługi mobilności na serwerach z systemem Windows i Linux, które mają być replikowane.

1. Zarejestrowane serwery konfiguracji są reprezentowane przez obiekt sieci szkieletowej w Site Recovery. Pobierz listę obiektów sieci szkieletowej w magazynie i Zidentyfikuj serwer konfiguracji.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Zidentyfikuj serwery przetwarzania, których można użyć do replikowania maszyn.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   Dane wyjściowe powyżej ***$ProcessServers [0]** _ odpowiadają _skalowania-ProcessServer * i ***$ProcessServers [1]**_ odpowiadają roli serwera przetwarzania w _ConfigurationServer *

3. Zidentyfikuj konta, które zostały skonfigurowane na serwerze konfiguracji.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   Z danych wyjściowych powyżej ***$AccountHandles [0]** _ odpowiada konto _vCenter_account *, ***$AccountHandles [1]**_ do konta _WindowsAccount * i ***$AccountHandles [2]**_ do konta _LinuxAccount *

## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

W tym kroku zostaną utworzone dwie zasady replikacji. Jedną z zasad w celu replikowania maszyn wirtualnych VMware na platformę Azure, a druga — w przypadku maszyn wirtualnych uruchomionych na platformie Azure z powrotem do lokalnej lokacji programu VMware.

> [!NOTE]
> Większość Azure Site Recovery operacji jest wykonywanych asynchronicznie. Po zainicjowaniu operacji zostanie przesłane zadanie Azure Site Recovery i zostanie zwrócony obiekt śledzenia zadań. Ten obiekt śledzenia zadań może służyć do monitorowania stanu operacji.

1. Utwórz zasady replikacji o nazwie *ReplicationPolicy* , aby replikować maszyny wirtualne VMware na platformę Azure przy użyciu określonych właściwości.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Utwórz zasady replikacji, które mają być używane na potrzeby powrotu po awarii z platformy Azure do lokalnej lokacji programu VMware.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Aby śledzić operację do ukończenia, użyj szczegółów zadania w *_FailbackPolicyCreate $Job* .

   * Utwórz mapowanie kontenera ochrony, aby mapować zasady replikacji z serwerem konfiguracji.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Dodawanie serwera vCenter i odnajdywanie maszyn wirtualnych

Dodaj vCenter Server według adresu IP lub nazwy hosta. Parametr **-port** określa port serwera vCenter, z którym ma zostać nawiązane połączenie, parametr **-name** określa przyjazną nazwę, która ma być używana dla serwera vCenter, a parametr  **-Account** określa uchwyt konta na serwerze konfiguracji, który będzie używany do odnajdywania maszyn wirtualnych zarządzanych przez serwer vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Tworzenie kont magazynu na potrzeby replikacji

**Aby zapisać na dysku zarządzanym, użyj polecenia [AZ. RecoveryServices module 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) lub nowszego.** Wymagane jest tylko utworzenie konta magazynu dzienników. Zaleca się użycie standardowego typu konta i nadmiarowości LRS, ponieważ jest on używany do przechowywania tylko dzienników tymczasowych. Upewnij się, że konto magazynu jest tworzone w tym samym regionie świadczenia usługi Azure co magazyn.

Jeśli używasz wersji programu AZ. RecoveryServices module starszej niż 2.0.0, wykonaj następujące kroki, aby utworzyć konta magazynu. Te konta magazynu są używane później do replikowania maszyn wirtualnych. Upewnij się, że konta magazynu są tworzone w tym samym regionie świadczenia usługi Azure co magazyn. Możesz pominąć ten krok, jeśli planujesz użyć istniejącego konta magazynu na potrzeby replikacji.

> [!NOTE]
> Podczas replikowania lokalnych maszyn wirtualnych do konta magazynu w warstwie Premium należy określić dodatkowe konto magazynu w warstwie Standardowa (konto magazynu dzienników). Konto magazynu dzienników przechowuje dzienniki replikacji jako magazyn pośredni do momentu zastosowania dzienników w docelowym magazynie Premium Storage.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replikowanie maszyn wirtualnych VMware

Wykrycie maszyn wirtualnych z serwera vCenter trwa około 15-20 minut. Po wykryciu, w Azure Site Recovery dla każdej wykrytej maszyny wirtualnej jest tworzony obiekt elementu z ochroną. W tym kroku zostaną zreplikowane trzy wykryte maszyny wirtualne do kont usługi Azure Storage utworzonych w poprzednim kroku.

Aby chronić odnalezioną maszynę wirtualną, potrzebne są następujące szczegóły:

* Element objęty ochroną do replikacji.
* Konto magazynu, do którego należy replikować maszynę wirtualną (tylko w przypadku replikowania do konta magazynu). 
* Magazyn dzienników jest wymagany do ochrony maszyn wirtualnych na koncie magazynu w warstwie Premium lub na dysku zarządzanym.
* Serwer przetwarzania, który ma być używany na potrzeby replikacji. Lista dostępnych serwerów przetwarzania została pobrana i zapisana w ***$ProcessServers [0]** _ _(skalowania-ProcessServer) * i ***$ProcessServers [1]**_ _ (ConfigurationServer) * zmienne.
* Konto używane do wypychania instalacji oprogramowania usługi mobilności na maszynach. Lista dostępnych kont została pobrana i zapisana w zmiennej ***$AccountHandles*** .
* Mapowanie kontenera ochrony dla zasad replikacji, które ma być używane na potrzeby replikacji.
* Grupa zasobów, w której maszyny wirtualne muszą zostać utworzone w trybie failover.
* Opcjonalnie Sieć wirtualna platformy Azure i podsieć, do której powinna być połączona maszyna wirtualna w trybie failover.

Teraz Replikuj następujące maszyny wirtualne przy użyciu ustawień określonych w tej tabeli


|Maszyna wirtualna  |Serwer przetwarzania        |Konto magazynu              |Konto magazynu dzienników  |Zasady           |Konto do instalacji usługi mobilności|Docelowa grupa zasobów  | Docelowa sieć wirtualna  |Podsieć docelowa  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |Nie dotyczy| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR — Sieć wirtualna                 |Podsieć-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR — Sieć wirtualna                 |Podsieć-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| Nie dotyczy                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR — Sieć wirtualna                 |Podsieć-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Po pomyślnym zakończeniu zadania włączania replikacji początkowa replikacja jest uruchamiana dla maszyn wirtualnych. Replikacja początkowa może zająć trochę czasu w zależności od ilości danych, które mają być replikowane, oraz przepustowości dostępnej na potrzeby replikacji. Po zakończeniu replikacji początkowej maszyna wirtualna przechodzi do stanu chronionego. Gdy maszyna wirtualna osiągnie stan chroniony, można wykonać test pracy w trybie failover dla maszyny wirtualnej, dodać go do planów odzyskiwania itp.

Można sprawdzić stan replikacji i kondycję replikacji maszyny wirtualnej za pomocą polecenia cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurowanie ustawień trybu failover

Ustawienia trybu failover dla chronionych maszyn można aktualizować za pomocą polecenia cmdlet Set-ASRReplicationProtectedItem. Niektóre z ustawień, które można zaktualizować za pomocą tego polecenia cmdlet, to:
* Nazwa maszyny wirtualnej, która ma zostać utworzona w trybie failover
* Rozmiar maszyny wirtualnej, która ma zostać utworzona w trybie failover
* Sieć wirtualna platformy Azure i podsieć, z którą powinny być połączone karty sieciowe maszyny wirtualnej w trybie failover
* Przełączenie w tryb failover do dysków zarządzanych
* Zastosuj korzyść użycia hybrydowego platformy Azure
* Przypisz statyczny adres IP z docelowej sieci wirtualnej, która ma zostać przypisana do maszyny wirtualnej w trybie failover.

W tym przykładzie aktualizujemy rozmiar maszyny wirtualnej w celu utworzenia w trybie failover dla maszyny wirtualnej *Win2K12VM1* i określ, że maszyna wirtualna będzie używać dysków zarządzanych w trybie failover.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

1. Uruchom przechodzenie do trybu failover w następujący sposób:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Po pomyślnym ukończeniu zadania testowego przejścia w tryb failover można zauważyć, że maszyna wirtualna z sufiksem *"-test"* (Win2K12VM1-test w tym przypadku) została utworzona na platformie Azure.
3. Teraz można nawiązać połączenie z testem zakończonym niepowodzeniem za pomocą maszyny wirtualnej i zweryfikować test pracy w trybie failover.
4. Oczyść test pracy w trybie failover za pomocą polecenia cmdlet Start-ASRTestFailoverCleanupJob. Ta operacja usuwa maszynę wirtualną utworzoną w ramach operacji testowej pracy w trybie failover.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Przełączenie do trybu failover na platformie Azure

W tym kroku przejdziemy do trybu failover maszyny wirtualnej Win2K12VM1 do określonego punktu odzyskiwania.

1. Pobierz listę dostępnych punktów odzyskiwania do użycia w trybie failover:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Po pomyślnym przełączeniu w tryb failover możesz zatwierdzić operację pracy awaryjnej i skonfigurować replikację odwrotną z platformy Azure z powrotem do lokalnej lokacji programu VMware.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zautomatyzować więcej zadań przy użyciu programu [Azure Site Recovery PowerShell](/powershell/module/Az.RecoveryServices).
