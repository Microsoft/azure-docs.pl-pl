---
title: DPDK na maszynie wirtualnej z systemem Linux na platformie Azure | Microsoft Docs
description: Poznaj zalety zestawu Data Development Kit (DPDK) i sposób konfigurowania DPDK na maszynie wirtualnej z systemem Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: ba7c2a37d58f20ac4ff1f49a46a406d1b1f70106
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704422"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Konfigurowanie DPDK na maszynie wirtualnej z systemem Linux

Zestaw Data Development Kit (DPDK) na platformie Azure oferuje szybszą platformę przetwarzania pakietów przez użytkownika dla aplikacji intensywnie korzystających z wydajności. Ta struktura pomija stos sieciowy jądra maszyny wirtualnej.

W typowym przetwarzaniu pakietów, który używa stosu sieci jądra, proces jest sterowany przerwaniem. Gdy interfejs sieciowy odbiera przychodzące pakiety, istnieje przerwa jądra do przetworzenia pakietu i przełączenia kontekstu z obszaru jądra do obszaru użytkownika. DPDK eliminuje przełączanie kontekstu i metodę opartą na przerwaniu na korzyść implementacji miejsca użytkownika, która używa sterowników trybu sondowania do szybkiego przetwarzania pakietów.

DPDK składa się z zestawów bibliotek miejsca użytkownika, które zapewniają dostęp do zasobów niższego poziomu. Te zasoby mogą obejmować sprzęt, rdzenie logiczne, zarządzanie pamięcią i sterowniki trybu sondowania kart sieciowych.

DPDK można uruchamiać na maszynach wirtualnych platformy Azure, które obsługują wiele dystrybucji systemu operacyjnego. DPDK zapewnia kluczowe zróżnicowanie wydajności podczas prowadzenia implementacji wirtualizacji funkcji sieci. Te implementacje mogą mieć postać wirtualnych urządzeń sieciowych (urządzeń WUS), takich jak routery wirtualne, zapory, sieci VPN, moduły równoważenia obciążenia, rozwijające się rdzenie pakietów i aplikacje typu "odmowa usługi" (DDoS).

## <a name="benefit"></a>Korzyść

**Wyższe pakiety na sekundę (PPS)**: pominięcie jądra i przejęcie kontroli nad pakietami w przestrzeni użytkownika zmniejsza liczbę cykli, eliminując przełączenia kontekstu. Zwiększa również szybkość pakietów przetwarzanych na sekundę na maszynach wirtualnych platformy Azure z systemem Linux.


## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Obsługiwane są następujące dystrybucje z portalu Azure Marketplace:

| System operacyjny Linux     | Wersja jądra               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-Azure +           | 
| Ubuntu 18.04 | 4.15.0-1014-Azure +           |
| SLES 15 SP1  | 4.12.14-8.19-Azure +          | 
| RHEL 7.5     | 3.10.0 — 862.11.6.el7.x86_64 +  | 
| CentOS 7.5   | 3.10.0 — 862.11.6.el7.x86_64 +  | 

**Obsługa niestandardowych jądra**

W przypadku wszystkich wersji jądra systemu Linux, których nie ma na liście, zobacz [poprawki dotyczące kompilowania jądra z systemem Linux](https://github.com/microsoft/azure-linux-kernel)z systemem Azure. Aby uzyskać więcej informacji, możesz również skontaktować się z [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) . 

## <a name="region-support"></a>Obsługa regionów

Wszystkie regiony platformy Azure obsługują usługę DPDK.

## <a name="prerequisites"></a>Wymagania wstępne

Na maszynie wirtualnej z systemem Linux musi być włączona szybsza sieć. Maszyna wirtualna powinna mieć co najmniej dwa interfejsy sieciowe z jednym interfejsem do zarządzania. Dowiedz się, jak [utworzyć maszynę wirtualną z systemem Linux z włączoną obsługą przyspieszonej sieci](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalowanie zależności DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Jądro platformy Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Domyślne jądro**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Skonfiguruj środowisko maszyny wirtualnej (raz)

1. [Pobierz najnowszą DPDK](https://core.dpdk.org/download). Na platformie Azure jest wymagana wersja 18,11 LTS lub 19,11 LTS.
2. Utwórz konfigurację domyślną przy użyciu `make config T=x86_64-native-linuxapp-gcc` .
3. Włącz opcję Mellanox PMDs w wygenerowanej konfiguracji przy użyciu programu `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Kompiluj z `make` .
5. Zainstaluj program przy użyciu programu `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>Konfigurowanie środowiska uruchomieniowego

Po ponownym uruchomieniu Uruchom następujące polecenia jeden raz:

1. Hugepages

   * Skonfiguruj hugepage, uruchamiając następujące polecenie, raz dla każdego węzła NUMA:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Utwórz katalog do zainstalowania za pomocą programu `mkdir /mnt/huge` .
   * Zainstaluj hugepages z `mount -t hugetlbfs nodev /mnt/huge` .
   * Sprawdź, czy hugepages są zastrzeżone z `grep Huge /proc/meminfo` .

     > KORYGUJĄC Istnieje możliwość zmodyfikowania pliku GRUB w taki sposób, aby hugepages zostały zarezerwowane przy rozruchu, postępując zgodnie z [instrukcjami](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) dla DPDK. Instrukcje znajdują się w dolnej części strony. W przypadku korzystania z maszyny wirtualnej platformy Azure z systemem Linux zamiast tego należy zmodyfikować pliki w obszarze **/etc/config/grub.d** , aby zarezerwować hugepages w ramach ponownych uruchomień.

2. Adresy IP & MAC: służy `ifconfig –a` do wyświetlania adresów MAC i IP interfejsów sieciowych. Interfejs sieciowy *VF* i interfejs sieciowy *NETVSC* mają taki sam adres MAC, ale tylko interfejs sieciowy *NETVSC* ma adres IP. Interfejsy funkcji *VF* działają jako interfejsy podrzędne interfejsów *NETVSC* .

3. Adresy PCI

   * Użyj, `ethtool -i <vf interface name>` Aby dowiedzieć się, który adres PCI ma być używany na potrzeby funkcji *wirtualnej*.
   * Jeśli funkcja *eth0* ma przyspieszoną obsługę sieci, upewnij się, że testpmd nie przejmowanie przypadkowo urządzenia z urządzeniem *VF* PCI dla *eth0*. Jeśli aplikacja DPDK przypadkowo przełączy się za pośrednictwem interfejsu sieciowego zarządzania i spowoduje utratę połączenia SSH, należy użyć konsoli szeregowej do zatrzymania aplikacji DPDK. Można również użyć konsoli szeregowej, aby zatrzymać lub uruchomić maszynę wirtualną.

4. Ładuj *ibuverbs* przy każdym ponownym uruchomieniu przy użyciu `modprobe -a ib_uverbs` . Tylko w przypadku SLES 15 Załaduj  również mlx4_ib `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>Failsafe PMD

Aplikacje DPDK muszą być uruchamiane przez PMD failsafe, które są dostępne na platformie Azure. Jeśli aplikacja działa bezpośrednio za pośrednictwem *funkcji PMD,* nie odbiera **wszystkich** pakietów, które są przeznaczone dla maszyny wirtualnej, ponieważ niektóre pakiety są widoczne w interfejsie syntetycznym. 

Jeśli uruchamiasz aplikację DPDK za pośrednictwem failsafe PMD, gwarantuje to, że aplikacja odbiera wszystkie pakiety, do których są przeznaczone. Gwarantuje również, że aplikacja działa w trybie DPDK, nawet jeśli klient jest odwołany, gdy host jest w trakcie obsługi. Aby uzyskać więcej informacji na temat failsafe PMD, zobacz [Biblioteka sterowników trybu sondowania z niepowodzeniem](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Uruchom testpmd

Aby uruchomić testpmd w trybie głównym, użyj `sudo` polecenia przed poleceniem *testpmd* .

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Podstawowa: sprawdzanie Sanity, inicjowanie karty failsafe

1. Uruchom następujące polecenia, aby uruchomić pojedynczą aplikację testpmd port:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Uruchom następujące polecenia, aby uruchomić aplikację Dual port testpmd:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Jeśli używasz programu testpmd z więcej niż dwiema kartami sieciowymi, `--vdev` argument jest następujący: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  Po uruchomieniu Uruchom polecenie, `show port info all` Aby sprawdzić informacje o porcie. Powinien zostać wyświetlony jeden lub dwa porty DPDK, które są net_failsafe (nie *net_mlx4*).
4.  Użyj `start <port> /stop <port>` do uruchomienia ruchu.

Poprzednie polecenia zaczynają *testpmd* w trybie interaktywnym, co jest zalecane do wypróbowania poleceń testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Podstawowa: pojedynczy nadawca/pojedynczy odbiornik

Następujące polecenia okresowo drukują statystyki pakietów na sekundę:

1. Na stronie TX Uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Na stronie odbierania Uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w programie, `app/test-pmd/txonly.c` aby odpowiadały rzeczywistemu adresowi IP maszynom wirtualnym przed kompilacją. W przeciwnym razie pakiety są porzucane przed osiągnięciem odbiornika.

### <a name="advanced-single-sendersingle-forwarder"></a>Zaawansowane: pojedynczy nadawca/pojedyncza usługa przesyłania dalej
Następujące polecenia okresowo drukują statystyki pakietów na sekundę:

1. Na stronie TX Uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Na stronie FWD Uruchom następujące polecenie:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Podczas uruchamiania poprzednich poleceń na maszynie wirtualnej, należy zmienić *IP_SRC_ADDR* i *IP_DST_ADDR* w programie, `app/test-pmd/txonly.c` aby odpowiadały rzeczywistemu adresowi IP maszynom wirtualnym przed kompilacją. W przeciwnym razie pakiety są usuwane przed osiągnięciem usługi przesyłania dalej. Nie będzie możliwe odbieranie przez trzeci komputer ruchu przesyłanego dalej, ponieważ usługa przesyłania dalej *testpmd* nie modyfikuje adresów warstwy 3, chyba że wprowadzisz pewne zmiany w kodzie.

## <a name="references"></a>Dokumentacja

* [Opcje EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Polecenia Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
