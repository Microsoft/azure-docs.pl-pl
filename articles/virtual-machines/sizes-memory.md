---
title: Rozmiary maszyn wirtualnych platformy Azure — pamięć | Microsoft Docs
description: Wyświetla różne rozmiary zoptymalizowane pod kątem pamięci dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Izolacja maszyny wirtualnej, izolowana maszyna wirtualna, izolacja, izolowana
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 08a14a1c97a1eae9c07c50334929fb16304e635a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556523"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci oferują duże proporcje pamięci dla serwerów relacyjnych baz danych, średnich i dużych pamięci podręcznych oraz analizę w pamięci. Ten artykuł zawiera informacje na temat liczby procesorów wirtualnych vCPU, dysków danych i kart sieciowych, a także przepływności magazynu i przepustowości sieci dla każdego rozmiaru w ramach tego grupowania.

- [Dv2 i DSv2 —](dv2-dsv2-series-memory.md)kolejne z serii D, które oferują bardziej wydajny procesor. Seria Dv2 jest o około 35% szybsza niż seria D. Działa na procesorach Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) lub Intel &reg; Xeon &reg; e5-2673 v4 2,3 GHz (Broadwell) lub Intel &reg; xeon &reg; E5-2673 v3 2,4 GHz (Haswell), a także z technologią Intel Turbo — rozwiązanie 2,0. Konfiguracje pamięci i dysków serii Dv2 są takie same jak w przypadku serii D.

    Dv2 i DSv2 są idealnym rozwiązaniem w przypadku aplikacji wymagających szybszego procesorów wirtualnych vCPU, lepszej wydajności magazynu tymczasowego lub większego zapotrzebowania na pamięć. Oferują one kombinację opcji o dużych możliwościach dla wielu aplikacji klasy korporacyjnej.

- [Seria Eav4 i Easv4](eav4-easv4-series.md) wykorzystuje procesor AMD 2.35 GHz EPYC<sup>TM</sup> 7452 w konfiguracji WIELOWĄTKOWEJ z maksymalnie 256 MB pamięci podręcznej L3, zwiększając opcje uruchamiania większości zoptymalizowanych pod kątem pamięci. Serie Eav4 i Easv4 mają takie same konfiguracje pamięci i dysków jak seria EV3 & Esv3.

- Procesor [EV3 i Esv3 serii](ev3-esv3-series.md) Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) lub procesor Intel &reg; xeon &reg; E5-2673 v4 2,3 GHz (Broadwell) w konfiguracji wielowątkowej funkcji Hyper-Threading, który zapewnia lepszą wartość w przypadku większości obciążeń ogólnego przeznaczenia, a EV3 do wyrównania z maszynami wirtualnymi ogólnego przeznaczenia większości innych chmur. Pamięć została rozszerzona (od 7 GiB/vCPU do 8 GiB/vCPU), podczas gdy limity dysku i sieci zostały skorygowane dla poszczególnych rdzeni, aby dostosować je do przenoszenia do funkcji Hyper-Threading. EV3 to kolejne rozmiary maszyn wirtualnych o dużej ilości pamięci w rodzinach D/Dv2.

- [Seria Ev4 i Esv4](ev4-esv4-series.md) jest uruchamiana w przypadku procesorów drugiej generacji Intel &reg; Xeon &reg; Platinum 8272CL (kaskad Lake) w konfiguracji wielowątkowej, idealnym rozwiązaniem jest idealne rozwiązanie dla różnych aplikacji dla przedsiębiorstw i funkcji do 504 GIB pamięci RAM. Jest to funkcja [ &reg; technologii zwiększania wydajności intel Turbo 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) i [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Seria Ev4 i Esv4 nie obejmuje lokalnego dysku tymczasowego. Aby uzyskać więcej informacji, zobacz  [rozmiary maszyn wirtualnych platformy Azure bez lokalnego dysku tymczasowego](azure-vms-no-temp-disk.md).

- [Seria Edv4 i Edsv4](edv4-edsv4-series.md) działa w &reg; &reg; przypadku procesorów Intel Xeon Platinum 8272CL (Kaskada Lake), idealnym rozwiązaniem dla bardzo dużych baz danych lub innych aplikacji, które korzystają z wysokich vCPU liczników i dużych ilości pamięci. Ponadto te rozmiary maszyn wirtualnych obejmują szybki, większy lokalny magazyn SSD dla aplikacji korzystających z małych opóźnień i magazynów lokalnych o dużej szybkości. Obejmuje ona wszystkie podstawowe szybkości zegara Turbo 3,4 GHz, [Intel &reg; Turbo zwiększa technologię 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) i [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- [Seria M](m-series.md) oferuje dużą liczbę vCPU (do 128 procesorów wirtualnych vCPU) i dużą ilość pamięci (do 3,8 TIB). Jest on również idealny dla bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci.

- [Seria Mv2](mv2-series.md) oferuje najwyższą liczbę vCPU (do 416 procesorów wirtualnych vCPU) i największą ilość pamięci (do 11,4 TIB) dowolnej maszyny wirtualnej w chmurze. Jest to idealne rozwiązanie w przypadku bardzo dużych baz danych lub innych aplikacji, które korzystają z dużej liczby vCPU i dużych ilości pamięci.

Usługa Azure COMPUTE oferuje rozmiary maszyn wirtualnych, które są odizolowane od określonego typu sprzętu i przeznaczone dla jednego klienta. Te rozmiary maszyn wirtualnych najlepiej nadają się do obciążeń wymagających wysokiego stopnia izolacji od innych klientów w przypadku obciążeń obejmujących takie elementy, jak zgodność i wymagania prawne. Klienci mogą również dodatkowo podzielić zasoby tych izolowanych maszyn wirtualnych, korzystając z [pomocy technicznej platformy Azure dla zagnieżdżonych maszyn wirtualnych](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Zapoznaj się z poniższą opcją dla izolowanych opcji maszyn wirtualnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

Aby uzyskać więcej informacji na temat nazw maszyn wirtualnych platformy Azure, zobacz [konwencje nazewnictwa maszyn wirtualnych platformy Azure](./vm-naming-conventions.md).
