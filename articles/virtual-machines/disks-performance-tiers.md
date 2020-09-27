---
title: Zmienianie wydajności usługi Azure Managed disks
description: Informacje o warstwach wydajności dla dysków zarządzanych oraz o sposobie zmieniania warstw wydajności dla istniejących dysków zarządzanych.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396590"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Warstwy wydajności dla dysków zarządzanych (wersja zapoznawcza)

Azure Disk Storage obecnie oferuje wbudowaną funkcję tworzenia serii, aby osiągnąć wyższą wydajność w przypadku obsługi nieoczekiwanego ruchu. Funkcja Premium dysków SSD zapewnia elastyczność zwiększania wydajności dysku bez zwiększania rzeczywistego rozmiaru dysku, co pozwala dopasować się do potrzeb związanych z wydajnością obciążeń i obniżyć koszty, ponieważ jest ona obecnie dostępna w wersji zapoznawczej. Jest to idealne rozwiązanie w przypadku zdarzeń, które tymczasowo wymagają stałego wyższego poziomu wydajności, na przykład zakupów świątecznych, testowania wydajności lub uruchamiania środowiska szkoleniowego. Aby obsłużyć te zdarzenia, można wybrać wyższą warstwę wydajności, tak długo, jak to konieczne, i wrócić do oryginalnej warstwy, gdy dodatkowa wydajność nie jest już potrzebna.

## <a name="how-it-works"></a>Jak to działa

Podczas pierwszego wdrażania lub inicjowania obsługi administracyjnej dysku podstawowa warstwa wydajności dla tego dysku jest ustawiana na podstawie rozmiaru dysku. Można wybrać wyższą warstwę wydajności, aby spełnić wyższe zapotrzebowanie i, gdy ta wydajność nie jest już wymagana, można powrócić do początkowej warstwy wydajności.

Zmiany w rozliczeniach są zmieniane w miarę zmiany warstwy. Na przykład jeśli zainicjujesz dysk P10 (128 GiB), warstwa wydajności linii bazowej zostanie ustawiona jako P10 (500 IOPS i 100 MB/s) i zostanie naliczona stawka za P10. Możesz zaktualizować warstwę, aby odpowiadała wydajności P50 (liczba IOPS 7500 i 250 MB/s) bez zwiększania rozmiaru dysku, w tym czasie będzie naliczana stawka za P50. Gdy wyższa wydajność nie jest już potrzebna, można powrócić do warstwy P10, a dysk zostanie ponownie rozliczony według stawki P10.

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

- Obecnie obsługiwane tylko w przypadku wersji Premium dysków SSD.
- Przed zmianą warstw należy odłączyć dyski od uruchomionej maszyny wirtualnej.
- Użycie warstw wydajności P60, P70 i P80 jest ograniczone do dysków o 4096 GiB lub większych.
- Warstwę wydajności dysków można zmienić tylko raz na 24 godziny.

## <a name="regional-availability"></a>Dostępność regionalna

Dostosowanie warstwy wydajności dysku zarządzanego jest obecnie dostępne tylko dla dysków SSD Premium w następujących regionach:

- Zachodnio-środkowe stany USA 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Utwórz pusty dysk danych o warstwie wyższej niż warstwa bazowa

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
     
## <a name="update-the-tier-of-a-disk"></a>Aktualizowanie warstwy dysku

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Pokaż warstwę dysku

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Następne kroki

Jeśli musisz zmienić rozmiar dysku, aby móc korzystać z większych warstw wydajności, zobacz nasze artykuły w temacie:

- [Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](linux/expand-disks.md)
- [Rozszerzanie dysku zarządzanego dołączonego do maszyny wirtualnej z systemem Windows](windows/expand-os-disk.md)
