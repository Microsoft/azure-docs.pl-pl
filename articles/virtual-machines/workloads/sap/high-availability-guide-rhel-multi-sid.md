---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NW na RHEL przewodnik z obsługą wielosid | Microsoft Docs
description: Ustanów wysoką dostępność dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure RHEL wiele identyfikatorów SID.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: b6365e9488dc00ae1bec3217b52fefa534bb0671
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956317"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux for SAP — Przewodnik dotyczący wiele identyfikatorów SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

W tym artykule opisano sposób wdrażania wielu systemów SAP NetWeaver o wysokiej dostępności (czyli wielu identyfikatorów SID) w klastrze z dwoma węzłami na maszynach wirtualnych platformy Azure z Red Hat Enterprise Linux dla aplikacji SAP.  

W przykładowych konfiguracjach polecenia instalacji itp. trzy systemy SAP NetWeaver 7,50 są wdrażane w jednym, dwuwęzłowym klastrze o wysokiej dostępności. Identyfikatory SID systemów SAP są następujące:
* **NW1**: ASCS wystąpienie numer **00** i nazwa hosta wirtualnego **msnw1ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **02** i nazwa hosta wirtualnego **msnw1ers**.  
* **NW2**: ASCS wystąpienie o numerze **10** i wirtualne nazwy hosta **msnw2ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **12** i nazwa hosta wirtualnego **msnw2ers**.  
* **NW3**: ASCS wystąpienie o numerze **20** i wirtualne nazwy hosta **msnw3ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **22** i nazwa hosta wirtualnego **msnw3ers**.  

Artykuł nie obejmuje warstwy bazy danych i wdrożenia udziałów SAP NFS. W przykładach w tym artykule używamy [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  Volume **SAPMSID** dla udziałów NFS, przy założeniu, że wolumin jest już wdrożony. Przyjęto również, że wolumin Azure NetApp Files jest wdrażany przy użyciu protokołu NFSv3 oraz że istnieją następujące ścieżki plików dla zasobów klastra dla wystąpień ASCS i wykres WYWOŁUJĄCYCH systemów SAP NW1, NW2 i NW3:  

* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>wykres wywołujących)
* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>wykres wywołujących)
* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>wykres wywołujących)

Przed rozpoczęciem zapoznaj się z poniższymi informacjami i dokumentami SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* [Dokumentacja Azure NetApp Files][anf-azure-doc]
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2002167] zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [NetWeaver SAP w klastrze Pacemaker](https://access.redhat.com/articles/3150081)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/wykres WYWOŁUJĄCYCH dla SAP NetWeaver z zasobami autonomicznymi w RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Skonfiguruj rozwiązanie SAP S/4HANA ASCS/wykres WYWOŁUJĄCYCH za pomocą autonomicznej kolejki serwer 2 (ENSA2) w Pacemaker na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Maszyny wirtualne, które uczestniczą w klastrze, muszą mieć rozmiar, aby można było uruchamiać wszystkie zasoby w przypadku przełączenia w tryb failover. Każdy identyfikator SID SAP może być przełączany w tryb failover niezależny od siebie w klastrze wysokiej dostępności z obsługą usługi wiele identyfikatorów SID.  

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga udziałów o wysokiej dostępności. W tej dokumentacji przedstawiono przykłady dotyczące udziałów SAP wdrożonych na [Azure NetApp Files woluminy NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Możliwe jest również hostowanie udziałów w [klastrze GlusterFS](./high-availability-guide-rhel-glusterfs.md)o wysokiej dostępności, który może być używany przez wiele systemów SAP.  

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Obsługa klastrowania wieloidentyfikatorowego usługi SAP ASCS/wykres WYWOŁUJĄCYCH z systemem Red Hat Linux jako systemu operacyjnego gościa na maszynach wirtualnych platformy Azure jest ograniczona do **pięciu** identyfikatorów SID SAP w tym samym klastrze. Każdy nowy identyfikator SID zwiększa złożoność. Mieszany serwer replikacji z kolejki SAP 1 i z kolejki serwer replikacji 2 w tym samym klastrze **nie jest obsługiwany**. Klastrowanie z wieloma SIDmi opisuje instalację wielu wystąpień SAP ASCS/wykres WYWOŁUJĄCYCH z różnymi identyfikatorami SID w jednym klastrze Pacemaker. Obecnie klastrowanie z obsługą wiele identyfikatorów SID jest obsługiwane tylko dla ASCS/wykres WYWOŁUJĄCYCH.  

> [!TIP]
> Klastrowanie z obsługą wiele identyfikatorów SID oprogramowania SAP ASCS/wykres WYWOŁUJĄCYCH jest rozwiązaniem o większej złożoności. Wdrożenie jest bardziej skomplikowane. Obejmuje to również wyższe nakłady administracyjne podczas wykonywania działań konserwacyjnych (na przykład poprawek systemu operacyjnego). Przed rozpoczęciem rzeczywistej implementacji należy uważnie zaplanować wdrożenie i wszystkie składniki, takie jak maszyny wirtualne, instalacje NFS, wirtualne adresy IP, konfiguracje modułu równoważenia obciążenia i tak dalej.  

SAP NetWeaver ASCS, SAP NetWeaver SCS i SAP NetWeaver wykres WYWOŁUJĄCYCH używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH dla tego przykładowego klastra z obsługą wiele identyfikatorów SID z trzema systemami SAP. W każdym wystąpieniu usługi ASCS i wykres WYWOŁUJĄCYCH dla każdego z identyfikatorów SID będą potrzebne oddzielne adresy IP frontonu, sondy kondycji i reguły równoważenia obciążenia. Przypisz wszystkie maszyny wirtualne, które są częścią klastra ASCS/ASCS do jednej puli zaplecza jednego ILB.  

### <a name="ascs"></a>Z SCS

* Konfiguracja frontonu
  * Adres IP dla NW1:10.3.1.50
  * Adres IP dla NW2:10.3.1.52
  * Adres IP dla NW3:10.3.1.54

* Porty sondy
  * Port 620 <strong> &lt; Nr &gt;</strong>, dlatego dla portów sondy NW1, NW2 i NW3 620 **00**, 620 **10** i 620 **20**
* Reguły równoważenia obciążenia — Utwórz jedną dla każdego wystąpienia, czyli NW1/ASCS, NW2/ASCS i NW3/ASCS.
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
  * W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 36<strong> &lt; Nr &gt; </strong> TCP
    * 39<strong> &lt; Nr &gt; </strong> TCP
    * 81<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong> &lt; Nr &gt; </strong>16 TCP

### <a name="ers"></a>Wykres WYWOŁUJĄCYCH

* Konfiguracja frontonu
  * Adres IP dla NW1 10.3.1.51
  * Adres IP dla NW2 10.3.1.53
  * Adres IP dla NW3 10.3.1.55

* Port sondy
  * Port 621 <strong> &lt; Nr &gt;</strong>, dlatego dla portów sondy NW1, NW2 i N3 621 **02**, 621 **12** i 621 **22**
* Reguły równoważenia obciążenia — Utwórz jedną dla każdego wystąpienia, czyli NW1/wykres WYWOŁUJĄCYCH, NW2/wykres WYWOŁUJĄCYCH i NW3/wykres WYWOŁUJĄCYCH.
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
  * W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong> &lt; Nr &gt; </strong>16 TCP

* Konfiguracja zaplecza
  * Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra programu (A) SCS/wykres WYWOŁUJĄCYCH

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-shares"></a>Udziały SAP

Rozwiązanie SAP NetWeaver wymaga magazynu udostępnionego dla transportu, katalogu profilu i tak dalej. W przypadku systemu SAP o wysokiej dostępności ważne jest posiadanie udziałów o wysokiej dostępności. Konieczne będzie określenie architektury dla udziałów SAP. Jedną z opcji jest wdrożenie udziałów na [Azure NetApp Files woluminy NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Dzięki Azure NetApp Files uzyskasz wbudowaną wysoką dostępność dla udziałów SAP NFS.

Kolejną opcją jest Kompilowanie [GlusterFS na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), które mogą być współużytkowane przez wiele systemów SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Wdróż pierwszy system SAP w klastrze

Teraz, po wybraniu architektury dla udziałów SAP, wdróż pierwszy system SAP w klastrze, postępując zgodnie z odpowiednią dokumentacją.

* Jeśli używasz Azure NetApp Files woluminów NFS, postępuj zgodnie [z wysoką dostępnością maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-rhel-netapp-files.md)  
* W przypadku korzystania z klastra GlusterFS należy postępować zgodnie z [GlusterFS na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

Dokumenty wymienione powyżej przeprowadzą Cię przez procedurę przygotowania niezbędnej infrastruktury, utworzenia klastra, przygotowania systemu operacyjnego do uruchamiania aplikacji SAP.  

> [!TIP]
> Przed dodaniem dodatkowych identyfikatorów SID protokołu SAP do klastra zawsze Przetestuj funkcjonalność klastra w trybie failover po wdrożeniu pierwszego systemu. Dzięki temu wiadomo, że funkcjonalność klastra działa, przed dodaniem złożoności dodatkowych systemów SAP do klastra.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Wdrażanie dodatkowych systemów SAP w klastrze

W tym przykładzie przyjęto założenie, że system **NW1** został już wdrożony w klastrze. Pokażemy sposób wdrażania w klastrach systemów SAP **NW2** i **NW3**. 

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

### <a name="prerequisites"></a>Wymagania wstępne 

> [!IMPORTANT]
> Przed wykonaniem instrukcji dotyczących wdrażania dodatkowych systemów SAP w klastrze postępuj zgodnie z instrukcjami, aby wdrożyć pierwszy system SAP w klastrze, ponieważ istnieją kroki, które są niezbędne tylko podczas pierwszego wdrożenia systemu.  

W tej dokumentacji przyjęto założenie, że:
* Klaster Pacemaker został już skonfigurowany i uruchomiony.  
* Co najmniej jeden system SAP (wystąpienie ASCS/wykres WYWOŁUJĄCYCH) jest już wdrożony i jest uruchomiony w klastrze.  
* Funkcja trybu failover klastra została przetestowana.  
* Udziały NFS dla wszystkich systemów SAP są wdrożone.  

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. Dodaj konfigurację nowo wdrożonego systemu (czyli **NW2**, **NW3**) do istniejącego Azure Load Balancer, postępując zgodnie z instrukcjami [Wdróż Azure Load Balancer ręcznie za pośrednictwem Azure Portal](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Dostosuj adresy IP, porty sondowania kondycji, reguły równoważenia obciążenia dla konfiguracji.  

2. **[A]** rozpoznawanie nazw Instalatora dla dodatkowych systemów SAP. Możesz użyć serwera DNS lub zmodyfikować `/etc/hosts` wszystkie węzły. Ten przykład pokazuje, jak używać `/etc/hosts` pliku.  Dostosuj adresy IP i nazwy hostów do środowiska. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** utwórz katalogi udostępnione dla dodatkowych systemów **NW2** i **NW3** SAP wdrażanych w klastrze. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Dodaj wpisy instalacji dla systemów plików/Sapmnt/SID i/usr/SAP/SID/sys dla dodatkowych systemów SAP wdrażanych w klastrze. W tym przykładzie **NW2** i **NW3**.  

   Aktualizacja pliku `/etc/fstab` z systemami plików dla dodatkowych systemów SAP wdrażanych w klastrze.  

   * Jeśli używasz Azure NetApp Files, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)  
   * W przypadku korzystania z klastra GlusterFS postępuj zgodnie z instrukcjami znajdującymi się [tutaj](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation) .  

### <a name="install-ascs--ers"></a>Zainstaluj ASCS/wykres WYWOŁUJĄCYCH

1. Utwórz zasoby klastra wirtualnego adresu IP i badania kondycji dla wystąpień ASCS dodatkowych systemów SAP, które są wdrażane w klastrze. Pokazany tutaj przykład dotyczy **NW2** i **NW3** ASCS, przy użyciu systemu plików NFS na woluminach Azure NetApp Files z protokołem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Upewnij się, że stan klastra to OK i że wszystkie zasoby są uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.  

2. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS. Na przykład w przypadku systemu **NW2** wirtualna nazwa hosta to <b>msnw2ascs</b>, <b>10.3.1.52</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>10</b>. W przypadku systemu **NW3** wirtualna nazwa hosta jest <b>msnw3ascs</b>, <b>10.3.1.54</b> i numerem wystąpienia użytym do sondowania modułu równoważenia obciążenia, na przykład <b>20</b>. Zanotuj, który węzeł klastra został zainstalowany ASCS dla każdego identyfikatora SID SAP.  

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**SID**/ASCS **instance #**, spróbuj ustawić dla właściciela wartość **SID** adm i Grupa na sapsys **wystąpienia ASCS #** i ponów próbę.

3. **[1]** Utwórz zasoby klastra wirtualnego adresu IP i badania kondycji dla wystąpienia wykres wywołujących dodatkowego systemu SAP, który jest wdrażany w klastrze. Pokazany tutaj przykład dotyczy **NW2** i **NW3** wykres wywołujących, przy użyciu systemu plików NFS na woluminach Azure NetApp Files z protokołem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Upewnij się, że stan klastra to OK i że wszystkie zasoby są uruchomione.  

   Następnie upewnij się, że zasoby nowo utworzonej grupy wykres WYWOŁUJĄCYCH są uruchomione w węźle klastra, w przeciwieństwie do węzła klastra, w którym zainstalowano wystąpienie ASCS dla tego samego systemu SAP.  Na przykład jeśli zainstalowano NW2 ASCS `rhelmsscl1` , upewnij się, że grupa NW2 wykres wywołujących jest uruchomiona `rhelmsscl2` .  Można migrować grupę NW2 wykres WYWOŁUJĄCYCH do programu `rhelmsscl2` , uruchamiając następujące polecenie dla jednego z zasobów klastra w grupie: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w innym węźle, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH. Na przykład w przypadku systemu **NW2** nazwa hosta wirtualnego będzie <b>msnw2ers</b>, <b>10.3.1.53</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>12</b>. W przypadku systemu **NW3** nazwa hosta wirtualnego <b>msnw3ers</b>, <b>10.3.1.55</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>22</b>. 

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub wyższej. Niższa wersja nie ustawia prawidłowo uprawnień i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w **wystąpieniu**/usr/SAP/**NW2**/ERS #, spróbuj ustawić właściciela na **SID** adm i grupę do sapsys w folderze wykres wywołujących **instance #** i ponów próbę.

   Jeśli było konieczne, aby przeprowadzić migrację grupy wykres WYWOŁUJĄCYCH nowo wdrożonego systemu SAP do innego węzła klastra, należy pamiętać o usunięciu ograniczenia lokalizacji dla grupy wykres WYWOŁUJĄCYCH. Ograniczenie można usunąć, uruchamiając następujące polecenie (przykład podano w przypadku systemów SAP Systems **NW2** i **NW3**). Pamiętaj o usunięciu ograniczeń tymczasowych dla tego samego zasobu, który został użyty w poleceniu, aby przenieść grupę klastra wykres WYWOŁUJĄCYCH.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Dostosuj profile wystąpienia ASCS/SCS i wykres wywołujących dla nowo zainstalowanych systemów SAP. Poniższy przykład dotyczy NW2. Konieczne będzie dostosowanie profilów ASCS/SCS i wykres WYWOŁUJĄCYCH dla wszystkich wystąpień SAP dodanych do klastra.  
 
   * Profil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     W przypadku programów ENSA1 i ENSA2 upewnij się, że `keepalive` parametry systemu operacyjnego zostały ustawione zgodnie z opisem w temacie SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Profil wykres WYWOŁUJĄCYCH

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Zaktualizuj plik/usr/SAP/sapservices

   Aby zapobiec uruchamianiu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez Pacemaker muszą mieć komentarz z `/usr/sap/sapservices` pliku.  Poniższy przykład dotyczy systemów SAP Systems **NW2** i **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Utwórz zasoby klastra SAP dla nowo zainstalowanego systemu SAP.  

   W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby dla systemów SAP Systems **NW2** i **NW3** w następujący sposób:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
   W przypadku korzystania z programu w ramach architektury Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) Zdefiniuj zasoby dla systemów SAP Systems **NW2** i **NW3** w następujący sposób:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej instalacji SAP. 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.
   Poniższy przykład przedstawia stan zasobów klastra, po dodaniu SAP Systems **NW2** i **NW3** do klastra. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Dodaj reguły zapory dla ASCS i wykres wywołujących na obu węzłach.  W poniższym przykładzie przedstawiono reguły zapory dla systemów SAP i **NW2** oraz **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Kontynuuj instalację oprogramowania SAP 

Ukończ instalację oprogramowania SAP, wykonując następujące czynności:

* [Przygotowywanie serwerów aplikacji SAP NetWeaver](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalowanie wystąpienia systemu DBMS](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Instalowanie podstawowego serwera aplikacji SAP](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Instalowanie co najmniej jednego wystąpienia aplikacji SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testowanie konfiguracji klastra z obsługą wiele identyfikatorów SID

Poniższe testy są podzbiorem przypadków testowych w przewodnikach z najlepszymi rozwiązaniami firmy Red Hat. Są one dostępne dla wygody użytkownika. Aby zapoznać się z pełną listą testów klastra, zanotuj następujące informacje:

* W przypadku korzystania z Azure NetApp Files woluminów NFS postępuj zgodnie [z wysoką dostępnością maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver na RHEL z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-rhel-netapp-files.md)
* Jeśli używasz wysokiej `GlusterFS` dostępności, postępuj zgodnie z [wysoką dostępnością maszyn wirtualnych platformy Azure dla oprogramowania SAP NETWEAVER na RHEL dla aplikacji SAP](./high-availability-guide-rhel.md).  

Zawsze Odczytuj przewodniki dotyczące najlepszych rozwiązań w zakresie rozwiązań Red Hat i wykonaj wszystkie dodatkowe testy, które mogły zostać dodane.  
Przedstawione testy znajdują się w dwóch węzłach klastra z obsługą wiele identyfikatorów SID z zainstalowanymi trzema systemami SAP.  

1. Ręcznie Migruj wystąpienie ASCS. W przykładzie przedstawiono Migrowanie wystąpienia ASCS dla oprogramowania SAP system NW3.

   Stan zasobu przed rozpoczęciem testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Stan zasobu po teście:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione co najmniej jedno wystąpienie ASCS. W tym przykładzie wykonamy polecenie w `rhelmsscl1` lokalizacji, w której są uruchomione wystąpienia ASCS dla NW1, NW2 i NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Stan po teście i po węźle, który uległ awarii, powinien wyglądać następująco.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Jeśli istnieją komunikaty dotyczące niezakończonych zasobów, wyczyść stan zasobów zakończonych niepowodzeniem. Przykład:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
