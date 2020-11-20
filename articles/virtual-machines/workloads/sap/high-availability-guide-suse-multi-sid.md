---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver na SLES z przewodnikiem dotyczącym wiele identyfikatorów SID | Microsoft Docs
description: Przewodnik dotyczący wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 3827fa7a98cef9358db0ee102925586bce97fae6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965242"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure w systemie SUSE Linux Enterprise Server for SAP — Przewodnik dotyczący wiele identyfikatorów SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania wielu systemów SAP NetWeaver lub S4HANA o wysokiej dostępności (czyli wielu identyfikatorów SID) w klastrze z dwoma węzłami na maszynach wirtualnych platformy Azure z SUSE Linux Enterprise Server dla aplikacji SAP.  

W przykładowych konfiguracjach polecenia instalacji itp. trzy systemy SAP NetWeaver 7,50 są wdrażane w jednym, dwuwęzłowym klastrze o wysokiej dostępności. Identyfikatory SID systemów SAP są następujące:
* **NW1**: ASCS wystąpienie numer **00** i nazwa hosta wirtualnego **msnw1ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **02** i nazwa hosta wirtualnego **msnw1ers**.  
* **NW2**: ASCS wystąpienie o numerze **10** i wirtualne nazwy hosta **msnw2ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **12** i nazwa hosta wirtualnego **msnw2ers**.  
* **NW3**: ASCS wystąpienie o numerze **20** i wirtualne nazwy hosta **msnw3ascs**; Wykres WYWOŁUJĄCYCH wystąpienie numer **22** i nazwa hosta wirtualnego **msnw3ers**.  

Artykuł nie obejmuje warstwy bazy danych i wdrożenia udziałów SAP NFS. W przykładach w tym artykule używamy nazw wirtualnych NW2-NFS dla udziałów NFS NW2 i NW3-NFS dla udziałów NW3 NFS, przy założeniu, że klaster NFS został wdrożony.  

Przed rozpoczęciem zapoznaj się z poniższymi informacjami i dokumentami SAP:

* Nota SAP [1928533][1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga dotycząca protokołu SAP [2015553][2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917][2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [1944799][1944799] SAP HANA wytycznych dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [2178632][2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498][2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692][2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787][1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Przewodniki dla systemu SUSE SAP ha Best Practices][suse-ha-guide] Przewodniki zawierają wszystkie informacje wymagane do skonfigurowania NetWeaver HA i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako ogólnego planu bazowego. Zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Przewodnik po klastrze z obsługą wiele identyfikatorów SID dla SLES 12 i SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]
## <a name="overview"></a>Omówienie

Maszyny wirtualne, które uczestniczą w klastrze, muszą mieć rozmiar, aby można było uruchamiać wszystkie zasoby w przypadku przełączenia w tryb failover. Każdy identyfikator SID SAP może być przełączany w tryb failover niezależny od siebie w klastrze wysokiej dostępności z obsługą usługi wiele identyfikatorów SID.  W przypadku używania SBD ogrodzenia urządzenia SBD mogą być współużytkowane przez wiele klastrów.  

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga wysoce dostępnych udziałów NFS. W tym przykładzie przyjęto założenie, że udziały SAP NFS są hostowane na [serwerze plików NFS](./high-availability-guide-suse-nfs.md)o wysokiej dostępności, który może być używany przez wiele systemów SAP. Lub udziały są wdrażane na [woluminach systemu plików NFS Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Obsługa klastrowania wieloidentyfikatorowego usługi SAP ASCS/wykres WYWOŁUJĄCYCH z systemem SUSE Linux jako systemu operacyjnego gościa na maszynach wirtualnych platformy Azure jest ograniczona do **pięciu** identyfikatorów SID SAP w tym samym klastrze. Każdy nowy identyfikator SID zwiększa złożoność. Mieszany serwer replikacji z kolejki SAP 1 i z kolejki serwer replikacji 2 w tym samym klastrze **nie jest obsługiwany**. Klastrowanie z wieloma SIDmi opisuje instalację wielu wystąpień SAP ASCS/wykres WYWOŁUJĄCYCH z różnymi identyfikatorami SID w jednym klastrze Pacemaker. Obecnie klastrowanie z obsługą wiele identyfikatorów SID jest obsługiwane tylko dla ASCS/wykres WYWOŁUJĄCYCH.  

> [!TIP]
> Klastrowanie z obsługą wiele identyfikatorów SID oprogramowania SAP ASCS/wykres WYWOŁUJĄCYCH jest rozwiązaniem o większej złożoności. Wdrożenie jest bardziej skomplikowane. Obejmuje to również wyższe nakłady administracyjne podczas wykonywania działań konserwacyjnych (na przykład poprawek systemu operacyjnego). Przed rozpoczęciem rzeczywistej implementacji należy uważnie zaplanować wdrożenie i wszystkie składniki, takie jak maszyny wirtualne, instalacje NFS, wirtualne adresy IP, konfiguracje modułu równoważenia obciążenia i tak dalej.  

Serwer plików NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH dla tego przykładowego klastra z obsługą wiele identyfikatorów SID z trzema systemami SAP. W każdym wystąpieniu usługi ASCS i wykres WYWOŁUJĄCYCH dla każdego z identyfikatorów SID będą potrzebne oddzielne adresy IP frontonu, sondy kondycji i reguły równoważenia obciążenia. Przypisz wszystkie maszyny wirtualne, które są częścią klastra ASCS/ASCS, do jednej puli zaplecza.  

### <a name="ascs"></a>Z SCS

* Konfiguracja frontonu
  * Adres IP dla NW1:10.3.1.14
  * Adres IP dla NW2:10.3.1.16
  * Adres IP dla NW3:10.3.1.13
* Porty sondy
  * Port 620 <strong> &lt; Nr &gt;</strong>, dlatego dla portów sondy NW1, NW2 i NW3 620 **00**, 620 **10** i 620 **20**
* Reguły równoważenia obciążenia — 
* Utwórz jeden dla każdego wystąpienia, czyli NW1/ASCS, NW2/ASCS i NW3/ASCS.
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
  * Adres IP dla NW1 10.3.1.15
  * Adres IP dla NW2 10.3.1.17
  * Adres IP dla NW3 10.3.1.19
* Port sondy
  * Port 621 <strong> &lt; Nr &gt;</strong>, w związku z tym dla portów sondy NW1, NW2 i N # 621 **02**, 621 **12** i 621 **22**
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

## <a name="sap-nfs-shares"></a>Udziały SAP NFS

Rozwiązanie SAP NetWeaver wymaga magazynu udostępnionego dla transportu, katalogu profilu i tak dalej. W przypadku systemu SAP o wysokiej dostępności ważne jest posiadanie udziałów NFS o wysokiej dostępności. Konieczne będzie określenie architektury dla udziałów SAP NFS. Jedną z opcji jest tworzenie [klastra NFS o wysokiej dostępności na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server][nfs-ha], które mogą być współużytkowane przez wiele systemów SAP. 

Innym rozwiązaniem jest wdrożenie udziałów na [Azure NetApp Files woluminy NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Dzięki Azure NetApp Files uzyskasz wbudowaną wysoką dostępność dla udziałów SAP NFS.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Wdróż pierwszy system SAP w klastrze

Teraz, po wybraniu architektury dla udziałów SAP NFS, wdróż pierwszy system SAP w klastrze, postępując zgodnie z odpowiednią dokumentacją.

* Jeśli używany jest serwer NFS o wysokiej dostępności, należy postępować zgodnie z [wysoką dostępnością dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP](./high-availability-guide-suse.md).  
* Jeśli używasz Azure NetApp Files woluminów NFS, postępuj zgodnie [z wysoką dostępnością dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na platformie SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-suse-netapp-files.md)

Dokumenty wymienione powyżej przeprowadzą Cię przez kroki umożliwiające przygotowanie niezbędnych infrastruktur, skompilowanie klastra i przygotowanie systemu operacyjnego do uruchamiania aplikacji SAP.  

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
* Przetestowano funkcję przełączania klastra w tryb failover.  
* Udziały NFS dla wszystkich systemów SAP są wdrożone.  

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. Dodaj konfigurację nowo wdrożonego systemu (czyli **NW2**, **NW3**) do istniejącego Azure Load Balancer, postępując zgodnie z instrukcjami [Wdróż Azure Load Balancer ręcznie za pośrednictwem Azure Portal](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal). Dostosuj adresy IP, porty sondowania kondycji, reguły równoważenia obciążenia dla konfiguracji.  

2. **[A]** Skonfiguruj rozpoznawanie nazw dla dodatkowych systemów SAP. Możesz użyć serwera DNS lub zmodyfikować `/etc/hosts` wszystkie węzły. Ten przykład pokazuje, jak używać `/etc/hosts` pliku.  Dostosuj adresy IP i nazwy hostów do środowiska. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
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

4. **[A]** Skonfiguruj `autofs` , aby zainstalować systemy plików/sapmnt/SID i/usr/SAP/SID/sys dla dodatkowych systemów SAP wdrażanych w klastrze. W tym przykładzie **NW2** i **NW3**.  

   Aktualizacja pliku `/etc/auto.direct` z systemami plików dla dodatkowych systemów SAP wdrażanych w klastrze.  

   * W przypadku korzystania z serwera plików NFS postępuj zgodnie z instrukcjami znajdującymi się [tutaj](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation) .
   * Jeśli używasz Azure NetApp Files, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) 

   `autofs`Aby zainstalować nowo dodane udziały, należy ponownie uruchomić usługę.  

### <a name="install-ascs--ers"></a>Zainstaluj ASCS/wykres WYWOŁUJĄCYCH

1. Utwórz zasoby klastra dla wirtualnego adresu IP i badania kondycji dla wystąpienia ASCS dodatkowego systemu SAP, który jest wdrażany w klastrze. Pokazany tutaj przykład dotyczy **NW2** i **NW3** ASCS, przy użyciu serwera NFS o wysokiej dostępności.  

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a przestawny adres IP stał się niedostępny.  
   > W przypadku istniejących klastrów Pacemaker zalecamy w przeszłości zastępowanie netcat socat. Obecnie zalecamy korzystanie z agenta zasobów Azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagała krótkiego przestoju.  
   > W przypadku istniejących klastrów Pacemaker, jeśli konfiguracja została już zmieniona tak, aby korzystała z socat zgodnie z opisem w obszarze [Ograniczanie poziomu wykrywania w usłudze azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), nie ma potrzeby natychmiastowego przełączenia do agenta zasobów platformy Azure.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Podczas tworzenia zasobów, które mogą być przypisane do różnych zasobów klastra. Gdy grupujesz je, zostaną zmigrowane do jednego z węzłów klastra. Upewnij się, że stan klastra to OK i że wszystkie zasoby są uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

2. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS. Na przykład w przypadku systemu **NW2** wirtualna nazwa hosta to <b>msnw2ascs</b>, <b>10.3.1.16</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>10</b>. w przypadku systemu **NW3** wirtualna nazwa hosta jest <b>msnw3ascs</b>, <b>10.3.1.13</b> i numerem wystąpienia użytym do sondowania modułu równoważenia obciążenia, na przykład <b>20</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**SID**/ASCS **instance #**, spróbuj ustawić dla właściciela wartość **SID** adm i Grupa na sapsys **wystąpienia ASCS #** i ponów próbę.

3. **[1]** Utwórz zasoby klastra wirtualnego adresu IP i badania kondycji dla wystąpienia wykres wywołujących dodatkowego systemu SAP, który jest wdrażany w klastrze. Pokazany tutaj przykład dotyczy **NW2** i **NW3** wykres wywołujących, przy użyciu serwera NFS o wysokiej dostępności. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Podczas tworzenia zasobów można je przypisać do innych węzłów klastra. Gdy grupujesz je, zostaną zmigrowane do jednego z węzłów klastra. Upewnij się, że stan klastra to OK i że wszystkie zasoby są uruchomione.  

   Następnie upewnij się, że zasoby nowo utworzonej grupy wykres WYWOŁUJĄCYCH są uruchomione w węźle klastra, w przeciwieństwie do węzła klastra, w którym zainstalowano wystąpienie ASCS dla tego samego systemu SAP.  Na przykład jeśli zainstalowano NW2 ASCS `slesmsscl1` , upewnij się, że grupa NW2 wykres wywołujących jest uruchomiona `slesmsscl2` .  Można migrować grupę NW2 wykres WYWOŁUJĄCYCH do programu `slesmsscl2` , uruchamiając następujące polecenie: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w innym węźle, używając wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH. Na przykład w przypadku systemu **NW2** nazwa hosta wirtualnego będzie <b>msnw2ers</b>, <b>10.3.1.17</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>12</b>. W przypadku systemu **NW3** nazwa hosta wirtualnego <b>msnw3ers</b>, <b>10.3.1.19</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>22</b>. 

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub wyższej. Niższa wersja nie ustawia prawidłowo uprawnień i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w **wystąpieniu**/usr/SAP/**NW2**/ERS #, spróbuj ustawić właściciela na **SID** adm i grupę do sapsys w folderze wykres wywołujących **instance #** i ponów próbę.

   Jeśli było konieczne, aby przeprowadzić migrację grupy wykres WYWOŁUJĄCYCH nowo wdrożonego systemu SAP do innego węzła klastra, należy pamiętać o usunięciu ograniczenia lokalizacji dla grupy wykres WYWOŁUJĄCYCH. Ograniczenie można usunąć, uruchamiając następujące polecenie (przykład podano w przypadku systemów SAP Systems **NW2** i **NW3**).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** Dostosuj profile wystąpienia ASCS/SCS i wykres wywołujących dla nowo zainstalowanych systemów SAP. Poniższy przykład dotyczy NW2. Konieczne będzie dostosowanie profilów ASCS/SCS i wykres WYWOŁUJĄCYCH dla wszystkich wystąpień SAP dodanych do klastra.  
 
 * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
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
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** Skonfiguruj użytkowników SAP dla nowo wdrożonego systemu SAP, w tym przykładzie **NW2** i **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Dodaj usługi SAP ASCS i wykres WYWOŁUJĄCYCH dla nowo zainstalowanego systemu SAP do `sapservice` pliku. Poniższy przykład dotyczy systemów SAP Systems **NW2** i **NW3**.  

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi wykres WYWOŁUJĄCYCH do pierwszego węzła. Wykonaj polecenia dla każdego systemu SAP w węźle, w którym zainstalowano wystąpienie ASCS systemu SAP.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** Utwórz zasoby klastra SAP dla nowo zainstalowanego systemu SAP. 

   W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby dla systemów SAP Systems **NW2** i **NW3** w następujący sposób:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
   W przypadku korzystania z programu w ramach architektury Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) Zdefiniuj zasoby dla systemów SAP Systems **NW2** i **NW3** w następujący sposób:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.
   Poniższy przykład przedstawia stan zasobów klastra, po dodaniu SAP Systems **NW2** i **NW3** do klastra. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Na poniższej ilustracji przedstawiono, jak będą wyglądać zasoby w sieci Web konsole (Hawk) o wysokiej dostępności, z zasobami dla systemu SAP system **NW2** expanded.  

   [![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Kontynuuj instalację oprogramowania SAP 

Ukończ instalację oprogramowania SAP, wykonując następujące czynności:

* [Przygotowywanie serwerów aplikacji SAP NetWeaver](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalowanie wystąpienia systemu DBMS](./high-availability-guide-suse.md#install-database)
* [Instalowanie podstawowego serwera aplikacji SAP](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Instalowanie co najmniej jednego wystąpienia aplikacji SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testowanie konfiguracji klastra z obsługą wiele identyfikatorów SID

Poniższe testy są podzbiorem przypadków testowych w przewodnikach z najlepszymi rozwiązaniami dla systemu SUSE. Są one dostępne dla wygody użytkownika. Aby zapoznać się z pełną listą testów klastra, zanotuj następujące informacje:

* Jeśli używany jest serwer NFS o wysokiej dostępności, należy postępować zgodnie z [wysoką dostępnością dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP](./high-availability-guide-suse.md).  
* Jeśli używasz Azure NetApp Files woluminów NFS, postępuj zgodnie [z wysoką dostępnością dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na platformie SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-suse-netapp-files.md)

Zawsze czytaj wskazówki dotyczące najlepszych rozwiązań SUSE i wykonaj wszystkie dodatkowe testy, które mogły zostać dodane.  
Przedstawione testy znajdują się w dwóch węzłach klastra z obsługą wiele identyfikatorów SID z zainstalowanymi trzema systemami SAP.  

1. Test HAGetFailoverConfig i HACheckFailoverConfig

   Uruchom następujące polecenia jako <sapsid> adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenie nie powiedzie się z powodu niewystarczającej ilości pamięci, może to być spowodowane przez myślniki w nazwie hosta. Jest to znany problem, który zostanie rozwiązany przez SUSE w pakiecie SAP-SUSE-Cluster-Connector.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Ręcznie Migruj wystąpienie ASCS. W przykładzie przedstawiono Migrowanie wystąpienia ASCS dla oprogramowania SAP system NW2.  
   Stan zasobu przed rozpoczęciem testu:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie NW2 ASCS.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Test HAFailoverToNode. W przedstawionym tutaj teście przedstawiono Migrowanie wystąpienia ASCS dla oprogramowania SAP system NW2.  

   Stan zasobu przed rozpoczęciem testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenia jako **NW2** adm, aby przeprowadzić migrację wystąpienia ASCS NW2.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione co najmniej jedno wystąpienie ASCS. W tym przykładzie wykonamy polecenie w `slesmsscl2` lokalizacji, w której działają wystąpienia ASCS dla NW1 i NW3.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Jeśli używasz SBD, Pacemaker nie powinien automatycznie rozpoczynać się w zamkniętym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Użyj następujących poleceń, aby uruchomić Pacemaker w zamkniętym węźle, wyczyść komunikaty SBD i wyczyść zasoby zakończone niepowodzeniem.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Stan zasobu po teście:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
