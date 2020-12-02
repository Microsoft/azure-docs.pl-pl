---
title: Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
description: Poznaj pewne porady dotyczące optymalizacji, aby upewnić się, że maszyna wirtualna z systemem Linux została skonfigurowana pod kątem optymalnej wydajności na platformie Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 1e3551834e7664d5036fa8a5e0497e5a37f61c2f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498510"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optymalizowanie maszyny wirtualnej systemu Linux na platformie Azure
Tworzenie maszyny wirtualnej z systemem Linux jest proste z poziomu wiersza polecenia lub portalu. W tym samouczku pokazano, jak upewnić się, że został skonfigurowany, aby zoptymalizować jego wydajność na platformie Microsoft Azure. W tym temacie jest używana maszyna wirtualna serwera Ubuntu, ale można również utworzyć maszynę wirtualną z systemem Linux przy użyciu [własnych obrazów jako szablonów](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Wymagania wstępne
W tym temacie przyjęto założenie, że masz już działającą subskrypcję platformy Azure ([bezpłatny okres próbny](https://azure.microsoft.com/pricing/free-trial/)) i masz już zainicjowaną maszynę wirtualną w ramach subskrypcji platformy Azure. Przed [utworzeniem maszyny wirtualnej](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)upewnij się, że masz zainstalowany [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogowano się do subskrypcji platformy Azure przy użyciu [AZ login](/cli/azure/reference-index) .

## <a name="azure-os-disk"></a>Dysk systemu operacyjnego Azure
Po utworzeniu maszyny wirtualnej z systemem Linux na platformie Azure są z nią skojarzone dwa dyski. **/dev/SDA** to dysk systemu operacyjnego, a **/dev/sdb** to dysk tymczasowy.  Nie należy używać głównego dysku systemu operacyjnego (**/dev/SDA**) dla wszystkich elementów oprócz systemu operacyjnego, ponieważ jest zoptymalizowany pod kątem szybkiego rozruchu maszyn wirtualnych i nie zapewnia dobrej wydajności obciążeń. Chcesz dołączyć co najmniej jeden dysk do maszyny wirtualnej w celu uzyskania trwałego i zoptymalizowanego magazynu dla danych. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Dodawanie dysków dla celów rozmiaru i wydajności
Na podstawie rozmiaru maszyny wirtualnej można dołączyć do 16 dodatkowych dysków na dyskach serii A, 32 na dyskach serii D i 64 na maszynie z serii G — każdy do 32 TB. Dodatkowe dyski należy dodać odpowiednio do wymagań dotyczących miejsca i liczby operacji we/wy na sekundę. Każdy dysk ma cel wydajności 500 operacji we/wy na sekundę w przypadku magazynu w warstwie Standardowa oraz do 20 000 liczby operacji we/wy na sekundę na dysk dla Premium Storage.

Aby osiągnąć największą liczbę operacji we/wy na dyskach Premium Storage, w których ustawienia pamięci podręcznej zostały ustawione na wartość **ReadOnly** lub **Brak**, należy wyłączyć **bariery** podczas instalowania systemu plików w systemie Linux. Nie jest wymagana żadna bariera, ponieważ zapisy do Premium Storage dysków z kopią zapasową są trwałe dla tych ustawień pamięci podręcznej.

* Jeśli używasz **reiserFS**, wyłącz bariery przy użyciu opcji instalacji `barrier=none` (w celu włączenia barier, użyj `barrier=flush` )
* Jeśli używasz **ext3/ext4**, wyłącz bariery przy użyciu opcji instalacji `barrier=0` (aby włączyć bariery, użyj `barrier=1` )
* Jeśli używasz **XFS**, wyłącz bariery przy użyciu opcji instalacji `nobarrier` (aby włączyć bariery, użyj opcji `barrier` )

## <a name="unmanaged-storage-account-considerations"></a>Zagadnienia dotyczące kont magazynu niezarządzanego
Domyślna akcja podczas tworzenia maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure to użycie usługi Azure Managed Disks.  Te dyski są obsługiwane przez platformę Azure i nie wymagają żadnego przygotowania ani lokalizacji do ich przechowywania.  Dyski niezarządzane wymagają konta magazynu i są dostępne dodatkowe zagadnienia dotyczące wydajności.  Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [Omówienie usługi Azure Managed Disks](../managed-disks-overview.md).  W poniższej sekcji omówiono zagadnienia związane z wydajnością tylko w przypadku korzystania z dysków niezarządzanych.  Ponownie domyślne i zalecane rozwiązanie magazynu to użycie dysków zarządzanych.

Jeśli tworzysz maszynę wirtualną z dyskami niezarządzanymi, pamiętaj o dołączeniu dysków z kont magazynu znajdujących się w tym samym regionie co maszyna wirtualna, aby zapewnić bliską bliskość i zminimalizować opóźnienie sieci.  Każde konto magazynu w warstwie Standardowa ma maksymalnie 20 000 operacji we/wy i pojemności 500 TB.  Ten limit obejmuje około 40 intensywnie używanych dysków, w tym dysk systemu operacyjnego i wszystkie utworzone dyski danych. W przypadku kont Premium Storage nie ma limitu maksymalnej liczby IOps, ale obowiązuje limit rozmiaru 32 TB. 

W przypadku obciążeń o dużej liczbie IOps i wybrania magazynu w warstwie Standardowa dla dysków może być konieczne podzielenie dysków na wiele kont magazynu, aby upewnić się, że nie osiągnięto limitu 20 000 operacji we/wy dla kont magazynu w warstwie Standardowa. Maszyna wirtualna może zawierać wiele dysków z różnych kont magazynu i typów kont magazynu, aby osiągnąć optymalną konfigurację.
 

## <a name="your-vm-temporary-drive"></a>Dysk tymczasowy maszyny wirtualnej
Domyślnie podczas tworzenia maszyny wirtualnej platforma Azure udostępnia dysk systemu operacyjnego (**/dev/SDA**) i dysk tymczasowy (**/dev/sdb**).  Wszystkie dodane dyski są wyświetlane jako **/dev/SDC**, **/dev/SDD**, **/dev/SDE** i tak dalej. Wszystkie dane na dysku tymczasowym (**/dev/sdb**) nie są trwałe i mogą zostać utracone, jeśli określone zdarzenia, takie jak zmiany rozmiarów maszyn wirtualnych, ponowne wdrożenie lub konserwacja, wymuszają ponowny uruchomienie maszyny wirtualnej.  Rozmiar i typ dysku tymczasowego są związane z wybranym rozmiarem maszyny wirtualnej w czasie wdrażania. Wszystkie maszyny wirtualne w rozmiarze Premium (w ramach serii DS, G i DS_V2) są obsługiwane przez lokalny dysk SSD w celu uzyskania dodatkowej wydajności do 48K operacji we/wy na sekundę. 

## <a name="linux-swap-partition"></a>Partycja wymiany systemu Linux
Jeśli maszyna wirtualna platformy Azure pochodzi z obrazu Ubuntu lub CoreOS, można użyć CustomData do wysłania pliku Cloud-config do usługi Cloud-init. W przypadku [przekazania niestandardowego obrazu systemu Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) korzystającego z usługi Cloud-init należy również skonfigurować partycje wymiany przy użyciu funkcji Cloud-init.

Nie można użyć pliku **/etc/waagent.conf** do zarządzania wymianą dla wszystkich obrazów, które są inicjowane i obsługiwane przez funkcję Cloud-init. Pełną listę obrazów można znaleźć w temacie [using Cloud-init](using-cloud-init.md). 

Najprostszym sposobem zarządzania wymianą tych obrazów jest wykonanie następujących czynności:

1. W folderze **/var/lib/Cloud/scripts/per-Boot** Utwórz plik o nazwie **create_swapfile. sh**:

   **$ sudo touch/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

1. Dodaj następujące wiersze do pliku:

   **$ sudo VI/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

   ```
   #!/bin/sh
   if [ ! -f '/mnt/swapfile' ]; then
   fallocate --length 2GiB /mnt/swapfile
   chmod 600 /mnt/swapfile
   mkswap /mnt/swapfile
   swapon /mnt/swapfile
   swapon -a ; fi
   ```

   > [!NOTE]
   > Możesz zmienić wartość zgodnie z potrzebami i na podstawie dostępnego miejsca na dysku zasobów, które różnią się w zależności od używanego rozmiaru maszyny wirtualnej.

1. Utwórz plik wykonywalny:

   **$ sudo chmod + x/var/lib/Cloud/scripts/per-Boot/create_swapfile. sh**

1. Aby utworzyć swapfile, wykonaj skrypt bezpośrednio po ostatnim kroku:

   **$ sudo/var/lib/Cloud/scripts/per-Boot/./create_swapfile. sh**

W przypadku obrazów bez obsługi usługi Cloud-init obrazy maszyn wirtualnych wdrożone z poziomu portalu Azure Marketplace mają agenta maszyny wirtualnej z systemem Linux zintegrowany z systemem operacyjnym. Ten Agent umożliwia korzystanie z maszyn wirtualnych z różnymi usługami platformy Azure. Przy założeniu, że został wdrożony standardowy obraz z portalu Azure Marketplace, należy wykonać następujące czynności, aby poprawnie skonfigurować ustawienia pliku wymiany systemu Linux:

Zlokalizuj i zmodyfikuj dwa wpisy w pliku **/etc/waagent.conf** . Kontrolują istnienie dedykowanego pliku wymiany i rozmiaru pliku wymiany. Parametry, które należy zweryfikować, są `ResourceDisk.EnableSwap` i `ResourceDisk.SwapSizeMB` 

Aby włączyć poprawnie włączony dysk i zainstalowany plik wymiany, upewnij się, że parametry mają następujące ustawienia:

* ResourceDisk. EnableSwap = Y
* ResourceDisk. SwapSizeMB = {rozmiar w MB, aby spełnić Twoje potrzeby} 

Po dokonaniu zmiany należy ponownie uruchomić waagent lub uruchomić ponownie maszynę wirtualną z systemem Linux w celu odzwierciedlenia tych zmian.  Wiadomo, że zmiany zostały zaimplementowane, a plik wymiany został utworzony, gdy użyjesz polecenia, `free` Aby wyświetlić wolne miejsce. W poniższym przykładzie plik wymiany z 512 MB został utworzony w wyniku modyfikacji pliku **waagent. conf** :

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algorytm planowania we/wy dla Premium Storage
Przy użyciu jądra 2.6.18 systemu Linux domyślny algorytm planowania we/wy został zmieniony z terminu ostatecznego na CFQ (całkowicie uczciwy algorytm kolejkowania). W przypadku wzorców dostępu losowego we/wy występuje niewielka różnica między CFQ i terminem ostatecznym.  W przypadku dysków SSD, w których wzorzec dysku we/wy jest głównie sekwencyjny, przełączenie do algorytmu AKTUALIZUJĄCY nie działa lub terminu ostatecznego może osiągnąć lepszą wydajność operacji we/wy.

### <a name="view-the-current-io-scheduler"></a>Wyświetlanie bieżącego harmonogramu we/wy
Użyj następującego polecenia:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobaczysz następujące dane wyjściowe, które wskazują bieżący harmonogram.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Zmień bieżące urządzenie (/dev/SDA) algorytmu planowania we/wy
Użyj następujących poleceń:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Stosowanie tego ustawienia wyłącznie dla **/dev/SDA** nie jest przydatne. Ustaw na wszystkich dyskach danych, w których sekwencyjne we/wy przeważa wzorca we/wy.  

Powinny zostać wyświetlone następujące dane wyjściowe, wskazujące, że **grub. cfg** zostały pomyślnie odbudowane i że domyślny harmonogram został zaktualizowany do aktualizujący nie działa.  

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

W przypadku rodziny dystrybucji Red Hat wystarczy wykonać następujące polecenie:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Ubuntu 18,04 z jądrem dostrojonym na platformie Azure korzysta z wielokolejkowych harmonogramów we/wy. W tym scenariuszu `none` jest to odpowiedni wybór zamiast `noop` . Aby uzyskać więcej informacji, zobacz [Ubuntue harmonogramy we/wy](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>Używanie oprogramowania RAID do osiągania wyższych operacji I/Ops
Jeśli obciążenia wymagają większej liczby operacji we/wy na sekundę, należy użyć konfiguracji oprogramowania RAID z wieloma dyskami. Ponieważ platforma Azure zapewnia już odporność dysku na lokalną warstwę sieci szkieletowej, osiągnięty jest najwyższy poziom wydajności z konfiguracji rozłożenia RAID-0.  Udostępnianie i tworzenie dysków w środowisku platformy Azure i dołączanie ich do maszyny wirtualnej z systemem Linux przed partycjonowaniem, formatowaniem i instalowaniem dysków.  Więcej informacji o konfigurowaniu oprogramowania instalacyjnego RAID na maszynie wirtualnej z systemem Linux na platformie Azure można znaleźć w dokumencie **[Konfigurowanie oprogramowania RAID w systemie Linux](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

Alternatywą dla tradycyjnej konfiguracji RAID jest również zainstalowanie Menedżera woluminów logicznych (LVM) w celu skonfigurowania wielu dysków fizycznych w jednym woluminie z rozłożonym logicznym magazynem. W tej konfiguracji operacje odczytu i zapisu są dystrybuowane do wielu dysków znajdujących się w grupie woluminów (podobnie jak w przypadku RAID0). Ze względu na wydajność prawdopodobnie chcesz umieścić woluminy logiczne, aby odczyty i zapisy używały wszystkich dołączonych dysków danych.  Więcej szczegółowych informacji na temat konfigurowania rozłożonego woluminu logicznego na maszynie wirtualnej z systemem Linux na platformie Azure można znaleźć w temacie **[Konfigurowanie LVM na maszynie wirtualnej z systemem Linux w dokumencie platformy Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

## <a name="next-steps"></a>Następne kroki
Pamiętaj, podobnie jak w przypadku wszystkich dyskusji optymalizacji, musisz wykonać testy przed i po każdej zmianie, aby zmierzyć wpływ zmiany.  Optymalizacja jest procesem krok po kroku, który ma różne wyniki na różnych maszynach w środowisku.  Co działa dla jednej konfiguracji, może nie działać dla innych osób.

Niektóre przydatne linki do dodatkowych zasobów:

* [Przewodnik użytkownika Agenta platformy Azure dla systemu Linux](../extensions/agent-linux.md)
* [Konfigurowanie RAID oprogramowania w systemie Linux](/previous-versions/azure/virtual-machines/linux/configure-raid)