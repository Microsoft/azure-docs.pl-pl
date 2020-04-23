---
title: Tworzenie i przekazywanie dysku VHD z systemem Linux w przedsiębiorstwie Red Hat do użytku na platformie Azure
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Red Hat Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: 4140f9f07a0fd653c8e0370d017cbae7effd0a07
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084315"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Przygotowywanie maszyny wirtualnej bazującej na systemie Red Hat dla platformy Azure
W tym artykule dowiesz się, jak przygotować maszynę wirtualną Red Hat Enterprise Linux (RHEL) do użycia na platformie Azure. Wersje RHEL, które są omówione w tym artykule są 6.7+ i 7.1+. Hipernadzorcy do przygotowania, które są opisane w tym artykule są Hyper-V, jądra na maszynie wirtualnej (KVM) i VMware. Aby uzyskać więcej informacji na temat wymagań dotyczących uprawnień do uczestnictwa w programie Dostęp do chmury Red Hat, zobacz [witrynę Red Hat cloud access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) i [uruchamianie RHEL na platformie Azure.](https://access.redhat.com/ecosystem/ccsp/microsoft-azure) Sposoby automatyzacji tworzenia obrazów RHEL można znaleźć w [usłudze Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Przygotowanie maszyny wirtualnej opartej na red hat z Menedżera funkcji Hyper-V

### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji przyjęto założenie, że plik ISO został już uzyskany ze strony red hat i zainstalowany obraz RHEL na wirtualnym dysku twardym (VHD). Aby uzyskać więcej informacji na temat instalowania obrazu systemu operacyjnego za pomocą Menedżera funkcji Hyper-V, zobacz [Instalowanie roli funkcji funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

**Uwagi dotyczące instalacji RHEL**

* Platforma Azure nie obsługuje formatu VHDX. Platforma Azure obsługuje tylko stały dysk VHD. Za pomocą Menedżera funkcji Hyper-V można przekonwertować dysk na format VHD lub użyć polecenia cmdlet convert-vhd. Jeśli używasz VirtualBox, wybierz **stały rozmiar,** w przeciwieństwie do domyślnej opcji alokacji dynamicznie przydzielonej podczas tworzenia dysku.
* Platforma Azure obsługuje maszyny wirtualne Gen1 (rozruch BIOS) & Gen2 (rozruch UEFI).
* Maksymalny rozmiar, który jest dozwolony dla VHD jest 1,023 GB.
* Menedżer woluminów logicznych (LVM) jest obsługiwany i może być używany na dysku systemu operacyjnego lub dyskach z danymi na maszynach wirtualnych platformy Azure. Jednak ogólnie zaleca się używanie partycji standardowych na dysku systemu operacyjnego, a nie LVM. Ta praktyka pozwoli uniknąć konfliktów nazw LVM z sklonowanych maszyn wirtualnych, szczególnie jeśli kiedykolwiek trzeba dołączyć dysk systemu operacyjnego do innej identycznej maszyny wirtualnej do rozwiązywania problemów. Zobacz też [dokumentację LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i [RAID.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Wymagana jest obsługa jądra do montażu systemów plików Uniwersalnego Formatu Dysku (UDF). Przy pierwszym uruchomieniu na platformie Azure nośnik w formacie UDF, który jest dołączony do gościa przekazuje konfigurację inicjowania obsługi administracyjnej do maszyny wirtualnej systemu Linux. Agent systemu Azure Linux musi mieć możliwość zainstalowania systemu plików UDF, aby odczytać jego konfigurację i aprowizować maszynę wirtualną.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Więcej informacji można znaleźć w poniższych krokach. Zobacz też [Informacje o instalacji systemu Linux, aby](create-upload-generic.md#general-linux-installation-notes) uzyskać więcej informacji.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Przygotowanie maszyny wirtualnej RHEL 6 z menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.

1. W RHEL 6 NetworkManager może zakłócać agenta systemu Azure Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

1. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś (lub usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

1. Zarejestruj subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby wykonać tę `/boot/grub/menu.lst` modyfikację, otwórz w edytorze tekstu i upewnij się, że domyślne jądro zawiera następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w debugowaniu problemów.
    
    Ponadto zalecamy usunięcie następujących parametrów:
    
        rhgb quiet crashkernel=auto
    
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego.  W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej. Ta konfiguracja może być problematyczne w mniejszych rozmiarach maszyn wirtualnych.


1. Upewnij się, że serwer bezpiecznej powłoki (SSH) jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co zwykle jest wartością domyślną. Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujący wiersz:

        ClientAliveInterval 180

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Zainstalowanie pakietu WALinuxAgent powoduje usunięcie pakietów NetworkManager i NetworkManager-gnome, jeśli nie zostały one jeszcze usunięte w kroku 3.

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim kroku odpowiednio zmodyfikuj następujące parametry w /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (jeśli to konieczne) uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kliknij **pozycję Zamknij akcję** > **Shut Down** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Przygotowanie maszyny wirtualnej RHEL 7 z menedżera funkcji Hyper-V

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.

1. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.

1. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=tak NM_CONTROLLED=tak

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby wykonać tę `/etc/default/grub` modyfikację, otwórz w `GRUB_CMDLINE_LINUX` edytorze tekstu i edytuj parametr. Przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w debugowaniu problemów. Ta konfiguracja wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`uruchom następujące polecenie, aby odbudować konfigurację grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co zwykle jest wartością domyślną. Zmodyfikuj, `/etc/ssh/sshd_config` aby uwzględnić następujący wiersz:

        ClientAliveInterval 180

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim `/etc/waagent.conf` kroku należy odpowiednio zmodyfikować następujące parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Jeśli chcesz wyrejestrować subskrypcję, uruchom następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Kliknij **pozycję Zamknij akcję** > **Shut Down** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Przygotowanie maszyny wirtualnej opartej na red hat z KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Przygotowanie maszyny wirtualnej RHEL 6 z KVM

1. Pobierz obraz KVM RHEL 6 ze strony Red Hat.

1. Ustaw hasło główne.

    Wygeneruj zaszyfrowane hasło i skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmień drugie pole użytkownika głównego z "!!" zaszyfrowanego hasła.

1. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku na **qcow2**i ustaw model urządzenia interfejsu sieci wirtualnej na **virtio**. Następnie uruchom maszynę wirtualną i zaloguj się jako root.

1. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś (lub usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # chkconfig network on

1. Zarejestruj subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby wykonać tę `/boot/grub/menu.lst` konfigurację, otwórz edytor tekstu i upewnij się, że domyślne jądro zawiera następujące parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w debugowaniu problemów.
    
    Ponadto zaleca się usunięcie następujących parametrów:
    
        rhgb quiet crashkernel=auto
    
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.


1. Dodaj moduły Hyper-V do initramfs:  

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Odbuduj initramfs:

        # dracut -f -v

1. Odinstaluj cloud-init:

        # yum remove cloud-init

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # chkconfig sshd on

    Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim kroku odpowiednio zmodyfikuj następujące parametry w **/etc/waagent.conf:**

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (jeśli to konieczne) uruchamiając następujące polecenie:

        # subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną w KVM.

1. Przekonwertuj obraz qcow2 na format VHD.

> [!NOTE]
> Istnieje znany błąd w wersjach qemu-img >= 2.2.1, który powoduje nieprawidłowo sformatowany VHD. Problem został rozwiązany w QEMU 2.6. Zaleca się stosowanie qemu-img 2.2.0 lub niższej lub aktualizacji do 2.6 lub wyższej. Numer https://bugs.launchpad.net/qemu/+bug/1490611referencyjny: .
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Przygotowanie maszyny wirtualnej RHEL 7 z KVM

1. Pobierz obraz KVM RHEL 7 ze strony Red Hat. W tej procedurze użyto RHEL 7 jako przykładu.

1. Ustaw hasło główne.

    Wygeneruj zaszyfrowane hasło i skopiuj dane wyjściowe polecenia:

        # openssl passwd -1 changeme

    Ustaw hasło główne z guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Zmień drugie pole użytkownika głównego z "!!" zaszyfrowanego hasła.

1. Utwórz maszynę wirtualną w KVM z obrazu qcow2. Ustaw typ dysku na **qcow2**i ustaw model urządzenia interfejsu sieci wirtualnej na **virtio**. Następnie uruchom maszynę wirtualną i zaloguj się jako root.

1. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=tak NM_CONTROLLED=tak

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj subskrypcję Red Hat, aby umożliwić instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby wykonać tę `/etc/default/grub` konfigurację, otwórz edytor `GRUB_CMDLINE_LINUX` tekstu i edytuj parametr. Przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To polecenie zapewnia również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w obsłudze platformy Azure w rozwiązywaniu problemów z debugowaniem. Polecenie wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`uruchom następujące polecenie, aby odbudować konfigurację grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Dodaj moduły Hyper-V do initramfs.

    Edytowanie `/etc/dracut.conf` i dodawanie zawartości:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Odbuduj initramfs:

        # dracut -f -v

1. Odinstaluj cloud-init:

        # yum remove cloud-init

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu:

        # systemctl enable sshd

    Zmodyfikuj /etc/ssh/sshd_config, aby uwzględnić następujące wiersze:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # yum install WALinuxAgent

    Włącz usługę waagent:

        # systemctl enable waagent.service

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim `/etc/waagent.conf` kroku należy odpowiednio zmodyfikować następujące parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (jeśli to konieczne) uruchamiając następujące polecenie:

        # subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną w KVM.

1. Przekonwertuj obraz qcow2 na format VHD.

> [!NOTE]
> Istnieje znany błąd w wersjach qemu-img >= 2.2.1, który powoduje nieprawidłowo sformatowany VHD. Problem został rozwiązany w QEMU 2.6. Zaleca się stosowanie qemu-img 2.2.0 lub niższej lub aktualizacji do 2.6 lub wyższej. Numer https://bugs.launchpad.net/qemu/+bug/1490611referencyjny: .
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Przygotowanie maszyny wirtualnej opartej na red hat z VMware
### <a name="prerequisites"></a>Wymagania wstępne
W tej sekcji przyjęto założenie, że maszyna wirtualna RHEL została już zainstalowana w oprogramowaniu VMware. Aby uzyskać szczegółowe informacje na temat instalowania systemu operacyjnego w VMware, zobacz [Przewodnik instalacji systemu operacyjnego VMware Dla gościa](https://partnerweb.vmware.com/GOSIG/home.html).

* Podczas instalowania systemu operacyjnego Linux zaleca się używanie partycji standardowych zamiast LVM, co często jest wartością domyślną dla wielu instalacji. Pozwoli to uniknąć konfliktów nazw LVM ze sklonowaną maszyną wirtualną, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny wirtualnej w celu rozwiązywania problemów. LVM lub RAID mogą być używane na dyskach z danymi, jeśli jest to preferowane.
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów. Więcej informacji na ten temat można znaleźć w kolejnych krokach.
* Podczas tworzenia wirtualnego dysku twardego wybierz pozycję **Przechowuj dysk wirtualny jako pojedynczy plik**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Przygotowanie maszyny wirtualnej RHEL 6 z VMware
1. W RHEL 6 NetworkManager może zakłócać agenta systemu Azure Linux. Odinstaluj ten pakiet, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager

1. Utwórz plik o nazwie **sieć** w katalogu /etc/sysconfig/, który zawiera następujący tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Przenieś (lub usuń) reguły udev, aby uniknąć generowania reguł statycznych dla interfejsu Ethernet. Te reguły powodują problemy podczas klonowania maszyny wirtualnej na platformie Azure lub funkcji Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo chkconfig network on

1. Zarejestruj subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz `/etc/default/grub` edytor tekstu `GRUB_CMDLINE_LINUX` i edytuj parametr. Przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w debugowaniu problemów. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Dodaj moduły Hyper-V do initramfs:

    Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Odbuduj initramfs:

        # dracut -f -v

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu, co zwykle jest wartością domyślną. Zmodyfikuj, `/etc/ssh/sshd_config` aby uwzględnić następujący wiersz:

    KlientAliveInterval 180

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim `/etc/waagent.conf` kroku należy odpowiednio zmodyfikować następujące parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Wyrejestruj subskrypcję (jeśli to konieczne) uruchamiając następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną i przekonwertuj plik VMDK na plik vhd.

> [!NOTE]
> Istnieje znany błąd w wersjach qemu-img >= 2.2.1, który powoduje nieprawidłowo sformatowany VHD. Problem został rozwiązany w QEMU 2.6. Zaleca się stosowanie qemu-img 2.2.0 lub niższej lub aktualizacji do 2.6 lub wyższej. Numer https://bugs.launchpad.net/qemu/+bug/1490611referencyjny: .
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Przygotowanie maszyny wirtualnej RHEL 7 z VMware
1. Utwórz lub `/etc/sysconfig/network` edytuj plik i dodaj następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Utwórz lub `/etc/sysconfig/network-scripts/ifcfg-eth0` edytuj plik i dodaj następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=tak NM_CONTROLLED=tak

1. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:

        # sudo systemctl enable network

1. Zarejestruj subskrypcję Red Hat, aby włączyć instalację pakietów z repozytorium RHEL, uruchamiając następujące polecenie:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby wykonać tę `/etc/default/grub` modyfikację, otwórz w `GRUB_CMDLINE_LINUX` edytorze tekstu i edytuj parametr. Przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Ta konfiguracja zapewnia również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w obsłudze platformy Azure w rozwiązywaniu problemów z debugowaniem. Wyłącza również nowe konwencje nazewnictwa RHEL 7 dla kart sieciowych. Ponadto zaleca się usunięcie następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
    Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego. W razie `crashkernel` potrzeby można pozostawić skonfigurowaną opcję. Należy zauważyć, że ten parametr zmniejsza ilość dostępnej pamięci na maszynie wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.

1. Po zakończeniu edycji `/etc/default/grub`uruchom następujące polecenie, aby odbudować konfigurację grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Dodaj moduły Hyper-V do initramfs.

    Edytuj, `/etc/dracut.conf`dodaj zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Odbuduj initramfs:

        # dracut -f -v

1. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu. To ustawienie jest zwykle ustawieniem domyślnym. Zmodyfikuj, `/etc/ssh/sshd_config` aby uwzględnić następujący wiersz:

        ClientAliveInterval 180

1. Pakiet `WALinuxAgent-<version>`WALinuxAgent został przesunięty do repozytorium dodatków Red Hat. Włącz repozytorium dodatków, uruchamiając następujące polecenie:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.

    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny wirtualnej po maszynie wirtualnej jest aprowizowana na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem tymczasowym i może zostać opróżniony, jeśli maszyna wirtualna zostanie wyrejestrowana. Po zainstalowaniu agenta systemu Azure Linux w poprzednim `/etc/waagent.conf` kroku należy odpowiednio zmodyfikować następujące parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Jeśli chcesz wyrejestrować subskrypcję, uruchom następujące polecenie:

        # sudo subscription-manager unregister

1. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Zamknij maszynę wirtualną i przekonwertuj plik VMDK na format VHD.

> [!NOTE]
> Istnieje znany błąd w wersjach qemu-img >= 2.2.1, który powoduje nieprawidłowo sformatowany VHD. Problem został rozwiązany w QEMU 2.6. Zaleca się stosowanie qemu-img 2.2.0 lub niższej lub aktualizacji do 2.6 lub wyższej. Numer https://bugs.launchpad.net/qemu/+bug/1490611referencyjny: .
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Automatyczne przygotowywanie maszyny wirtualnej opartej na technologii Red Hat z iso przy użyciu pliku kickstart
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Przygotowywanie maszyny wirtualnej RHEL 7 z pliku kickstart

1.  Utwórz plik kickstart, który zawiera następującą zawartość, i zapisz plik. Szczegółowe informacje na temat instalacji kickstart można znaleźć w [Przewodniku instalacji Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

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
    PERSISTENT_DHCLIENT=tak NM_CONTROLLED=tak EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Umieść plik kickstartu w miejscu, w którym system instalacyjny może uzyskać do niego dostęp.

1. W Menedżerze funkcji Hyper-V utwórz nową maszynę wirtualną. Na stronie **Podłącz wirtualny dysk twardy** wybierz pozycję Dołącz **wirtualny dysk twardy później**i ukończ Kreatora nowej maszyny wirtualnej.

1. Otwórz ustawienia maszyny wirtualnej:

    a.  Dołącz nowy wirtualny dysk twardy do maszyny wirtualnej. Upewnij się, że wybierz format **VHD** i **stały rozmiar**.

    b.  Podłącz instalację ISO do napędu DVD.

    d.  Ustaw system BIOS do rozruchu z dysku CD.

1. Uruchom maszynę wirtualną. Po wyświetleniu przewodnika instalacyjnego naciśnij klawisz **Tab,** aby skonfigurować opcje rozruchu.

1. Wprowadź `inst.ks=<the location of the kickstart file>` na końcu opcji rozruchu i naciśnij **klawisz Enter**.

1. Poczekaj na zakończenie instalacji. Po zakończeniu maszyna wirtualna zostanie automatycznie zamknięta. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="known-issues"></a>Znane problemy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Nie można uwzględnić sterownika funkcji Hyper-V na początkowym dysku PAMIĘCI RAM podczas korzystania z funkcji hypervisor innych niż Hyper-V

W niektórych przypadkach instalatory systemu Linux mogą nie zawierać sterowników funkcji Hyper-V do początkowego dysku RAM (initrd lub initramfs), chyba że system Linux wykryje, że działa w środowisku funkcji Hyper-V.

Gdy używasz innego systemu wirtualizacji (czyli VirtualBox, Xen itp.) do przygotowania obrazu Linuksa, może być konieczne odbudowanie initrd, aby upewnić się, że co najmniej hv_vmbus i hv_storvsc modułów jądra są dostępne na początkowym dysku RAM. Jest to znany problem przynajmniej w systemach, które są oparte na nadrzędnej dystrybucji Red Hat.

Aby rozwiązać ten problem, dodaj moduły funkcji Hyper-V do initramfów i odbuduj je:

Edytuj `/etc/dracut.conf`i dodaj następującą zawartość:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Odbuduj initramfs:

        # dracut -f -v

Aby uzyskać więcej informacji, zobacz informacje na temat [odbudowy initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Następne kroki
* Teraz możesz używać wirtualnego dysku twardego Red Hat Enterprise Linux do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).
* Aby uzyskać więcej informacji na temat hipernadzorców, które są certyfikowane do uruchamiania Red Hat Enterprise Linux, zobacz [red hat stronie internetowej](https://access.redhat.com/certified-hypervisors).
* Aby dowiedzieć się więcej na temat korzystania z gotowych do produkcji obrazów RHEL BYOS, przejdź do strony dokumentacji [systemu BYOS](../workloads/redhat/byos.md).
