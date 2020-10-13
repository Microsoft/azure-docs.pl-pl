---
title: Włącz dyski udostępnione dla usługi Azure Managed disks
description: Skonfiguruj dysk zarządzany przez platformę Azure z dyskami udostępnionymi, aby umożliwić udostępnianie go na wielu maszynach wirtualnych
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 82d482cd0a1b3a5b6028b9249030b7b1a093d22d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570545"
---
# <a name="enable-shared-disk"></a>Włączanie dysku udostępnionego

W tym artykule opisano sposób włączania funkcji dysków udostępnionych dla usługi Azure Managed Disks. Azure Shared disks to nowa funkcja dysków zarządzanych przez platformę Azure, która umożliwia równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure. 

Jeśli szukasz informacji koncepcyjnych dotyczących dysków zarządzanych z włączonymi dyskami udostępnionymi, zapoznaj się z tematem:

* System Linux: [udostępnione dyski platformy Azure](linux/disks-shared.md)

* Dla systemu Windows: [dyski udostępnione platformy Azure](windows/disks-shared.md)

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Dyski udostępnione obsługują kilka systemów operacyjnych. Zapoznaj się z sekcjami [systemu Windows](windows/disks-shared.md#windows) i [Linux](linux/disks-shared.md#linux) artykułu koncepcyjnego dla obsługiwanych systemów operacyjnych.

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Wdrażanie dysków udostępnionych

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Wdrażanie dysków SSD Premium jako dysku udostępnionego

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, użyj nowej właściwości `maxShares` i Zdefiniuj wartość większą od 1. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości dla `maxShares` .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Szablon Menedżer zasobów](#tab/azure-resource-manager)

Przed użyciem następującego szablonu Zastąp `[parameters('dataDiskName')]` wartości, `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` i `[parameters('maxShares')]` własnymi wartościami.

[Szablon udostępnionego dysku SSD w warstwie Premium](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Wdróż dysk typu Ultra jako dysk udostępniony

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, należy zmienić `maxShares` parametr na wartość większą niż 1. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości dla `maxShares` .


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Przykład dysku regionalnego

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Przykład dysku zona

Ten przykład jest niemal taki sam jak poprzedni, z wyjątkiem tego, że tworzy dysk w strefie dostępności 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Przykład dysku regionalnego

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Przykład dysku zona

Ten przykład jest niemal taki sam jak poprzedni, z wyjątkiem tego, że tworzy dysk w strefie dostępności 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Szablon Menedżer zasobów](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Przykład dysku regionalnego

Przed użyciem następującego szablonu Zastąp wartości,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` i `[parameters('diskMBpsReadOnly')]` własnymi wartościami.

[Szablon regionalnej udostępnionej dyskietki](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Przykład dysku zona

Przed użyciem następującego szablonu Zastąp wartości,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` i `[parameters('diskMBpsReadOnly')]` własnymi wartościami.

[Szablon współużytkowanych dysków Ultra disks](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Używanie dysków udostępnionych platformy Azure z maszynami wirtualnymi

Po wdrożeniu udostępnionego dysku za pomocą programu możesz `maxShares>1` zainstalować dysk na co najmniej jednej z maszyn wirtualnych.

> [!NOTE]
> W przypadku wdrażania programu Ultra Disk upewnij się, że jest on zgodny z wymaganymi wymaganiami. Aby uzyskać szczegółowe informacje, zobacz [Korzystanie z usługi Azure Ultra disks](disks-enable-ultra-ssd.md) .

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Obsługiwane polecenia SCSI PR

Po zainstalowaniu dysku udostępnionego na maszynach wirtualnych w klastrze można ustanowić kworum i odczyt/zapis na dysku przy użyciu żądania ściągnięcia SCSI. Następujące polecenia żądania ściągnięcia są dostępne w przypadku korzystania z dysków udostępnionych platformy Azure:

Aby współdziałać z dyskiem, należy zacząć od listy Akcja zastrzeżenia trwałego:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

W przypadku korzystania z PR_RESERVE, PR_PREEMPT_RESERVATION lub PR_RELEASE_RESERVATION Podaj jeden z następujących typów trwałych:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

W przypadku korzystania z PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION lub PR_RELEASE-rezerwacji należy również podać klucz trwały.


## <a name="next-steps"></a>Następne kroki

Jeśli wolisz używać szablonów Azure Resource Manager do wdrożenia dysku, dostępne są następujące przykładowe szablony:
- [Dysk SSD w warstwie Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Regionalne Ultra disks](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [W strefach Ultra disks](https://aka.ms/SharedUltraDiskARMtemplateZonal)