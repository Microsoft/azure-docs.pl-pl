---
title: Tworzenie i przekazywanie Oracle Linux wirtualnego dysku twardego
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure zawierającego Oracle Linux system operacyjny.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 9984589b19f15ab00e895bca75c295a92a68d0fe
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557798"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Przygotuj Oracle Linux maszynę wirtualną dla platformy Azure

W tym artykule założono, że zainstalowano już Oracle Linux system operacyjny na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux uwagi dotyczące instalacji
* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Obsługa funkcji Hyper-V i platformy Azure Oracle Linux z nieprzerwanym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z systemem Red Hat.
* UEK2 firmy Oracle nie jest obsługiwana w przypadku funkcji Hyper-V i platformy Azure, ponieważ nie obejmują one wymaganych sterowników.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD.
* **Wymagana jest obsługa jądra na potrzeby instalowania systemów plików UDF.** Podczas pierwszego rozruchu na platformie Azure konfiguracja aprowizacji jest przesyłana do maszyny wirtualnej z systemem Linux za pomocą nośnika sformatowanego przez UDF, który jest dołączony do gościa. Agent systemu Azure Linux musi być w stanie zainstalować system plików UDF, aby odczytać jego konfigurację i zainicjować obsługę administracyjną maszyny wirtualnej.
* W przypadku instalowania systemu Linux zaleca się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) lub [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) , jeśli są preferowane.
* Wersje jądra systemu Linux starsze niż 2.6.37 nie obsługują architektury NUMA w funkcji Hyper-V o większych rozmiarach maszyn wirtualnych. Ten problem ma głównie wpływ na starsze dystrybucje przy użyciu wbudowanego jądra Red Hat 2.6.32 i został ustalony w Oracle Linux 6,6 i nowszych
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .
* Upewnij się, że `Addons` repozytorium jest włączone. Edytuj plik `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) lub `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) i Zmień wiersz `enabled=0` na `enabled=1` **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 i nowsze
Należy wykonać określone czynności konfiguracyjne w systemie operacyjnym, aby maszyna wirtualna mogła działać na platformie Azure.

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno dla maszyny wirtualnej.
3. Odinstaluj program NetworkManager, uruchamiając następujące polecenie:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Uwaga:** Jeśli pakiet nie jest jeszcze zainstalowany, to polecenie zakończy się niepowodzeniem z komunikatem o błędzie. Jest to oczekiwane zachowanie.
4. Utwórz plik o nazwie **Network** w `/etc/sysconfig/` katalogu zawierającym następujący tekst:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalogu zawierającym następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Upewnij się, że usługa sieciowa zacznie działać w czasie rozruchu, uruchamiając następujące polecenie:

    ```console
    # chkconfig network on
    ```

8. Zainstaluj środowisko Python-pyasn1, uruchamiając następujące polecenie:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/boot/grub/menu.lst" w edytorze tekstów i upewnij się, że jądro zawiera następujące parametry:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Dzięki temu wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
   
   Oprócz powyższych zaleca się *usunięcie* następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   `crashkernel`Opcja może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB/większej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.
10. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
11. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie. Najnowsza wersja to 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Należy pamiętać, że zainstalowanie pakietu WALinuxAgent spowoduje usunięcie pakietów sieciowych i programu NetworkManager-GNOME, jeśli nie zostały one jeszcze usunięte zgodnie z opisem w kroku 2.
12. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 i nowsze
**Zmiany w Oracle Linux 7**

Przygotowywanie maszyny wirtualnej Oracle Linux 7 na platformie Azure jest bardzo podobne do Oracle Linux 6, jednak istnieje kilka ważnych różnic:

* Platforma Azure obsługuje Oracle Linux z nieprzerwanym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z systemem Red Hat. Zaleca się Oracle Linux z UEK.
* Pakiet programu NetworkManager nie jest już w konflikcie z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zalecamy, aby nie został usunięty.
* GRUB2 jest teraz używany jako domyślne program inicjujący, więc procedura edytowania parametrów jądra została zmieniona (patrz poniżej).
* XFS jest teraz domyślnym systemem plików. W razie potrzeby można nadal używać systemu plików ext4.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Utwórz plik o nazwie **Network** w `/etc/sysconfig/` katalogu zawierającym następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalogu zawierającym następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Upewnij się, że usługa sieciowa zacznie działać w czasie rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo chkconfig network on
    ```

7. Zainstaluj pakiet Python-pyasn1, uruchamiając następujące polecenie:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zainstalować wszystkie aktualizacje:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz "/etc/default/grub" w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr, na przykład:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Spowoduje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Wyłącza również konwencje nazewnictwa dla kart sieciowych w Oracle Linux 7 z nieprzerwanym jądrem przedsiębiorstwa. Oprócz powyższych zaleca się *usunięcie* następujących parametrów:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.
   
   `crashkernel`Opcja może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB/większej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.
10. Po zakończeniu edycji "/etc/default/grub" powyżej, uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.
12. Zainstaluj agenta i zależności platformy Azure Linux:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Instalowanie usługi Cloud-init w celu obsługi aprowizacji

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. Konfiguracja wymiany nie tworzy obszaru wymiany na dysku systemu operacyjnego.

    Wcześniej Agent systemu Azure Linux został użyty automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Jest to jednak obsługiwane przez funkcję Cloud-init, ale **nie należy** używać agenta systemu Linux do formatowania dysku zasobu Utwórz plik wymiany, zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Jeśli chcesz instalować, formatować i tworzyć zamiany, możesz:
    * Przekazuj ten element jako plik w formacie Cloud-init za każdym razem, gdy tworzysz maszynę wirtualną
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
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz możesz przystąpić do tworzenia nowych maszyn wirtualnych na platformie Azure za pomocą pliku Oracle Linux. VHD. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).