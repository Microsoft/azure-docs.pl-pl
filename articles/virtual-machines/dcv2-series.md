---
title: Seria DC-Virtual Machines platformy Azure
description: Specyfikacje dotyczące maszyn wirtualnych z serii DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085726"
---
# <a name="dcsv2-series"></a>Seria DCsv2


Seria DCsv2 może pomóc w ochronie poufności i integralności danych oraz kodu podczas przetwarzania w chmurze publicznej. Te maszyny są obsługiwane przez najnowszą generację procesora Intel XEON E-2288G z technologią SGX. Dzięki technologii zwiększania procesora Intel Turbo te maszyny mogą mieć wartość do 5,0 GHz. Wystąpienia serii DCsv2 umożliwiają klientom tworzenie bezpiecznych aplikacji opartych na enklawy w celu ochrony kodu i danych, gdy jest używany.

Przykładowe przypadki użycia obejmują: poufne udostępnianie danych wieloczęściowych, wykrywanie oszustw, pranie antypieniężne, łańcucha bloków, poufne analizy użycia, analizę analizy oraz poufne Uczenie maszynowe.

Premium Storage: obsługiwane *

Buforowanie Premium Storage: obsługiwane *

Migracja na żywo: nieobsługiwane

Aktualizacje z zachowaniem pamięci: nieobsługiwane

* Z wyjątkiem Standard_DC8_v2



| Rozmiar             | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność magazynu buforowanego i tymczasowego: liczba operacji we/wy na sekundę / MB/s (rozmiar pamięci podręcznej w GiB) | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Maszyny wirtualne z serii DCsv2 to [maszyny wirtualne 2](./linux/generation-2.md#creating-a-generation-2-vm) . generacji `Gen2` i obsługują tylko obrazy.
- Obecnie dostępne tylko w Południowe Zjednoczone Królestwo, Kanada Środkowa i Wschodnie stany USA.
- Poprzednia generacja poufnych maszyn wirtualnych obliczeń: [Seria DC](sizes-previous-gen.md#preview-dc-series)
- Tworzenie maszyn wirtualnych DCsv2 za pomocą [Azure Portal](./linux/quick-create-portal.md) lub [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
