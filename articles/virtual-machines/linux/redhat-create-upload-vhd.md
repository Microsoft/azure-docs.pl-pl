---
title: Tworzenie i przekazywanie Red Hat Enterprise Linux wirtualnego dysku twardego do użycia na platformie Azure
description: Zapoznaj się z tematem tworzenie i przekazywanie wirtualnego dysku twardego (VHD) platformy Azure, który zawiera system operacyjny Red Hat Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 28f82b641216dcd77b650b066c72bef4c4470dd2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098966"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej bazującej na systemie Red Hat dla platformy Azure

W tym artykule dowiesz się, jak przygotować maszynę wirtualną Red Hat Enterprise Linux (RHEL) do użycia na platformie Azure. Wersje RHEL omówione w tym artykule to 6.7 + i 7.1 +. Funkcja hypervisor dla przygotowania, która jest omówiona w tym artykule, to funkcja Hyper-V, maszyna wirtualna oparta na jądrze (KVM) i oprogramowanie VMware. Aby uzyskać więcej informacji na temat wymagań dotyczących uprawnień dla uczestnictwa w programie w ramach programu dostępu do chmury Red Hat, zobacz [witrynę sieci Web dostępu do chmury w systemie Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) i [RHEL na platformie Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Aby poznać sposoby automatyzowania tworzenia obrazów RHEL, zobacz [Azure Image Builder](../image-builder-overview.md).

## <a name="hyper-v-manager"></a>Menedżer funkcji Hyper-V

W tej sekcji pokazano, jak przygotować maszynę wirtualną z systemem [RHEL 6](#rhel-6-using-hyper-v-manager), [RHEL 7](#rhel-7-using-hyper-v-manager)lub [RHEL 8](#rhel-8-using-hyper-v-manager) przy użyciu Menedżera funkcji Hyper-V.

### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, że plik ISO został już pobrany z witryny sieci Web firmy Red Hat i został zainstalowany obraz RHEL na wirtualnym dysku twardym (VHD). Aby uzyskać więcej informacji na temat sposobu instalowania obrazu systemu operacyjnego za pomocą Menedżera funkcji Hyper-V, zobacz [Instalowanie roli funkcji Hyper-v i Konfigurowanie maszyny wirtualnej](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**RHEL uwagi dotyczące instalacji**

* Platforma Azure nie obsługuje formatu VHDX. Platforma Azure obsługuje tylko stały dysk VHD. Można użyć Menedżera funkcji Hyper-V do przekonwertowania dysku na format VHD lub można użyć polecenia cmdlet Convert-VHD. Jeśli używasz VirtualBox, wybierz **ustalony rozmiar** w przeciwieństwie do domyślnej opcji przydzielanej dynamicznie podczas tworzenia dysku.
* Platforma Azure obsługuje Gen1 (BIOS Boot) & Gen2 (UEFI boot) maszyn wirtualnych.
* Maksymalny rozmiar dozwolony dla wirtualnego dysku twardego to 1 023 GB.
* Menedżer woluminów logicznych (LVM) jest obsługiwany i może być używany na dysku systemu operacyjnego lub dyskach z danymi w usłudze Azure Virtual Machines. Jednak ogólnie zaleca się używanie partycji standardowych na dysku systemu operacyjnego, a nie LVM. Ta metoda pozwala uniknąć konfliktów nazw LVM z sklonowanymi maszynami wirtualnymi, szczególnie jeśli kiedykolwiek konieczna jest dołączenie dysku systemu operacyjnego do innej identycznej maszyny wirtualnej w celu rozwiązywania problemów. Zobacz również dokumentację  [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) i [macierz RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) .
* **Wymagana jest obsługa jądra na potrzeby instalowania systemów plików (UDF) w formacie dysków uniwersalnych**. Podczas pierwszego rozruchu na platformie Azure nośnik sformatowany w formacie UDF, który jest dołączony do gościa, przekazuje konfigurację aprowizacji do maszyny wirtualnej z systemem Linux. Agent systemu Azure Linux musi być w stanie zainstalować system plików UDF, aby odczytać jego konfigurację i zainicjować obsługę administracyjną maszyny wirtualnej bez tego, Inicjowanie obsługi zakończy się niepowodzeniem.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Więcej informacji na ten temat można znaleźć w poniższych krokach.

* Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Więcej szczegółów można znaleźć w poniższych krokach. Aby uzyskać więcej informacji, zobacz także [uwagi dotyczące instalacji systemu Linux](create-upload-generic.md#general-linux-installation-notes) .

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 przy użyciu Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.

1. W RHEL 6, usługa NetworkManager może zakłócać działanie Agenta platformy Azure w systemie Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network` plik i Dodaj następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Przenieś (lub Usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej w Microsoft Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo chkconfig network on
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/boot/grub/menu.lst` w edytorze tekstów i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
    
    Ponadto zalecamy usunięcie następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego.  W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej. Ta konfiguracja może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.


1. Upewnij się, że serwer Secure Shell (SSH) jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co zazwyczaj jest ustawieniem domyślnym. Zmodyfikuj sshd_config/etc/ssh/w taki sposób, aby obejmował następujący wiersz:

    ```config
    ClientAliveInterval 180
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    Zainstalowanie pakietu WALinuxAgent powoduje usunięcie pakietów i WorkManager-GNOME, jeśli nie zostały one jeszcze usunięte w kroku 3.

1. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem tymczasowym i może zostać opróżniony w przypadku anulowania aprowizacji maszyny wirtualnej. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku zmodyfikuj odpowiednio następujące parametry w/etc/waagent.conf:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager unregister
    ```

1. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Kliknij pozycję **Akcja**  >  **Zamknij** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 za pomocą Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.

1. Utwórz lub Edytuj `/etc/sysconfig/network` plik i Dodaj następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo systemctl enable network
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/etc/default/grub` w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr. Na przykład:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego i umożliwiają interakcję z konsolą szeregową, która może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Ta konfiguracja wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub` Uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > W przypadku przekazywania maszyny wirtualnej z obsługą interfejsu UEFI polecenie aktualizacji grub ma wartość `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co jest zazwyczaj ustawieniem domyślnym. Zmodyfikuj, `/etc/ssh/sshd_config` Aby uwzględnić następujący wiersz:

    ```config
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Azure Linux, Cloud-init i innych niezbędnych narzędzi, uruchamiając następujące polecenie:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Skonfiguruj obsługę inicjowania obsługi administracyjnej w programie Cloud-init:

    1. Skonfiguruj waagent dla usługi Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Jeśli migrujesz konkretną maszynę wirtualną i nie chcesz tworzyć uogólnionego obrazu, ustaw `Provisioning.Agent=disabled` `/etc/waagent.conf` konfigurację.
    
    1. Konfiguruj instalacje:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Konfiguruj źródło danych platformy Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. W przypadku skonfigurowania Usuń istniejące swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Skonfiguruj rejestrowanie w usłudze Cloud-init:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Konfiguracja wymiany nie tworzy obszaru wymiany na dysku systemu operacyjnego.

    Wcześniej Agent systemu Azure Linux został użyty automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Jest to jednak obsługiwane przez funkcję Cloud-init, ale **nie należy** używać agenta systemu Linux do formatowania dysku zasobu Utwórz plik wymiany, zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Jeśli chcesz wyrejestrować subskrypcję, uruchom następujące polecenie:

    ```console
    # sudo subscription-manager unregister
    ```

1. Anulowanie aprowizacji

    Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    > [!CAUTION]
    > Jeśli migrujesz konkretną maszynę wirtualną i nie chcesz tworzyć uogólnionego obrazu, pomiń krok anulowania obsługi. Uruchomienie polecenia `waagent -force -deprovision` spowoduje, że maszyna źródłowa nie będzie użyteczna, ten krok będzie przeznaczony tylko do tworzenia uogólnionego obrazu.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Kliknij pozycję **Akcja**  >  **Zamknij** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 przy użyciu Menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij przycisk **Połącz** , aby otworzyć okno konsoli dla maszyny wirtualnej.

1. Upewnij się, że usługa Menedżera sieci rozpocznie się w czasie rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Skonfiguruj interfejs sieciowy do automatycznego uruchamiania przy rozruchu i używania protokołu DHCP:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure i włączyć konsolę seryjną. 

    1. Usuń bieżące parametry GRUB:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Edytuj `/etc/default/grub` w edytorze tekstów i Dodaj następujący parametrów:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego i umożliwiają interakcję z konsolą szeregową, która może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Ta konfiguracja wyłącza również nowe konwencje nazewnictwa dla kart sieciowych.
   
   1. Ponadto zalecamy usunięcie następujących parametrów:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub` Uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    W przypadku maszyny wirtualnej z obsługą interfejsu UEFI Uruchom następujące polecenie:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co jest zazwyczaj ustawieniem domyślnym. Zmodyfikuj, `/etc/ssh/sshd_config` Aby uwzględnić następujący wiersz:

    ```config
    ClientAliveInterval 180
    ```

1. Zainstaluj agenta systemu Azure Linux, Cloud-init i innych niezbędnych narzędzi, uruchamiając następujące polecenie:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Skonfiguruj obsługę inicjowania obsługi administracyjnej w programie Cloud-init:

    1. Skonfiguruj waagent dla usługi Cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Jeśli migrujesz konkretną maszynę wirtualną i nie chcesz tworzyć uogólnionego obrazu, ustaw `Provisioning.Agent=disabled` `/etc/waagent.conf` konfigurację.
    
    1. Konfiguruj instalacje:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Konfiguruj źródło danych platformy Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. W przypadku skonfigurowania Usuń istniejące swapfile:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Skonfiguruj rejestrowanie w usłudze Cloud-init:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Konfiguracja wymiany nie tworzy obszaru wymiany na dysku systemu operacyjnego.

    Wcześniej Agent systemu Azure Linux został użyty automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Jest to jednak obsługiwane przez funkcję Cloud-init, ale **nie należy** używać agenta systemu Linux do formatowania dysku zasobu Utwórz plik wymiany, zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
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
1. Jeśli chcesz wyrejestrować subskrypcję, uruchom następujące polecenie:

    ```console
    # sudo subscription-manager unregister
    ```

1. Anulowanie aprowizacji

    Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Jeśli migrujesz konkretną maszynę wirtualną i nie chcesz tworzyć uogólnionego obrazu, pomiń krok anulowania obsługi. Uruchomienie polecenia `waagent -force -deprovision` spowoduje, że maszyna źródłowa nie będzie użyteczna, ten krok będzie przeznaczony tylko do tworzenia uogólnionego obrazu.


1. Kliknij pozycję **Akcja**  >  **Zamknij** w Menedżerze funkcji Hyper-V. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.


## <a name="kvm"></a>KVM

W tej sekcji pokazano, jak za pomocą przełącznika KVM przygotować [RHEL 6](#rhel-6-using-kvm) lub [RHEL 7](#rhel-7-using-kvm) dystrybucji w celu przekazania go do platformy Azure. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 przy użyciu przełącznika KVM

1. Pobierz obraz KVM RHEL 6 z witryny Red Hat.

1. Ustaw hasło główne.

    Wygeneruj zaszyfrowane hasło i skopiuj dane wyjściowe polecenia:

    ```console
    # openssl passwd -1 changeme
    ```

    Ustaw hasło główne za pomocą guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Zmień drugie pole użytkownika root z "!!" na zaszyfrowane hasło.

1. Utwórz maszynę wirtualną w programie KVM z obrazu QCOW2. Ustaw typ dysku na **QCOW2**, a następnie ustaw model urządzenia wirtualnego interfejsu sieciowego na **VirtIO**. Następnie uruchom maszynę wirtualną i zaloguj się jako element główny.

1. Utwórz lub Edytuj `/etc/sysconfig/network` plik i Dodaj następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Przenieś (lub Usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # chkconfig network on
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby wykonać tę konfigurację, Otwórz `/boot/grub/menu.lst` w edytorze tekstów i upewnij się, że domyślne jądro zawiera następujące parametry:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem.
    
    Ponadto zalecamy usunięcie następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.


1. Dodawanie modułów funkcji Hyper-V do initramfs:  

    Edytuj `/etc/dracut.conf` i Dodaj następującą zawartość:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs ponownego kompilowania:

    ```config-conf
    # dracut -f -v
    ```

1. Odinstalowywanie usługi Cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

    ```console
    # chkconfig sshd on
    ```

    Zmodyfikuj sshd_config/etc/ssh/, aby uwzględnić następujące wiersze:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku zmodyfikuj odpowiednio następujące parametry w **/etc/waagent.conf** :

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

    ```console-conf
    # subscription-manager unregister
    ```

1. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

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

1. Zamknij maszynę wirtualną w programie KVM.

1. Przekonwertuj obraz QCOW2 na format VHD.

    > [!NOTE]
    > W wersjach QEMU-IMG występuje znany błąd >= 2.2.1, który powoduje niewłaściwie sformatowany dysk VHD. Problem został rozwiązany w QEMU 2,6. Zaleca się użycie opcji QEMU-IMG 2.2.0 lub Lower lub aktualizację do wersji 2,6 lub nowszej. Dokumentacja: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Najpierw przekonwertuj obraz do formatu RAW:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Upewnij się, że rozmiar nieprzetworzonego obrazu jest wyrównany do 1 MB. W przeciwnym razie Zaokrąglij rozmiar w górę, aby wyrównać 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Przekonwertuj dysk nieprzetworzony na stały dysk VHD:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Lub z QEMU w wersji **2.6 +** include `force_size` :

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 przy użyciu przełącznika KVM

1. Pobierz obraz KVM RHEL 7 z witryny Red Hat. Ta procedura powoduje użycie RHEL 7 jako przykładu.

1. Ustaw hasło główne.

    Wygeneruj zaszyfrowane hasło i skopiuj dane wyjściowe polecenia:

    ```console
    # openssl passwd -1 changeme
    ```

    Ustaw hasło główne za pomocą guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Zmień drugie pole użytkownika root z "!!" na zaszyfrowane hasło.

1. Utwórz maszynę wirtualną w programie KVM z obrazu QCOW2. Ustaw typ dysku na **QCOW2**, a następnie ustaw model urządzenia wirtualnego interfejsu sieciowego na **VirtIO**. Następnie uruchom maszynę wirtualną i zaloguj się jako element główny.

1. Utwórz lub Edytuj `/etc/sysconfig/network` plik i Dodaj następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo systemctl enable network
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby wykonać tę konfigurację, Otwórz `/etc/default/grub` w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr. Na przykład:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   To polecenie gwarantuje również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Polecenie wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych. Ponadto zalecamy usunięcie następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub` Uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Dodawanie modułów funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf` i Dodaj zawartość:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs ponownego kompilowania:

    ```config-conf
    # dracut -f -v
    ```

1. Odinstalowywanie usługi Cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

    ```console
    # systemctl enable sshd
    ```

    Zmodyfikuj sshd_config/etc/ssh/, aby uwzględnić następujące wiersze:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```console
    # yum install WALinuxAgent
    ```

    Włącz usługę waagent:

    ```console
    # systemctl enable waagent.service
    ```

1. Install Cloud-init postępuj zgodnie z instrukcjami w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", Krok 12, "Zainstaluj chmurę w celu obsługi aprowizacji".

1. Konfiguracja wymiany 

    Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    Wykonaj kroki opisane w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", krok 13, "Konfiguracja wymiany"


1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

    ```console
    # subscription-manager unregister
    ```


1. Anulowanie aprowizacji

    Wykonaj kroki opisane w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", krok 15, "Anulowanie aprowizacji"

1. Zamknij maszynę wirtualną w programie KVM.

1. Przekonwertuj obraz QCOW2 na format VHD.

    > [!NOTE]
    > W wersjach QEMU-IMG występuje znany błąd >= 2.2.1, który powoduje niewłaściwie sformatowany dysk VHD. Problem został rozwiązany w QEMU 2,6. Zaleca się użycie opcji QEMU-IMG 2.2.0 lub Lower lub aktualizację do wersji 2,6 lub nowszej. Dokumentacja: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Najpierw przekonwertuj obraz do formatu RAW:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Upewnij się, że rozmiar nieprzetworzonego obrazu jest wyrównany do 1 MB. W przeciwnym razie Zaokrąglij rozmiar w górę, aby wyrównać 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Przekonwertuj dysk nieprzetworzony na stały dysk VHD:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Lub z QEMU w wersji **2.6 +** include `force_size` :

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

W tej sekcji pokazano, jak przygotować [RHEL 6](#rhel-6-using-vmware) lub [RHEL 7](#rhel-6-using-vmware)  dystrybucji z programu VMware.

### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji założono, że zainstalowano już maszynę wirtualną RHEL w oprogramowaniu VMware. Aby uzyskać szczegółowe informacje na temat sposobu instalowania systemu operacyjnego w oprogramowaniu VMware, zobacz [Przewodnik instalacji systemu operacyjnego gościa programu VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* W przypadku instalowania systemu operacyjnego Linux zaleca się używanie partycji standardowych zamiast LVM, co jest często domyślne dla wielu instalacji. Pozwoli to uniknąć konfliktów nazw LVM z sklonowaną maszyną wirtualną, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek trzeba dołączyć do innej maszyny wirtualnej w celu rozwiązywania problemów. LVM lub RAID można używać na dyskach danych, jeśli są preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agenta systemu Linux można skonfigurować w taki sposób, aby utworzyć plik wymiany na tymczasowym dysku zasobów. Więcej informacji na ten temat można znaleźć w kolejnych krokach.
* Podczas tworzenia wirtualnego dysku twardego wybierz opcję **przechowuj dysk wirtualny jako pojedynczy plik**.

### <a name="rhel-6-using-vmware"></a>RHEL 6 przy użyciu oprogramowania VMware
1. W RHEL 6, usługa NetworkManager może zakłócać działanie Agenta platformy Azure w systemie Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Utwórz plik o nazwie **Network** w katalogu/etc/sysconfig/, który zawiera następujący tekst:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Przenieś (lub Usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo chkconfig network on
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/etc/default/grub` w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr. Na przykład:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Ponadto zalecamy usunięcie następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.

1. Dodawanie modułów funkcji Hyper-V do initramfs:

    Edytuj `/etc/dracut.conf` i Dodaj następującą zawartość:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs ponownego kompilowania:

    ```config-cong
    # dracut -f -v
    ```

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co jest zazwyczaj ustawieniem domyślnym. Zmodyfikuj, `/etc/ssh/sshd_config` Aby uwzględnić następujący wiersz:

    ```config
    ClientAliveInterval 180
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux może automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobu dołączonego do maszyny wirtualnej po aprowizacji maszyny wirtualnej na platformie Azure. Należy pamiętać, że lokalny dysk zasobów jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie anulowana. Po zainstalowaniu agenta systemu Linux platformy Azure w poprzednim kroku zmodyfikuj odpowiednio następujące parametry `/etc/waagent.conf` :

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Wyrejestruj subskrypcję (w razie potrzeby), uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager unregister
    ```

1. Uruchom następujące polecenia, aby anulować obsługę administracyjną maszyny wirtualnej i przygotować ją do aprowizacji na platformie Azure:

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

1. Zamknij maszynę wirtualną i przekonwertuj plik VMDK na plik VHD.

    > [!NOTE]
    > W wersjach QEMU-IMG występuje znany błąd >= 2.2.1, który powoduje niewłaściwie sformatowany dysk VHD. Problem został rozwiązany w QEMU 2,6. Zaleca się użycie opcji QEMU-IMG 2.2.0 lub Lower lub aktualizację do wersji 2,6 lub nowszej. Dokumentacja: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Najpierw przekonwertuj obraz do formatu RAW:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Upewnij się, że rozmiar nieprzetworzonego obrazu jest wyrównany do 1 MB. W przeciwnym razie Zaokrąglij rozmiar w górę, aby wyrównać 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Przekonwertuj dysk nieprzetworzony na stały dysk VHD:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Lub z QEMU w wersji **2.6 +** include `force_size` :

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 przy użyciu oprogramowania VMware
1. Utwórz lub Edytuj `/etc/sysconfig/network` plik i Dodaj następujący tekst:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Utwórz lub Edytuj `/etc/sysconfig/network-scripts/ifcfg-eth0` plik i Dodaj następujący tekst:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Upewnij się, że usługa sieciowa zacznie działać przy rozruchu, uruchamiając następujące polecenie:

    ```console
    # sudo systemctl enable network
    ```

1. Zarejestruj swoją subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Zmodyfikuj wiersz rozruchowy jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra platformy Azure. Aby to zrobić, Otwórz `/etc/default/grub` w edytorze tekstów i edytuj `GRUB_CMDLINE_LINUX` parametr. Na przykład:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Ta konfiguracja zapewnia również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w pomocy technicznej platformy Azure z problemami z debugowaniem. Wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych. Ponadto zalecamy usunięcie następujących parametrów:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    Rozruch graficzny i cichy nie są przydatne w środowisku chmury, w którym wszystkie dzienniki mają być wysyłane do portu szeregowego. W `crashkernel` razie potrzeby można pozostawić opcję skonfigurowaną. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci w maszynie wirtualnej o 128 MB lub więcej, co może być przyczyną problemów w mniejszych rozmiarach maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub` Uruchom następujące polecenie, aby ponownie skompilować konfigurację grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Dodaj moduły funkcji Hyper-V do initramfs.

    Edytuj `/etc/dracut.conf` , Dodaj zawartość:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs ponownego kompilowania:

    ```console
    # dracut -f -v
    ```

1. Upewnij się, że serwer SSH został zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. To ustawienie jest zwykle domyślnie. Zmodyfikuj, `/etc/ssh/sshd_config` Aby uwzględnić następujący wiersz:

    ```config
    ClientAliveInterval 180
    ```

1. Pakiet WALinuxAgent `WALinuxAgent-<version>` został wypychany do repozytorium rozszerzeń Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Zainstaluj agenta systemu Linux platformy Azure, uruchamiając następujące polecenie:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Instalowanie usługi Cloud-init

    Postępuj zgodnie z instrukcjami w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", Krok 12, "Zainstaluj chmurę w celu obsługi aprowizacji".

1. Konfiguracja wymiany

    Nie należy tworzyć obszaru wymiany na dysku systemu operacyjnego.
    Wykonaj kroki opisane w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", krok 13, "Konfiguracja wymiany"

1. Jeśli chcesz wyrejestrować subskrypcję, uruchom następujące polecenie:

    ```console
    # sudo subscription-manager unregister
    ```

1. Anulowanie aprowizacji

    Wykonaj kroki opisane w temacie "przygotowanie maszyny wirtualnej RHEL 7 z Menedżera funkcji Hyper-V", krok 15, "Anulowanie aprowizacji"


1. Zamknij maszynę wirtualną i przekonwertuj plik VMDK na format VHD.

    > [!NOTE]
    > W wersjach QEMU-IMG występuje znany błąd >= 2.2.1, który powoduje niewłaściwie sformatowany dysk VHD. Problem został rozwiązany w QEMU 2,6. Zaleca się użycie opcji QEMU-IMG 2.2.0 lub Lower lub aktualizację do wersji 2,6 lub nowszej. Dokumentacja: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Najpierw przekonwertuj obraz do formatu RAW:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Upewnij się, że rozmiar nieprzetworzonego obrazu jest wyrównany do 1 MB. W przeciwnym razie Zaokrąglij rozmiar w górę, aby wyrównać 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Przekonwertuj dysk nieprzetworzony na stały dysk VHD:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Lub z QEMU w wersji **2.6 +** include `force_size` :

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Plik Stwórz

W tej sekcji przedstawiono sposób przygotowania RHEL 7 dystrybucji z obrazu ISO przy użyciu pliku Stwórz.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 z pliku Stwórz

1.  Utwórz plik Stwórz zawierający poniższą zawartość i Zapisz plik. Szczegółowe informacje o instalacji Stwórz można znaleźć w [podręczniku instalacji Stwórz](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Umieść plik Stwórz, do którego system instalacyjny może uzyskać do niego dostęp.

1. W Menedżerze funkcji Hyper-V Utwórz nową maszynę wirtualną. Na stronie **Podłączanie wirtualnego dysku twardego** wybierz pozycję **Dołącz wirtualny dysk twardy później** i Ukończ pracę Kreatora nowej maszyny wirtualnej.

1. Otwórz ustawienia maszyny wirtualnej:

    a.  Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej. Upewnij się, że wybrano **format VHD** i **ustalony rozmiar**.

    b.  Dołącz instalację ISO do stacji dysków DVD.

    c.  Ustaw rozruch systemu BIOS z dysku CD.

1. Uruchom maszynę wirtualną. Po wyświetleniu przewodnika instalacji naciśnij klawisz **Tab** , aby skonfigurować opcje rozruchu.

1. Wprowadź `inst.ks=<the location of the kickstart file>` na końcu opcji rozruchu i naciśnij klawisz **Enter**.

1. Poczekaj na zakończenie instalacji. Po zakończeniu maszyna wirtualna zostanie automatycznie wyłączona. Wirtualny dysk twardy z systemem Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="known-issues"></a>Znane problemy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Nie można uwzględnić sterownika funkcji Hyper-V na początkowym dysku RAM w przypadku korzystania z funkcji hypervisor innej niż Hyper-V

W niektórych przypadkach Instalatorzy systemu Linux mogą nie uwzględniać sterowników funkcji Hyper-V na początkowym dysku RAM (oryginalnych initrd lub initramfs), chyba że system Linux wykryje, że jest uruchomiony w środowisku funkcji Hyper-V.

W przypadku korzystania z innego systemu wirtualizacji (czyli VirtualBox, Xen itp.) w celu przygotowania obrazu systemu Linux może być konieczne odbudowanie oryginalnych initrd w celu upewnienia się, że na początkowym dysku RAM są dostępne co najmniej hv_vmbus i hv_storvsc modułów jądra. Jest to znany problem co najmniej w systemach opartych na systemie z rozkładem Red Hat.

Aby rozwiązać ten problem, Dodaj moduły funkcji Hyper-V do initramfs i Skompiluj ponownie:

Edytuj `/etc/dracut.conf` i Dodaj następującą zawartość:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Initramfs ponownego kompilowania:

```console
# dracut -f -v
```

Aby uzyskać więcej informacji, zobacz informacje o [odbudowywaniu initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Następne kroki
* Teraz możesz przystąpić do korzystania z Red Hat Enterprise Linux wirtualnego dysku twardego w celu utworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli przekazujesz plik VHD na platformę Azure po raz pierwszy, zobacz [Tworzenie maszyny wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
* Aby uzyskać więcej informacji na temat funkcji hypervisor certyfikowanych do uruchamiania Red Hat Enterprise Linux, zobacz [witrynę sieci Web Red Hat](https://access.redhat.com/certified-hypervisors).
* Aby dowiedzieć się więcej o korzystaniu z gotowych do produkcji obrazów RHEL BYOS, przejdź do strony dokumentacji dla [BYOS](../workloads/redhat/byos.md).
