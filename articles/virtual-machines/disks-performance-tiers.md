---
title: Zwiększ wydajność dysku zarządzanego przez platformę Azure
description: Informacje o warstwach wydajności dla dysków zarządzanych oraz o sposobie uaktualniania warstw wydajności dla dysków zarządzanych.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4ad0ce1aef45dc4126d3ce17c7093b885f1b8bd7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938016"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Warstwy wydajności dla dysków zarządzanych (wersja zapoznawcza)

Azure Disk Storage obecnie oferuje wbudowaną funkcję tworzenia serii, aby osiągnąć wyższą wydajność w przypadku obsługi nieoczekiwanego ruchu. Dysków SSD Premium zapewnia elastyczność zwiększania wydajności dysku bez zwiększania rzeczywistego rozmiaru dysku, co pozwala dopasować się do potrzeb związanych z wydajnością obciążeń i obniżyć koszty. Jest to idealne rozwiązanie w przypadku zdarzeń, które tymczasowo wymagają stałego wyższego poziomu wydajności, na przykład zakupów świątecznych, testowania wydajności lub uruchamiania środowiska szkoleniowego. Aby obsłużyć te zdarzenia, można wybrać wyższą warstwę wydajności, tak długo, jak to konieczne, i wrócić do oryginalnej warstwy, gdy dodatkowa wydajność nie jest już potrzebna.

## <a name="how-it-works"></a>Jak to działa

Podczas pierwszego wdrażania lub inicjowania obsługi administracyjnej dysku podstawowa warstwa wydajności dla tego dysku jest ustawiana na podstawie rozmiaru dysku. Można wybrać wyższą warstwę wydajności, aby spełnić wyższe zapotrzebowanie i, gdy ta wydajność nie jest już wymagana, można powrócić do początkowej warstwy wydajności. Na przykład jeśli zainicjujesz dysk P10 (128 GiB), warstwa wydajności linii bazowej zostanie ustawiona jako P10 (500 IOPS i 100 MB/s). Warstwę można zaktualizować tak, aby odpowiadała wydajności P50 (liczba IOPS 7500 i 250 MB/s) bez zwiększania rozmiaru dysku i powrotu do P10, gdy wyższa wydajność nie jest już wymagana.

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

## <a name="restrictions"></a>Ograniczenia

- Obecnie obsługiwane tylko w przypadku wersji Premium dysków SSD.
- Przed zmianą warstw należy odłączyć dyski od uruchomionej maszyny wirtualnej.
- Użycie warstw wydajności P60, P70 i P80 jest ograniczone do dysków o 4096 GiB lub większych.

## <a name="regional-availability"></a>Dostępność regionalna

Dostosowanie warstwy wydajności dysku zarządzanego jest obecnie dostępne tylko dla dysków SSD Premium w następujących regionach:

- Zachodnio-środkowe stany USA 
- Wschodnie stany USA 2 
- Europa Zachodnia
- Australia Wschodnia 
- Australia Południowo-Wschodnia 
- Indie Południowe

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Utwórz/zaktualizuj dysk danych o warstwie wyższej niż warstwa bazowa

1. Utwórz pusty dysk danych o warstwie wyższej niż warstwa bazowa lub zaktualizuj warstwę dysku wyższą niż warstwa bazowa przy użyciu przykładowego szablonu [CreateUpdateDataDiskWithTier.jsna](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Potwierdź warstwę dysku

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Utwórz/zaktualizuj dysk systemu operacyjnego za pomocą warstwy wyższej niż warstwa podstawowa

1. Utwórz dysk systemu operacyjnego na podstawie obrazu z witryny Marketplace lub zaktualizuj warstwę dysku systemu operacyjnego wyższego niż warstwa bazowa przy użyciu przykładowego szablonu [CreateUpdateOSDiskWithTier.jsna](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Potwierdź warstwę dysku
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Następne kroki

Jeśli musisz zmienić rozmiar dysku, aby móc korzystać z większych warstw wydajności, zobacz nasze artykuły w temacie:

- [Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](linux/expand-disks.md)
- [Rozszerzanie dysku zarządzanego dołączonego do maszyny wirtualnej z systemem Windows](windows/expand-os-disk.md)