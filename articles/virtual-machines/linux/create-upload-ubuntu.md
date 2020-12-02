---
title: Tworzenie i przekazywanie Ubuntu Linux wirtualnego dysku twardego na platformie Azure
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego Ubuntu Linux system operacyjny.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: d3bdc3d0705bd8edc70f55b8372818e60859eedd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500567"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem Ubuntu dla platformy Azure


Ubuntu teraz publikuje oficjalne wirtualne dyski twarde platformy Azure do pobrania w systemie [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Jeśli musisz utworzyć własny, wyspecjalizowany obraz Ubuntu dla platformy Azure, zamiast korzystać z procedury ręcznej poniżej, zaleca się rozpoczęcie od tych znanych, działających wirtualnych dysków twardych i dostosowanie ich w razie potrzeby. Najnowsze wersje obrazów można zawsze znaleźć w następujących lokalizacjach:

* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-amd64-disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że zainstalowano już Ubuntu Linux system operacyjny na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Ubuntu uwagi dotyczące instalacji**

* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub `Convert-VHD` polecenia cmdlet.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Nie należy konfigurować partycji wymiany ani swapfile na dysku systemu operacyjnego. Agenta aprowizacji usługi Cloud-init można skonfigurować do tworzenia pliku wymiany lub partycji wymiany na tymczasowym dysku zasobów. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Kroki ręczne
> [!NOTE]
> Przed podjęciem próby utworzenia własnego niestandardowego obrazu Ubuntu na platformie Azure Rozważ użycie wstępnie skompilowanych i przetestowanych obrazów [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.

3. Zastąp bieżące repozytoria w obrazie, aby użyć repozytorium Azure Ubuntu.

    Przed rozpoczęciem edycji zaleca się wykonanie `/etc/apt/sources.list` kopii zapasowej:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 i Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Ubuntu Azure images już używają [jądra dopasowanego do platformy Azure](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Zaktualizuj system operacyjny do najnowszego jądra dopasowanego do platformy Azure i zainstaluj narzędzia platformy Azure Linux (w tym zależności funkcji Hyper-V), uruchamiając następujące polecenia:

    Ubuntu 16,04 i Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Zmodyfikuj wiersz rozruchowy jądra dla Grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić `/etc/default/grub` , Otwórz w edytorze tekstów, znajdź zmienną o nazwie `GRUB_CMDLINE_LINUX_DEFAULT` (lub Dodaj ją w razie potrzeby) i zmodyfikuj ją w celu uwzględnienia następujących parametrów:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Zapisz i Zamknij ten plik, a następnie uruchom polecenie `sudo update-grub` . Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.

6. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.

7. Zainstaluj funkcję Cloud-init (Agent aprowizacji) i agenta systemu Azure Linux (program obsługi rozszerzeń gościa). Cloud-init przy użyciu planu sieciowego Skonfiguruj konfigurację sieci systemowej podczas aprowizacji i każdego kolejnego rozruchu.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent`Pakiet może usunąć `NetworkManager` `NetworkManager-gnome` pakiety i, jeśli są zainstalowane.

8. Usuń domyślne konfiguracje usługi Cloud-init i obskakujące artefakty planu, które mogą powodować konflikt z inicjowaniem obsługi inicjalizacji w chmurze na platformie Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Skonfiguruj funkcję Cloud-init, aby udostępnić system przy użyciu źródła danych platformy Azure:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Skonfiguruj agenta platformy Azure dla systemu Linux w celu zainicjowania obsługi przy użyciu funkcji Cloud-init. Aby uzyskać więcej informacji na temat tych opcji, zobacz [projekt WALinuxAgent](https://github.com/Azure/WALinuxAgent) .

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Oczyść artefakty i dzienniki środowiska uruchomieniowego w chmurze i platformie Azure Linux:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    > [!NOTE]
    > `sudo waagent -force -deprovision+user`Polecenie podejmie próbę wyczyszczenia systemu i nadaje się do ponownego udostępnienia. `+user`Opcja powoduje usunięcie ostatniego zainicjowanego konta użytkownika i skojarzonych danych.

    > [!WARNING]
    > Anulowanie aprowizacji za pomocą powyższego polecenia nie gwarantuje, że obraz jest czyszczony dla wszystkich poufnych informacji i jest odpowiedni do ponownej dystrybucji.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V.

14. Na platformie Azure są akceptowane wirtualne dyski twarde o stałym rozmiarze. Jeśli dysk systemu operacyjnego maszyny wirtualnej nie ma dysku VHD o stałym rozmiarze, użyj `Convert-VHD` polecenia cmdlet programu PowerShell i określ `-VHDType Fixed` opcję. Zapoznaj się z dokumentami `Convert-VHD` tutaj: [convert-VHD](/powershell/module/hyper-v/convert-vhd?view=win10-ps).


## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do korzystania z Ubuntu Linux wirtualnego dysku twardego w celu utworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).