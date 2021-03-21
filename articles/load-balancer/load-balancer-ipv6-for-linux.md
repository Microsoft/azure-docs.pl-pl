---
title: Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych z systemem Linux
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak skonfigurować protokół DHCPv6 dla maszyn wirtualnych z systemem Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, moduł równoważenia obciążenia platformy Azure, podwójny stos, publiczny adres IP, natywny protokół IPv6, urządzenia przenośne, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006750"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurowanie protokołu DHCPv6 dla maszyn wirtualnych z systemem Linux


Niektóre obrazy maszyn wirtualnych z systemem Linux w portalu Azure Marketplace nie mają domyślnie skonfigurowanego Dynamic Host Configuration Protocol w wersji 6 (DHCPv6). W celu zapewnienia obsługi protokołu IPv6 należy skonfigurować protokół DHCPv6 w dystrybucji systemu operacyjnego Linux, która jest używana. Różne dystrybucje systemu Linux konfigurują protokół DHCPv6 na różne sposoby, ponieważ korzystają z różnych pakietów.

> [!NOTE]
> Najnowsze obrazy SUSE Linux i CoreOS w portalu Azure Marketplace zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. W przypadku korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany.

W tym dokumencie opisano sposób włączania protokołu DHCPv6 w celu uzyskania adresu IPv6 przez maszynę wirtualną z systemem Linux.

> [!WARNING]
> Przez nieprawidłowe edytowanie plików konfiguracji sieci można utracić dostęp sieciowy do maszyny wirtualnej. Zalecamy przetestowanie zmian konfiguracji w systemach nieprodukcyjnych. Instrukcje zawarte w tym artykule zostały przetestowane na najnowszych wersjach obrazów systemu Linux w portalu Azure Marketplace. Aby uzyskać bardziej szczegółowe instrukcje, zapoznaj się z dokumentacją dla własnej wersji systemu Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edytuj plik */etc/DHCP/dhclient6.conf* i Dodaj następujący wiersz:

    ```config
    timeout 10;
    ```

2. Edytuj konfigurację sieci dla interfejsu eth0, korzystając z następującej konfiguracji:

   * W systemach **Ubuntu 12,04 i 14,04** Edytuj plik */etc/network/interfaces.d/eth0.cfg* . 
   * W witrynie **Ubuntu 16,04** Edytuj plik */etc/network/interfaces.d/50-Cloud-init.cfg* .

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Począwszy od Ubuntu 17,10, domyślnym mechanizmem konfiguracji sieci jest [Plan]( https://netplan.io).  W czasie instalacji/tworzenia wystąpienia, PLAN sieciowy odczytuje konfigurację sieci z plików konfiguracji YAML w tej lokalizacji:/{lib, itp., uruchom}/netplan/*. YAML.

Dołącz instrukcję *dhcp6: true* dla każdego interfejsu Ethernet w konfiguracji.  Na przykład:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Podczas wczesnego rozruchu, plan sieci "moduł renderowania sieciowego" zapisuje konfigurację, aby można było usunąć kontrolę nad urządzeniami do określonego demona sieci, aby uzyskać informacje referencyjne na temat planu, zobacz https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Edytuj plik */etc/DHCP/dhclient6.conf* i Dodaj następujący wiersz:

    ```config
    timeout 10;
    ```

2. Edytuj plik */etc/network/interfaces* i Dodaj następującą konfigurację:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS i Oracle Linux

1. Edytuj plik */etc/sysconfig/Network* i Dodaj następujący parametr:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Edytuj plik */etc/sysconfig/Network-scripts/ifcfg-eth0* i Dodaj następujące dwa parametry:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 i openSUSE 13

Najnowsze SUSE Linux Enterprise Server (SLES) i obrazy openSUSE na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. W przypadku korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie SUSE, wykonaj następujące czynności:

1. Zainstaluj `dhcp-client` pakiet, w razie konieczności:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edytuj plik */etc/sysconfig/Network/ifcfg-eth0* i Dodaj następujący parametr:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 i openSUSE przestępnie

Ostatnio używane obrazy SLES i openSUSE na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. W przypadku korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie SUSE, wykonaj następujące czynności:

1. Edytuj plik */etc/sysconfig/Network/ifcfg-eth0* i Zastąp `#BOOTPROTO='dhcp4'` parametr następującą wartością:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Do pliku */etc/sysconfig/Network/ifcfg-eth0* Dodaj następujący parametr:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Odnów adres IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Ostatnio używane obrazy CoreOS na platformie Azure zostały wstępnie skonfigurowane przy użyciu protokołu DHCPv6. W przypadku korzystania z tych obrazów nie są wymagane żadne dodatkowe zmiany. Jeśli masz maszynę wirtualną opartą na starszym lub niestandardowym obrazie CoreOS, wykonaj następujące czynności:

1. Edytuj plik */etc/systemd/network/10_dhcp. Network* :

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Odnów adres IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
