---
title: Seria NP — Azure Virtual Machines
description: Specyfikacje dotyczące maszyn wirtualnych z serii NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861053"
---
# <a name="np-series"></a>Seria NP 
Maszyny wirtualne z serii NP są obsługiwane przez [układy FPGA Firmy Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) w celu przyspieszania obciążeń, w tym wnioskowania uczenia maszynowego, transkodowania wideo i wyszukiwania & danych. Maszyny wirtualne z serii NP są również obsługiwane przez procesory CPU Intel Xeon 8171M (Skylake) ze wszystkimi rdzeniami z zegarem turbo o szybkości 3,2 GHz.

[Premium Storage:](premium-storage-performance.md)Obsługiwane<br>
[Premium Storage buforowania:](premium-storage-performance.md)obsługiwane<br>
[migracja na żywo:](maintenance-and-updates.md)nie jest obsługiwana<br>
[Aktualizacje zachowujące pamięć:](maintenance-and-updates.md)nie są obsługiwane<br>
Obsługa generacji maszyny wirtualnej: generacja 1<br>
[Przyspieszona sieć:](../virtual-network/create-vm-accelerated-networking-cli.md)obsługiwana<br>
[Efemeracyjne dyski systemu operacyjnego:](ephemeral-os-disks.md)nie są obsługiwane <br>
<br>

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Fpga | Pamięć FPGA: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych/oczekiwana przepustowość sieci (MB/s) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** Jakiej wersji programu V pomięć? 

**A:** Firma Xilinx zaleca [V 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**Pytanie:** Czy w celu opracowania rozwiązania muszę używać maszyn wirtualnych NP? 

**A:** Nie, możesz opracowywać aplikacje lokalne i wdrażać je w chmurze. Pamiętaj, aby postępować zgodnie z dokumentacją dotyczącą zaświadczenia, aby wdrożyć go na serwerach wirtualnych np. 

**Pytanie:** Jakiej wersji XRT należy użyć?

**A:** xrt_202020.2.8.832 

**Pytanie:** Jaka jest docelowa platforma wdrażania?

**A:** Użyj następujących platform.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**Pytanie:** Która platforma powinna być ukierunkowana na rozwój?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Pytanie:** Jaki jest obsługiwany system operacyjny (systemy operacyjne)? 

**A:** Systemy Xilinx i Microsoft zwalidowały systemy Ubuntu 18.04 LTS i CentOS 7.8.

 Program Xilinx utworzył następujące obrazy z witryny Marketplace, aby uprościć wdrażanie tych maszyn wirtualnych. 

[Maszyna wirtualna wdrożenia Xilinx Alveo U250 — Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Maszyna wirtualna wdrożenia Xilinx Alveo U250 — CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Pytanie:** Czy mogę wdrożyć własne maszyny wirtualne z systemem Ubuntu/CentOS i zainstalować platformę docelową XRT/deployment? 

**Odpowiedź:** tak.

**Pytanie:** Jeśli wdrażam własną maszynę wirtualną z systemem Ubuntu 18.04, jakie są wymagane pakiety i kroki?

**A:** Używanie jądra 4.1 X na [dokumentację xrt programu Xilinx](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Zainstaluj następujące pakiety.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**Pytanie:** W systemie Ubuntu po ponownym uruchomieniu maszyny wirtualnej nie mogę znaleźć układów FPGA: 

**A:** Sprawdź, czy twoje jądro nie zostało uaktualnione (uname -a). Jeśli tak, przejdź na starszą wersję jądra 4.1X. 

**Pytanie:** Jeśli wdrażam własną maszynę wirtualną z systemem CentOS7.8, jakie są wymagane pakiety i kroki?

**A:** Użyj wersji jądra: 3.10.0-1160.15.2.el7.x86_64

 Zainstaluj następujące pakiety.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**Pytanie:** Podczas uruchamiania weryfikacji xbutil w systemie CentOS jest wyświetlane ostrzeżenie: "OSTRZEŻENIE: Wersja jądra 3.10.0-1160.15.2.el7.x86_64 nie jest oficjalnie obsługiwana. 4.18.0–193 to najnowsza obsługiwana wersja." 

**A:** Można to bezpiecznie zignorować. 

**Pytanie:** Jakie są różnice między maszynami maszyn wirtualnych OnPrem i NP w odniesieniu do XRT? 

**A:** Na platformie Azure platforma XDMA 2.1 obsługuje tylko funkcje przechowywania Host_Mem (SB) i DDR. 

Aby włączyć Host_Mem(SB) (1 GB pamięci RAM): sudo xbutil host_mem --enable --size 1g 

Aby wyłączyć Host_Mem(SB): sudo xbutil host_mem --disable 

**Pytanie:** Czy można uruchamiać polecenia xbmgmt? 

**A:** Nie, na maszynach wirtualnych platformy Azure nie ma obsługi zarządzania bezpośrednio z maszyny wirtualnej platformy Azure. 

 **Pytanie:** Czy muszę załadować plp? 

**A:** Nie, plp jest ładowany automatycznie, więc nie ma potrzeby ładowania za pomocą poleceń xbmgmt. 

 
**Pytanie:** Czy platforma Azure obsługuje różne pl/s? 

**A:** Obecnie nie. Obsługujemy tylko pakiet PLP dostarczony w pakietach platformy wdrażania. 

**Pytanie:** Jak mogę query the PLP information? (Jak mogę odpytać informacje PLP?) 

**A:** Należy uruchomić zapytanie xbutil i przyjrzeć się dolnej części. 

**Pytanie:** Jakie dodatkowe zmiany należy wprowadzić w przypadku ręcznego tworzenia własnej maszyny wirtualnej i wdrażania XRT? 

**A:** W /opt/xilinx/xrt/setup.sh dodaj wpis dla XRT_INI_PATH, który wskaże /opt/xilinx/xrt/xrt.ini

 
Zawartość /opt/xilinx/xrt/xrt.ini powinna zawierać: <br>
[Środowisko uruchomieniowe]<br>
ert = false <br>

## <a name="other-sizes"></a>Inne rozmiary

- [Ogólnego przeznaczenia](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej między jednostkami SKU platformy Azure.
