---
title: Warstwy wydajności dla usługi Azure Managed disks
description: Dowiedz się więcej o warstwach wydajności dla dysków zarządzanych.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677316"
---
# <a name="performance-tiers-for-managed-disks"></a>Warstwy wydajności dla dysków zarządzanych

Wydajność dysku zarządzanego na platformie Azure jest ustawiana podczas tworzenia dysku w postaci jego warstwy wydajności. Warstwa wydajności określa liczbę operacji we/wy na sekundę i przepływność dysku zarządzanego. Po ustawieniu wstępnie zainicjowanego rozmiaru dysku jest automatycznie wybierana warstwa wydajności. Warstwę wydajności można zmienić podczas wdrażania lub później, bez zmiany rozmiaru dysku.

Zmiana warstwy wydajności umożliwia przygotowanie i spełnienie wyższego zapotrzebowania bez użycia możliwości tworzenia dysków na dysku. Zmiana warstwy wydajności nie polega na przeniesieniu obciążeń, w zależności od tego, jak długo wymagana jest dodatkowa wydajność. Jest to idealne rozwiązanie w przypadku zdarzeń, które tymczasowo wymagają stałego wyższego poziomu wydajności, np. zakupów świątecznych, testowania wydajności lub uruchamiania środowiska szkoleniowego. Aby obsłużyć te zdarzenia, można użyć wyższej warstwy wydajności, o ile będzie potrzebna. Następnie można wrócić do oryginalnej warstwy, gdy nie potrzebujesz już dodatkowej wydajności.

## <a name="how-it-works"></a>Jak to działa

Podczas pierwszego wdrażania lub inicjowania obsługi administracyjnej dysku podstawowa warstwa wydajności dla tego dysku jest ustawiana na podstawie rozmiaru dysku. Możesz użyć warstwy wydajności wyższej niż oryginalna linia bazowa, aby sprostać wyższym zapotrzebowaniu. Gdy poziom wydajności nie jest już potrzebny, można powrócić do początkowej warstwy wydajności.

Zmiany w rozliczeniach są zmieniane w miarę zmiany warstwy wydajności. Na przykład jeśli zainicjujesz dysk P10 (128 GiB), warstwa wydajności linii bazowej zostanie ustawiona jako P10 (500 IOPS i 100 MB/s). Zostanie naliczona stawka za P10. Warstwę można uaktualnić, aby pasowała do wydajności P50 (7 500 IOPS i 250 MB/s) bez zwiększania rozmiaru dysku. W czasie uaktualniania zostanie naliczona stawka za P50. Gdy nie potrzebujesz już wyższej wydajności, możesz powrócić do warstwy P10. Po ponownym rozliczeniu dysku zostanie naliczona stawka za P10.

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

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zmienić warstwę wydajności, zobacz artykuł [Portal](disks-performance-tiers-portal.md) lub [PowerShell/interfejsu wiersza polecenia](disks-performance-tiers.md) .

