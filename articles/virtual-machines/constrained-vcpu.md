---
title: Ograniczone rozmiary vCPU
description: Wyświetla listę rozmiarów maszyn wirtualnych, które mogą mieć ograniczoną liczbę vCPU.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557084"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Ograniczone rozmiary maszyn wirtualnych z ograniczeniami vCPU

Niektóre obciążenia baz danych, takie jak SQL Server lub Oracle, wymagają dużej ilości pamięci, magazynu i przepustowości we/wy, ale nie dużej liczby rdzeni. Wiele obciążeń związanych z bazami danych nie jest intensywnie obciążających procesora CPU. System Azure oferuje pewne rozmiary maszyn wirtualnych, w których można ograniczyć liczbę vCPU maszyny wirtualnej w celu zmniejszenia kosztu licencjonowania oprogramowania przy zachowaniu tej samej pojemności pamięci, magazynu i przepustowości we/wy.

Liczba vCPU może być ograniczona do jednej połowy lub jednego kwartału oryginalnego rozmiaru maszyny wirtualnej. Te nowe rozmiary maszyn wirtualnych mają sufiks określający liczbę aktywnych procesorów wirtualnych vCPU, które ułatwiają identyfikację.

Na przykład bieżący rozmiar maszyny wirtualnej Standard_GS5 obejmuje 32 procesorów wirtualnych vCPU, 448 GB pamięci RAM, 64 dysków (do 256 TB) 80 000 i liczby operacji wejścia/wyjścia na sekundę lub 2 GB/s. Nowe rozmiary maszyn wirtualnych Standard_GS5-16 i Standard_GS5-8 są dostarczane z 16 i 8 aktywnymi procesorów wirtualnych vCPU, przy jednoczesnym zachowaniu pozostałej części specyfikacji Standard_GS5 dla pamięci, magazynu i przepustowości we/wy.

Opłaty licencyjne za SQL Server lub Oracle są ograniczone do nowej liczby vCPU, a inne produkty powinny być obciążane w oparciu o nową liczbę vCPU. Powoduje to 50% do 75% wzrostu w stosunku do specyfikacji maszyn wirtualnych do aktywnych (rozliczanych) procesorów wirtualnych vCPU. Te nowe rozmiary maszyn wirtualnych umożliwiają klientom korzystanie z tej samej pamięci, magazynu i przepustowości we/wy podczas optymalizowania kosztu licencjonowania oprogramowania. W tej chwili koszt obliczeń, który obejmuje Licencjonowanie systemu operacyjnego, pozostaje taki sam jak rozmiar oryginalny. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych platformy Azure w celu uzyskania bardziej ekonomicznych obciążeń związanych z bazami danych](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Nazwa                | Procesor wirtualny | Specyfikacje           |
|---------------------|------|-----------------|
| Standard_M8 — 2 MS     | 2    | Analogicznie jak M8ms    |
| Standard_M8 — 4 MS     | 4    | Analogicznie jak M8ms    |
| Standard_M16 — 4 MS    | 4    | Analogicznie jak M16ms   |
| Standard_M16 — 8ms    | 8    | Analogicznie jak M16ms   |
| Standard_M32 — 8ms    | 8    | Analogicznie jak M32ms   |
| Standard_M32 — 16ms   | 16   | Analogicznie jak M32ms   |
| Standard_M64 — 32ms   | 32   | Analogicznie jak M64ms   |
| Standard_M64 — 16ms   | 16   | Analogicznie jak M64ms   |
| Standard_M128 — 64ms  | 64   | Analogicznie jak M128ms  |
| Standard_M128 — 32ms  | 32   | Analogicznie jak M128ms  |
| Standard_E4 — 2s_v3   | 2    | Taki sam jak E4s_v3  |
| Standard_E8 — 4s_v3   | 4    | Taki sam jak E8s_v3  |
| Standard_E8 — 2s_v3   | 2    | Taki sam jak E8s_v3  |
| Standard_E16 — 8s_v3  | 8    | Taki sam jak E16s_v3 |
| Standard_E16 — 4s_v3  | 4    | Taki sam jak E16s_v3 |
| Standard_E32 — 16s_v3 | 16   | Taki sam jak E32s_v3 |
| Standard_E32 — 8s_v3  | 8    | Taki sam jak E32s_v3 |
| Standard_E64 — 32s_v3 | 32   | Taki sam jak E64s_v3 |
| Standard_E64 — 16s_v3 | 16   | Taki sam jak E64s_v3 |
| Standard_E4 — 2s_v4   | 2    | Taki sam jak E4s_v4  |
| Standard_E8 — 4s_v4   | 4    | Taki sam jak E8s_v4  |
| Standard_E8 — 2s_v4   | 2    | Taki sam jak E8s_v4  |
| Standard_E16 — 8s_v4  | 8    | Taki sam jak E16s_v4 |
| Standard_E16 — 4s_v4  | 4    | Taki sam jak E16s_v4 |
| Standard_E32 — 16s_v4 | 16   | Taki sam jak E32s_v4 |
| Standard_E32 — 8s_v4  | 8    | Taki sam jak E32s_v4 |
| Standard_E64 — 32s_v4 | 32   | Taki sam jak E64s_v4 |
| Standard_E64 — 16s_v4 | 16   | Taki sam jak E64s_v4 |
| Standard_E4 — 2ds_v4  | 2    | Taki sam jak E4ds_v4 |
| Standard_E8 — 4ds_v4  | 4    | Taki sam jak E8ds_v4 |
| Standard_E8 — 2ds_v4  | 2    | Taki sam jak E8ds_v4 |
| Standard_E16 — 8ds_v4 | 8    | Taki sam jak E16ds_v4|
| Standard_E16 — 4ds_v4 | 4    | Taki sam jak E16ds_v4|
| Standard_E32 — 16ds_v4| 16   | Taki sam jak E32ds_v4|
| Standard_E32 — 8ds_v4 | 8    | Taki sam jak E32ds_v4|
| Standard_E64 — 32ds_v4| 32   | Taki sam jak E64ds_v4|
| Standard_E64 — 16ds_v4| 16   | Taki sam jak E64ds_v4|
| Standard_E4 — 2as_v4  | 2    | Taki sam jak E4as_v4 |
| Standard_E8 — 4as_v4  | 4    | Taki sam jak E8as_v4 |
| Standard_E8 — 2as_v4  | 2    | Taki sam jak E8as_v4 |
| Standard_E16 — 8as_v4 | 8    | Taki sam jak E16as_v4|
| Standard_E16 — 4as_v4 | 4    | Taki sam jak E16as_v4|
| Standard_E32 — 16as_v4| 16   | Taki sam jak E32as_v4|
| Standard_E32 — 8as_v4 | 8    | Taki sam jak E32as_v4|
| Standard_E64 — 32as_v4| 32   | Taki sam jak E64as_v4|
| Standard_E64 — 16as_v4| 16   | Taki sam jak E64as_v4|
| Standard_E96 — 48as_v4| 48   | Taki sam jak E96as_v4|
| Standard_E96 — 24as_v4| 24   | Taki sam jak E96as_v4|
| Standard_GS4-8      | 8    | Analogicznie jak GS4     |
| Standard_GS4-4      | 4    | Analogicznie jak GS4     |
| Standard_GS5-16     | 16   | Analogicznie jak GS5     |
| Standardowa_GS5-8      | 8    | Analogicznie jak GS5     |
| Standard_DS11 — 1_v2  | 1    | Taki sam jak DS11_v2 |
| Standard_DS12 — 2_v2  | 2    | Taki sam jak DS12_v2 |
| Standard_DS12 — 1_v2  | 1    | Taki sam jak DS12_v2 |
| Standard_DS13 — 4_v2  | 4    | Taki sam jak DS13_v2 |
| Standard_DS13 — 2_v2  | 2    | Taki sam jak DS13_v2 |
| Standard_DS14 — 8_v2  | 8    | Taki sam jak DS14_v2 |
| Standard_DS14 — 4_v2  | 4    | Taki sam jak DS14_v2 |
| Standard_M416 — 208s_v2 | 208    | Taki sam jak M416s_v2|
| Standard_M416 — 208ms_v2 | 208    | Taki sam jak M416ms_v2 |

## <a name="other-sizes"></a>Inne rozmiary
- [Optymalizacja pod kątem obliczeń](./sizes-compute.md)
- [Optymalizacja pod kątem pamięci](./sizes-memory.md)
- [Optymalizacja pod kątem magazynu](./sizes-storage.md)
- [Procesor GPU](./sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](./sizes-hpc.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](./acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
