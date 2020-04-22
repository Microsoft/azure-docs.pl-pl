---
title: Tworzenie i przesyłanie dysku VHD systemu Oracle Linux
description: Dowiedz się, jak utworzyć i przekazać wirtualny dysk twardy platformy Azure (VHD), który zawiera system operacyjny Oracle Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: fd6d17709cc3e5e9f6bb89ed7480fcd9ee80fd97
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759389"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Przygotowanie maszyny wirtualnej Oracle Linux na platformę Azure

W tym artykule przyjęto założenie, że system operacyjny Oracle Linux został już zainstalowany na wirtualnym dysku twardym. Istnieje wiele narzędzi do tworzenia plików vhd, na przykład rozwiązanie wirtualizacji, takie jak Hyper-V. Aby uzyskać instrukcje, zobacz [Instalowanie roli funkcji Hyper-V i konfigurowanie maszyny wirtualnej](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Uwagi dotyczące instalacji systemu Oracle Linux
* Zobacz też [Ogólne uwagi dotyczące instalacji systemu Linux,](create-upload-generic.md#general-linux-installation-notes) aby uzyskać więcej wskazówek dotyczących przygotowywania systemu Linux dla platformy Azure.
* Funkcje Hyper-V i Azure obsługują system Oracle Linux z nierozerwalnym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z red hat.
* Interfejs UEK2 firmy Oracle nie jest obsługiwany w funkcji Hyper-V i azure, ponieważ nie zawiera wymaganych sterowników.
* Format VHDX nie jest obsługiwany na platformie Azure, tylko **stały dysk VHD**.  Dysk można przekonwertować na format VHD za pomocą Menedżera funkcji Hyper-V lub polecenia cmdlet convert-vhd.
* Podczas instalacji systemu Linux zaleca się używanie standardowych partycji zamiast LVM (często domyślnych dla wielu instalacji). Pozwoli to uniknąć konfliktów nazw LVM ze sklonowanymi maszynami wirtualnymi, szczególnie jeśli dysk systemu operacyjnego kiedykolwiek musi być dołączony do innej maszyny Wirtualnej w celu rozwiązywania problemów. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mogą być używane na dyskach z danymi, jeśli jest to preferowane.
* Wersje jądra linuksa starsze niż 2.6.37 nie obsługują numa na Hyper-V z większymi rozmiarami maszyn wirtualnych. Ten problem dotyczy przede wszystkim starszych dystrybucji przy użyciu wyższego strumienia jądra Red Hat 2.6.32 i został naprawiony w systemie Oracle Linux 6.6 lub nowszym
* Nie należy konfigurować partycji wymiany na dysku systemu operacyjnego. Agent systemu Linux można skonfigurować do tworzenia pliku wymiany na tymczasowym dysku zasobów.  Więcej informacji na ten temat można znaleźć w poniższych krokach.
* Wszystkie dyski VHD na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwersji z dysku nieprzetworzonego na dysk wirtualny należy upewnić się, że rozmiar dysku nieprzetworzonego jest wielokrotnością 1 MB przed konwersją. Zobacz [Uwagi dotyczące instalacji systemu Linux, aby](create-upload-generic.md#general-linux-installation-notes) uzyskać więcej informacji.
* Upewnij się, `Addons` że repozytorium jest włączone. Edytuj plik `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) `/etc/yum.repos.d/public-yum-ol7.repo`lub (Oracle Linux `enabled=0` 7) i zmień linię na `enabled=1` **[ol6_addons]** lub **[ol7_addons]** w tym pliku.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 i nowsze
Należy wykonać określone kroki konfiguracji w systemie operacyjnym, aby maszyna wirtualna działała na platformie Azure.

1. W środkowym okienku Menedżera funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij przycisk **Połącz,** aby otworzyć okno dla maszyny wirtualnej.
3. Odinstaluj program NetworkManager, uruchamiając następujące polecenie:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Uwaga:** Jeśli pakiet nie jest jeszcze zainstalowany, to polecenie zakończy się niepowodzeniem z komunikatem o błędzie. Jest to oczekiwane.
4. Utwórz plik **network** o `/etc/sysconfig/` nazwie sieć w katalogu zawierającym następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalogu zawierającym następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Zmodyfikuj reguły udev, aby uniknąć generowania reguł statycznych dla interfejsów Ethernet. Te reguły mogą powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:
   
        # chkconfig network on
8. Zainstaluj python-pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
9. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz "/boot/grub/menu.lst" w edytorze tekstu i upewnij się, że jądro zawiera następujące parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Zapewni to, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc pomocy technicznej platformy Azure w rozwiązywaniu problemów z debugowaniem.
   
   Oprócz powyższego zaleca się *usunięcie* następujących parametrów:
   
        rhgb quiet crashkernel=auto
   
   Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego.
   
   Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejszy ilość dostępnej pamięci na maszynie Wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.
10. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
11. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie. Najnowsza wersja to 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Należy zauważyć, że zainstalowanie pakietu WALinuxAgent spowoduje usunięcie pakietów NetworkManager i NetworkManager-gnome, jeśli nie zostały one już usunięte zgodnie z opisem w kroku 2.
12. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (patrz poprzedni krok) odpowiednio zmodyfikuj następujące parametry w /etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 i nowsze
**Zmiany w Systemie Oracle Linux 7**

Przygotowanie maszyny wirtualnej Oracle Linux 7 na platformę Azure jest bardzo podobne do Oracle Linux 6, jednak istnieje kilka ważnych różnic, na które warto zwrócić uwagę:

* Platforma Azure obsługuje system Oracle Linux z nierozerwalnym jądrem przedsiębiorstwa (UEK) lub jądrem zgodnym z red hat. Oracle Linux z UEK jest zalecane.
* Pakiet NetworkManager nie jest już w konflikcie z agentem systemu Azure Linux. Ten pakiet jest zainstalowany domyślnie i zaleca się, że nie jest usuwany.
* GRUB2 jest teraz używany jako domyślny bootloader, więc procedura edycji parametrów jądra uległa zmianie (patrz poniżej).
* XFS jest teraz domyślnym systemem plików. System plików ext4 może być nadal używany w razie potrzeby.

**Kroki konfiguracji**

1. W Menedżerze funkcji Hyper-V wybierz maszynę wirtualną.
2. Kliknij **przycisk Połącz,** aby otworzyć okno konsoli dla maszyny wirtualnej.
3. Utwórz plik **network** o `/etc/sysconfig/` nazwie sieć w katalogu zawierającym następujący tekst:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Utwórz plik o nazwie **ifcfg-eth0** w `/etc/sysconfig/network-scripts/` katalogu zawierającym następujący tekst:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Zmodyfikuj reguły udev, aby uniknąć generowania reguł statycznych dla interfejsów Ethernet. Te reguły mogą powodować problemy podczas klonowania maszyny wirtualnej na platformie Microsoft Azure lub funkcji Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Upewnij się, że usługa sieciowa zostanie uruchomiona w czasie rozruchu, uruchamiając następujące polecenie:
   
        # sudo chkconfig network on
7. Zainstaluj pakiet python-pyasn1, uruchamiając następujące polecenie:
   
        # sudo yum install python-pyasn1
8. Uruchom następujące polecenie, aby wyczyścić bieżące metadane yum i zainstalować wszelkie aktualizacje:
   
        # sudo yum clean all
        # sudo yum -y update
9. Zmodyfikuj linię rozruchową jądra w konfiguracji grub, aby uwzględnić dodatkowe parametry jądra dla platformy Azure. Aby to zrobić, otwórz "/etc/default/grub" w `GRUB_CMDLINE_LINUX` edytorze tekstu i edytuj parametr, na przykład:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Zapewni to również, że wszystkie komunikaty konsoli są wysyłane do pierwszego portu szeregowego, co może pomóc w obsłudze platformy Azure w rozwiązywaniu problemów z debugowaniem. Wyłącza również konwencje nazewnictwa kart sieciowych w systemie Oracle Linux 7 z jądrem Unbreakable Enterprise. Oprócz powyższego zaleca się *usunięcie* następujących parametrów:
   
       rhgb quiet crashkernel=auto
   
   Graficzny i cichy rozruch nie są przydatne w środowisku chmury, w którym chcemy, aby wszystkie dzienniki były wysyłane do portu szeregowego.
   
   Opcja `crashkernel` może pozostać skonfigurowana w razie potrzeby, ale należy pamiętać, że ten parametr zmniejszy ilość dostępnej pamięci na maszynie Wirtualnej o 128 MB lub więcej, co może być problematyczne w przypadku mniejszych rozmiarów maszyn wirtualnych.
10. Po zakończeniu edycji "/etc/default/grub" na powyżej, uruchom następujące polecenie, aby odbudować konfigurację grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Upewnij się, że serwer SSH jest zainstalowany i skonfigurowany do uruchamiania w czasie rozruchu.  Zazwyczaj jest to wartość domyślna.
12. Zainstaluj agenta systemu Azure Linux, uruchamiając następujące polecenie:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nie należy tworzyć miejsca wymiany na dysku systemu operacyjnego.
    
    Agent systemu Azure Linux można automatycznie skonfigurować miejsce wymiany przy użyciu lokalnego dysku zasobów, który jest dołączony do maszyny Wirtualnej po inicjowaniu obsługi administracyjnej na platformie Azure. Należy zauważyć, że dysk zasobów lokalnych jest dyskiem *tymczasowym* i może zostać opróżniony po usunięciu obsługi administracyjnej maszyny Wirtualnej. Po zainstalowaniu agenta systemu Azure Linux (zobacz poprzedni krok), odpowiednio zmodyfikuj następujące parametry w /etc/waagent.conf:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Uruchom następujące polecenia, aby anulować aprovision maszyny wirtualnej i przygotować ją do inicjowania obsługi administracyjnej na platformie Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Kliknij **pozycję Akcja -> Zamknij** w Menedżerze funkcji Hyper-V. Dysk VHD systemu Linux jest teraz gotowy do przekazania na platformę Azure.

## <a name="next-steps"></a>Następne kroki
Teraz możesz używać urządzenia Oracle Linux .vhd do tworzenia nowych maszyn wirtualnych na platformie Azure. Jeśli po raz pierwszy przesyłasz plik vhd na platformę Azure, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux z dysku niestandardowego](upload-vhd.md#option-1-upload-a-vhd).

