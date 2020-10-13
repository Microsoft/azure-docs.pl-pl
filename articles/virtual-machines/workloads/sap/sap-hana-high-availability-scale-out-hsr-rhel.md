---
title: SAP HANA skalowanie w poziomie za pomocą HSR i Pacemaker na RHEL | Microsoft Docs
description: SAP HANA skalowanie w poziomie za pomocą HSR i Pacemaker na RHEL
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/02/2020
ms.author: radeltch
ms.openlocfilehash: edca4b44bd9e7aa9f100db3cea0bc69880a4c533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744851"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Wysoka dostępność SAP HANA skalowalnego w poziomie systemu na Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


W tym artykule opisano sposób wdrażania systemu SAP HANA o wysokiej dostępności w konfiguracji skalowalnej w poziomie za pomocą replikacji systemu HANA (HSR) i Pacemaker na maszynach wirtualnych platformy Azure Red Hat Enterprise Linux. Udostępnione systemy plików w prezentowanej architekturze są udostępniane przez [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) i są instalowane za pośrednictwem systemu plików NFS.  

W przykładowych konfiguracjach, poleceniach instalacji i tak dalej wystąpienie HANA ma wartość **03** , a identyfikator systemu Hana to **HN1**. Przykłady są oparte na platformie HANA 2,0 SP4 i Red Hat Enterprise Linux dla oprogramowania SAP 7,6. 

Przed rozpoczęciem zapoznaj się z następującymi informacjami i dokumentami SAP:

* Uwaga dotycząca oprogramowania SAP [1928533] obejmuje:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Test SAP [2015553]: zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure
* Uwaga SAP [2002167] ma zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Test SAP [2178632]: zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure
* Test SAP [2191498]: zawiera wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure
* Test SAP [2243692]: zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure
* Uwaga dotycząca oprogramowania SAP [1999351]: zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla oprogramowania SAP
* Uwaga dotycząca oprogramowania SAP [1900823]: zawiera informacje o wymaganiach dotyczących magazynu SAP HANA
* [Strona typu wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): zawiera wszystkie wymagane uwagi SAP dla systemu Linux
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Wymagania sieci SAP HANA](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Przewodnik po sieci Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Jak mogę skonfigurować replikację systemu SAP HANA Scale-Out w klastrze Pacemaker z systemami plików HANA w udziałach NFS](https://access.redhat.com/solutions/5423971)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zainstaluj SAP HANA na Red Hat Enterprise Linux do użycia w Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat Enterprise Linux rozwiązanie do SAP HANA Scale-Out i replikacji systemu](https://access.redhat.com/solutions/4386601)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]
* [Dokumentacja Azure NetApp Files][anf-azure-doc] 


## <a name="overview"></a>Omówienie

Jedną z metod zapewnienia wysokiej dostępności platformy HANA w przypadku instalacji w poziomie platformy Hana jest skonfigurowanie replikacji systemu HANA i ochronę rozwiązania przy użyciu klastra Pacemaker, aby umożliwić automatyczne przełączanie do trybu failover. Gdy aktywny węzeł ulegnie awarii, klaster przejdzie w tryb failover zasobów platformy HANA do innej lokacji.  
Pokazana konfiguracja przedstawia trzy węzły HANA w każdej lokacji, a także węzeł większościer, aby zapobiec scenariuszowi Split-mózg. Instrukcje można dostosować, aby dołączyć więcej maszyn wirtualnych jako węzły bazy danych HANA.  

Udostępniony system plików HANA `/hana/shared` w prezentowanej architekturze jest udostępniany przez [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Jest on instalowany za pośrednictwem NFSv 4.1 w każdym węźle HANA w tej samej lokacji replikacji systemu HANA. Systemy plików `/hana/data` i `/hana/log` są lokalnymi systemami plików i nie są współużytkowane przez węzły bazy danych Hana. SAP HANA zostanie zainstalowana w trybie nieudostępnionym. 

> [!TIP]
> Aby uzyskać zalecane SAP HANA Konfiguracje magazynów, zobacz [SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).   

[![SAP HANA skalowanie w poziomie przy użyciu klastra HSR i Pacemaker](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Na powyższym diagramie trzy podsieci są reprezentowane w jednej sieci wirtualnej platformy Azure, postępując zgodnie z zaleceniami SAP HANA sieci: 
* w przypadku komunikacji z klientem — `client` 10.23.0.0/24  
* w przypadku wewnętrznej komunikacji między węzłami HANA — `inter` 10.23.1.128/26  
* w przypadku replikacji systemu HANA — `hsr` 10.23.1.192/26  

Jak `/hana/data` i `/hana/log` są wdrażane na dyskach lokalnych, nie trzeba wdrażać oddzielnej podsieci i oddzielnych kart sieci wirtualnych do komunikacji z magazynem.  

Woluminy usługi Azure NetApp są wdrażane w oddzielnej podsieci [delegowane do Azure NetApp Files] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Konfigurowanie infrastruktury

W poniższych instrukcjach przyjęto założenie, że została już utworzona grupa zasobów, Sieć wirtualna platformy Azure z trzema podsieciami sieci platformy Azure: `client` , `inter` i `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Wdrażanie maszyn wirtualnych z systemem Linux za pomocą Azure Portal
1. Wdróż maszyny wirtualne platformy Azure.  
Aby zapoznać się z konfiguracją przedstawioną w tym dokumencie, należy wdrożyć siedem maszyn wirtualnych: 
   - trzy maszyny wirtualne, które mają być węzłami bazy danych HANA dla lokacji replikacji HANA 1: **Hana-S1-DB1**, **Hana-S1-DB2** i **Hana-S1-DB3**  
   - trzy maszyny wirtualne, które mają być węzłami bazy danych HANA dla lokacji replikacji HANA 2: **Hana-S2-DB1**, **Hana-S2-DB2** i **Hana-S2-DB3**  
   - Mała maszyna wirtualna, która ma stanowić *większość*twórców: **Hana-s-mm**

   Maszyny wirtualne, wdrożone jako węzły SAP DB HANA, powinny być certyfikowane przez oprogramowanie SAP dla HANA jako opublikowane w [katalogu sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Podczas wdrażania węzłów bazy danych HANA upewnij się, że wybrano opcję [przyspieszona sieć](../../../virtual-network/create-vm-accelerated-networking-cli.md) .  
  
   W przypadku węzła producent większości można wdrożyć małą maszynę wirtualną, ponieważ ta maszyna wirtualna nie uruchamia żadnego z zasobów SAP HANA. Maszyna wirtualna większość twórców jest używana w konfiguracji klastra w celu osiągnięcia nieparzystej liczby węzłów klastra w scenariuszu z podziałem na mózg. Maszyna wirtualna większość maszyn wirtualnych potrzebuje tylko jednego interfejsu sieci wirtualnej w `client` podsieci w tym przykładzie.        

   Wdróż lokalne dyski zarządzane dla systemów `/hana/data` i `/hana/log` . Minimalna zalecana konfiguracja magazynu dla systemu `/hana/data` i `/hana/log` została opisana w temacie [SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).

   Wdróż podstawowy interfejs sieciowy dla każdej maszyny wirtualnej w `client` podsieci sieci wirtualnej.  
   Po wdrożeniu maszyny wirtualnej za pośrednictwem Azure Portal nazwa interfejsu sieciowego jest generowana automatycznie. W tych instrukcjach dotyczących uproszczenia będziemy odnosić się do automatycznie generowanych, podstawowych interfejsów sieciowych dołączonych do `client` podsieci sieci wirtualnej platformy Azure jako **Hana-S1-DB1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-DB3-Client**i tak dalej.  


   > [!IMPORTANT]
   > Upewnij się, że wybrany system operacyjny to SAP-Certified for SAP HANA na określonych typach maszyn wirtualnych, których używasz. Aby uzyskać listę SAP HANA certyfikowane typy maszyn wirtualnych i wersje systemu operacyjnego dla tych typów, przejdź do witryny [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Kliknij Szczegóły wymienionego typu maszyny wirtualnej, aby uzyskać pełną listę obsługiwanych przez SAP HANA wydań systemu operacyjnego dla tego typu.  
  

2. Utwórz sześć interfejsów sieciowych, po jednej dla każdej maszyny wirtualnej usługi HANA DB w `inter` podsieci sieci wirtualnej (w tym przykładzie platformą **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-** z i Hana- **S2-DB3-Inter**).  

3. Utwórz sześć interfejsów sieciowych, jedną dla każdej maszyny wirtualnej usługi HANA DB, w `hsr` podsieci sieci wirtualnej (w tym przykładzie: **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR**i **Hana-S2-DB3-HSR**).  

4. Dołącz nowo utworzone interfejsy sieci wirtualnej do odpowiednich maszyn wirtualnych:  

    a. Przejdź do maszyny wirtualnej w [Azure Portal](https://portal.azure.com/#home).  

    b. W lewym okienku wybierz pozycję **Virtual Machines**. Odfiltruj nazwę maszyny wirtualnej (na przykład **Hana-S1-DB1**), a następnie wybierz maszynę wirtualną.  

    c. W okienku **Przegląd** wybierz pozycję **Zatrzymaj** , aby cofnąć przydział maszyny wirtualnej.  

    d. Wybierz pozycję **Sieć**, a następnie Dołącz interfejs sieciowy. Z listy rozwijanej **Dołącz interfejs sieciowy** wybierz już utworzone interfejsy sieciowe dla `inter` `hsr` podsieci i.  
    
    e. Wybierz pozycję **Zapisz**. 
 
    f. Powtórz kroki od b do e dla pozostałych maszyn wirtualnych (w naszym przykładzie  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** i **Hana-S2-DB3**).
 
    przykład Pozostaw teraz maszyny wirtualne w stanie zatrzymania. Następnie włączysz [przyspieszoną sieć](../../../virtual-network/create-vm-accelerated-networking-cli.md) dla wszystkich nowo dołączonych interfejsów sieciowych.  

5. Włącz przyspieszone sieci dla dodatkowych interfejsów sieciowych dla `inter` podsieci i, `hsr` wykonując następujące czynności:  

    a. Otwórz [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w [Azure Portal](https://portal.azure.com/#home).  

    b. Wykonaj następujące polecenia, aby włączyć przyspieszone sieci dla dodatkowych interfejsów sieciowych dołączonych do `inter` `hsr` podsieci i.  

    ```
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Uruchamianie maszyn wirtualnych bazy danych HANA

### <a name="deploy-azure-load-balancer"></a>Wdróż Azure Load Balancer

1. Zalecamy korzystanie z usługi równoważenia obciążenia w warstwie Standardowa. Wykonaj następujące czynności konfiguracyjne, aby wdrożyć moduł równoważenia obciążenia w warstwie Standardowa:
   1. Najpierw Utwórz pulę adresów IP frontonu:

      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **Hana-fronton**).
      1. Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.23.0.18**).
      1. Wybierz przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.

   1. Następnie Utwórz pulę zaplecza i Dodaj wszystkie maszyny wirtualne klastra do puli zaplecza:

      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **Hana-zaplecze**).
      1. Wybierz pozycję **Dodaj maszynę wirtualną**.
      1. Wybierz pozycję **Maszyna wirtualna**.
      1. Wybierz Maszyny wirtualne klastra SAP HANA i ich adresy IP dla `client` podsieci.
      1. Wybierz pozycję **Dodaj**.

   1. Następnie utwórz sondę kondycji:

      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-HP**).
      1. Wybierz pozycję **TCP** jako protokół i port 625**03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
      1. Wybierz przycisk **OK**.

   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Hana-lb**).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**, **Hana — zaplecze** i **Hana-HP**).
      1. Wybierz pozycję **porty ha**.
      1. Zwiększ **limit czasu bezczynności** do 30 minut.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.

   > [!Note]
   > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Zobacz również artykuł SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Wdrażanie infrastruktury Azure NetApp Files 

Wdróż woluminy ANF dla `/hana/shared` systemu plików. Wymagany jest osobny `/hana/shared` wolumin dla każdej lokacji replikacji systemu Hana. Aby uzyskać więcej informacji, zobacz [Konfigurowanie infrastruktury Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel#set-up-the-azure-netapp-files-infrastructure).

W tym przykładzie użyto następujących Azure NetApp Files woluminów: 

* Volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* wolumin **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>Konfiguracja i przygotowanie systemu operacyjnego

Instrukcje w następnych sekcjach są poprzedzone jednym z następujących skrótów:
* **[A]**: dotyczy wszystkich węzłów
* **[Ah]**: dotyczy wszystkich węzłów bazy danych Hana
* **[M]**: dotyczy węzła producenta większości
* **[AH1]**: dotyczy wszystkich węzłów bazy danych Hana w lokacji 1
* **[AH2]**: dotyczy wszystkich węzłów bazy danych Hana w lokacji 2
* **[1]**: dotyczy tylko bazy danych Hana w węźle 1, lokacja 1
* **[2]**: dotyczy tylko bazy danych Hana węzeł 1, lokacja 2


Skonfiguruj i przygotuj system operacyjny, wykonując następujące czynności:

1. **[A]** przechowywanie plików hosta na maszynach wirtualnych. Uwzględnij wpisy dla wszystkich podsieci. Do `/etc/hosts` tego przykładu dodano następujące wpisy.  

    ```
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** Zainstaluj pakiet klienta systemu plików NFS.  

    ```yum install nfs-utils ```


3. **[Ah]** Konfiguracja Red Hat dla platformy HANA.  

    Skonfiguruj RHEL zgodnie z opisem w temacie <https://access.redhat.com/solutions/2447641> i w następujących uwagach dotyczących oprogramowania SAP:  
   - [2292690 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 9. x](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Przygotowywanie systemów plików
### <a name="mount-the-shared-file-systems"></a>Instalowanie udostępnionych systemów plików

W tym przykładzie udostępnione systemy plików HANA są wdrażane na Azure NetApp Files i instalowane przez NFSv4.  

1. **[Ah]** Utwórz punkty instalacji dla woluminów bazy danych HANA.  

    ```
    mkdir -p /hana/shared
    ```

2. **[Ah]** Sprawdź ustawienie domeny systemu plików NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, to jest, **`defaultv4iddomain.com`** a mapowanie jest ustawione na wartość **nikt**.  
   Ten krok jest wymagany tylko w przypadku korzystania z platformy Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Upewnij się, że na maszynie wirtualnej ustawiono domenę systemu plików NFS `/etc/idmapd.conf` zgodną z domyślną konfiguracją domeny na Azure NetApp Files: **`defaultv4iddomain.com`** . Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, wówczas uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody` .  

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Sprawdź `nfs4_disable_idmapping` . Powinna być ustawiona na wartość **Y**. Aby utworzyć strukturę katalogów, w której `nfs4_disable_idmapping` znajduje się, wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.  
   Ten krok jest wymagany tylko w przypadku korzystania z platformy Azure NetAppFiles NFSv 4.1.  

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Aby uzyskać więcej informacji na temat zmiany `nfs4_disable_idmapping` parametru, zobacz https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Zainstaluj udostępnione woluminy Azure NetApp Files na maszynach wirtualnych SITE1 HANA DB.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Zainstaluj udostępnione woluminy Azure NetApp Files na maszynach wirtualnych SITE2 HANA DB.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Sprawdź, czy odpowiednie `/hana/shared/` systemy plików są zainstalowane na wszystkich maszynach wirtualnych w usłudze Hana DB z wersją protokołu NFS **NFSv4**.  

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Przygotowywanie danych i rejestrowanie lokalnych systemów plików
W prezentowanej konfiguracji i systemach plików `/hana/data` `/hana/log` wdrożonych na dysku zarządzanym i są one lokalnie dołączone do każdej maszyny wirtualnej platformy Hana. Należy wykonać kroki, aby utworzyć lokalne dane i woluminy dziennika na każdej maszynie wirtualnej platformy HANA. 

Skonfiguruj układ dysku za pomocą  **Menedżera woluminów logicznych (LVM)**. W poniższym przykładzie przyjęto założenie, że każda maszyna wirtualna HANA ma trzy dyski z danymi, które są używane do tworzenia dwóch woluminów.

1. **[Ah]** Wyświetl listę wszystkich dostępnych dysków:
    ```
    ls /dev/disk/azure/scsi1/lun*
    ```

   Przykładowe dane wyjściowe:

    ```
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Utwórz woluminy fizyczne dla wszystkich dysków, które mają być używane:
    ```
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednego dla katalogu udostępnionego SAP HANA:
    ```
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Utwórz woluminy logiczne. 
   Wolumin liniowy jest tworzony, gdy jest używany `lvcreate` bez `-i` przełącznika. Zalecamy utworzenie woluminu rozłożonego w celu uzyskania lepszej wydajności operacji we/wy i dostosowanie rozmiarów rozłożonych do wartości przedstawionych w [SAP HANA konfiguracjach magazynu maszyny wirtualnej](./hana-vm-operations-storage.md). `-i`Argument powinien być liczbą podstawowych woluminów fizycznych, a `-I` argument jest rozmiarem paska. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` argument Switch ma wartość **2**. Rozmiar rozłożonego woluminu danych to **256 KiB**. Jeden wolumin fizyczny jest używany w woluminie dziennika, więc żadne `-i` `-I` przełączniki nie są jawnie używane dla poleceń woluminu dziennika.  

   > [!IMPORTANT]
   > Użyj `-i` przełącznika i ustaw go na numer bazowego woluminu fizycznego, jeśli używasz więcej niż jednego woluminu fizycznego dla poszczególnych woluminów danych lub dzienników. Użyj `-I` przełącznika, aby określić rozmiar paska podczas tworzenia woluminu rozłożonego.  
   > Zapoznaj się z [SAP HANA konfiguracjami magazynu maszyn wirtualnych](./hana-vm-operations-storage.md) , aby uzyskać zalecane konfiguracje magazynu, w tym rozmiary i liczbę dysków.  

    ```
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Utwórz katalogi instalacji i skopiuj identyfikator UUID wszystkich woluminów logicznych:
    ```
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Utwórz `fstab` wpisy dla woluminów logicznych i zainstaluj:
    ```
    sudo vi /etc/fstab
    ```

   Wstaw następujący wiersz do `/etc/fstab` pliku:

    ```
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Zainstaluj nowe woluminy:

    ```
    sudo mount -a
    ```

## <a name="installation"></a>Instalacja  

W tym przykładzie do wdrażania SAP HANA w konfiguracji skalowania z HSR na maszynach wirtualnych platformy Azure została użyta platforma HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Przygotowanie do instalacji platformy HANA

1. **[Ah]** Przed instalacją HANA należy ustawić hasło główne. Hasło główne można wyłączyć po zakończeniu instalacji. Uruchom jako `root` polecenie `passwd` .  

2. **[1, 2]** Zmień uprawnienia na `/hana/shared` 
    ```
    chmod 775 /hana/shared
    ```

3. **[1]** Sprawdź, czy możesz zalogować się za pośrednictwem protokołu SSH do maszyn wirtualnych usługi Hana DB w tej lokacji **Hana-S1-DB2** i **Hana-S1-DB3**bez monitowania o hasło.  
   Jeśli tak nie jest, klucze SSH programu Exchange, zgodnie z opisem w temacie [Korzystanie z uwierzytelniania opartego na kluczach](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Sprawdź, czy możesz zalogować się za pośrednictwem protokołu SSH do maszyn wirtualnych usługi Hana DB w tej lokacji **Hana-S2-DB2** i **Hana-S2-DB3**bez monitowania o hasło.  
   Jeśli tak nie jest, klucze SSH programu Exchange, zgodnie z opisem w temacie [Korzystanie z uwierzytelniania opartego na kluczach](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Zainstaluj dodatkowe pakiety, które są wymagane dla platformy HANA 2,0 SP4. Aby uzyskać więcej informacji, zobacz temat SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) dla RHEL 7. 

    ```
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** Wyłącz zaporę tymczasowo, aby nie zakłócać instalacji platformy Hana. Po zakończeniu instalacji platformy HANA można ją ponownie włączyć. 
    ```
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>Instalacja platformy HANA w pierwszym węźle w poszczególnych lokacjach

1. **[1]** Zainstaluj SAP HANA, postępując zgodnie z instrukcjami w [przewodniku instalacji i aktualizacji SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). W poniższych instrukcjach zostanie wyświetlona instalacja SAP HANA w pierwszym węźle w lokacji 1.   

   a. Uruchom program **hdblcm** jako `root` z katalogu oprogramowania instalacyjnego platformy Hana. Użyj `internal_network` parametru i przekaż przestrzeń adresową dla podsieci, która jest używana dla wewnętrznej komunikacji między węzłami Hana.  

    ```
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. W wierszu polecenia wprowadź następujące wartości:

     * Dla **opcji wybierz akcję**: wprowadź **1** (na potrzeby instalacji)
     * Aby uzyskać **dodatkowe składniki do instalacji**: wprowadź **2, 3**
     * Ścieżka instalacji: naciśnij klawisz ENTER (wartość domyślna to/Hana/Shared)
     * **Nazwa hosta lokalnego**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Czy chcesz dodać hosty do systemu?**: wprowadź **n**
     * Dla **identyfikatora System SAP HANA**: wprowadź **HN1**
     * Dla **wystąpienia o numerze** [00]: wprowadź wartość **03**
     * Dla **grupy procesów roboczych hosta lokalnego** [domyślnie]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **opcji wybierz użycie systemu/wprowadź indeks [4]**: wprowadź **4** (dla niestandardowych)
     * W przypadku **lokalizacji woluminów danych** [/Hana/Data/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Lokalizacja dzienników woluminów dziennika** [/Hana/log/HN1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Aby **ograniczyć maksymalną alokację pamięci?** [n]: wprowadź **n**
     * **Nazwa hosta certyfikatu dla hosta Hana-S1-DB1** [Hana-S1-DB1]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Hasło użytkownika agenta hosta SAP (sapadm)**: wprowadź hasło
     * **Potwierdzenie hasła użytkownika agenta hosta SAP (sapadm)**: wprowadź hasło
     * W polu **hasło administratora systemu (hn1adm)**: wprowadź hasło
     * W przypadku **katalogu macierzystego administratora systemu** [/usr/SAP/HN1/Home]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **powłoki logowania administratora systemu** [/bin/sh]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Identyfikator użytkownika administratora systemu** [1001]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W polu **Wprowadź identyfikator grupy użytkowników (sapsys)** [79]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **systemu systemowej bazy danych hasło użytkownika (system)**: wprowadź hasło systemu
     * Potwierdź hasło użytkownika systemowej **bazy danych (system)**: wprowadź hasło systemu
     * **Czy po ponownym uruchomieniu komputera należy ponownie uruchomić system?** [n]: wprowadź **n** 
     * Na **pewno chcesz kontynuować (t/n)**: Sprawdź poprawność podsumowania i jeśli wszystko wygląda dobrze, wprowadź wartość **y**

2. **[2]** Powtórz poprzedni krok w celu zainstalowania SAP HANA na pierwszym WĘŹLE w lokacji 2.   

3. **[1, 2]** Sprawdź global.ini  

   Wyświetl global.ini i upewnij się, że konfiguracja wewnętrznego SAP HANA komunikacji między węzłami jest na miejscu. Sprawdź sekcję **komunikacja** . Powinna mieć przestrzeń adresową dla `inter` podsieci i `listeninterface` powinna być ustawiona na wartość `.internal` . Sprawdź sekcję **internal_hostname_resolution** . Powinny mieć adresy IP dla maszyn wirtualnych HANA należących do `inter` podsieci.  

   ```
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** przygotowanie `global.ini` do instalacji w środowisku nieudostępnionym, zgodnie z opisem w artykule SAP Uwaga [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** ponowne uruchomienie SAP HANA w celu aktywowania zmian.  

   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** Sprawdź, czy interfejs klienta będzie używać adresów IP z `client` podsieci do komunikacji.  

    ```
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Aby uzyskać informacje na temat weryfikowania konfiguracji, zobacz artykuł SAP Uwaga [2183363-konfiguracja sieci wewnętrznej SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Zmień uprawnienia do katalogów danych i dzienników, aby uniknąć błędu instalacji platformy HANA.  

   ```
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** zainstaluj dodatkowe węzły platformy Hana. Przykładowe instrukcje w tym kroku dotyczą witryny 1.  
   a. Uruchom program rezydenta **hdblcm** jako `root` .    
    ```
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. W wierszu polecenia wprowadź następujące wartości:

     * Dla **opcji wybierz akcję**: wprowadź **2** (w przypadku dodawania hostów)
     * Aby **wprowadzić nazwy hostów rozdzielane przecinkami, które mają zostać dodane**: Hana-S1-DB2, Hana-S1-DB3
     * Aby uzyskać **dodatkowe składniki do instalacji**: wprowadź **2, 3**
     * W polu **Wprowadź nazwę użytkownika głównego [root]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **opcji wybierz role dla hosta "Hana-S1-DB2" [1]**: 1 (dla procesu roboczego)
     * W polu **wprowadź grupę trybu failover hosta dla hosta "Hana-S1-DB2" [default]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Wprowadź numer partycji magazynu dla hosta "Hana-S1-DB2" [<<assign automatically>>]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W polu **wprowadź grupę procesów roboczych dla hosta "Hana-S1-DB2" [default]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla **opcji wybierz role hosta "Hana-S1-DB3" [1]**: 1 (dla procesu roboczego)
     * W polu **wprowadź grupę trybu failover hosta dla hosta "Hana-S1-DB3" [default]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Wprowadź numer partycji magazynu dla hosta "Hana-S1-DB3" [<<assign automatically>>]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Dla ustawienia **wprowadź grupę procesów roboczych dla hosta "Hana-S1-DB3" [domyślnie]**: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * W polu **hasło administratora systemu (hn1adm)**: wprowadź hasło
     * **Wprowadź hasło użytkownika agenta hosta SAP (sapadm)**: wprowadź hasło
     * **Potwierdzenie hasła użytkownika agenta hosta SAP (sapadm)**: wprowadź hasło
     * **Nazwa hosta certyfikatu dla hosta Hana-S1-DB2** [Hana-S1-DB2]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * **Nazwa hosta certyfikatu dla hosta Hana-S1-DB3** [Hana-S1-DB3]: naciśnij klawisz ENTER, aby zaakceptować wartość domyślną
     * Na **pewno chcesz kontynuować (t/n)**: Sprawdź poprawność podsumowania i jeśli wszystko wygląda dobrze, wprowadź wartość **y**

9. **[2]** Powtórz poprzedni krok, aby zainstalować pomocnicze węzły SAP HANA w lokacji 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 2,0

1. **[1]** Skonfiguruj replikację systemu w lokacji 1:

   Wykonaj kopię zapasową baz danych jako **hn1**adm:

    ```
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Skopiuj systemowe pliki PKI do lokacji dodatkowej:

    ```
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Utwórz lokację główną:

    ```
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Skonfiguruj replikację systemu w lokacji 2:
    
   Zarejestruj drugą lokację, aby rozpocząć replikację systemu. Uruchom następujące polecenie jako <hanasid \> adm:

    ```
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Sprawdź stan replikacji

   Sprawdź stan replikacji i poczekaj na zsynchronizowanie wszystkich baz danych.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** Zmień konfigurację platformy Hana w taki sposób, aby komunikacja z replikacją systemu Hana była kierowana w przypadku, gdy interfejsy sieci wirtualnej replikacji systemu platformy Hana.   
   - Zatrzymaj platformę HANA w obu lokacjach
    ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Edytuj global.ini, aby dodać mapowanie hosta dla replikacji systemu HANA: Użyj adresów IP z `hsr` podsieci.  
    ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - Uruchom platformę HANA w obu lokacjach
   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw hostów na potrzeby replikacji systemu](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Włącz ponownie zaporę.  
   - Włącz ponownie zaporę
       ```
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Otwórz wymagane porty zapory. Konieczne będzie dostosowanie portów dla numeru wystąpienia platformy HANA.  

       > [!IMPORTANT]
       > Utwórz reguły zapory, aby umożliwić komunikację między węzłami platformy HANA i ruchem klientów. Wymagane porty są wymienione w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Poniższe polecenia są tylko przykładem. W tym scenariuszu z użytym numerem systemu 03.

       ```
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego serwera platformy Hana.
Uwzględnij wszystkie maszyny wirtualne, w tym większość twórców w klastrze.  

> [!IMPORTANT]
> Nie ustawiono wartości `quorum expected-votes` 2, ponieważ nie jest to klaster z dwoma węzłami.  
> Upewnij się, że właściwość klaster `concurrent-fencing`  jest włączona, co spowoduje, że ogrodzenie węzłów jest deserializowane.   

## <a name="create-file-system-resources"></a>Tworzenie zasobów systemu plików

1. **[1, 2]** Zatrzymaj SAP HANA w obu lokacjach replikacji. Wykonaj jako <SID \> adm.  

    ```
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Odinstaluj system plików `/hana/shared` , który został tymczasowo zainstalowany na potrzeby instalacji na wszystkich maszynach wirtualnych usługi Hana DB. Należy zatrzymać wszystkie procesy i sesje, które korzystają z systemu plików, zanim będzie można go odinstalować. 
 
    ```
    umount /hana/shared 
    ```

3. **[1]** Utwórz zasoby klastra systemu plików dla `/hana/shared` programu w stanie wyłączenia. Zasoby są tworzone z opcją `--disabled` , ponieważ przed włączeniem instalacji należy zdefiniować ograniczenia lokalizacji.  

    ```
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` atrybut jest dodawany do operacji monitorowania, tak aby operacje monitorowania wykonywały test odczytu/zapisu w systemie plików. Bez tego atrybutu operacja monitorowania sprawdza tylko, czy system plików jest zainstalowany. Może to być spowodowane problemem, ponieważ po utracie łączności system plików może pozostać zainstalowany, mimo że jest niedostępny.  

   `on-fail=fence` atrybut jest również dodawany do operacji monitorowania. W przypadku tej opcji, jeśli operacja monitorowania zakończy się niepowodzeniem w węźle, ten węzeł zostanie natychmiast ogrodzony. Bez tej opcji domyślnym zachowaniem jest zatrzymanie wszystkich zasobów, które są zależne od zasobu, a następnie ponowne uruchomienie zasobu, a następnie uruchomienie wszystkich zasobów zależnych od zasobu, który zakończył się niepowodzeniem. To działanie może nie tylko zająć dużo czasu, gdy zasób SAPHana zależy od zasobu, ale również może zakończyć się niepowodzeniem. Nie można pomyślnie zatrzymać zasobu SAPHana, jeśli udział NFS zawierający dane binarne platformy HANA jest niedostępny.  

4. **[1]** Skonfiguruj i sprawdź atrybuty węzła. Wszystkie węzły SAP HANA DB w lokacji replikacji 1 są przypisanymi atrybutami `S1` , a wszystkie węzły SAP HANA DB w lokacji replikacji 2 są przypisanymi atrybutami `S2` .  

    ```
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** Skonfiguruj ograniczenia, które określają, gdzie mają zostać zainstalowane systemy plików NFS i włączyć zasoby systemu plików.  
    ```
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Po włączeniu zasobów systemu plików klaster zainstaluje `/hana/shared` systemy plików.
 
6. **[Ah]** Sprawdź, czy woluminy ANF są zainstalowane `/hana/shared` we wszystkich maszynach wirtualnych usługi Hana DB w obu lokacjach.

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** Skonfiguruj zasoby atrybutów. Skonfiguruj ograniczenia, które będą ustawiać atrybuty na `true` , jeśli instalacje systemu plików NFS dla programu `hana/shared` są zainstalowane.  

    ```
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Jeśli konfiguracja obejmuje inne systemy plików, oprócz/ `hana/shared` , które są zainstalowane w systemie plików NFS, a następnie dołączania `sequential=false` opcji, tak że nie ma żadnych zależności porządkunia między systemami plików. Wszystkie zainstalowane systemy plików NFS muszą zostać uruchomione przed odpowiednim zasobem atrybutu, ale nie muszą być uruchamiane w żadnej kolejności względem siebie. Aby uzyskać więcej informacji, zobacz [Jak mogę konfigurowania SAP HANA Scale-Out HSR w klastrze Pacemaker, gdy systemy plików Hana to udziały NFS](https://access.redhat.com/solutions/5423971).  

8. **[1]** Umieść Pacemaker w trybie konserwacji, przygotowując do tworzenia zasobów klastra Hana.  
    ```
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

1. **[A]** Zainstaluj agenta zasobów skalowalnego w poziomie dla wszystkich węzłów klastra, w tym większość twórców.    

    ```
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Zapoznaj się z [zasadami obsługi klastrów RHEL ha — zarządzanie SAP HANA w klastrze](https://access.redhat.com/articles/3397471) dla minimalnej obsługiwanej wersji pakietu `resource-agents-sap-hana-scaleout` dla wydania systemu operacyjnego.  

2. **[1, 2]** instalacja platformy Hana "hak replikacji systemu". Punkt zaczepienia musi być zainstalowany w jednym węźle bazy danych HANA w każdej lokacji replikacji systemu. SAP HANA powinna być nadal wyłączona.        

   1. Przygotuj punkt zaczepienia jako `root` 
    ```
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Dostosowanie `global.ini`
    ```
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** Klaster wymaga konfiguracji sudo w węźle klastra dla <SID \> adm. W tym przykładzie, który został osiągnięty przez utworzenie nowego pliku. Wykonaj polecenia jako `root` .    
    ``` 
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** Uruchom SAP HANA w obu lokacjach replikacji. Wykonaj jako <SID \> adm.  

    ```
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** Sprawdź instalację punktu zaczepienia. Wykonaj jako <SID \> adm w aktywnej lokacji replikacji systemu Hana.   

    ```
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** Utwórz zasoby klastra Hana. Wykonaj następujące polecenia jako `root` .    
   1. Upewnij się, że klaster jest już w trybie konserwacji.  
    
   2. Następnie utwórz zasób topologii HANA.  
      W przypadku kompilowania klastra RHEL **7. x** Użyj następujących poleceń:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      W przypadku kompilowania klastra RHEL **8. x** Użyj następujących poleceń:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Następnie utwórz zasób wystąpienia platformy HANA.  
      > [!NOTE]
      > Ten artykuł zawiera odwołania do warunku *podrzędnego*, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.  
 
      W przypadku kompilowania klastra RHEL **7. x** Użyj następujących poleceń:    
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      W przypadku kompilowania klastra RHEL **8. x** Użyj następujących poleceń:  
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Zaleca się, aby jako najlepsze rozwiązanie było możliwe ustawienie AUTOMATED_REGISTER na **nie**, podczas przeprowadzania gruntownych testów zakończonych niepowodzeniem, aby zapobiec automatycznemu zarejestrowaniu wystąpienia niepowodzenia jako pomocniczego. Po pomyślnym zakończeniu testów zakończonych niepowodzeniem ustaw wartość AUTOMATED_REGISTER **tak**, aby po automatycznym wznowieniu replikacji systemu przejęcia. 

   4. Utwórz wirtualny adres IP i skojarzone zasoby.  
      ```
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Tworzenie ograniczeń klastra  
      W przypadku kompilowania klastra RHEL **7. x** Użyj następujących poleceń:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      W przypadku kompilowania klastra RHEL **8. x** Użyj następujących poleceń:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** Umieść klaster poza trybem konserwacji. Upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione.  
    ```
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej konfiguracji platformy HANA. Na przykład może być konieczne zwiększenie limitu czasu uruchamiania, Jeśli uruchomienie SAP HANA bazy danych trwa dłużej.
  
## <a name="test-sap-hana-failover"></a>Testowanie pracy w trybie failover SAP HANA 

1. Przed rozpoczęciem testu sprawdź, czy klaster i SAP HANA stan replikacji systemu.  

   a. Sprawdź, czy nie ma żadnych akcji klastra zakończonych niepowodzeniem  
     ```
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Sprawdź, czy replikacja systemu SAP HANA jest zsynchronizowana

      ```
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Sprawdź konfigurację klastra dla scenariusza awarii, gdy węzeł utraci dostęp do udziału NFS ( `/hana/shared` ).  

   Agenci zasobów SAP HANA zależą od plików binarnych przechowywanych w programie w `/hana/shared` celu wykonywania operacji w trybie failover. System plików `/hana/shared` jest instalowany za pośrednictwem systemu plików NFS w przedstawionej konfiguracji. Test, który można wykonać, to ponowne zainstalowanie `/hana/shared` systemu plików jako *tylko do odczytu*. To podejście sprawdza, czy klaster przejdzie w tryb failover, jeśli dostęp do programu `/hana/shared` zostanie utracony w aktywnej lokacji replikacji systemu.  

   **Oczekiwany wynik**: po ponownym zainstalowaniu `/hana/shared` programu jako *tylko do odczytu*, operacja monitorowania, która wykonuje operację odczytu/zapisu w systemie plików, nie powiedzie się, ponieważ nie będzie można zapisywać w systemie plików i wyzwalać tryb failover zasobów platformy Hana. Ten sam wynik jest oczekiwany, gdy węzeł HANA utraci dostęp do udziału NFS.  
     
   Stan zasobów klastra można sprawdzić, wykonując `crm_mon` lub `pcs status` . Stan zasobu przed rozpoczęciem testu:
      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Aby symulować awarię `/hana/shared` na jednej z maszyn wirtualnych lokacji podstawowej replikacji, należy wykonać następujące polecenie:
      ```
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   Maszyna wirtualna HANA, która utraciła dostęp do programu `/hana/shared` , powinna zostać uruchomiona lub zatrzymana, w zależności od konfiguracji klastra. Zasoby klastra są migrowane do innej lokacji replikacji systemu HANA.  
         
   Jeśli klaster nie został uruchomiony na maszynie wirtualnej, który został ponownie uruchomiony, uruchom klaster, wykonując następujące kroki: 

      ```
      # Start the cluster 
      pcs cluster start
      ```
   
   Po uruchomieniu klastra system plików `/hana/shared` zostanie automatycznie zainstalowany.     
   Jeśli ustawisz AUTOMATED_REGISTER = "false", musisz skonfigurować SAP HANA replikację systemu w lokacji dodatkowej. W takim przypadku można wykonać te polecenia, aby ponownie skonfigurować SAP HANA jako pomocniczy.   

      ```
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Stan zasobów po teście: 

      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Zalecamy gruntowną przetestowanie konfiguracji klastra SAP HANA, wykonując także testy opisane w [HA SAP HANA na maszynach wirtualnych platformy Azure w witrynie RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup).


## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha].
