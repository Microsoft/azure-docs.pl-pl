---
title: Seria DC-Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii DC.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: fccdaaab121f315f1cb98b2b6ee3bffcb9ac2b3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653013"
---
# <a name="dcsv2-series"></a>Seria DCsv2


Seria DCsv2 może pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny są obsługiwane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii zwiększania procesora Intel Turbo te maszyny mogą mieć wartość do 5,0 GHz. Wystąpienia serii DCsv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawy w celu ochrony kodu i danych, gdy jest używany.

Przykładowe przypadki użycia obejmują: poufne udostępnianie danych wieloczęściowych, wykrywanie oszustw, pranie antypieniężne, łańcucha bloków, poufne analizy użycia, analizę analizy oraz poufne Uczenie maszynowe.

[Premium Storage](premium-storage-performance.md): obsługiwane *<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
[Obsługa generacji maszyny wirtualnej](generation-2.md): generacja 1 i 2<br>

* Z wyjątkiem Standard_DC8_v2

| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) | Pamięć EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Maszyny wirtualne z serii DCsv2 to [maszyny wirtualne 2. generacji](./linux/generation-2.md#creating-a-generation-2-vm) i obsługują tylko `Gen2` obrazy.
- Obecnie dostępne w regionach wymienionych [tutaj](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).
- Poprzednia generacja poufnych maszyn wirtualnych obliczeń: [Seria DC](sizes-previous-gen.md#preview-dc-series)
- Tworzenie maszyn wirtualnych DCsv2 za pomocą [Azure Portal](./linux/quick-create-portal.md) lub [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Inne rozmiary i informacje

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

Kalkulator cen: [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)

Więcej informacji na temat typów dysków: [typy dysków](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.