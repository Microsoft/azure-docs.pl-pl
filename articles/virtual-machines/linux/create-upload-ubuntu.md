---
title: Tworzenie i przekazywanie Ubuntu Linux wirtualnego dysku twardego na platformie Azure
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego Ubuntu Linux system operacyjny.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066727"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem Ubuntu dla platformy Azure


Ubuntu teraz publikuje oficjalne wirtualne dyski twarde platformy Azure [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)do pobrania w systemie. Jeśli musisz utworzyć własny, wyspecjalizowany obraz Ubuntu dla platformy Azure, zamiast korzystać z procedury ręcznej poniżej, zaleca się rozpoczęcie od tych znanych, działających wirtualnych dysków twardych i dostosowanie ich w razie potrzeby. Najnowsze wersje obrazów można zawsze znaleźć w następujących lokalizacjach:

* Ubuntu 12.04/precyzyjne: [Ubuntu-12,04-Server-cloudimg-amd64-disk1. VHD. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/TRUSTe: [Ubuntu-14,04-Server-cloudimg-amd64-disk1. VHD. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-amd64-disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [Cosmic-Server-cloudimg-amd64. VHD. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że zainstalowano już Ubuntu Linux system operacyjny na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu uwagi dotyczące instalacji**

* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować tak, aby utworzył plik wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Kroki ręczne
> [!NOTE]
> Przed podjęciem próby utworzenia własnego niestandardowego obrazu Ubuntu na platformie Azure Rozważ użycie wstępnie skompilowanych i przetestowanych obrazów [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.

3. Zastąp bieżące repozytoria w obrazie, aby użyć repozytorium Azure Ubuntu. Kroki różnią się nieco w zależności od wersji Ubuntu.
   
    Przed rozpoczęciem edycji `/etc/apt/sources.list`zaleca się wykonanie kopii zapasowej:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu obrazy platformy Azure są teraz następujące po jądrze *włączenia sprzętu* (HWE). Aby zaktualizować system operacyjny do najnowszego jądra, należy uruchomić następujące polecenia:

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Zobacz również:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Zmodyfikuj wiersz rozruchowy jądra dla Grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, `/etc/default/grub` Otwórz w edytorze tekstów, znajdź zmienną o nazwie `GRUB_CMDLINE_LINUX_DEFAULT` (lub Dodaj ją w razie potrzeby) i zmodyfikuj ją w celu uwzględnienia następujących parametrów:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Zapisz i Zamknij ten plik, a następnie uruchom `sudo update-grub`polecenie. Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.

6. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.

7. Zainstaluj agenta platformy Azure dla systemu Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  `walinuxagent` Pakiet może usunąć pakiety `NetworkManager` i `NetworkManager-gnome` , jeśli są zainstalowane.


1. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="references"></a>Dokumentacja
[Ubuntu Włączanie sprzętu (HWE)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do korzystania z Ubuntu Linux wirtualnego dysku twardego w celu utworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).

