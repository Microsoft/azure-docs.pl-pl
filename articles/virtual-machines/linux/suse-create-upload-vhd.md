---
title: Tworzenie i przekazywanie dysku VHD systemu SUSE z systemem Linux na platformie Azure
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: fe057cb4e831fd95669ad5d5f93332a3952f16ef
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460940"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem SLES lub openSUSE dla platformy Azure


W tym artykule przyjęto założenie, że system operacyjny SUSE lub openSUSE Linux został już zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików vhd, na przykład rozwiązanie wirtualizacji, takie jak Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>Uwagi dotyczące instalacji SLES / openSUSE
* Zobacz też [Ogólne uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać więcej wskazówek dotyczących przygotowywania systemu Linux dla platformy Azure.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stały dysk VHD**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalacji systemu Linux zaleca się używanie standardowych partycji zamiast LVM (często domyślnych dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM ze sklonowanymi maszynami wirtualnymi, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej w celu rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi, jeśli jest to preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Zobacz [Uwagi dotyczące instalacji systemu Linux, aby](create-upload-generic.md#general-linux-installation-notes) uzyskać więcej informacji.

## <a name="use-suse-studio"></a>Korzystanie z SUSE Studio
[SUSE Studio](http://www.susestudio.com) może łatwo tworzyć i zarządzać swoimi SLES i otwierać obrazySUSE dla platformy Azure i funkcji Hyper-V. Jest to zalecane podejście do dostosowywania własnych obrazów SLES i openSUSE.

Jako alternatywę dla budowania własnego VHD, SUSE publikuje również byos (Bring Your Own Subscription) obrazy dla SLES w [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Przygotowanie dodatku SP4 dla systemu SUSE Linux Enterprise Server 11
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz,** aby otworzyć okno dla maszyny wirtualnej.
3. Zarejestruj swój system SUSE Linux Enterprise, aby umożliwić pobieranie aktualizacji i instalowanie pakietów.
4. Zaktualizuj system za pomocą najnowszych poprawek:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Azure Linux z repozytorium SLES:
   
        # sudo zypper install python-azure-agent
6. Sprawdź, czy waagent jest ustawiony na "on" w chkconfig, a jeśli nie, włącz go do autostartu:
   
        # sudo chkconfig waagent on
7. Sprawdź, czy usługa waagent jest uruchomiona, a jeśli nie, uruchom ją: 
   
        # sudo service waagent start
8. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz "/boot/grub/menu.lst" w edytorze tekstu i upewnij się, że domyślne jądro zawiera następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Zapewni to, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w rozwiązywaniu problemów z debugowaniem.
9. Upewnij się, że /boot/grub/menu.lst i /etc/fstab odwołują się do dysku przy użyciu jego identyfikatora UUID (by-uuid) zamiast identyfikatora dysku (by-id). 
   
    Pobierz uuid dysku
   
        # ls /dev/disk/by-uuid/
   
    Jeśli używany jest /dev/disk/by-id/, zaktualizuj zarówno /boot/grub/menu.lst i /etc/fstab z właściwą wartością by-uuid
   
    Przed zmianą
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po zmianie
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Zmodyfikuj reguły udev, aby uniknąć generowania reguł statycznych dla interfejsów Ethernet. Te reguły mogą powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Zaleca się edycję pliku "/etc/sysconfig/network/dhcp" `DHCLIENT_SET_HOSTNAME` i zmianę parametru na następujący:
    
     DHCLIENT_SET_HOSTNAME="nie"
12. W "/etc/sudoers" wyrzuć lub usuń następujące wiersze, jeśli takie istnieją:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
14. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (patrz poprzedni krok) odpowiednio zmodyfikuj następujące parametry w /etc/waagent.conf:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## UWAGA: ustaw to na wszystko, czego potrzebujesz.
15. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="prepare-opensuse-131"></a>Przygotuj openSUSE 13.1+
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz,** aby otworzyć okno dla maszyny wirtualnej.
3. Na powłoce uruchom`zypper lr`polecenie ' '. Jeśli to polecenie zwraca dane wyjściowe podobne do następującego, repozytoria są skonfigurowane zgodnie z oczekiwaniami — nie są wymagane żadne korekty (należy pamiętać, że numery wersji mogą się różnić):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Jeśli polecenie zwraca "Brak zdefiniowanych repozytoriów..." następnie użyj następujących poleceń, aby dodać te repozytoria:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Następnie można sprawdzić, czy repozytoria zostały dodane,`zypper lr`uruchamiając polecenie ' ponownie. W przypadku, gdy jedno z odpowiednich repozytoriów aktualizacji nie jest włączone, włącz go za pomocą następującego polecenia:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Zaktualizuj jądro do najnowszej dostępnej wersji:
   
        # sudo zypper up kernel-default
   
    Lub zaktualizować system ze wszystkimi najnowszymi poprawkami:
   
        # sudo zypper update
5. Zainstaluj agenta systemu Azure Linux.
   
        # sudo zypper install WALinuxAgent
6. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz "/boot/grub/menu.lst" w edytorze tekstu i upewnij się, że domyślne jądro zawiera następujące parametry:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Zapewni to, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w rozwiązywaniu problemów z debugowaniem. Ponadto usuń następujące parametry z linii rozruchowej jądra, jeśli takie istnieją:
   
     libata.atapi_enabled=0 rezerwa=0x1f0,0x8
7. Zaleca się edycję pliku "/etc/sysconfig/network/dhcp" `DHCLIENT_SET_HOSTNAME` i zmianę parametru na następujący:
   
     DHCLIENT_SET_HOSTNAME="nie"
8. **Ważne:** W "/etc/sudoers" wyrzuć lub usuń następujące wiersze, jeśli takie istnieją:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
10. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (patrz poprzedni krok) odpowiednio zmodyfikuj następujące parametry w /etc/waagent.conf:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## UWAGA: ustaw to na wszystko, czego potrzebujesz.
11. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Upewnij się, że agent systemu Azure Linux działa podczas uruchamiania:
    
        # sudo systemctl enable waagent.service
13. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz możesz używać wirtualnego dysku twardego SUSE Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
