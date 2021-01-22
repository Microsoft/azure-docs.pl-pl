---
title: Tworzenie i przekazywanie wirtualnego dysku twardego systemu Linux opartego na CentOS
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure, który zawiera system operacyjny Linux oparty na CentOS.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 018df112c344fc08f2839752fcda3dfd9370af4e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682595"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej bazującej na systemie CentOS dla platformy Azure

Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure, który zawiera system operacyjny Linux oparty na CentOS.

* [Przygotuj maszynę wirtualną z systemem CentOS 6. x dla platformy Azure](#centos-6x)
* [Przygotuj maszynę wirtualną z systemem CentOS 7.0 lub nowszym dla platformy Azure](#centos-70)


## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że już zainstalowano system operacyjny CentOS (lub podobny pochodny) na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików VHD, na przykład rozwiązanie wirtualizacji, takie jak funkcja Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**CentOS uwagi dotyczące instalacji**

* Aby uzyskać więcej porad dotyczących przygotowywania systemu Linux dla platformy Azure, zobacz również [Ogólne informacje o instalacji](create-upload-generic.md#general-linux-installation-notes) w systemie Linux.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stałego dysku VHD**.  Dysk można przekonwertować na format VHD przy użyciu Menedżera funkcji Hyper-V lub polecenia cmdlet Convert-VHD. Jeśli używasz VirtualBox, oznacza to, że wybranie **stałego rozmiaru** w przeciwieństwie do domyślnego przydzielanego dynamicznie podczas tworzenia dysku.
* W przypadku instalowania systemu Linux *zaleca* się używanie partycji standardowych zamiast LVM (często jest to ustawienie domyślne dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM z klonowanymi maszynami wirtualnymi, szczególnie w przypadku, gdy kiedykolwiek konieczne jest dołączenie dysku systemu operacyjnego do innej identycznej maszyny wirtualnej w celu rozwiązywania problemów. Na dyskach danych można używać [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) lub [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) .
* **Wymagana jest obsługa jądra na potrzeby instalowania systemów plików UDF.** Podczas pierwszego rozruchu na platformie Azure konfiguracja aprowizacji jest przesyłana do maszyny wirtualnej z systemem Linux za pomocą nośnika sformatowanego przez UDF, który jest dołączony do gościa. Agent systemu Azure Linux musi być w stanie zainstalować system plików UDF, aby odczytać jego konfigurację i zainicjować obsługę administracyjną maszyny wirtualnej.
* Wersje jądra systemu Linux poniżej 2.6.37 nie obsługują architektury NUMA w funkcji Hyper-V o większych rozmiarach maszyn wirtualnych. Ten problem ma głównie wpływ na starsze dystrybucje przy użyciu wbudowanego jądra Red Hat 2.6.32 i został ustalony w RHEL 6,6 (jądro-2.6.32-504). Systemy z uruchomionymi niestandardowymi jądrami starszymi niż 2.6.37 lub jądrem opartym na RHEL starszym niż 2.6.32-504 muszą ustawić parametr rozruchu w `numa=off` wierszu polecenia jądra w grub. conf. Aby uzyskać więcej informacji, zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Aby uzyskać więcej informacji, zobacz [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.

3. W CentOS 6, usługa NetworkManager może zakłócać działanie Agenta platformy Azure w systemie Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Utwórz lub edytuj plik `/etc/sysconfig/network` i Dodaj następujący tekst:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Utwórz lub edytuj plik `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Upewnij się, że usługa sieciowa zacznie działać w czasie rozruchu, uruchamiając następujące polecenie:

    ```bash
    sudo chkconfig network on
    ```

8. Jeśli chcesz użyć dublowanych OpenLogic, które są hostowane w centrach danych platformy Azure, Zastąp `/etc/yum.repos.d/CentOS-Base.repo` plik poniższymi repozytoriami.  Spowoduje to również dodanie repozytorium **[OpenLogic]** zawierającego dodatkowe pakiety, takie jak Agent platformy Azure dla systemu Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > W pozostałej części tego przewodnika przyjęto założenie, że używasz co najmniej `[openlogic]` repozytorium, które zostanie użyte do zainstalowania poniższego agenta systemu Linux.

9. Dodaj następujący wiersz do/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zaktualizować system przy użyciu najnowszych pakietów:

    ```bash
    yum clean all
    ```

    Jeśli nie tworzysz obrazu dla starszej wersji programu CentOS, zaleca się zaktualizowanie wszystkich pakietów do najnowszych:

    ```bash
    sudo yum -y update
    ```

    Po uruchomieniu tego polecenia może być wymagane ponowne uruchomienie komputera.

11. Obowiązkowe Zainstaluj sterowniki dla usług integracji systemu Linux (LIS).

    > [!IMPORTANT]
    > Ten krok jest **wymagany** w przypadku systemów CentOS 6,3 i starszych oraz opcjonalnych w nowszych wersjach.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Alternatywnie można wykonać instrukcje instalacji ręcznej na [stronie pobierania lis](https://www.microsoft.com/download/details.aspx?id=55106) , aby zainstalować RPM na maszynie wirtualnej.

12. Zainstaluj agenta i zależności platformy Azure dla systemu Linux. Uruchom i Włącz usługę waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Pakiet WALinuxAgent usunie pakiety NetworkManager i WorkManager-GNOME, jeśli nie zostały jeszcze usunięte zgodnie z opisem w kroku 3.

13. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/boot/grub/menu.lst` w edytorze tekstów i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Spowoduje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.

    Oprócz powyższych zaleca się *usunięcie* następujących parametrów:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.  `crashkernel`Opcja może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB/większej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.

    > [!Important]
    > CentOS 6,5 i wcześniejszy muszą także ustawiać parametr jądra `numa=off` . Zobacz Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zwykle jest to ustawienie domyślne.

15. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po zainicjowaniu obsługi administracyjnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem *tymczasowym* i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure (zobacz poprzedni krok) zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.



## <a name="centos-70"></a>CentOS 7.0 +

**Zmiany w CentOS 7 (i podobnych pochodnych)**

Przygotowywanie maszyny wirtualnej CentOS 7 na platformie Azure jest bardzo podobne do CentOS 6, jednak istnieje kilka ważnych różnic:

* Pakiet programu NetworkManager nie jest już w konflikcie z agentem systemu Linux platformy Azure. Ten pakiet jest instalowany domyślnie i zalecamy, aby nie został usunięty.
* GRUB2 jest teraz używany jako domyślne program inicjujący, więc procedura edytowania parametrów jądra została zmieniona (patrz poniżej).
* XFS jest teraz domyślnym systemem plików. W razie potrzeby można nadal używać systemu plików ext4.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

2. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.

3. Utwórz lub edytuj plik `/etc/sysconfig/network` i Dodaj następujący tekst:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Utwórz lub edytuj plik `/etc/sysconfig/network-scripts/ifcfg-eth0` i Dodaj następujący tekst:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Zmodyfikuj reguły udev, aby uniknąć generowania statycznych reguł dla interfejsów sieci Ethernet. Te reguły mogą spowodować problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Jeśli chcesz użyć dublowanych OpenLogic, które są hostowane w centrach danych platformy Azure, Zastąp `/etc/yum.repos.d/CentOS-Base.repo` plik poniższymi repozytoriami.  Spowoduje to również dodanie repozytorium **[OpenLogic]** zawierającego pakiety dla agenta platformy Azure dla systemu Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > W pozostałej części tego przewodnika przyjęto założenie, że używasz co najmniej `[openlogic]` repozytorium, które zostanie użyte do zainstalowania poniższego agenta systemu Linux.

7. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zainstalować wszystkie aktualizacje:

    ```bash
    sudo yum clean all
    ```

    Jeśli nie tworzysz obrazu dla starszej wersji programu CentOS, zaleca się zaktualizowanie wszystkich pakietów do najnowszych:

    ```bash
    sudo yum -y update
    ```

    Po uruchomieniu tego polecenia może być wymagane ponowne uruchomienie komputera.

8. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/etc/default/grub` w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr, na przykład:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Spowoduje to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, który może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Wyłącza również nowe konwencje nazewnictwa CentOS 7 dla kart sieciowych. Oprócz powyższych zaleca się *usunięcie* następujących parametrów:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. `crashkernel`Opcja może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB/większej, co może powodować problemy w mniejszych rozmiarach maszyn wirtualnych.

9. Po zakończeniu edycji `/etc/default/grub` na powyższe Uruchom następujące polecenie, aby skompilować konfigurację grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. W przypadku kompilowania obrazu z programu **VMware, VirtualBox lub KVM:** upewnij się, że sterowniki funkcji Hyper-V znajdują się w initramfs:

    Edytuj `/etc/dracut.conf` , Dodaj zawartość:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Kompiluj ponownie initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Zainstaluj agenta systemu Linux i zależności dla rozszerzeń maszyny wirtualnej platformy Azure:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Instalowanie usługi Cloud-init w celu obsługi aprowizacji

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


13. Konfiguracja wymiany nie tworzy obszaru wymiany na dysku systemu operacyjnego.

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

14. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

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

15. Kliknij **akcję-> wyłączyć** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki

Możesz teraz przystąpić do tworzenia nowych maszyn wirtualnych na platformie Azure za pomocą wirtualnego dysku twardego z systemem CentOS Linux. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).