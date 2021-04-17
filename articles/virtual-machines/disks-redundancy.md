---
title: Opcje nadmiarowości dla dysków zarządzanych platformy Azure
description: Dowiedz się więcej na temat magazynu strefowo nadmiarowego i magazynu lokalnie nadmiarowego dla dysków zarządzanych platformy Azure.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588092"
---
# <a name="redundancy-options-for-managed-disks"></a>Opcje nadmiarowości dla dysków zarządzanych

Dyski zarządzane platformy Azure oferują dwie opcje nadmiarowości magazynu: magazyn strefowo nadmiarowy (ZRS) jako wersja zapoznawcza i magazyn lokalnie nadmiarowy. Magazyn ZRS zapewnia wyższą dostępność dysków zarządzanych niż magazyn lokalnie nadmiarowy (LRS). Jednak opóźnienie zapisu dla dysków LRS jest lepsze niż dyski ZRS, ponieważ dyski LRS synchronicznie zapisują dane do trzech kopii w jednym centrum danych.

## <a name="locally-redundant-storage-for-managed-disks"></a>Magazyn lokalnie nadmiarowy dla dysków zarządzanych

Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy w jednym centrum danych w wybranym regionie. Magazyn LRS chroni dane przed awariami stojaków i dysków serwera. 

Istnieje kilka sposobów ochrony aplikacji przy użyciu dysków LRS przed awarią całej strefy, która może wystąpić z powodu klęski żywiołowej lub problemów ze sprzętem:
- Użyj aplikacji, SQL Server AlwaysOn, która może synchronicznie zapisywać dane w dwóch strefach i automatycznie przejść w tryb failover do innej strefy podczas awarii.
- Częste tworzenie kopii zapasowych dysków LRS za pomocą migawek magazynu ZRS.
- Włącz odzyskiwanie po awarii między strefami dla dysków LRS za [pośrednictwem Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md). Jednak odzyskiwanie po awarii między strefami nie zapewnia zerowego celu punktu odzyskiwania (RPO, Recovery Point Objective).

Jeśli przepływ pracy nie obsługuje synchronicznych operacji zapisu na poziomie aplikacji w strefach lub aplikacja musi spełniać zerowy celu punktu odzyskiwania, dyski ZRS będą idealne.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>Magazyn strefowo nadmiarowy dla dysków zarządzanych (wersja zapoznawcza)

Magazyn strefowo nadmiarowy (ZRS) replikuje dysk zarządzany platformy Azure synchronicznie w trzech strefach dostępności platformy Azure w wybranym regionie. Każda strefa dostępności jest oddzielną lokalizacją fizyczną z niezależnym zasilaniem, chłodzeniem i siecią. 

Dyski ZRS umożliwiają odzyskiwanie po awarii w strefach dostępności. W przypadku awarii całej strefy dysk ZRS można dołączyć do maszyny wirtualnej w innej strefie. Możesz również użyć dysków ZRS jako dysku udostępnionego, aby zapewnić lepszą dostępność dla aplikacji klastrowanych lub rozproszonych, takich jak SQL FCI, SAP ASCS/SCS lub GFS2. Udostępniony dysk ZRS można dołączyć do podstawowej i pomocniczej maszyny wirtualnej w różnych strefach, aby korzystać z zalet zarówno ZRS, [jak i Strefy dostępności.](../availability-zones/az-overview.md) Jeśli strefa podstawowa ulegnie awarii, możesz szybko przejść do trybu fail over dodatkowej maszyny wirtualnej przy użyciu [trwałej rezerwacji SCSI.](disks-shared-enable.md#supported-scsi-pr-commands)

### <a name="limitations"></a>Ograniczenia

W wersji zapoznawczej usługi ZRS dla dysków zarządzanych mają następujące ograniczenia:

- Obsługiwane tylko w przypadku dysków półprzewodnikowych (SSD) i dysków SSD w warstwie Standardowa w warstwie Premium.
- Obecnie dostępne tylko w regionie EastUS2EUAP.
- Dyski ZRS można tworzyć tylko za pomocą Azure Resource Manager szablonów przy użyciu interfejsu `2020-12-01` API.

Zarejestruj się, aby uzyskać podgląd [tutaj.](https://aka.ms/ZRSDisksPreviewSignUp)

### <a name="billing-implications"></a>Implikacje dotyczące rozliczeń

Aby uzyskać szczegółowe informacje, zobacz [stronę cennika platformy Azure.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="comparison-with-other-disk-types"></a>Porównanie z innymi typami dysków

Z wyjątkiem większej opóźnienia zapisu dyski korzystające z magazynu ZRS są identyczne z dyskami korzystającymi z magazynu LRS. Mają one te same cele wydajności. Zalecamy przeprowadzenie testów [porównawczych](disks-benchmarks.md) dysków w celu symulowania obciążenia aplikacji w celu porównania opóźnienia między dyskami LRS i ZRS. 

### <a name="create-zrs-managed-disks"></a>Tworzenie dysków zarządzanych magazynu ZRS

Użyj `2020-12-01` interfejsu API z szablonem Azure Resource Manager, aby utworzyć dysk ZRS.

#### <a name="create-a-vm-with-zrs-disks"></a>Tworzenie maszyny wirtualnej z dyskami ZRS

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

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Tworzenie maszyn wirtualnych z udostępnionym dyskiem ZRS dołączonym do maszyn wirtualnych w różnych strefach

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

- Użyj tych przykładowych [szablonów Azure Resource Manager, aby utworzyć maszynę wirtualną z dyskami ZRS.](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)
