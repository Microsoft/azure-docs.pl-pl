---
title: Seria NP. Virtual Machines platformy Azure
description: Specyfikacje dla maszyn wirtualnych z serii NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551377"
---
# <a name="np-series"></a>Seria NP. 
Maszyny wirtualne z serii NP są obsługiwane przez [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA w celu przyspieszenia obciążeń, w tym wnioskowania o uczenie maszynowe, transkodowania wideo i wyszukiwania w bazie danych & analizy. Maszyny wirtualne z serii NP są również obsługiwane przez procesory Intel Xeon 8171M (Skylake) i wszystkie podstawowe szybkości taktu Turbo 3,2 GHz.

[Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Buforowanie Premium Storage](premium-storage-performance.md): obsługiwane<br>
[Migracja na żywo](maintenance-and-updates.md): nieobsługiwane<br>
[Aktualizacje z zachowaniem pamięci](maintenance-and-updates.md): nieobsługiwane<br>
Obsługa generacji maszyny wirtualnej: generacja 1<br>
[Przyspieszone sieci](../virtual-network/create-vm-accelerated-networking-cli.md): obsługiwane<br>
[Dyski tymczasowe systemu operacyjnego](ephemeral-os-disks.md): nieobsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | FPGA | FPGA pamięć: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Często zadawane pytania

**P:** Jakiej wersji Vitis należy używać? 

Odp **.:** Xilinx zaleca [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**P:** Czy muszę użyć maszyn wirtualnych NP. do opracowania mojego rozwiązania? 

Odp **.:** Nie, możesz opracowywać lokalną i wdrożyć ją w chmurze. Upewnij się, że zapoznaj się z dokumentacją zaświadczania, aby wdrożyć ją na maszynach wirtualnych NP. 

**P:** Jakiej wersji XRT należy używać?

Odp. **:** xrt_202020.2.8.832 

**P:** Co to jest docelowa platforma wdrażania?

Odp **.:** Użyj następujących platform.
- Xilinx-U250-gen3x16-xdma-platform-2.1-3_all
- Xilinx-U250-gen3x16-xdma-validate_2.1-3005608.1 

**P:** Która platforma powinna być ukierunkowana na programowanie?

Odp **.:** Xilinx-U250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**P:** Jakie są obsługiwane systemy operacyjne? 

Odp **.:** Xilinx i firma Microsoft sprawdziły Ubuntu 18,04 LTS i CentOS 7,8.

 Xilinx utworzył następujące obrazy z witryny Marketplace, aby uprościć wdrażanie tych maszyn wirtualnych. 

[Maszyna wirtualna wdrożenia Xilinx ALVEO U250 — Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Maszyna wirtualna wdrożenia Xilinx ALVEO U250 — CentOS 7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**P:** Czy mogę wdrożyć własne maszyny wirtualne Ubuntu/CentOS i zainstalować platformę docelową XRT/Deployment? 

**Odpowiedź:** tak.

**P:** Jeśli wdrażam własną maszynę wirtualną Ubuntu 18.04, jakie są wymagane pakiety i kroki?

Odp **.:** Używanie jądra 4.1 X na [Xilinx XRT](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Zainstaluj następujące pakiety.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- Xilinx-U250-gen3x16-xdma-platform-2.1-3_all_18.04. deb. tar. gz
       
- Xilinx-U250-gen3x16-xdma-validate_2.1-3005608.1_all. deb  

**P:** W witrynie Ubuntu po ponownym uruchomieniu maszyny wirtualnej nie można znaleźć FPGA (s): 

Odp **.:** Sprawdź, czy jądro nie zostało uaktualnione (uname-a). Jeśli tak, Obniż poziom do jądra 4.1 X. 

**P:** Jeśli wdrażam własną maszynę wirtualną CentOS 7.8, jakie są wymagane pakiety i kroki?

Odp **.:** Użyj wersji jądra: 3.10.0-1160.15.2.el7.x86_64

 Zainstaluj następujące pakiety.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - Xilinx-U250-gen3x16-xdma-platform-2.1 -3. noarch. rpm. tar. gz 
      
 - Xilinx-U250-gen3x16-xdma-Validate-2.1-3005608.1. noarch. rpm  

**P:** Podczas uruchamiania xbutil Sprawdź, czy na CentOS otrzymuję to ostrzeżenie: "Ostrzeżenie: wersja jądra 3.10.0-1160.15.2.el7.x86_64 nie jest oficjalnie obsługiwana. 4.18.0-193 to najnowsza obsługiwana wersja ". 

Odp **.:** Można je bezpiecznie zignorować. 

**P:** Jakie są różnice między maszynami wirtualnymi lokalnego i NP. dotyczą XRT? 

Odp **.:** Na platformie Azure platforma XDMA 2,1 obsługuje tylko funkcje przechowywania danych w systemach Host_Mem (SB) i DDR. 

Aby włączyć Host_Mem (SB) (1 GB pamięci RAM): sudo xbutil host_mem--Enable--size 1G 

Aby wyłączyć Host_Mem (SB): sudo xbutil host_mem--Disable 

**P:** Czy można uruchamiać polecenia xbmgmt? 

Odp **.:** Nie, na maszynach wirtualnych platformy Azure nie ma żadnego wsparcia zarządzania bezpośrednio z maszyny wirtualnej platformy Azure. 

 **P:** Czy muszę załadować PLP? 

Odp **.:** Nie, PLP jest ładowany automatycznie, więc nie ma potrzeby ładowania za pośrednictwem poleceń xbmgmt. 

 
**P:** Czy platforma Azure obsługuje różne PLPs? 

Odp **.:** W tej chwili nie. Obsługujemy tylko PLP udostępniane w pakietach platformy wdrażania. 

**P:** Jak można badać informacje PLP? 

Odp **.:** Należy uruchomić zapytanie xbutil i zobaczyć dolną część. 

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
