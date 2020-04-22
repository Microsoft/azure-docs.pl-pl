---
title: Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
description: Poznaj kilka wskazówek dotyczących optymalizacji, aby upewnić się, że skonfigurowałeś maszynę wirtualną z systemem Linux w celu uzyskania optymalnej wydajności na platformie Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 87776c14e45ff4bb3cce6661323d74a1315c8ab2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757089"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
Tworzenie maszyny wirtualnej systemu Linux (VM) jest łatwe do zrobienia z wiersza polecenia lub z portalu. W tym samouczku pokazano, jak upewnić się, że został skonfigurowany w celu optymalizacji jego wydajności na platformie Microsoft Azure. W tym temacie użyto maszyny wirtualnej Ubuntu Server, ale można również utworzyć maszynę wirtualną systemu Linux przy użyciu [własnych obrazów jako szablonów.](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="prerequisites"></a>Wymagania wstępne
W tym temacie przyjęto założenie, że masz już działającą subskrypcję platformy Azure[(bezpłatne rejestracje próbne)](https://azure.microsoft.com/pricing/free-trial/)i już zainicjowałeś aprowizowanie maszyny wirtualnej do subskrypcji platformy Azure. Przed utworzeniem maszyny [Wirtualnej](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)upewnij się, że masz zainstalowaną i zalogowaną usługę [Azure CLI](/cli/azure/install-az-cli2) w ramach subskrypcji platformy Azure za pomocą logowania [az.](/cli/azure/reference-index)

## <a name="azure-os-disk"></a>Dysk systemu operacyjnego platformy Azure
Po utworzeniu maszyny Wirtualnej z systemem Linux na platformie Azure, ma dwa dyski skojarzone z nim. **/dev/sda** to dysk systemu operacyjnego, **/dev/sdb** jest twoim dyskiem tymczasowym.  Nie należy używać głównego dysku systemu operacyjnego (**/dev/sda**) dla niczego z wyjątkiem systemu operacyjnego, ponieważ jest zoptymalizowany pod kątem szybkiego czasu rozruchu maszyny Wirtualnej i nie zapewnia dobrej wydajności dla obciążeń. Chcesz dołączyć jeden lub więcej dysków do maszyny Wirtualnej, aby uzyskać trwałe i zoptymalizowane miejsce do magazynowania danych. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Dodawanie dysków dla obiektów docelowych rozmiar i wydajność
Na podstawie rozmiaru maszyny wirtualnej można podłączyć do 16 dodatkowych dysków na dyskach serii A, 32 dyskach z serii D i 64 dyskach na maszynie z serii G — każdy o pojemności do 32 TB. Dodaj dodatkowe dyski zgodnie z potrzebami na swoje miejsce i wymagania we/wy. Każdy dysk ma docelowy wydajność 500 IOps dla magazynu standardowego i do 20 000 IOps na dysk dla magazynu w wersji Premium.

Aby osiągnąć najwyższe IOps na dyskach magazynu w warstwie Premium, gdzie ich ustawienia pamięci podręcznej zostały ustawione na **ReadOnly** lub **None**, należy wyłączyć **bariery** podczas montażu systemu plików w systemie Linux. Nie potrzebujesz barier, ponieważ zapisy na dyskach kopii magazynu w warstwie Premium są trwałe dla tych ustawień pamięci podręcznej.

* Jeśli używasz **reiserFS,** wyłącz bariery `barrier=none` za pomocą opcji montowania `barrier=flush`(Aby włączyć bariery, użyj)
* Jeśli używasz **ext3/ext4**, wyłącz bariery `barrier=0` za pomocą opcji montowania (Aby włączyć bariery, użyj `barrier=1`)
* Jeśli używasz **XFS,** wyłącz bariery `nobarrier` za pomocą opcji montowania (Aby włączyć bariery, użyj opcji) `barrier`

## <a name="unmanaged-storage-account-considerations"></a>Zagadnienia dotyczące konta magazynu niezarządzanego
Domyślną akcją podczas tworzenia maszyny Wirtualnej z platformą Azure CLI jest użycie dysków zarządzanych platformy Azure.  Dyski te są obsługiwane przez platformę Azure i nie wymagają żadnych przygotowania lub lokalizacji do ich przechowywania.  Dyski niezarządzane wymagają konta magazynu i mają pewne dodatkowe zagadnienia dotyczące wydajności.  Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../windows/managed-disks-overview.md).  W poniższej sekcji opisano zagadnienia dotyczące wydajności tylko wtedy, gdy używane są dyski niezarządzane.  Ponownie domyślnym i zalecanym rozwiązaniem magazynu jest użycie dysków zarządzanych.

Jeśli tworzysz maszynę wirtualną z dyskami niezarządzanymi, upewnij się, że dyski z kont magazynu znajdują się w tym samym regionie co maszyna wirtualna, aby zapewnić bliskość i zminimalizować opóźnienia sieci.  Każde standardowe konto magazynu ma maksymalnie 20k IOps i pojemność rozmiaru 500 TB.  Ten limit działa na około 40 mocno używanych dysków, w tym zarówno na dysku systemu operacyjnego, jak i na wszystkich utworzonych dyskach z danymi. W przypadku kont magazynu w wersji premium nie ma limitu maksymalnych we/wy, ale istnieje limit rozmiaru 32 TB. 

W przypadku dużych obciążeń we/wy i wybranego magazynu standardowego dla dysków może być konieczne podzielenie dysków na wiele kont magazynu, aby upewnić się, że nie osiągnięto limitu 20 000 IOps dla kont magazynu standardowego. Maszyna wirtualna może zawierać kombinację dysków z różnych kont magazynu i typów kont magazynu, aby osiągnąć optymalną konfigurację.
 

## <a name="your-vm-temporary-drive"></a>Dysk tymczasowy maszyny Wirtualnej
Domyślnie podczas tworzenia maszyny Wirtualnej platforma Azure udostępnia dysk systemu operacyjnego (**/dev/sda**) i dysk tymczasowy (**/dev/sdb**).  Wszystkie dodatkowe dyski, które dodasz, są wyświetlane jako **/dev/sdc**, **/dev/sdd**, **/dev/sde** i tak dalej. Wszystkie dane na dysku tymczasowym (**/dev/sdb**) nie są trwałe i mogą zostać utracone, jeśli określone zdarzenia, takie jak zmiana rozmiaru maszyny Wirtualnej, ponowne wdrożenie lub konserwacja wymuszają ponowne uruchomienie maszyny Wirtualnej.  Rozmiar i typ dysku tymczasowego jest powiązany z rozmiarem maszyny Wirtualnej wybranym w czasie wdrażania. Wszystkie maszyny wirtualne o rozmiarze premium (DS, G i DS_V2 serii) są wspierane przez lokalny dysk SSD, co ma dodatkową wydajność do 48 k. IOps. 

## <a name="linux-swap-partition"></a>Partycja wymiany Linuksa
Jeśli maszyna wirtualna platformy Azure pochodzi z obrazu Ubuntu lub CoreOS, możesz użyć CustomData do wysłania konfiguracji w chmurze do init chmury. Jeśli [przesłano niestandardowy obraz systemu Linux,](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) który używa cloud-init, można również skonfigurować partycje wymiany przy użyciu cloud-init.

W Ubuntu Cloud Images należy użyć cloud-init, aby skonfigurować partycję wymiany. Aby uzyskać więcej informacji, zobacz [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

W przypadku obrazów bez obsługi cloud-init obrazy maszyn wirtualnych wdrożone z portalu Azure Marketplace mają agenta systemu wirtualnego systemu Linux zintegrowanego z systemem operacyjnym. Ten agent umożliwia maszynie wirtualnej do interakcji z różnymi usługami platformy Azure. Zakładając, że wdrożono standardowy obraz z portalu Azure Marketplace, należy wykonać następujące czynności, aby poprawnie skonfigurować ustawienia pliku wymiany systemu Linux:

Zlokalizuj i zmodyfikuj dwa wpisy w pliku **/etc/waagent.conf.** Kontrolują one istnienie dedykowanego pliku wymiany i rozmiar pliku wymiany. Parametry, które należy zweryfikować, są `ResourceDisk.EnableSwap` i`ResourceDisk.SwapSizeMB` 

Aby włączyć prawidłowo włączony dysk i zamontowany plik wymiany, upewnij się, że parametry mają następujące ustawienia:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size w MB, aby spełnić Twoje potrzeby} 

Po wprowadzeniu zmiany należy ponownie uruchomić waagent lub ponownie uruchomić maszynę wirtualną z systemem Linux, aby odzwierciedlić te zmiany.  Wiesz, że zmiany zostały zaimplementowane i plik `free` wymiany został utworzony podczas korzystania z polecenia, aby wyświetlić wolne miejsce. Poniższy przykład zawiera plik wymiany 512MB utworzony w wyniku modyfikacji pliku **waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algorytm planowania we/wy dla magazynu w wersji Premium
Dzięki jądrze Linuksa 2.6.18 domyślny algorytm planowania we/wy został zmieniony z Deadline na CFQ (całkowicie uczciwy algorytm kolejkowania). W przypadku wzorców we/wy dostępu losowego istnieje nieznaczna różnica w różnicach w wydajności między CFQ a Deadline.  W przypadku dysków opartych na dyskach SSD, gdzie wzorzec we/wy dysku jest głównie sekwencyjny, ponowne przełączenie się z powrotem do algorytmu NOOP lub Deadline może osiągnąć lepszą wydajność we/wy.

### <a name="view-the-current-io-scheduler"></a>Wyświetlanie bieżącego harmonogramu we/wy
Użyj następującego polecenia:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobaczysz następujące dane wyjściowe, który wskazuje bieżący harmonogram.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Zmienianie bieżącego urządzenia (/dev/sda) algorytmu planowania we/wy
Użyj następujących poleceń:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Zastosowanie tego ustawienia dla **/dev/sda** sam nie jest przydatne. Ustaw na wszystkich dyskach z danymi, na których sekwencyjne we/wy dominuje wzorzec we/wy.  

Powinieneś zobaczyć następujące dane wyjściowe, wskazując, że **grub.cfg** został pomyślnie przebudowany i że domyślny harmonogram został zaktualizowany do NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

W przypadku rodziny dystrybucji Red Hat potrzebne jest tylko następujące polecenie:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Korzystanie z software raid w celu osiągnięcia wyższych operacji
Jeśli obciążenia wymagają więcej funkcji We/Wy niż jeden dysk może zapewnić, należy użyć konfiguracji macierzy RAID oprogramowania wielu dysków. Ponieważ platforma Azure już wykonuje odporność dysku w lokalnej warstwie sieci szkieletowej, można osiągnąć najwyższy poziom wydajności z konfiguracji rozkładania RAID-0.  Aprowizuj i utwórz dyski w środowisku platformy Azure i dołącz je do maszyny Wirtualnej systemu Linux przed partycjonowaniem, formatowaniem i instalowaniem dysków.  Więcej informacji na temat konfigurowania konfiguracji macierzy RAID oprogramowania na maszynie Wirtualnej systemu Linux na platformie Azure można znaleźć w dokumencie **[Konfigurowanie macierzy RAID oprogramowania w](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** systemie Linux.

Alternatywą dla tradycyjnej konfiguracji RAID można również zainstalować Menedżera woluminów logicznych (LVM), aby skonfigurować liczbę dysków fizycznych w jeden rozłożony wolumin logiczny. W tej konfiguracji odczyty i zapisy są dystrybuowane na wiele dysków znajdujących się w grupie woluminów (podobnie jak RAID0). Ze względu na wydajność jest prawdopodobne, że będzie chcesz paski woluminów logicznych, tak aby odczyty i zapisy wykorzystać wszystkie dołączone dyski danych.  Więcej szczegółów na temat konfigurowania woluminu logicznego rozłożone na maszynie Wirtualnej systemu Linux na platformie Azure można znaleźć w **[konfigurowanie LVM na maszynie Wirtualnej systemu Linux w](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumencie platformy Azure.

## <a name="next-steps"></a>Następne kroki
Pamiętaj, że podobnie jak w wszystkich dyskusjach optymalizacji, należy wykonać testy przed i po każdej zmianie, aby zmierzyć wpływ zmiany.  Optymalizacja jest procesem krok po kroku, który ma różne wyniki na różnych komputerach w środowisku.  To, co działa dla jednej konfiguracji, może nie działać dla innych.

Kilka przydatnych linków do dodatkowych zasobów:

* [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md)
* [Konfigurowanie macierzy RAID oprogramowania w systemie Linux](configure-raid.md)
