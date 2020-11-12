---
title: Zmienianie wydajności usługi Azure Managed disks
description: Dowiedz się więcej o warstwach wydajności dla dysków zarządzanych i Dowiedz się, jak zmienić warstwy wydajności dla istniejących dysków zarządzanych przy użyciu modułu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 923c5970183bd192ac1a2f20fb775d96dcc06865
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540641"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Warstwy wydajności dla dysków zarządzanych (wersja zapoznawcza)

Azure Disk Storage oferuje wbudowaną funkcję tworzenia seryjnego w celu zapewnienia wyższej wydajności do obsługi nieoczekiwanego ruchu. Dysków SSD Premium zapewnia elastyczność zwiększania wydajności dysku bez zwiększania rzeczywistego rozmiaru dysku. Ta funkcja umożliwia dopasowanie wydajności obciążeń i obniżenie kosztów. 

> [!NOTE]
> Ta funkcja jest obecnie w wersji zapoznawczej. 

Ta funkcja jest idealnym rozwiązaniem w przypadku zdarzeń, które tymczasowo wymagają stałego wyższego poziomu wydajności, takich jak zakupy wolne, testowanie wydajności lub Uruchamianie środowiska szkoleniowego. Aby obsłużyć te zdarzenia, można użyć wyższej warstwy wydajności, o ile będzie potrzebna. Następnie można wrócić do oryginalnej warstwy, gdy nie potrzebujesz już dodatkowej wydajności.

## <a name="how-it-works"></a>Jak to działa

Podczas pierwszego wdrażania lub inicjowania obsługi administracyjnej dysku podstawowa warstwa wydajności dla tego dysku jest ustawiana na podstawie rozmiaru dysku. Możesz użyć wyższej warstwy wydajności, aby sprostać wyższym zapotrzebowaniu. Gdy poziom wydajności nie jest już potrzebny, można powrócić do początkowej warstwy wydajności.

Zmiany w rozliczeniach są zmieniane w miarę zmiany warstwy. Na przykład jeśli zainicjujesz dysk P10 (128 GiB), warstwa wydajności linii bazowej zostanie ustawiona jako P10 (500 IOPS i 100 MB/s). Zostanie naliczona stawka za P10. Warstwę można uaktualnić, aby pasowała do wydajności P50 (7 500 IOPS i 250 MB/s) bez zwiększania rozmiaru dysku. W czasie uaktualniania zostanie naliczona stawka za P50. Gdy nie potrzebujesz już wyższej wydajności, możesz powrócić do warstwy P10. Po ponownym rozliczeniu dysku zostanie naliczona stawka za P10.

| Rozmiar dysku | Warstwa wydajności linii bazowej | Można uaktualnić do programu |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Brak |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | Brak |

Aby uzyskać informacje dotyczące rozliczeń, zobacz [Cennik dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Ograniczenia

- Ta funkcja jest obecnie obsługiwana tylko w przypadku wersji Premium dysków SSD.
- Aby można było zmienić warstwę dysku, należy cofnąć przydział maszyny wirtualnej lub odłączyć dysk od uruchomionej maszyny wirtualnej.
- Użycie warstw wydajności P60, P70 i P80 jest ograniczone do dysków 4 096 GiB lub wyższych.
- Warstwę wydajności dysku można zmienić na starszą wersję co 24 godziny.

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Utwórz pusty dysk danych o warstwie wyższej niż warstwa bazowa

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Tworzenie dysku systemu operacyjnego z warstwą wyższą niż warstwa bazowa z obrazu portalu Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$subscriptionId='yourSubscriptionID'
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$diskSizeInGiB=4
$performanceTier='P50'
$sku='Premium_LRS'
$region='westcentralus'

Connect-AzAccount

Set-AzContext -Subscription $subscriptionId

$diskConfig = New-AzDiskConfig -SkuName $sku -Location $region -CreateOption Empty -DiskSizeGB $diskSizeInGiB -Tier $performanceTier
New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
```
---

## <a name="update-the-tier-of-a-disk"></a>Aktualizowanie warstwy dysku

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName='yourResourceGroupName'
$diskName='yourDiskName'
$performanceTier='P1'

$diskUpdateConfig = New-AzDiskUpdateConfig -Tier $performanceTier

Update-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName -DiskUpdate $diskUpdateConfig
```
---

## <a name="show-the-tier-of-a-disk"></a>Pokaż warstwę dysku

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName

$disk.Tier
```
---

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz zmienić rozmiar dysku, aby wykorzystać wyższe warstwy wydajności, zobacz następujące artykuły:

- [Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](linux/expand-disks.md)
- [Rozszerzanie dysku zarządzanego dołączonego do maszyny wirtualnej z systemem Windows](windows/expand-os-disk.md)
