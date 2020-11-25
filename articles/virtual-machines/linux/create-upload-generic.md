---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego system operacyjny Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: a80cc29f318cff8e5a4c665cd07ba1829d25d66d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016339"
---
# <a name="information-for-non-endorsed-distributions"></a>Informacje dotyczące dystrybucji niepotwierdzonych

Umowa SLA platformy Azure ma zastosowanie do maszyn wirtualnych z systemem operacyjnym Linux tylko wtedy, gdy jest używana jedna z [poświadczonej dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . W przypadku tych potwierdzonych dystrybucji wstępnie skonfigurowane obrazy systemu Linux są dostępne w portalu Azure Marketplace.

* [System Linux w przypadku dystrybucji z zatwierdzeniem na platformie Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Obsługa obrazów systemu Linux w Microsoft Azure](https://support.microsoft.com/kb/2941892)

Wszystkie dystrybucje działające na platformie Azure mają kilka wymagań wstępnych. Ten artykuł nie może być wyczerpujący, ponieważ każda dystrybucja różni się. Nawet jeśli spełniasz wszystkie poniższe kryteria, może być konieczne znaczne dostosowanie systemu Linux pod kątem prawidłowego działania.

Zalecamy rozpoczęcie od jednego z systemów Linux w ramach wykorzystanych [dystrybucji na platformie Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). W poniższych artykułach pokazano, jak przygotować różne, zatwierdzone dystrybucje systemu Linux, które są obsługiwane na platformie Azure:

- [dystrybucje oparte na systemie CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES i openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Ten artykuł koncentruje się na ogólnych wskazówkach dotyczących uruchamiania dystrybucji systemu Linux na platformie Azure.

## <a name="general-linux-installation-notes"></a>Ogólne informacje o instalacji systemu Linux
* Format wirtualnego dysku twardego funkcji Hyper-V (VHDX) nie jest obsługiwany na platformie Azure, tylko *stałego dysku VHD*.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet [convert-VHD](/powershell/module/hyper-v/convert-vhd) . Jeśli używasz VirtualBox, wybierz opcję **stały rozmiar** , a nie wartość domyślną (przydzieloną dynamicznie) podczas tworzenia dysku.
* Platforma Azure obsługuje Gen1 (BIOS Boot) & Gen2 (UEFI boot) maszyn wirtualnych.
* Maksymalny dozwolony rozmiar dysku VHD to 1 023 GB.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast Menedżera woluminów logicznych (LVM), który jest wartością domyślną dla wielu instalacji. Użycie partycji standardowych spowoduje uniknięcie konfliktów nazw LVM z sklonowanymi maszynami wirtualnymi, szczególnie jeśli dysk systemu operacyjnego jest kiedykolwiek podłączony do innej identycznej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
* Konieczna jest obsługa jądra do instalowania systemów plików UDF. Podczas pierwszego rozruchu na platformie Azure konfiguracja aprowizacji jest przesyłana do maszyny wirtualnej z systemem Linux przy użyciu nośnika sformatowanego w formacie UDF, który jest dołączony do gościa. Aby można było odczytać konfigurację i zainicjować obsługę administracyjną maszyny wirtualnej, Agent systemu Azure Linux musi zainstalować system plików UDF.
* Wersje jądra systemu Linux starsze niż 2.6.37 nie obsługują architektury NUMA w funkcji Hyper-V o większych rozmiarach maszyn wirtualnych. Ten problem ma głównie wpływ na starsze dystrybucje przy użyciu wbudowanego jądra Red Hat 2.6.32 i został ustalony w Red Hat Enterprise Linux (RHEL) 6,6 (jądro-2.6.32-504). Systemy z uruchomionymi niestandardowymi jądrami starszymi niż 2.6.37 lub jądrem opartym na RHEL starszym niż 2.6.32-504 muszą ustawić parametr rozruchu w `numa=off` wierszu polecenia jądra w grub. conf. Aby uzyskać więcej informacji, zobacz [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować tak, aby utworzył plik wymiany na tymczasowym dysku zasobów, zgodnie z opisem w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją, zgodnie z opisem w poniższych krokach.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalowanie modułów jądra bez funkcji Hyper-V
System Azure działa na funkcji hypervisor funkcji Hyper-V, więc system Linux wymaga, aby niektóre moduły jądra działały na platformie Azure. Jeśli masz maszynę wirtualną, która została utworzona poza funkcją Hyper-V, instalatorzy systemu Linux mogą nie zawierać sterowników dla funkcji Hyper-V w początkowym systemie Ramdisk (oryginalnych initrd lub initramfs), chyba że maszyna wirtualna wykryje, że jest uruchomiona w środowisku funkcji Hyper-V. W przypadku korzystania z innego systemu wirtualizacji (takiego jak VirtualBox, KVM itd.) w celu przygotowania obrazu systemu Linux może być konieczne odbudowanie oryginalnych initrd, tak aby co najmniej hv_vmbus i hv_storvsc moduły jądra były dostępne w początkowym systemie Ramdisk.  Ten znany problem dotyczy systemów opartych na nadrzędnym systemie Red Hat i prawdopodobnie innych.

Mechanizm odbudowy obrazu oryginalnych initrd lub initramfs może się różnić w zależności od rozkładu. Zapoznaj się z dokumentacją dystrybucji lub pomocą techniczną w celu uzyskania odpowiedniej procedury.  Oto przykład odbudowy oryginalnych initrd za pomocą `mkinitrd` Narzędzia:

1. Wykonaj kopię zapasową istniejącego obrazu oryginalnych initrd:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Skompiluj ponownie oryginalnych initrd z modułami jądra hv_vmbus i hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Zmienianie rozmiarów dysków VHD
Obrazy VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB.  Zwykle dyski VHD utworzone przy użyciu funkcji Hyper-V są prawidłowo wyrównane.  Jeśli wirtualny dysk twardy nie jest prawidłowo wyrównany, podczas próby utworzenia obrazu z wirtualnego dysku twardego może pojawić się komunikat o błędzie podobny do poniższego.

* Dysk VHD http: \/ / \<mystorageaccount> . blob.Core.Windows.NET/VHDs/MyLinuxVM.VHD ma nieobsługiwany rozmiar wirtualny wynoszący 21475270656 bajtów. Rozmiar musi być liczbą całkowitą (w MB).

W takim przypadku należy zmienić rozmiar maszyny wirtualnej przy użyciu konsoli Menedżera funkcji Hyper-V lub polecenia cmdlet programu PowerShell [Zmień rozmiar dysku VHD](/powershell/module/hyper-v/resize-vhd?view=win10-ps) .  Jeśli nie Pracujesz w środowisku systemu Windows, zalecamy użycie `qemu-img` programu do konwersji (w razie potrzeby) i zmiany rozmiaru dysku VHD.

> [!NOTE]
> [W wersjach QEMU-IMG występuje znany błąd](https://bugs.launchpad.net/qemu/+bug/1490611) >= 2.2.1, który powoduje niewłaściwie sformatowany dysk VHD. Problem został rozwiązany w QEMU 2,6. Zalecamy użycie `qemu-img` 2.2.0 lub niższych lub 2,6 lub wyższych.
> 

1. Zmianę rozmiarów wirtualnego dysku twardego bezpośrednio przy użyciu narzędzi takich jak `qemu-img` lub `vbox-manage` może spowodować nierozruchowy dysk VHD.  Zalecamy najpierw przekonwertowanie dysku VHD na obraz RAW.  Jeśli obraz maszyny wirtualnej został utworzony jako obraz dysku RAW (domyślnie dla niektórych funkcji hypervisor, takich jak KVM), można pominąć ten krok.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Oblicz wymagany rozmiar obrazu dysku, aby rozmiar wirtualny był wyrównany do 1 MB.  Poniższy skrypt powłoki bash używa `qemu-img info` do określenia rozmiaru wirtualnego obrazu dysku, a następnie oblicza rozmiar do następnych 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Zmień rozmiar dysku nieprzetworzonego `$rounded_size` , używając zgodnie z powyższym zestawem.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Teraz przekonwertuj PIERWOTNy dysk do dysku VHD o stałym rozmiarze.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Lub, z qemu wersja 2.6 +, należy uwzględnić `force_size` opcję.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Wymagania jądra systemu Linux

Sterowniki systemu Linux Integration Services (LIS) dla funkcji Hyper-V i platformy Azure są bezpośrednio przeznaczone do nadrzędnego jądra systemu Linux. Wiele dystrybucji, które obejmują ostatnią wersję jądra systemu Linux (np. 3. x), ma już dostępne te sterowniki lub w inny sposób udostępniają w sposób nieaktualne wersje tych sterowników z ich jądrem.  Te sterowniki są stale aktualizowane w jądrze nadrzędnym z nowymi poprawkami i funkcjami, dlatego w przypadku, gdy jest to możliwe, zalecamy uruchomienie [rozpowszechnianej dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) zawierającej te poprawki i aktualizacje.

Jeśli używasz wariantu Red Hat Enterprise Linux wersje 6,0 do 6,3, należy zainstalować [najnowsze sterowniki lis dla funkcji Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Począwszy od RHEL 6.4 + (i pochodnych) Sterowniki LIS są już dołączone do jądra i dlatego nie są potrzeby żadne dodatkowe pakiety instalacyjne.

Jeśli jest wymagane niestandardowe jądro, zalecamy użycie najnowszej wersji jądra (takiej jak 3.8 +). W przypadku dystrybucji lub dostawców, którzy utrzymują własne jądro, należy regularnie backport sterowniki LIS z jądra nadrzędnego do niestandardowego jądra.  Nawet jeśli korzystasz już z stosunkowo najnowszej wersji jądra, zdecydowanie zalecamy śledzenie wszelkich poprawek nadrzędnych w sterownikach LIS i backport je w razie konieczności. Lokalizacje plików źródłowych sterowników LIS są określone w pliku [obsługi](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) w drzewie źródłowym jądra systemu Linux:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
W jądrze muszą być zawarte następujące poprawki. Nie można ukończyć tej listy dla wszystkich dystrybucji.

* [ata_piix: domyślnie Odłóż dyski do sterowników funkcji Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: konto dla pakietów w trakcie przesyłania w ścieżce RESETowania](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Unikaj używania WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Wyłącz zapis w obszarze RAID i sterownik karty hosta wirtualnego](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: poprawka do usuwania wskaźnika o wartości NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: awarie buforu pierścienia mogą spowodować zatrzymanie operacji we/wy](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: Chroń przed podwójnym wykonaniem __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent platformy Azure dla systemu Linux
[Agent platformy Azure dla systemu Linux](../extensions/agent-linux.md) `waagent` inicjuje maszynę wirtualną z systemem Linux na platformie Azure. Możesz uzyskać najnowszą wersję, problemy z plikami lub przesłać żądania ściągnięcia w [repozytorium GitHub agenta systemu Linux](https://github.com/Azure/WALinuxAgent).

* Agent systemu Linux jest wydawany w ramach licencji Apache 2,0. Wiele dystrybucji udostępnia już pakiety RPM lub deb dla agenta. te pakiety można łatwo instalować i aktualizować.
* Agent platformy Azure dla systemu Linux wymaga języka Python v 2.6 lub nowszego.
* Agent wymaga również modułu Python-pyasn1. Większość dystrybucji dostarcza ten moduł jako osobny pakiet do zainstalowania.
* W niektórych przypadkach Agent systemu Azure Linux może być niezgodny z programem NetworkManager. Wiele pakietów RPM/DEB dostarczonych przez dystrybucje konfiguruje program NetworkManager jako konflikt z pakietem waagent. W takich przypadkach program NetworkManager zostanie odinstalowany podczas instalacji pakietu agenta systemu Linux.
* Agent systemu Azure Linux musi mieć wartość co [najmniej minimalnej obsługiwanej wersji](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Ogólne wymagania systemowe systemu Linux

1. Zmodyfikuj wiersz rozruchowy jądra w GRUB lub GRUB2, aby uwzględnić następujące parametry, aby wszystkie komunikaty konsoli były wysyłane do pierwszego portu szeregowego. Te komunikaty mogą pomóc w pomocy technicznej platformy Azure w zakresie debugowania wszelkich problemów.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Zalecamy również *usunięcie* następujących parametrów, jeśli istnieją.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Rozruch graficzny i cichy nie jest przydatny w środowisku chmury, gdzie wszystkie dzienniki są wysyłane do portu szeregowego. `crashkernel`Opcja może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej przez co najmniej 128 MB, co może powodować problemy w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Zainstaluj agenta platformy Azure dla systemu Linux.
  
    Agent systemu Azure Linux jest wymagany do aprowizacji obrazu systemu Linux na platformie Azure.  Wiele dystrybucji dostarcza agenta jako pakiet RPM lub deb (pakiet jest zwykle nazywany WALinuxAgent lub WALinuxAgent).  Agenta można także zainstalować ręcznie, wykonując czynności opisane w [przewodniku po agencie systemu Linux](../extensions/agent-linux.md).

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Ta konfiguracja jest zazwyczaj domyślnie.

1. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
  
    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (krok 2 powyżej) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Na VirtualBox może zostać wyświetlony następujący błąd po uruchomieniu `waagent -force -deprovision` `[Errno 5] Input/output error` . Ten komunikat o błędzie nie jest krytyczny i można go zignorować.

* Zamknij maszynę wirtualną i przekaż dysk VHD na platformę Azure.
