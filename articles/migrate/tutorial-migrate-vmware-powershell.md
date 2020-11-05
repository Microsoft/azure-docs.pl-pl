---
title: Migrowanie maszyn wirtualnych VMware na platformę Azure (bez agenta) — PowerShell
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware bez wykorzystania agentów przy użyciu Azure Migrate za pośrednictwem programu PowerShell.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: 7698e91f008fbed1f314a0cf9d39be6282493688
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359786"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrowanie maszyn wirtualnych VMware na platformę Azure (bez agenta) — PowerShell

W tym artykule dowiesz się, jak przeprowadzić migrację odnalezionych maszyn wirtualnych VMware przy użyciu metody bez agenta za pomocą Azure PowerShell dla [Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool).

Omawiane kwestie:

> [!div class="checklist"]
> * Pobierz odnalezione maszyny wirtualne VMware w projekcie Azure Migrate.
> * Rozpocznij replikację maszyn wirtualnych.
> * Aktualizowanie właściwości służących do replikowania maszyn wirtualnych.
> * Monitoruj replikację.
> * Uruchom migrację testową, aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.
> * Uruchom pełną migrację maszyny wirtualnej.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Jeśli to możliwe, samouczki używają opcji domyślnych i nie przedstawiają wszystkich możliwych ustawień i ścieżek.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. [Ukończ samouczek odnajdywania](tutorial-discover-vmware.md) , aby przygotować platformę Azure i oprogramowanie VMware do migracji.
2. Zalecamy wykonanie drugiego samouczka, aby [ocenić maszyny wirtualne VMware](tutorial-assess-vmware.md) przed przeprowadzeniem migracji na platformę Azure.
3. Masz `Az` moduł Azure PowerShell. Jeśli musisz zainstalować lub uaktualnić Azure PowerShell, postępuj zgodnie [z tym przewodnikiem, aby zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Zainstaluj moduł Azure Migrate PowerShell

Moduł Azure Migrate PowerShell jest dostępny w wersji zapoznawczej. Należy zainstalować moduł programu PowerShell przy użyciu następującego polecenia.

```azurepowershell
Install-Module -Name Az.Migrate
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji Microsoft Azure

Zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia cmdlet.

```azurepowershell
Connect-AzAccount
```

Wybierz swoją subskrypcję platformy Azure. Użyj `Get-AzSubscription` polecenia cmdlet, aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure, która ma projekt Azure Migrate do pracy przy użyciu `Set-AzContext` polecenia cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Pobierz projekt Azure Migrate

Projekt Azure Migrate jest używany do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz.
W projekcie można śledzić odnalezione zasoby, organizować oceny i przeprowadzać migracje.

W ramach wymagań wstępnych utworzono już projekt Azure Migrate. Użyj `Get-AzMigrateProject` polecenia cmdlet, aby pobrać szczegóły projektu Azure Migrate. Należy określić nazwę projektu programu Azure Migrate ( `Name` ) i nazwę grupy zasobów projektu Azure Migrate ( `ResourceGroupName` ).

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Pobieranie odnalezionych maszyn wirtualnych w projekcie Azure Migrate

Azure Migrate używa urządzenia uproszczonego [Azure Migrate](migrate-appliance-architecture.md). W ramach wymagań wstępnych wdrożono urządzenie Azure Migrate jako maszynę wirtualną VMware.

Aby pobrać konkretną maszynę wirtualną VMware w projekcie Azure Migrate, określ nazwę projektu Azure Migrate ( `ProjectName` ), grupę zasobów projektu Azure Migrate ( `ResourceGroupName` ) i nazwę maszyny wirtualnej ( `DisplayName` ).

> [!IMPORTANT]
> Nazwa maszyny wirtualnej uwzględnia wielkość liter, gdy jest używana jako wartość `DisplayName` parametru.

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
Przeprowadzimy migrację tej maszyny wirtualnej w ramach tego samouczka.

Możesz również pobrać wszystkie maszyny wirtualne VMware w projekcie Azure Migrate za pomocą `ProjectName` `ResourceGroupName` parametrów i.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```
Jeśli masz wiele urządzeń w projekcie Azure Migrate, możesz użyć `ProjectName` `ResourceGroupName` parametrów, i, `ApplianceName` Aby pobrać wszystkie maszyny wirtualne wykryte przy użyciu określonego urządzenia Azure Migrate.

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Zainicjuj infrastrukturę replikacji

[Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) wykorzystuje wiele zasobów platformy Azure do migrowania maszyn wirtualnych. Migracja serwera udostępnia następujące zasoby w tej samej grupie zasobów co projekt.

- **Service Bus** : Migracja serwera używa usługi Service Bus do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy** : Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników** : urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych przez replikę.
- **Magazyn kluczy** : urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji.

Przed replikacją pierwszej maszyny wirtualnej w projekcie Azure Migrate uruchom następujący skrypt, aby zainicjować obsługę infrastruktury replikacji. Ten skrypt inicjuje i konfiguruje wyżej wymienione zasoby, aby można było rozpocząć Migrowanie maszyn wirtualnych VMware.

> [!NOTE]
> Jeden Azure Migrate projekt obsługuje migracje tylko do jednego regionu platformy Azure. Po uruchomieniu tego skryptu nie można zmienić regionu docelowego, do którego chcesz zmigrować maszyny wirtualne VMware.
> Aby `Initialize-AzMigrateReplicationInfrastructure` skonfigurować nowe urządzenie w projekcie Azure Migrate, należy uruchomić skrypt.

W tym artykule zainicjujemy infrastrukturę replikacji, aby umożliwić nam Migrowanie maszyn wirtualnych do `Central US` regionu. Plik można [pobrać](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) z repozytorium GitHub lub uruchomić go przy użyciu poniższego fragmentu kodu.

```azurepowershell
# Download the script from Azure Migrate GitHub repository
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS"
```


## <a name="replicate-vms"></a>Replikowanie maszyn wirtualnych

Po ukończeniu odnajdywania i zainicjowaniu infrastruktury replikacji można rozpocząć replikację maszyn wirtualnych VMware na platformę Azure. Jednocześnie można uruchomić maksymalnie 300 replikacji.

Właściwości replikacji można określić w następujący sposób.

- **Docelowa subskrypcja i Grupa zasobów** — Określ subskrypcję i grupę zasobów, do której należy migrować maszynę wirtualną, podając identyfikator grupy zasobów przy użyciu `TargetResourceGroupId` parametru.
- **Docelowa sieć wirtualna i podsieć** — określ identyfikator Virtual Network platformy Azure i nazwę podsieci, do której należy przeprowadzić migrację maszyny wirtualnej przy użyciu `TargetNetworkId` parametrów i `TargetSubnetName` .
- **Nazwa docelowej maszyny wirtualnej** — Określ nazwę maszyny wirtualnej platformy Azure, która ma zostać utworzona przy użyciu `TargetVMName` parametru.
- **Docelowy rozmiar maszyny wirtualnej** — Określ rozmiar maszyny wirtualnej platformy Azure, który ma być używany dla replikacji maszyny wirtualnej za pomocą `TargetVMSize` parametru. Na przykład aby przeprowadzić migrację maszyny wirtualnej do D2_v2 maszyny wirtualnej na platformie Azure, określ wartość `TargetVMSize` "Standard_D2_v2".
- **Licencja** — aby użyć korzyść użycia hybrydowego platformy Azure dla maszyn z systemem Windows Server, które są objęte aktywnym programem Software Assurance lub subskrypcjami systemu Windows Server, określ wartość `LicenseType` parametru jako "WindowsServer". W przeciwnym razie Określ wartość `LicenseType` parametru jako "Nolicensetype".
- **Dysk systemu operacyjnego** — Określ unikatowy identyfikator dysku z programu inicjującego i Instalatora systemu operacyjnego. Identyfikator dysku, który ma być używany, to właściwość unikatowego identyfikatora (UUID) dla dysku pobranego za pomocą `Get-AzMigrateServer` polecenia cmdlet.
- **Typ dysku** — Określ wartość `DiskType` parametru w następujący sposób.
    - Aby użyć dysków zarządzanych w warstwie Premium, określ wartość "Premium_LRS" jako `DiskType` parametr.
    - Aby użyć standardowych dysków SSD, określ "StandardSSD_LRS" jako wartość `DiskType` parametru.
    - Aby użyć standardowych dysków DYSKowych, określ wartość "Standard_LRS" jako `DiskType` parametr.
- **Nadmiarowość infrastruktury** — określ opcję nadmiarowości infrastruktury w następujący sposób.
    - Strefa dostępności służąca do przypinania zmigrowanej maszyny do określonej strefy dostępności w regionie. Użyj tej opcji, aby dystrybuować serwery tworzące wielowęzłową warstwę aplikacji między Strefy dostępności. Ta opcja jest dostępna tylko wtedy, gdy region docelowy wybrany do migracji obsługuje Strefy dostępności. Aby używać stref dostępności, określ wartość strefy dostępności dla `TargetAvailabilityZone` parametru.
    - Zestaw dostępności umożliwiający umieszczenie zmigrowanej maszyny w zestawie dostępności. Docelowa Grupa zasobów, która została wybrana, musi mieć co najmniej jeden zestaw dostępności, aby można było użyć tej opcji. Aby użyć zestawu dostępności, określ identyfikator zestawu dostępności dla `TargetAvailabilitySet` parametru.

### <a name="replicate-vms-with-all-disks"></a>Replikowanie maszyn wirtualnych ze wszystkimi dyskami
W tym samouczku będziemy replikować wszystkie dyski wykrytej maszyny wirtualnej i określić nową nazwę dla maszyny wirtualnej na platformie Azure. Określamy pierwszy dysk odnalezionego serwera jako dysk systemu operacyjnego i migruje wszystkie dyski jako HDD w warstwie Standardowa. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego.

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replikowanie maszyn wirtualnych z wybieraniem dysków
Można również wybiórczo przeprowadzić replikację dysków wykrytej maszyny wirtualnej za pomocą `New-AzMigrateDiskMapping` polecenia cmdlet i dostarczając tę wartość jako dane wejściowe do `DiskToInclude` parametru w `New-AzMigrateServerReplication` poleceniu cmdlet. Można również użyć `New-AzMigrateDiskMapping` polecenia cmdlet, aby określić różne typy dysków docelowych dla każdego dysku do replikacji.

Określ wartości dla następujących parametrów `New-AzMigrateDiskMapping` polecenia cmdlet.

- **DiskId** — Określ unikatowy identyfikator dysku, który ma zostać zmigrowany. Identyfikator dysku, który ma być używany, to właściwość unikatowego identyfikatora (UUID) dla dysku pobranego za pomocą `Get-AzMigrateServer` polecenia cmdlet.
- **IsOSDisk** -Określ wartość "true", jeśli dysk, który ma zostać migrowany, jest dyskiem systemu operacyjnego maszyny wirtualnej, w przeciwnym razie "false".
- Typ **dysku** — umożliwia określenie typu dysku, który ma być używany na platformie Azure.

W poniższym przykładzie będziemy replikować tylko dwa dyski wykrytej maszyny wirtualnej. określimy dysk systemu operacyjnego i użyjesz różnych typów dysków dla każdego dysku do replikacji.

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Monitorowanie replikacji

Replikacja odbywa się w następujący sposób:

- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych przez repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

Śledź stan replikacji przy użyciu `Get-AzMigrateServerReplication` polecenia cmdlet.

> [!NOTE]
> Odnaleziony Identyfikator maszyny wirtualnej i identyfikator maszyny wirtualnej replikacji są dwa różne unikatowe identyfikatory. Oba te identyfikatory mogą służyć do pobierania szczegółów serwera replikowanego.

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Monitoruj replikację przy użyciu odnalezionego identyfikatora maszyny wirtualnej
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Monitoruj replikację przy użyciu identyfikatora replikacji maszyny wirtualnej

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

W danych wyjściowych można śledzić właściwości "stan migracji" i "opis stanu migracji".
- W przypadku replikacji początkowej wartości właściwości stan migracji i opis stanu migracji będą odpowiednio "InitialSeedingInProgress" i "Replikacja początkowa".
- Podczas replikacji różnicowej, wartości właściwości stan migracji i opis stanu migracji będą odpowiednio "replikowane" i "gotowe do migracji".
- Po zakończeniu migracji wartości właściwości stan migracji i opis stanu migracji będą odpowiednio "Migracja powiodła się" i "zmigrowany".

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Aby uzyskać szczegółowe informacje na temat postępu replikacji, uruchom następujące polecenie cmdlet.

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
Postęp replikacji początkowej można śledzić przy użyciu właściwości "początkowy procent postępu postępów" w danych wyjściowych.

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Replikacja odbywa się w następujący sposób:

- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych przez repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

## <a name="update-properties-of-a-replicating-vm"></a>Aktualizowanie właściwości replikacji maszyny wirtualnej

[Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) umożliwia zmianę właściwości docelowych, takich jak nazwa, rozmiar, Grupa zasobów, konfiguracja karty sieciowej i tak dalej, w przypadku replikacji maszyny wirtualnej.

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```
Następujące właściwości można zaktualizować dla maszyny wirtualnej.

- **Nazwa maszyny wirtualnej** — Określ nazwę maszyny wirtualnej platformy Azure, która ma zostać utworzona przy użyciu `TargetVMName` parametru.
- **Rozmiar maszyny wirtualnej** — Określ rozmiar maszyny wirtualnej platformy Azure, który ma być używany dla replikacji maszyny wirtualnej za pomocą `TargetVMSize` parametru. Na przykład aby przeprowadzić migrację maszyny wirtualnej do D2_v2 maszyny wirtualnej na platformie Azure, określ wartość `TargetVMSize` "Standard_D2_v2".
- **Virtual Network** — określ identyfikator Virtual Network platformy Azure, do którego należy migrować maszynę wirtualną przy użyciu `TargetNetworkId` parametru.
- **Grupa zasobów** — Określ identyfikator grupy zasobów, do której należy migrować maszynę wirtualną, podając identyfikator grupy zasobów przy użyciu `TargetResourceGroupId` parametru.
- **Interfejs sieciowy** — konfiguracja karty sieciowej można określić przy użyciu `New-AzMigrateNicMapping` polecenia cmdlet. Obiekt jest następnie przekazywać dane wejściowe do `NicToUpdate` parametru w `Set-AzMigrateServerReplication` poleceniu cmdlet.

    - **Zmiana alokacji adresu IP** — aby określić statyczny adres IP dla karty sieciowej, podaj adres IPv4, który ma być używany jako statyczne adresy IP dla maszyny wirtualnej przy użyciu `TargetNicIP` parametru. Aby dynamicznie przypisać adres IP karty sieciowej, należy podać wartość parametru "automatycznie" `TargetNicIP` .
    - Użyj wartości "Primary", "pomocniczy" lub "DoNotCreate" dla `TargetNicSelectionType` parametru, aby określić, czy karta sieciowa powinna być podstawowa, pomocnicza, czy nie ma być utworzona na migrowanej maszynie wirtualnej. Jako podstawowej karty sieciowej maszyny wirtualnej można określić tylko jedną kartę sieciową.
    - Aby utworzyć podstawową kartę sieciową, należy również określić inne karty sieciowe, które powinny być tworzone jako pomocnicze lub nie mają być tworzone na migrowanej maszynie wirtualnej.
    - Aby zmienić podsieć karty sieciowej, należy określić nazwę podsieci przy użyciu `TargetNicSubnet` parametru.

 - **Strefa dostępności** — aby użyć stref dostępności, określ wartość strefy dostępności dla `TargetAvailabilityZone` parametru.
 - **Zestaw dostępności** — aby użyć zestawu dostępności, określ identyfikator zestawu dostępności dla `TargetAvailabilitySet` parametru.

W poniższym przykładzie zaktualizujemy konfigurację karty sieciowej, tworząc pierwszą kartę sieciową jako podstawową i przypisując do niej statyczny adres IP. odrzucimy drugą kartę sieciową do migracji i zaktualizujesz nazwę i rozmiar docelowej maszyny wirtualnej.

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping
```

Można również wyświetlić listę wszystkich serwerów replikowanych w projekcie Azure Migrate, a następnie użyć replikowanego identyfikatora maszyny wirtualnej w celu zaktualizowania właściwości maszyny wirtualnej.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```


## <a name="run-a-test-migration"></a>Uruchamianie migracji testowej

Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy przetestowanie migracji co najmniej raz dla każdej maszyny przed jej migracją.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami. Migracja testowa nie ma wpływu na maszynę lokalną, która pozostaje operacyjna i kontynuuje replikację.
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wybierz Virtual Network platformy Azure, który ma być używany do testowania, określając identyfikator sieci wirtualnej przy użyciu `TestNetworkID` parametru.

```azurepowershell
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```

Po zakończeniu testowania Oczyść test migracji przy użyciu `Start-AzMigrateTestMigrationCleanup` polecenia cmdlet.

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```

## <a name="migrate-vms"></a>Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację serwera replikowanego za pomocą następującego polecenia cmdlet.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer
```
Jeśli nie chcesz wyłączyć serwera źródłowego, nie używaj `TurnOffSourceServer` parametru.

## <a name="complete-the-migration"></a>Kończenie migracji

1. Po zakończeniu migracji Zatrzymaj replikację na maszynie lokalnej i Wyczyść informacje o stanie replikacji dla maszyny wirtualnej za pomocą następującego polecenia cmdlet.

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer
```

2. Zainstaluj agenta maszyny wirtualnej platformy Azure dla [systemu Windows](../virtual-machines/extensions/agent-windows.md) lub [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach.
3. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
4. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
5. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
6. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
7. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
8. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.

## <a name="post-migration-best-practices"></a>Najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.



