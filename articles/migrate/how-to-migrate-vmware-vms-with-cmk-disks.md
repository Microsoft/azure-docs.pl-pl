---
title: Migrowanie maszyn wirtualnych VMware na platformę Azure za pomocą szyfrowania po stronie serwera (SSE) i kluczy zarządzanych przez klienta (CMK) przy użyciu migracji serwera Azure Migrate
description: Dowiedz się, jak migrować maszyny wirtualne VMware na platformę Azure za pomocą szyfrowania po stronie serwera (SSE) i kluczy zarządzanych przez klienta (CMK) przy użyciu migracji serwera Azure Migrate
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: 89b795011460ee2803d07d14b9c2abe9c8ffca55
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483355"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrowanie maszyn wirtualnych VMware do maszyn wirtualnych platformy Azure z włączoną funkcją szyfrowania po stronie serwera i kluczami zarządzanymi przez klienta

W tym artykule opisano sposób migrowania maszyn wirtualnych VMware do usługi Azure Virtual Machines z dyskami szyfrowanymi przy użyciu funkcji szyfrowania po stronie serwera (SSE) z kluczami zarządzanymi przez klienta (CMK) przy użyciu migracji serwera Azure Migrate (replikacja bez wykorzystania agentów).

Środowisko portalu migracji serwera Azure Migrate umożliwia [Migrowanie maszyn wirtualnych VMware na platformę Azure z replikacją bez agenta.](tutorial-migrate-vmware.md) Środowisko portalu obecnie nie oferuje możliwości włączania funkcji SSE z CMK dla replikowanych dysków na platformie Azure. Możliwość włączenia instrukcji SSE z CMK dla replikowanych dysków jest obecnie dostępna tylko za pomocą interfejsu API REST. W tym artykule opisano sposób tworzenia i wdrażania [szablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) , aby replikować maszynę wirtualną VMware i skonfigurować zreplikowane dyski na platformie Azure, aby używać instrukcji SSE z CMK.

W przykładach w tym artykule użyto [Azure PowerShell](/powershell/azure/new-azureps-module-az) do wykonywania zadań wymaganych do utworzenia i wdrożenia szablonu Menedżer zasobów.

[Dowiedz się więcej](../virtual-machines/disk-encryption.md) o szyfrowaniu po stronie serwera (SSE) z kluczami zarządzanymi przez klienta (CMK) dla dysków zarządzanych.

## <a name="prerequisites"></a>Wymagania wstępne

- [Zapoznaj się z samouczkiem](tutorial-migrate-vmware.md) dotyczącym migracji maszyn wirtualnych VMware na platformę Azure z replikacją bez agenta, aby poznać wymagania dotyczące narzędzi.
- [Postępuj zgodnie z poniższymi instrukcjami](./create-manage-projects.md) , aby utworzyć projekt Azure Migrate i Azure Migrate dodać do projektu narzędzie do **migracji serwera** .
- [Postępuj zgodnie z tymi instrukcjami](how-to-set-up-appliance-vmware.md) , aby skonfigurować urządzenie Azure Migrate dla oprogramowania VMware w środowisku lokalnym i pełne odnajdowanie.

## <a name="prepare-for-replication"></a>Przygotuj do replikacji

Po zakończeniu odnajdywania maszyny wirtualnej w wierszu odnalezione serwery na kafelku Migracja serwera zostanie wyświetlona liczba maszyn wirtualnych VMware odnalezionych przez urządzenie.

Aby można było rozpocząć replikację maszyn wirtualnych, należy przygotować infrastrukturę replikacji.

1. Utwórz wystąpienie Service Bus w regionie docelowym. Service Bus jest używany przez lokalne urządzenie Azure Migrate do komunikowania się z usługą migracji serwera w celu koordynowania replikacji i migracji.
2. Utwórz konto magazynu na potrzeby transferu dzienników operacji z replikacji.
3. Utwórz konto magazynu, do którego urządzenie Azure Migrate przekazuje dane replikacji.
4. Utwórz Key Vault i skonfiguruj Key Vault do zarządzania tokenami sygnatury dostępu współdzielonego dla dostępu do obiektów BLOB na kontach magazynu utworzonych w kroku 3 i 4.
5. Wygeneruj token sygnatury dostępu współdzielonego dla magistrali usług utworzonej w kroku 1 i Utwórz wpis tajny dla tokenu w Key Vault utworzonym w poprzednim kroku.
6. Utwórz zasady dostępu Key Vault, aby zapewnić lokalne urządzenie Azure Migrate (za pomocą aplikacji usługi AAD) i usługę migracji serwera dostęp do Key Vault.
7. Utwórz zasady replikacji i skonfiguruj usługę migracji serwera ze szczegółami infrastruktury replikacji utworzonej w poprzednim kroku.

Infrastruktura replikacji musi być utworzona w docelowym regionie platformy Azure na potrzeby migracji i w docelowej subskrypcji platformy Azure, do której są migrowane maszyny wirtualne.

Środowisko portalu migracji serwera upraszcza Przygotowanie infrastruktury replikacji przez automatyczne wykonanie tej czynności podczas replikowania maszyny wirtualnej po raz pierwszy w projekcie. W tym artykule przyjęto założenie, że jedna lub więcej maszyn wirtualnych została już zreplikowana przy użyciu środowiska portalu i że infrastruktura replikacji została już utworzona. Dowiesz się, jak odnaleźć szczegóły istniejącej infrastruktury replikacji i jak używać tych szczegółów jako danych wejściowych szablonu Menedżer zasobów, który zostanie użyty do skonfigurowania replikacji z CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identyfikowanie składników infrastruktury replikacji

1. Na Azure Portal przejdź do strony grupy zasobów i wybierz grupę zasobów, w której został utworzony projekt Azure Migrate.
2. Wybierz pozycję **wdrożenia** z menu po lewej stronie i wyszukaj nazwę wdrożenia zaczynającą się od ciągu *"Microsoft. MigrateV2. VMwareV2EnableMigrate"*. Zostanie wyświetlona lista szablonów Menedżer zasobów utworzonych przez środowisko portalu, aby skonfigurować replikację dla maszyn wirtualnych w tym projekcie. Pobierzemy ten szablon i użyjesz go jako podstawy do przygotowania szablonu do replikacji za pomocą CMK.
3. Aby pobrać szablon, Wybierz dowolne wdrożenie zgodne ze wzorcem ciągu w poprzednim kroku > wybierz opcję **szablon** z menu po lewej stronie, > kliknij pozycję **Pobierz** w górnym menu. Zapisz template.jsw pliku lokalnie. Ten plik szablonu należy edytować w ostatnim kroku.

## <a name="create-a-disk-encryption-set"></a>Tworzenie zestawu szyfrowania dysków

Obiekt zestawu szyfrowania dysku mapuje Managed Disks do Key Vault, który zawiera CMK do użycia na potrzeby SSE. Aby replikować maszyny wirtualne za pomocą CMK, należy utworzyć zestaw szyfrowania dysku i przekazać go jako dane wejściowe do operacji replikacji.

Postępuj zgodnie [z poniższym przykładem](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) , aby utworzyć zestaw szyfrowania dysków przy użyciu Azure PowerShell. Upewnij się, że zestaw szyfrowania dysków został utworzony w subskrypcji docelowej, do której maszyny wirtualne są migrowane, i w docelowym regionie świadczenia usługi Azure na potrzeby migracji.

Zestaw szyfrowanie dysków można skonfigurować tak, aby szyfrowane dyski zarządzane z kluczem zarządzanym przez klienta lub w przypadku podwójnego szyfrowania zarówno kluczem zarządzanym przez klienta, jak i kluczem platformy. Aby użyć opcji podwójne szyfrowanie w spoczynku, Skonfiguruj zestaw szyfrowanie dysków zgodnie z opisem w [tym miejscu](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

W przykładzie przedstawionym poniżej zestaw szyfrowania dysków jest skonfigurowany do korzystania z klucza zarządzanego przez klienta.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Pobierz szczegóły maszyny wirtualnej VMware do migracji

W tym kroku użyjesz Azure PowerShell, aby uzyskać szczegółowe informacje o maszynie wirtualnej, która ma zostać zmigrowana. Te szczegóły będą używane do konstruowania szablonu Menedżer zasobów na potrzeby replikacji. W szczególnych przypadkach istnieją dwie właściwości zainteresowania:

- Identyfikator zasobu maszyny dla odnalezionych maszyn wirtualnych.
- Lista dysków dla maszyny wirtualnej i ich identyfikatorów dysków.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Skopiuj wartość ciągu identyfikator odpowiadającego urządzeniu Azure Migrate, za pomocą którego wykryto maszynę wirtualną. W przykładzie pokazanym powyżej identyfikator witryny to *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Skopiuj wartości identyfikatora ResourceId, nazwy i dysku dla maszyny, która ma zostać poddana migracji.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Utwórz szablon Menedżer zasobów na potrzeby replikacji

- Otwórz plik szablonu Menedżer zasobów pobrany w kroku **Identyfikowanie składników infrastruktury replikacji** w wybranym edytorze.
- Usuń wszystkie definicje zasobów z szablonu, z wyjątkiem zasobów typu *"Microsoft. RecoveryServices/magazyns/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Jeśli istnieje wiele definicji zasobów powyższego typu, Usuń wszystkie oprócz jednego. Usuń wszystkie definicje właściwości **dependsOn** z definicji zasobu.
- Na końcu tego kroku powinien znajdować się plik, który wygląda podobnie do poniższego przykładu i ma ten sam zestaw właściwości.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Edytuj Właściwość **name** w definicji zasobu. Zastąp ciąg, który następuje po ostatnim "/" we właściwości Name wartością *$Machine. Nazwa*(z poprzedniego kroku).
- Zmień wartość właściwości **właściwości. providerSpecificDetails. vmwareMachineId** na wartość *$Machine. ResourceId*(z poprzedniego kroku).
- Ustaw wartości dla **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** na identyfikator docelowej grupy zasobów, identyfikator docelowego zasobu sieci wirtualnej i odpowiednio nazwę podsieci docelowej.
- Ustaw wartość "WindowsServer" dla elementu **LicenseType** , aby zastosować korzyść użycia hybrydowego platformy Azure dla tej maszyny wirtualnej. Jeśli ta maszyna wirtualna nie kwalifikuje się do Korzyść użycia hybrydowego platformy Azure, ustaw wartość **LicenseType** na nolicensetype.
- Zmień wartość właściwości **targetVmName** na żądaną nazwę maszyny wirtualnej platformy Azure dla MIGROWANEJ maszyny wirtualnej.
- Opcjonalnie Dodaj właściwość o nazwie **targetVmSize** pod właściwością **targetVmName** . Ustaw wartość właściwości **targetVmSize** na żądany rozmiar maszyny wirtualnej platformy Azure dla MIGROWANEj maszyny wirtualnej.
- Właściwość **disksToInclude** jest listą danych wejściowych dysku do replikacji za pomocą poszczególnych elementów listy reprezentujących jeden dysk lokalny. Utwórz dowolną liczbę elementów listy jako liczbę dysków na lokalnej maszynie wirtualnej. Zastąp Właściwość **diskId** w elemencie list do identyfikatora UUID dysków zidentyfikowanych w poprzednim kroku. Dla opcji **isOSDisk** ustaw wartość "true" dla dysku systemu operacyjnego maszyny wirtualnej i "false" dla wszystkich innych dysków. Pozostaw właściwości **logStorageAccountId** i **logStorageAccountSasSecretName** bez zmian. Ustaw wartość typ **dysku** na dysk zarządzany platformy Azure (*Standard_LRS, Premium_LRS, StandardSSD_LRS),* który ma być używany dla tego dysku. W przypadku dysków, które mają być szyfrowane za pomocą CMK, Dodaj właściwość o nazwie **diskEncryptionSetId** i ustaw wartość na identyfikator zasobu utworzonego zestawu szyfrowania dysku (**$des. ID**) w kroku *Tworzenie zestawu szyfrowania dysku*
- Zapisz edytowany plik szablonu. W przypadku powyższego przykładu edytowany plik szablonu wygląda następująco:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Konfigurowanie replikacji

Teraz możesz wdrożyć edytowany szablon Menedżer zasobów w grupie zasobów projektu, aby skonfigurować replikację dla maszyny wirtualnej. Dowiedz się [, jak wdrażać zasoby za pomocą szablonów Azure Resource Manager i Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Następne kroki

[Monitoruj stan replikacji](tutorial-migrate-vmware.md#track-and-monitor) za pomocą środowiska portalu i Przeprowadź migrację testów i migrację.