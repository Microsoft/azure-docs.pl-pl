---
title: Przygotowywanie wirtualnego dysku twardego Debian systemu Linux
description: Dowiedz się, jak tworzyć obrazy Debian VHD dla wdrożeń maszyn wirtualnych na platformie Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: 5d99e6ec0beb6eaf83f9c664683c33fc1b71e3f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500550"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Przygotowywanie wirtualnego dysku twardego Debian dla platformy Azure
## <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, że zainstalowano już system operacyjny Debian Linux z pliku ISO pobranego z [witryny internetowej Debian](https://www.debian.org/distrib/) do wirtualnego dysku twardego. Istnieje wiele narzędzi do tworzenia plików VHD; Funkcja Hyper-V jest tylko jednym przykładem. Aby uzyskać instrukcje dotyczące korzystania z funkcji Hyper-V, zobacz [Instalowanie roli funkcji Hyper-v i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Uwagi dotyczące instalacji
* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Nowszy format VHDX nie jest obsługiwany na platformie Azure. Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet **convert-VHD** .
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Azure Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów. Więcej informacji można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Tworzenie Debian wirtualnych dysków twardych przy użyciu Azure-Manage
Dostępne są narzędzia do generowania wirtualnych dysków twardych Debian dla platformy Azure, takich jak skrypty [platformy Azure — zarządzanie](https://github.com/credativ/azure-manage) z [credativ](https://www.credativ.com/). Jest to zalecane rozwiązanie, a nie Tworzenie obrazu od podstaw. Aby na przykład utworzyć dysk VHD Debian 8, uruchom następujące polecenia w celu pobrania `azure-manage` narzędzia (i zależności) i uruchomienia `azure_build_image` skryptu:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Ręczne przygotowanie wirtualnego dysku twardego Debian
1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Jeśli system operacyjny został zainstalowany przy użyciu obrazu ISO, należy dodać komentarz do dowolnego wiersza odnoszącego się do " `deb cdrom` " w `/etc/apt/source.list` .

4. Edytuj `/etc/default/grub` plik i zmodyfikuj parametr **GRUB_CMDLINE_LINUX** w następujący sposób, aby uwzględnić dodatkowe parametry jądra dla platformy Azure.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Odbuduj grub i uruchom:

    ```console
    # sudo update-grub
    ```

6. Dodaj repozytoria platformy Azure Debian do/etc/apt/sources.list dla Debian 8, 9 lub 10:

    **Debian 8. x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9. x "Rozciągnij"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10. x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Zainstaluj agenta platformy Azure dla systemu Linux:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. W przypadku Debian 9 + zaleca się używanie nowego jądra chmury Debian do użycia z maszynami wirtualnymi na platformie Azure. Aby zainstalować to nowe jądro, należy najpierw utworzyć plik o nazwie/etc/apt/preferences.d/Linux.PREF z następującą zawartością:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Następnie uruchom polecenie "sudo apt-get install Linux-Image-Cloud-amd64", aby zainstalować nowe jądro w chmurze debian.

9. Wycofaj obsługę administracyjną maszyny wirtualnej i przygotuj ją do aprowizacji na platformie Azure i uruchom:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Kliknij **akcję** -> wyłączyć w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Możesz teraz przystąpić do tworzenia nowych maszyn wirtualnych na platformie Azure przy użyciu wirtualnego dysku twardego debian. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).