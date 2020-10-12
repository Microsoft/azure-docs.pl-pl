---
title: Tworzenie i przekazywanie wirtualnego dysku twardego w systemie SUSE Linux na platformie Azure
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego system operacyjny SUSE Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 73e07c612486d5f48b1ad3eca8044a561549092b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292120"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem SLES lub openSUSE dla platformy Azure


W tym artykule przyjęto założenie, że już zainstalowano system operacyjny SUSE lub openSUSE Linux na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Uwagi dotyczące instalacji SLES/openSUSE
* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować tak, aby utworzył plik wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>Korzystanie z programu SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) może łatwo tworzyć obrazy SLES i openSUSE oraz zarządzać nimi na platformie Azure i funkcji Hyper-V. Jest to zalecane podejście do dostosowywania własnych obrazów SLES i openSUSE.

Alternatywą dla tworzenia własnego wirtualnego dysku twardego jest również publikowanie BYOS (przenoszenie własnych subskrypcji) dla SLES na [repozytorium VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Przygotuj SUSE Linux Enterprise Server 11 SP4
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.
3. Zarejestruj system SUSE Linux Enterprise, aby zezwolić na pobieranie aktualizacji i instalowanie pakietów.
4. Zaktualizuj system przy użyciu najnowszych poprawek:

    ```console
    # sudo zypper update
    ```

1. Zainstaluj agenta systemu Linux platformy Azure z repozytorium SLES (SLE11-Public-Cloud-module):

    ```console
    # sudo zypper install python-azure-agent
    ```

1. Sprawdź, czy waagent jest ustawiona na wartość "on" w chkconfig, a jeśli nie, włącz ją dla autostartu:

    ```console
    # sudo chkconfig waagent on
    ```

7. Sprawdź, czy usługa waagent jest uruchomiona, a jeśli nie, uruchom ją: 

    ```console
    # sudo service waagent start
    ```

8. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/boot/grub/menu.lst" w edytorze tekstów i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
9. Upewnij się, że/boot/grub/menu.lst i/etc/fstab odwołują się do dysku przy użyciu identyfikatora UUID (według-UUID) zamiast identyfikatora dysku (o identyfikatorze). 
   
    Pobierz identyfikator UUID dysku

    ```console
    # ls /dev/disk/by-uuid/
    ```

    Jeśli jest używany/dev/Disk/by-ID/, zaktualizuj zarówno/boot/grub/menu.lst, jak i/etc/fstab z poprawną wartością przez-UUID
   
    Przed zmianą
   
    `root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1`
   
    Po zmianie
   
    `root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`

10. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

11. Zaleca się zmodyfikowanie pliku "/etc/sysconfig/Network/DHCP" i zmianę `DHCLIENT_SET_HOSTNAME` parametru na następujący:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. W pozycji "/etc/sudoers" Dodaj komentarz lub usuń następujące wiersze, jeśli istnieją:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
14. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

15. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```
16. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="prepare-opensuse-131"></a>Przygotuj openSUSE 13.1 +
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.
3. W powłoce Uruchom polecenie " `zypper lr` ". Jeśli to polecenie zwraca dane wyjściowe podobne do następujących, repozytoria są konfigurowane zgodnie z oczekiwaniami — nie są wymagane żadne korekty (Zwróć uwagę na to, że numery wersji mogą się różnić):

   | # | Alias                 | Nazwa                  | Enabled (Włączony) | Odśwież
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Chmura: Tools_13.1      | Chmura: Tools_13.1      | Tak     | Tak
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Tak     | Tak
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Tak     | Tak

    Jeśli polecenie zwróci wartość "brak zdefiniowanych repozytoriów..." następnie użyj następujących poleceń, aby dodać te repozytoria:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Następnie można sprawdzić, czy repozytoria zostały dodane, ponownie uruchamiając polecenie " `zypper lr` ". W przypadku, gdy jeden z odpowiednich repozytoriów aktualizacji nie jest włączony, włącz go przy użyciu następującego polecenia:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Zaktualizuj jądro do najnowszej dostępnej wersji:

    ```console
    # sudo zypper up kernel-default
    ```

    Lub zaktualizować system przy użyciu wszystkich najnowszych poprawek:

    ```console
    # sudo zypper update
    ```

5. Zainstaluj agenta platformy Azure dla systemu Linux.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/boot/grub/menu.lst" w edytorze tekstów i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Ponadto Usuń następujące parametry z wiersza rozruchowego jądra, jeśli istnieją:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Zaleca się zmodyfikowanie pliku "/etc/sysconfig/Network/DHCP" i zmianę `DHCLIENT_SET_HOSTNAME` parametru na następujący:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Ważne:** W pozycji "/etc/sudoers" Dodaj komentarz lub usuń następujące wiersze, jeśli istnieją:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
10. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Upewnij się, że Agent systemu Linux Azure jest uruchamiany podczas uruchamiania:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz można przystąpić do tworzenia nowych maszyn wirtualnych na platformie Azure za pomocą wirtualnego dysku twardego w systemie SUSE Linux. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
