---
title: Migrowanie maszyn wirtualnych VMware na platformę Azure (bez agenta) — PowerShell
description: Dowiedz się, jak uruchomić migrację maszyn wirtualnych VMware bez wykorzystania agentów przy użyciu Azure Migrate za pośrednictwem programu PowerShell.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 006b2838a4e593397f8968e53ba2364d16753a40
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547064"
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
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z opcji domyślnych, tam gdzie to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="1-prerequisites"></a>1. wymagania wstępne

Przed rozpoczęciem tego samouczka należy:

1. Ukończ [Samouczek: odnajdywanie maszyn wirtualnych VMware z oceną serwera](tutorial-discover-vmware.md) w celu przygotowania platformy Azure i oprogramowania VMware do migracji.
1. Ukończ [Samouczek: ocenianie maszyn wirtualnych VMware na potrzeby migracji do maszyn wirtualnych platformy Azure](./tutorial-assess-vmware-azure-vm.md) przed ich migracją na platformę Azure.
1. [Instalowanie polecenia AZ PowerShell module](/powershell/azure/install-az-ps)

## <a name="2-install-azure-migrate-powershell-module"></a>2. Zainstaluj moduł Azure Migrate PowerShell

Moduł Azure Migrate PowerShell jest dostępny w wersji zapoznawczej. Należy zainstalować moduł programu PowerShell przy użyciu następującego polecenia.

```azurepowershell-interactive
Install-Module -Name Az.Migrate
```

## <a name="3-sign-in-to-your-microsoft-azure-subscription"></a>3. Zaloguj się do swojej subskrypcji Microsoft Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```azurepowershell
Connect-AzAccount
```

### <a name="select-your-azure-subscription"></a>Wybierz subskrypcję platformy Azure

Użyj polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) , aby uzyskać listę subskrypcji platformy Azure, do których masz dostęp. Wybierz subskrypcję platformy Azure, która ma projekt Azure Migrate do pracy przy użyciu polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="4-retrieve-the-azure-migrate-project"></a>4. Pobierz projekt Azure Migrate

Projekt Azure Migrate jest używany do przechowywania metadanych odnajdywania, oceny i migracji zebranych ze środowiska, które oceniasz lub migrujesz.
W projekcie można śledzić odnalezione zasoby, organizować oceny i przeprowadzać migracje.

W ramach wymagań wstępnych utworzono już projekt Azure Migrate. Użyj polecenia cmdlet [Get-AzMigrateProject](/powershell/module/az.migrate/get-azmigrateproject) , aby pobrać szczegóły projektu Azure Migrate. Należy określić nazwę projektu programu Azure Migrate ( `Name` ) i nazwę grupy zasobów projektu Azure Migrate ( `ResourceGroupName` ).

```azurepowershell-interactive
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name MyResourceGroup

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name MyMigrateProject -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
Write-Output $MigrateProject
```

## <a name="5-retrieve-discovered-vms-in-an-azure-migrate-project"></a>5. Pobieranie odnalezionych maszyn wirtualnych w projekcie Azure Migrate

Azure Migrate używa urządzenia uproszczonego [Azure Migrate](migrate-appliance-architecture.md). W ramach wymagań wstępnych wdrożono urządzenie Azure Migrate jako maszynę wirtualną VMware.

Aby pobrać konkretną maszynę wirtualną VMware w projekcie Azure Migrate, określ nazwę projektu Azure Migrate ( `ProjectName` ), grupę zasobów projektu Azure Migrate ( `ResourceGroupName` ) i nazwę maszyny wirtualnej ( `DisplayName` ).

> [!IMPORTANT]
> **W wartości parametru Nazwa maszyny wirtualnej ( `DisplayName` ) jest rozróżniana wielkość** liter.

```azurepowershell-interactive
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName MyTestVM

# View discovered server details
Write-Output $DiscoveredServer
```

Przeprowadzimy migrację tej maszyny wirtualnej w ramach tego samouczka.

Możesz również pobrać wszystkie maszyny wirtualne VMware w projekcie Azure Migrate przy użyciu parametrów **ProjectName** i **ResourceGroupName** .

```azurepowershell-interactive
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName
```

Jeśli masz wiele urządzeń w projekcie Azure Migrate, możesz **użyć parametrów** **ProjectName**, **ResourceGroupName** i dostawname, aby pobrać wszystkie maszyny wirtualne wykryte przy użyciu określonego urządzenia Azure Migrate.

```azurepowershell-interactive
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName MyMigrateAppliance

```

## <a name="6-initialize-replication-infrastructure"></a>6. zainicjuj infrastrukturę replikacji

[Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) wykorzystuje wiele zasobów platformy Azure do migrowania maszyn wirtualnych. Migracja serwera udostępnia następujące zasoby w tej samej grupie zasobów co projekt.

- **Service Bus**: Migracja serwera używa usługi Service Bus do wysyłania komunikatów aranżacji replikacji do urządzenia.
- **Konto magazynu bramy**: Migracja serwera używa konta magazynu bramy do przechowywania informacji o stanie replikowanych maszyn wirtualnych.
- **Konto magazynu dzienników**: urządzenie Azure Migrate przekazuje dzienniki replikacji dla maszyn wirtualnych do konta magazynu dzienników. Azure Migrate stosuje informacje o replikacji do dysków zarządzanych przez replikę.
- **Magazyn kluczy**: urządzenie Azure Migrate używa magazynu kluczy do zarządzania parametrami połączenia dla magistrali usług i kluczy dostępu dla kont magazynu używanych w replikacji.

Przed replikacją pierwszej maszyny wirtualnej w projekcie Azure Migrate uruchom następujący skrypt, aby zainicjować obsługę infrastruktury replikacji. Ten skrypt inicjuje i konfiguruje wyżej wymienione zasoby, aby można było rozpocząć Migrowanie maszyn wirtualnych VMware.

> [!NOTE]
> Jeden Azure Migrate projekt obsługuje migracje tylko do jednego regionu platformy Azure. Po uruchomieniu tego skryptu nie można zmienić regionu docelowego, do którego chcesz zmigrować maszyny wirtualne VMware.
> Aby `Initialize-AzMigrateReplicationInfrastructure` skonfigurować nowe urządzenie w projekcie Azure Migrate, należy uruchomić skrypt.

W tym artykule zainicjujemy infrastrukturę replikacji, aby umożliwić nam Migrowanie maszyn wirtualnych do `Central US` regionu. Plik można [pobrać](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) z repozytorium GitHub lub uruchomić go przy użyciu poniższego fragmentu kodu.

```azurepowershell-interactive
# Download the script from Azure Migrate GitHub repository
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion CentralUS
```

## <a name="7-replicate-vms"></a>7. replikowanie maszyn wirtualnych

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

W tym samouczku będziemy replikować wszystkie dyski wykrytej maszyny wirtualnej i określić nową nazwę dla maszyny wirtualnej na platformie Azure. Określamy pierwszy dysk odnalezionego serwera jako dysk systemu operacyjnego i migruje wszystkie dyski jako HDD w warstwie Standardowa. Dysk systemu operacyjnego to dysk, na którym jest zainstalowany program ładujący i instalator systemu operacyjnego. Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

```azurepowershell-interactive
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType Standard_LRS -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replikowanie maszyn wirtualnych z wybieraniem dysków

Można również wybiórczo przeprowadzić replikację dysków wykrytej maszyny wirtualnej za pomocą polecenia cmdlet [New-AzMigrateDiskMapping](/powershell/module/az.migrate/new-azmigratediskmapping) i zapewnić, że jako dane wejściowe do parametru **DiskToInclude** w polecenia cmdlet [New-AzMigrateServerReplication](/powershell/module/az.migrate/new-azmigrateserverreplication) . Można również użyć `New-AzMigrateDiskMapping` polecenia cmdlet, aby określić różne typy dysków docelowych dla każdego dysku do replikacji.

Określ wartości dla następujących parametrów `New-AzMigrateDiskMapping` polecenia cmdlet.

- **DiskId** — Określ unikatowy identyfikator dysku, który ma zostać zmigrowany. Identyfikator dysku, który ma być używany, to właściwość unikatowego identyfikatora (UUID) dla dysku pobranego za pomocą `Get-AzMigrateServer` polecenia cmdlet.
- **IsOSDisk** -Określ wartość "true", jeśli dysk, który ma zostać migrowany, jest dyskiem systemu operacyjnego maszyny wirtualnej, w przeciwnym razie "false".
- Typ **dysku** — umożliwia określenie typu dysku, który ma być używany na platformie Azure.

W poniższym przykładzie będziemy replikować tylko dwa dyski wykrytej maszyny wirtualnej. Określimy dysk systemu operacyjnego i użyjesz różnych typów dysków dla każdego dysku do replikacji. Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

```azurepowershell-interactive
# View disk details of the discovered server
Write-Output $DiscoveredServer.Disk

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType StandardSSD_LRS -IsOSDisk true
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType Premium_LRS -IsOSDisk false

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name MyTargetResourceGroup

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name MyVirtualNetwork

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType NoLicenseType -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName MyMigratedTestVM -TargetVMSize Standard_DS2_v2

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="8-monitor-replication"></a>8. Monitoruj replikację

Replikacja odbywa się w następujący sposób:

- Po pomyślnym zakończeniu zadania Rozpocznij replikację maszyny rozpoczynają replikację początkową na platformę Azure.
- Podczas replikacji początkowej tworzona jest migawka maszyny wirtualnej. Dane dysku z migawki są replikowane do dysków zarządzanych przez repliki na platformie Azure.
- Po zakończeniu replikacji początkowej rozpoczyna się replikacja różnicowa. Przyrostowe zmiany w dyskach lokalnych są okresowo replikowane do dysków repliki na platformie Azure.

Śledź stan replikacji za pomocą polecenia cmdlet [Get-AzMigrateServerReplication](/powershell/module/az.migrate/get-azmigrateserverreplication) .

> [!NOTE]
> Odnaleziony Identyfikator maszyny wirtualnej i identyfikator maszyny wirtualnej replikacji są dwa różne unikatowe identyfikatory. Oba te identyfikatory mogą służyć do pobierania szczegółów serwera replikowanego.

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Monitoruj replikację przy użyciu odnalezionego identyfikatora maszyny wirtualnej

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Monitoruj replikację przy użyciu identyfikatora replikacji maszyny wirtualnej

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

W danych wyjściowych można śledzić **stan migracji** i właściwości **opisu stanu migracji** .

- W przypadku replikacji początkowej wartości właściwości **stan migracji** i **Opis stanu migracji** będą `InitialSeedingInProgress` odpowiednio i odpowiednie `Initial replication` .
- Podczas replikacji różnicowej wartości właściwości **stan migracji** i **Opis stanu migracji** będą `Replicating` odpowiednio i odpowiednie `Ready to migrate` .
- Po zakończeniu migracji wartości właściwości **stan migracji** i **Opis stanu migracji** będą `Migration succeeded` odpowiednio i odpowiednie `Migrated` .

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

```azurepowershell-interactive
Write-Output $replicatingserver.ProviderSpecificDetail
```

Postęp replikacji początkowej można śledzić przy użyciu **początkowych właściwości procentowego postępu wypełniania** w danych wyjściowych.

```Output
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

## <a name="9-retrieve-the-status-of-a-job"></a>9. Pobieranie stanu zadania

Stan zadania można monitorować za pomocą polecenia cmdlet [Get-AzMigrateJob](/powershell/module/az.migrate/get-azmigratejob) .

```azurepowershell-interactive
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="10-update-properties-of-a-replicating-vm"></a>10. Aktualizowanie właściwości replikacji maszyny wirtualnej

[Azure Migrate: Migracja serwera](migrate-services-overview.md#azure-migrate-server-migration-tool) umożliwia zmianę właściwości docelowych, takich jak nazwa, rozmiar, Grupa zasobów, konfiguracja karty sieciowej i tak dalej, w przypadku replikacji maszyny wirtualnej.

Następujące właściwości można zaktualizować dla maszyny wirtualnej.

- **Nazwa maszyny wirtualnej** — Określ nazwę maszyny wirtualnej platformy Azure, która ma zostać utworzona przy użyciu parametru **TargetVMName** .
- **Rozmiar maszyny wirtualnej** — Określ rozmiar maszyny wirtualnej platformy Azure, który ma być używany dla replikacji maszyny wirtualnej za pomocą parametru **TargetVMSize** . Na przykład aby przeprowadzić migrację maszyny wirtualnej do D2_v2 maszyny wirtualnej na platformie Azure, określ wartość **TargetVMSize** jako `Standard_D2_v2` .
- **Virtual Network** — określ identyfikator Virtual Network platformy Azure, do którego należy migrować maszynę wirtualną przy użyciu parametru **TargetNetworkId** .
- **Grupa zasobów** — Określ identyfikator grupy zasobów, do której należy migrować maszynę wirtualną, podając identyfikator grupy zasobów przy użyciu parametru **TargetResourceGroupId** .
- **Interfejs sieciowy** — konfiguracja karty sieciowej można określić za pomocą polecenia cmdlet [New-AzMigrateNicMapping](/powershell/module/az.migrate/new-azmigratenicmapping) . Obiekt jest następnie przekazywać dane wejściowe do parametru **NicToUpdate** w poleceniu cmdlet [Set-AzMigrateServerReplication](/powershell/module/az.migrate/set-azmigrateserverreplication) .

    - **Zmiana alokacji adresu IP** — aby określić statyczny adres IP dla karty sieciowej, podaj adres IPv4, który ma być używany jako statyczne adresy IP dla maszyny wirtualnej przy użyciu parametru **TargetNicIP** . Aby dynamicznie przypisać adres IP karty sieciowej, podaj `auto` jako wartość parametru **TargetNicIP** .
    - Użyj wartości `Primary` `Secondary` lub `DoNotCreate` parametru **TargetNicSelectionType** , aby określić, czy karta sieciowa powinna być podstawowa, pomocnicza, czy nie ma być utworzona na migrowanej maszynie wirtualnej. Jako podstawowej karty sieciowej maszyny wirtualnej można określić tylko jedną kartę sieciową.
    - Aby utworzyć podstawową kartę sieciową, należy również określić inne karty sieciowe, które powinny być tworzone jako pomocnicze lub nie mają być tworzone na migrowanej maszynie wirtualnej.
    - Aby zmienić podsieć karty sieciowej, należy określić nazwę podsieci przy użyciu parametru **TargetNicSubnet** .

 - **Strefa dostępności** — aby użyć stref dostępności, określ wartość strefy dostępności dla parametru **TargetAvailabilityZone** .
 - **Zestaw dostępności** — aby użyć zestawu dostępności, określ identyfikator zestawu dostępności dla parametru **TargetAvailabilitySet** .

`Get-AzMigrateServerReplication`Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

```azurepowershell-interactive
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic
```

W poniższym przykładzie zaktualizujemy konfigurację karty sieciowej, tworząc pierwszą kartę sieciową jako podstawową i przypisując do niej statyczny adres IP. odrzucimy drugą kartę sieciową do migracji i zaktualizujesz nazwę i rozmiar docelowej maszyny wirtualnej.

```azurepowershell-interactive
# Specify the NIC properties to be updated for a replicating VM.
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP ###.###.###.### -TargetNicSelectionType Primary
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType DoNotCreate

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize Standard_DS13_v2 -TargetVMName MyMigratedVM -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq 'InProgress') -or ($UpdateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $UpdateJob.State
```

Można również wyświetlić listę wszystkich serwerów replikowanych w projekcie Azure Migrate, a następnie użyć replikowanego identyfikatora maszyny wirtualnej w celu zaktualizowania właściwości maszyny wirtualnej.

```azurepowershell-interactive
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName |
                     Where-Object MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id
```

## <a name="11-run-a-test-migration"></a>11. Przeprowadź migrację testową

Po rozpoczęciu replikacji różnicowej można przeprowadzić migrację testową dla maszyn wirtualnych przed uruchomieniem pełnej migracji na platformę Azure. Zdecydowanie zalecamy przetestowanie migracji co najmniej raz dla każdej maszyny przed jej migracją. Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

- Podczas przeprowadzania migracji testów migracja będzie działać zgodnie z oczekiwaniami. Migracja testowa nie ma wpływu na maszynę lokalną, która pozostaje operacyjna i kontynuuje replikację.
- Migracja testowa symuluje migrację, tworząc maszynę wirtualną platformy Azure przy użyciu zreplikowanych danych (zazwyczaj migrujesz do nieprodukcyjnej sieci wirtualnej w ramach subskrypcji platformy Azure).
- Możesz użyć zreplikowanego testowej maszyny wirtualnej platformy Azure, aby zweryfikować migrację, przeprowadzić testowanie aplikacji i rozwiązać wszelkie problemy przed pełną migracją.

Wybierz Virtual Network platformy Azure, który ma być używany do testowania, określając identyfikator sieci wirtualnej przy użyciu parametru **TestNetworkID** .

```azurepowershell-interactive
# Retrieve the Azure virtual network created for testing
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq 'InProgress') -or ($TestMigrationJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $TestMigrationJob.State
```

Po zakończeniu testowania Oczyść test migracji za pomocą polecenia cmdlet [Start-AzMigrateTestMigrationCleanup](/powershell/module/az.migrate/start-azmigratetestmigrationcleanup) . Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

```azurepowershell-interactive
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $CleanupTestMigrationJob.State
```

## <a name="12-migrate-vms"></a>12. Migrowanie maszyn wirtualnych

Po zweryfikowaniu, że migracja testowa działa zgodnie z oczekiwaniami, można przeprowadzić migrację serwera replikowanego za pomocą następującego polecenia cmdlet. Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

Jeśli nie chcesz wyłączyć serwera źródłowego, nie używaj parametru **TurnOffSourceServer** .

```azurepowershell-interactive
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer

# Track job status to check for completion
while (($MigrateJob.State -eq 'InProgress') -or ($MigrateJob.State -eq 'NotStarted')){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
Write-Output $MigrateJob.State
```

## <a name="13-complete-the-migration"></a>13. Ukończ migrację

1. Po zakończeniu migracji Zatrzymaj replikację na maszynie lokalnej i Wyczyść informacje o stanie replikacji dla maszyny wirtualnej za pomocą następującego polecenia cmdlet. Polecenie cmdlet zwraca zadanie, które może być śledzone w celu monitorowania stanu operacji.

   ```azurepowershell-interactive
   # Stop replication for a migrated server
   $StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer

   # Track job status to check for completion
   while (($StopReplicationJob.State -eq 'InProgress') -or ($StopReplicationJob.State -eq 'NotStarted')){
           #If the job hasn't completed, sleep for 10 seconds before checking the job status again
           sleep 10;
           $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
   }
   #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded".
   Write-Output $StopReplicationJob.State
   ```

1. Zainstaluj agenta systemu [Linux](../virtual-machines/extensions/agent-linux.md) na zmigrowanych maszynach, jeśli komputer ma system operacyjny Linux. Podczas migracji automatycznie instalujemy agenta maszyny wirtualnej dla maszyn wirtualnych z systemem Windows.
1. Po zakończeniu migracji wykonaj wszystkie potrzebne czynności konfiguracyjne, takie jak aktualizacja parametrów połączenia bazy danych i serwera sieci Web.
1. Wykonaj dla zmigrowanej aplikacji uruchomionej na platformie Azure testy końcowe aplikacji i akceptacji migracji.
1. Obcinaj ruch do zmigrowanego wystąpienia maszyny wirtualnej platformy Azure.
1. Usuń lokalne maszyny wirtualne z lokalnego spisu maszyn wirtualnych.
1. Usuń lokalne maszyny wirtualne z lokalnych kopii zapasowych.
1. Zaktualizuj wszystkie dokumenty wewnętrzne, aby wyświetlić nową lokalizację i adres IP maszyn wirtualnych platformy Azure.

## <a name="14-post-migration-best-practices"></a>14. najlepsze rozwiązania po migracji

- Aby zwiększyć elastyczność:
    - Zapewnij bezpieczeństwo danych – utwórz kopie zapasowe maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup. [Dowiedz się więcej](../backup/quick-backup-vm-portal.md).
    - Zadbaj, aby pakiety robocze były uruchomione i stale dostępne, replikując maszyny wirtualne platformy Azure do regionu pomocniczego za pomocą usługi Site Recovery. [Dowiedz się więcej](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Aby zwiększyć bezpieczeństwo:
    - Zablokuj i Ogranicz dostęp do ruchu przychodzącego za pomocą [administracji Azure Security Center w czasie](../security-center/security-center-just-in-time.md).
    - Ogranicz ruch sieciowy do punktów końcowych zarządzania za pomocą [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).
    - Wdróż usługę [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md), aby ułatwić zabezpieczenie dysków i zabezpieczyć dane przed kradzieżą lub nieautoryzowanym dostępem.
    - Przeczytaj więcej na temat [zabezpieczania zasobów IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) i skorzystaj z usługi [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Na potrzeby monitorowania i zarządzania:
-  Rozważ wdrożenie usługi [Azure Cost Management](../cost-management-billing/cloudyn/overview.md), aby monitorować użycie zasobu i wydatki.
