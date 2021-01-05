---
title: Tworzenie i przekazywanie wirtualnego dysku twardego w systemie SUSE Linux na platformie Azure
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego system operacyjny SUSE Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 36af60082c575dfb19e71710fbdd8e3bf181bf96
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896229"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej z systemem SLES lub openSUSE dla platformy Azure


W tym artykule przyjęto założenie, że już zainstalowano system operacyjny SUSE lub openSUSE Linux na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Uwagi dotyczące instalacji SLES/openSUSE
* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , jeśli są preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować tak, aby utworzył plik wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>Korzystanie z programu SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) może łatwo tworzyć obrazy SLES i openSUSE oraz zarządzać nimi na platformie Azure i funkcji Hyper-V. Jest to zalecane podejście do dostosowywania własnych obrazów SLES i openSUSE.

Alternatywą dla tworzenia własnego wirtualnego dysku twardego jest również opublikowanie BYOS (przenoszenie własnych subskrypcji) dla SLES na [maszynie wirtualnej magazynu](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Przygotuj SUSE Linux Enterprise Server platformy Azure
1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.
3. Zarejestruj system SUSE Linux Enterprise, aby zezwolić na pobieranie aktualizacji i instalowanie pakietów.
4. Zaktualizuj system przy użyciu najnowszych poprawek:

    ```console
    # sudo zypper update
    ```
    
5. Zainstaluj agenta systemu Linux platformy Azure i chmurę — init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Włącz waagent & Cloud-init, aby rozpocząć przy rozruchu

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Aktualizacja waagent i konfiguracja usługi Cloud-init

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Edytuj plik/etc/default/grub, aby upewnić się, że dzienniki konsoli są wysyłane do portu szeregowego, a następnie zaktualizuj główny plik konfiguracji za pomocą grub2-mkconfig-o/Boot/grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
    
9. Upewnij się, że plik/etc/fstab odwołuje się do dysku przy użyciu identyfikatora UUID (według identyfikatora UUID)
         
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

13. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. Zwykle jest to ustawienie domyślne.

14. Konfiguracja wymiany
 
    Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.

    Wcześniej Agent systemu Azure Linux został użyty automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Jest to jednak obsługiwane przez funkcję Cloud-init, ale **nie należy** używać agenta systemu Linux do formatowania dysku zasobu Utwórz plik wymiany, zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Jeśli chcesz instalować, formatować i tworzyć zamiany, możesz:
    * Przekazuj ten element jako plik w formacie Cloud-init za każdym razem, gdy tworzysz maszynę wirtualną.
    * Użyj dyrektywy rozszerzania w chmurze do obrazu, który będzie za każdym razem tworzony przez maszynę wirtualną:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

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