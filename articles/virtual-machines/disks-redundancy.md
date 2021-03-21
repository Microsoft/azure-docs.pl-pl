---
title: Opcje nadmiarowości dla usługi Azure Managed disks
description: Informacje na temat magazynu Strefowo nadmiarowego i lokalnie nadmiarowego magazynu dla usługi Azure Managed Disks.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043647"
---
# <a name="redundancy-options-for-managed-disks"></a>Opcje nadmiarowości dla dysków zarządzanych

Usługa Azure Managed disks oferuje dwie opcje nadmiarowości magazynu, magazyn strefowo nadmiarowy (ZRS) jako wersja zapoznawcza i Magazyn lokalnie nadmiarowy. Usługa ZRS zapewnia wyższą dostępność dla dysków zarządzanych niż magazyn lokalnie nadmiarowy (LRS). Jednak opóźnienie zapisu dla dysków LRS jest lepsze niż dyski ZRS, ponieważ dyski LRS synchronicznie zapisują dane do trzech kopii w jednym centrum danych.

## <a name="locally-redundant-storage-for-managed-disks"></a>Magazyn lokalnie nadmiarowy dla dysków zarządzanych

Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy w jednym centrum danych w wybranym regionie. LRS chroni dane przed awarią serwera i uszkodzeniem dysku. 

Istnieje kilka sposobów na ochronę aplikacji przy użyciu dysków LRS z całego błędu strefy, który może wystąpić z powodu klęski żywiołowej lub problemów ze sprzętem:
- Użyj aplikacji, takiej jak SQL Server AlwaysOn, która może synchronicznie zapisywać dane w dwóch strefach i automatycznie przełączeć w tryb failover do innej strefy podczas awarii.
- Twórz często wykonywane kopie zapasowe dysków LRS za pomocą migawek ZRS.
- Włącz odzyskiwanie po awarii między strefami dla dysków LRS za pośrednictwem [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Jednak odzyskiwanie awaryjne między strefami nie zapewnia zerowego celu punktu odzyskiwania.

Jeśli przepływ pracy nie obsługuje synchronicznych zapisów na poziomie aplikacji w różnych strefach lub aplikacja musi spełniać zerowy cel punktu odzyskiwania, dyski ZRS są idealnym rozwiązaniem.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Magazyn strefowo nadmiarowy dla dysków zarządzanych (wersja zapoznawcza)

Magazyn strefowo nadmiarowy (ZRS) replikuje dysk zarządzany platformy Azure synchronicznie w trzech strefach dostępności platformy Azure w wybranym regionie. Każda strefa dostępności jest oddzielną lokalizacją fizyczną z niezależnym zasilaniem, chłodzeniem i siecią. 

Dyski ZRS umożliwiają odzyskiwanie z błędów w strefach dostępności. W przypadku przełączenia całej strefy dysk ZRS może zostać dołączony do maszyny wirtualnej w innej strefie. Dysków ZRS można także używać jako dysku udostępnionego, aby zapewnić lepszą dostępność aplikacji klastrowanych lub rozproszonych, takich jak SQL FCI, SAP ASCS/SCS lub GFS2. Udostępniony dysk ZRS można dołączyć do podstawowych i dodatkowych maszyn wirtualnych w różnych strefach, aby korzystać z obu ZRS i [strefy dostępności](../availability-zones/az-overview.md). Jeśli Twoja strefa podstawowa ulegnie awarii, możesz szybko przejść w tryb failover na pomocniczą maszynę wirtualną, używając [trwałej rezerwacji interfejsu SCSI](disks-shared-enable.md#supported-scsi-pr-commands).

### <a name="limitations"></a>Ograniczenia

W wersji zapoznawczej ZRS dla dysków zarządzanych mają następujące ograniczenia:

- Obsługiwane tylko w przypadku dysków SSD w warstwie Premium (SSD) i Standardowa dysków SSD.
- Obecnie dostępne tylko w regionie EastUS2EUAP.
- Dyski ZRS można tworzyć tylko za pomocą szablonów Azure Resource Manager przy użyciu `2020-12-01` interfejsu API.

Utwórz konto w wersji zapoznawczej [tutaj](https://aka.ms/ZRSDisksPreviewSignUp).

### <a name="billing-implications"></a>Implikacje rozliczeń

Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem platformy Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="comparison-with-other-disk-types"></a>Porównanie z innymi typami dysków

Z wyjątkiem opóźnień zapisu dyski używające ZRS są identyczne z dyskami przy użyciu LRS. Mają one te same cele wydajności.

### <a name="create-zrs-managed-disks"></a>Tworzenie dysków zarządzanych ZRS

Użyj `2020-12-01` interfejsu API z szablonem Azure Resource Manager, aby utworzyć dysk ZRS.

#### <a name="create-a-vm-with-zrs-disks"></a>Tworzenie maszyny wirtualnej przy użyciu dysków ZRS

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Tworzenie maszyn wirtualnych z udostępnionym dyskiem ZRS podłączonym do maszyn wirtualnych w różnych strefach

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu dysków ZRS

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>Następne kroki

- Użyj tych przykładowych [szablonów Azure Resource Manager, aby utworzyć maszynę wirtualną z dyskami ZRS](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).