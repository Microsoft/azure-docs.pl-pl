---
title: SAP HANA skalowanie w poziomie za pomocą HSR i Pacemaker na SLES | Microsoft Docs
description: SAP HANA skalowanie w poziomie za pomocą HSR i Pacemaker na SLES.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315497"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Wysoka dostępność dla SAP HANA skalowalnego w poziomie systemu z HSR na SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


W tym artykule opisano sposób wdrażania systemu SAP HANA o wysokiej dostępności w konfiguracji skalowalnej w poziomie za pomocą replikacji systemu HANA (HSR) i Pacemaker na maszynach wirtualnych platformy Azure SUSE Linux Enterprise Server. Udostępnione systemy plików w prezentowanej architekturze są udostępniane przez [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) i są instalowane za pośrednictwem systemu plików NFS.  

W przykładowych konfiguracjach, poleceniach instalacji i tak dalej wystąpienie HANA ma wartość **03** , a identyfikator systemu Hana to **HN1**. Przykłady są oparte na platformie HANA 2,0 SP4 i SUSE Linux Enterprise Server 12 SP5. 

Przed rozpoczęciem zapoznaj się z następującymi informacjami i dokumentami SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Uwaga dotycząca oprogramowania SAP [1928533] obejmuje:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Test SAP [2015553]: zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez SAP na platformie Azure
* Test SAP [2205917]: zawiera zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [1944799]: zawiera wskazówki dotyczące oprogramowania sap dla SUSE Linux Enterprise Server aplikacji SAP
* Test SAP [2178632]: zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure
* Test SAP [2191498]: zawiera wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure
* Test SAP [2243692]: zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure
* Uwaga dotycząca oprogramowania SAP [1984787]: zawiera ogólne informacje dotyczące SUSE Linux Enterprise Server 12
* Uwaga dotycząca oprogramowania SAP [1999351]: zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzeniem rozszerzonego monitorowania platformy Azure dla oprogramowania SAP
* Uwaga dotycząca oprogramowania SAP [1900823]: zawiera informacje o wymaganiach dotyczących magazynu SAP HANA
* [Strona typu wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): zawiera wszystkie wymagane uwagi SAP dla systemu Linux
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Przewodniki po najlepszych rozwiązaniach SUSE SAP ha][suse-ha-guide]: zawiera wszystkie informacje wymagane do skonfigurowania NetWeaver wysokiej dostępności i replikacji systemu SAP HANA lokalnego (do użycia jako ogólna linia bazowa). zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji firmy SUSE o wysokiej dostępności 12 SP5](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Obsługa niepowodzenia udziału NFS w klastrze SUSE HA na potrzeby replikacji systemu HANA](https://www.suse.com/support/kb/doc/?id=000019904)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Omówienie

Jedną z metod zapewnienia wysokiej dostępności platformy HANA w przypadku instalacji w poziomie platformy Hana jest skonfigurowanie replikacji systemu HANA i ochronę rozwiązania przy użyciu klastra Pacemaker, aby umożliwić automatyczne przełączanie do trybu failover. Gdy aktywny węzeł ulegnie awarii, klaster przejdzie w tryb failover zasobów platformy HANA do innej lokacji.  
Pokazana konfiguracja przedstawia trzy węzły HANA w każdej lokacji, a także węzeł większościer, aby zapobiec scenariuszowi Split-mózg. Instrukcje można dostosować, aby dołączyć więcej maszyn wirtualnych jako węzły bazy danych HANA.  

Udostępniony system plików HANA `/hana/shared` w prezentowanej architekturze jest udostępniany przez [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Jest on instalowany za pośrednictwem NFSv 4.1 w każdym węźle HANA w tej samej lokacji replikacji systemu HANA. Systemy plików `/hana/data` i `/hana/log` są lokalnymi systemami plików i nie są współużytkowane przez węzły bazy danych Hana. SAP HANA zostanie zainstalowana w trybie nieudostępnionym. 

> [!TIP]
> Aby uzyskać zalecane SAP HANA Konfiguracje magazynów, zobacz [SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md).   

[![SAP HANA skalowanie w poziomie przy użyciu klastra HSR i Pacemaker w systemie SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

Na powyższym diagramie trzy podsieci są reprezentowane w jednej sieci wirtualnej platformy Azure, postępując zgodnie z zaleceniami SAP HANA sieci: 
* w przypadku komunikacji z klientem — `client` 10.23.0.0/24  
* w przypadku wewnętrznej komunikacji między węzłami HANA — `inter` 10.23.1.128/26  
* w przypadku replikacji systemu HANA — `hsr` 10.23.1.192/26  

Jak `/hana/data` i `/hana/log` są wdrażane na dyskach lokalnych, nie trzeba wdrażać oddzielnej podsieci i oddzielnych kart sieci wirtualnych do komunikacji z magazynem.  

Woluminy usługi Azure NetApp są wdrażane w oddzielnej podsieci [delegowanej do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> Replikacja systemu do trzeciej lokacji nie jest obsługiwana. Aby uzyskać szczegółowe informacje, zobacz sekcję "ważne wymagania wstępne" w [scenariuszu zoptymalizowanym pod kątem wydajności skalowanie w systemie SLES SAP HANA](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites).     

## <a name="set-up-the-infrastructure"></a>Konfigurowanie infrastruktury

W poniższych instrukcjach przyjęto założenie, że została już utworzona grupa zasobów, Sieć wirtualna platformy Azure z trzema podsieciami sieci platformy Azure: `client` , `inter` i `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Wdrażanie maszyn wirtualnych z systemem Linux za pomocą Azure Portal
1. Wdróż maszyny wirtualne platformy Azure.  
Aby zapoznać się z konfiguracją przedstawioną w tym dokumencie, należy wdrożyć siedem maszyn wirtualnych: 
   - trzy maszyny wirtualne, które mają być węzłami bazy danych HANA dla lokacji replikacji HANA 1: **Hana-S1-DB1**, **Hana-S1-DB2** i **Hana-S1-DB3**  
   - trzy maszyny wirtualne, które mają być węzłami bazy danych HANA dla lokacji replikacji HANA 2: **Hana-S2-DB1**, **Hana-S2-DB2** i **Hana-S2-DB3**  
   - Mała maszyna wirtualna, która ma stanowić *większość* twórców: **Hana-s-mm**

   Maszyny wirtualne, wdrożone jako węzły SAP DB HANA, powinny być certyfikowane przez oprogramowanie SAP dla HANA jako opublikowane w [katalogu sprzętu SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Podczas wdrażania węzłów bazy danych HANA upewnij się, że wybrano opcję [przyspieszona sieć](../../../virtual-network/create-vm-accelerated-networking-cli.md) .  
  
   W przypadku węzła producent większości można wdrożyć małą maszynę wirtualną, ponieważ ta maszyna wirtualna nie uruchamia żadnego z zasobów SAP HANA. Maszyna wirtualna większość twórców jest używana w konfiguracji klastra w celu osiągnięcia nieparzystej liczby węzłów klastra w scenariuszu z podziałem na mózg. Maszyna wirtualna większość maszyn wirtualnych potrzebuje tylko jednego interfejsu sieci wirtualnej w `client` podsieci w tym przykładzie.        

   Wdróż lokalne dyski zarządzane dla systemów `/hana/data` i `/hana/log` . Minimalna zalecana konfiguracja magazynu dla systemu `/hana/data` i `/hana/log` została opisana w temacie [SAP HANA Konfiguracja magazynu maszyn wirtualnych platformy Azure](./hana-vm-operations-storage.md).

   Wdróż podstawowy interfejs sieciowy dla każdej maszyny wirtualnej w `client` podsieci sieci wirtualnej.  
   Po wdrożeniu maszyny wirtualnej za pośrednictwem Azure Portal nazwa interfejsu sieciowego jest generowana automatycznie. W tych instrukcjach dotyczących uproszczenia będziemy odnosić się do automatycznie generowanych, podstawowych interfejsów sieciowych dołączonych do `client` podsieci sieci wirtualnej platformy Azure jako **Hana-S1-DB1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-DB3-Client** i tak dalej.  


   > [!IMPORTANT]
   > Upewnij się, że wybrany system operacyjny to SAP-Certified for SAP HANA na określonych typach maszyn wirtualnych, których używasz. Aby uzyskać listę SAP HANA certyfikowane typy maszyn wirtualnych i wersje systemu operacyjnego dla tych typów, przejdź do witryny [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Kliknij Szczegóły wymienionego typu maszyny wirtualnej, aby uzyskać pełną listę obsługiwanych przez SAP HANA wydań systemu operacyjnego dla tego typu.  
  

2. Utwórz sześć interfejsów sieciowych, po jednej dla każdej maszyny wirtualnej usługi HANA DB w `inter` podsieci sieci wirtualnej (w tym przykładzie platformą **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-** z i Hana- **S2-DB3-Inter**).  

3. Utwórz sześć interfejsów sieciowych, jedną dla każdej maszyny wirtualnej usługi HANA DB, w `hsr` podsieci sieci wirtualnej (w tym przykładzie: **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** i **Hana-S2-DB3-HSR**).  

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

    ```azurecli
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
      1. Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.23.0.27**).
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
      1. Wybierz pozycję **TCP** jako protokół i port 625 **03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
      1. Wybierz przycisk **OK**.

   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Hana-lb**).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**, **Hana — zaplecze** i **Hana-HP**).
      1. Wybierz pozycję **porty ha**.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.

   > [!IMPORTANT]
   > Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.    
   
   > [!Note]
   > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Zobacz również artykuł SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Wdrażanie infrastruktury Azure NetApp Files 

Wdróż woluminy ANF dla `/hana/shared` systemu plików. Wymagany jest osobny `/hana/shared` wolumin dla każdej lokacji replikacji systemu Hana. Aby uzyskać więcej informacji, zobacz [Konfigurowanie infrastruktury Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

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

    ```bash
     # Client subnet
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE dostarcza specjalnych agentów zasobów dla SAP HANA i przez domyślnych agentów dla SAP HANA ScaleUp są zainstalowane. Odinstaluj pakiety dla programu ScaleUp, jeśli zostały zainstalowane i zainstaluj pakiety dla scenariusza SAP HANAScaleOut. Ten krok należy wykonać na wszystkich maszynach wirtualnych klastra, w tym większość twórców.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[Ah]** Przygotuj maszyny wirtualne — Zastosuj zalecane ustawienia dla uwagi SAP [2205917] dla SUSE Linux Enterprise Server dla aplikacji SAP.  

## <a name="prepare-the-file-systems"></a>Przygotowywanie systemów plików
### <a name="mount-the-shared-file-systems"></a>Instalowanie udostępnionych systemów plików

W tym przykładzie udostępnione systemy plików HANA są wdrażane na Azure NetApp Files i instalowane przez NFSv4.  

1. **[Ah]** Utwórz punkty instalacji dla woluminów bazy danych HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Sprawdź ustawienie domeny systemu plików NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, to jest, **`defaultv4iddomain.com`** a mapowanie jest ustawione na wartość **nikt**.  
   Ten krok jest wymagany tylko w przypadku korzystania z platformy Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Upewnij się, że na maszynie wirtualnej ustawiono domenę systemu plików NFS `/etc/idmapd.conf` zgodną z domyślną konfiguracją domeny na Azure NetApp Files: **`defaultv4iddomain.com`** . Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, wówczas uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody` .  

    ```bash
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

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Zainstaluj udostępnione woluminy Azure NetApp Files na maszynach wirtualnych SITE1 HANA DB.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Zainstaluj udostępnione woluminy Azure NetApp Files na maszynach wirtualnych SITE2 HANA DB.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    ```


10. **[Ah]** Sprawdź, czy odpowiednie `/hana/shared/` systemy plików są zainstalowane na wszystkich maszynach wirtualnych w usłudze Hana DB z wersją protokołu NFS **NFSv4**.  

    ```bash
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
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Przykładowe dane wyjściowe:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Utwórz woluminy fizyczne dla wszystkich dysków, które mają być używane:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednego dla katalogu udostępnionego SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Utwórz woluminy logiczne. 
   Wolumin liniowy jest tworzony, gdy jest używany `lvcreate` bez `-i` przełącznika. Zalecamy utworzenie woluminu rozłożonego w celu uzyskania lepszej wydajności operacji we/wy i dostosowanie rozmiarów rozłożonych do wartości przedstawionych w [SAP HANA konfiguracjach magazynu maszyny wirtualnej](./hana-vm-operations-storage.md). `-i`Argument powinien być liczbą podstawowych woluminów fizycznych, a `-I` argument jest rozmiarem paska. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` argument Switch ma wartość **2**. Rozmiar rozłożonego woluminu danych to **256 KiB**. Jeden wolumin fizyczny jest używany w woluminie dziennika, więc żadne `-i` `-I` przełączniki nie są jawnie używane dla poleceń woluminu dziennika.  

   > [!IMPORTANT]
   > Użyj `-i` przełącznika i ustaw go na numer bazowego woluminu fizycznego, jeśli używasz więcej niż jednego woluminu fizycznego dla poszczególnych woluminów danych lub dzienników. Użyj `-I` przełącznika, aby określić rozmiar paska podczas tworzenia woluminu rozłożonego.  
   > Zapoznaj się z [SAP HANA konfiguracjami magazynu maszyn wirtualnych](./hana-vm-operations-storage.md) , aby uzyskać zalecane konfiguracje magazynu, w tym rozmiary i liczbę dysków.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Utwórz katalogi instalacji i skopiuj identyfikator UUID wszystkich woluminów logicznych:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Utwórz `fstab` wpisy dla woluminów logicznych i zainstaluj:
    ```bash
    sudo vi /etc/fstab
    ```

   Wstaw następujący wiersz do `/etc/fstab` pliku:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Zainstaluj nowe woluminy:

    ```bash
    sudo mount -a
    ```

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego serwera platformy Hana.
Uwzględnij wszystkie maszyny wirtualne, w tym większość twórców w klastrze.  

> [!IMPORTANT]
> Nie ustawiono wartości `quorum expected-votes` 2, ponieważ nie jest to klaster z dwoma węzłami.  
> Upewnij się, że właściwość klaster `concurrent-fencing`  jest włączona, co spowoduje, że ogrodzenie węzłów jest deserializowane.   

## <a name="installation"></a>Instalacja  

W tym przykładzie do wdrażania SAP HANA w konfiguracji skalowania z HSR na maszynach wirtualnych platformy Azure została użyta platforma HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Przygotowanie do instalacji platformy HANA

1. **[Ah]** Przed instalacją HANA należy ustawić hasło główne. Hasło główne można wyłączyć po zakończeniu instalacji. Uruchom jako `root` polecenie `passwd` .  

2. **[1, 2]** Zmień uprawnienia na `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Sprawdź, czy możesz zalogować się za pośrednictwem protokołu SSH do maszyn wirtualnych usługi Hana DB w tej lokacji **Hana-S1-DB2** i **Hana-S1-DB3** bez monitowania o hasło. Jeśli tak się nie dzieje, klucze SSH programu Exchange zgodnie z opisem w temacie [Włączanie dostępu SSH za pośrednictwem klucza publicznego](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Sprawdź, czy możesz zalogować się za pośrednictwem protokołu SSH do maszyn wirtualnych usługi Hana DB w tej lokacji **Hana-S2-DB2** i **Hana-S2-DB3** bez monitowania o hasło.  
   Jeśli tak się nie dzieje, klucze SSH programu Exchange.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Zainstaluj dodatkowe pakiety, które są wymagane dla platformy HANA 2,0 SP4. Aby uzyskać więcej informacji, zobacz temat SAP Uwaga [2593824](https://launchpad.support.sap.com/#/notes/2593824) dla wersji SLES. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
    ```
### <a name="hana-installation-on-the-first-node-on-each-site"></a>Instalacja platformy HANA w pierwszym węźle w poszczególnych lokacjach

1. **[1]** Zainstaluj SAP HANA, postępując zgodnie z instrukcjami w [przewodniku instalacji i aktualizacji SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). W poniższych instrukcjach zostanie wyświetlona instalacja SAP HANA w pierwszym węźle w lokacji 1.   

   a. Uruchom program **hdblcm** jako `root` z katalogu oprogramowania instalacyjnego platformy Hana. Użyj `internal_network` parametru i przekaż przestrzeń adresową dla podsieci, która jest używana dla wewnętrznej komunikacji między węzłami Hana.  

    ```bash
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

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
   ```

4. **[1, 2]** przygotowanie `global.ini` do instalacji w środowisku nieudostępnionym, zgodnie z opisem w artykule SAP Uwaga [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** ponowne uruchomienie SAP HANA w celu aktywowania zmian.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** Sprawdź, czy interfejs klienta będzie używać adresów IP z `client` podsieci do komunikacji.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.22"
   ```

   Aby uzyskać informacje na temat weryfikowania konfiguracji, zobacz artykuł SAP Uwaga [2183363-konfiguracja sieci wewnętrznej SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Zmień uprawnienia do katalogów danych i dzienników, aby uniknąć błędu instalacji platformy HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** zainstaluj dodatkowe węzły platformy Hana. Przykładowe instrukcje w tym kroku dotyczą witryny 1.  
   a. Uruchom program rezydenta **hdblcm** jako `root` .    
    ```bash
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

   Wykonaj kopię zapasową baz danych jako **hn1** adm:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Skopiuj systemowe pliki PKI do lokacji dodatkowej:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Utwórz lokację główną:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** Skonfiguruj replikację systemu w lokacji 2:
    
   Zarejestruj drugą lokację, aby rozpocząć replikację systemu. Uruchom następujące polecenie jako <hanasid \> adm:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Sprawdź stan replikacji

   Sprawdź stan replikacji i poczekaj na zsynchronizowanie wszystkich baz danych.

    ```bash
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
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Edytuj global.ini, aby dodać mapowanie hosta dla replikacji systemu HANA: Użyj adresów IP z `hsr` podsieci.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Uruchom platformę HANA w obu lokacjach
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw hostów na potrzeby replikacji systemu](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Tworzenie zasobów systemu plików

Utwórz zasób klastra fikcyjnego systemu plików, który będzie monitorować i zgłaszać błędy, w przypadku gdy występuje problem z uzyskaniem dostępu do systemu plików zainstalowanego w systemie plików NFS `/hana/shared` . Dzięki temu klaster może wyzwolić tryb failover w przypadku wystąpienia problemu z uzyskaniem dostępu `/hana/shared` . Aby uzyskać więcej informacji, zobacz [Obsługa niepowodzenia udziału NFS w KLASTRZE SUSE ha na potrzeby replikacji systemu Hana](https://www.suse.com/support/kb/doc/?id=000019904) 

1. **[1]** Umieść Pacemaker w trybie konserwacji, przygotowując do tworzenia zasobów klastra Hana.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1, 2]** Utwórz katalog na woluminie/Hana/sahred ANF, który będzie używany w specjalnym zasobie monitorowania systemu plików. Katalogi należy tworzyć w obu lokacjach.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[Ah]** Utwórz katalog, który będzie używany do instalowania specjalnego zasobu monitorowania systemu plików. Katalog należy utworzyć na wszystkich węzłach klastra HANA.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** Utwórz zasoby klastra systemu plików.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` atrybut jest dodawany do operacji monitorowania, tak aby operacje monitorowania wykonywały test odczytu/zapisu w systemie plików. Bez tego atrybutu operacja monitorowania sprawdza tylko, czy system plików jest zainstalowany. Może to być spowodowane problemem, ponieważ po utracie łączności system plików może pozostać zainstalowany, mimo że jest niedostępny.  

   `on-fail=fence` atrybut jest również dodawany do operacji monitorowania. W przypadku tej opcji, jeśli operacja monitorowania zakończy się niepowodzeniem w węźle, ten węzeł zostanie natychmiast ogrodzony.   

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

1. **[1, 2]** instalacja platformy Hana "hak replikacji systemu". Punkt zaczepienia musi być zainstalowany w jednym węźle bazy danych HANA w każdej lokacji replikacji systemu.         

   1. Przygotuj punkt zaczepienia jako `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Zatrzymaj platformę HANA w obu lokacjach replikacji systemowej. Wykonaj jako <SID \> adm:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Dostosowanie `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[Ah]** Klaster wymaga konfiguracji sudo w węźle klastra dla <SID \> adm. W tym przykładzie, który został osiągnięty przez utworzenie nowego pliku. Wykonaj polecenia jako `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1, 2]** Uruchom SAP HANA w obu lokacjach replikacji. Wykonaj jako <SID \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Sprawdź instalację punktu zaczepienia. Wykonaj jako <SID \> adm w aktywnej lokacji replikacji systemu Hana.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** Utwórz zasoby klastra Hana. Wykonaj następujące polecenia jako `root` .    
   1. Upewnij się, że klaster jest już w trybie konserwacji.  
    
   2. Następnie utwórz zasób topologii HANA.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Następnie utwórz zasób wystąpienia platformy HANA.  
      > [!NOTE] 
      > Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Zaleca się, aby jako najlepsze rozwiązanie było możliwe ustawienie AUTOMATED_REGISTER na **nie**, podczas przeprowadzania gruntownych testów zakończonych niepowodzeniem, aby zapobiec automatycznemu zarejestrowaniu wystąpienia niepowodzenia jako pomocniczego. Po pomyślnym zakończeniu testów zakończonych niepowodzeniem ustaw wartość AUTOMATED_REGISTER **tak**, aby po automatycznym wznowieniu replikacji systemu przejęcia. 

   4. Utwórz wirtualny adres IP i skojarzone zasoby.  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Tworzenie ograniczeń klastra  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** skonfiguruj dodatkowe właściwości klastra   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** weryfikacja komunikacji między punktem zaczepienia a klastrem
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** Umieść klaster poza trybem konserwacji. Upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej konfiguracji platformy HANA. Na przykład może być konieczne zwiększenie limitu czasu uruchamiania, Jeśli uruchomienie SAP HANA bazy danych trwa dłużej.
  

## <a name="test-sap-hana-failover"></a>Testowanie pracy w trybie failover SAP HANA 

> [!NOTE]
> Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.

1. Przed rozpoczęciem testu sprawdź, czy klaster i SAP HANA stan replikacji systemu.    

   a. Sprawdź, czy nie ma żadnych akcji klastra zakończonych niepowodzeniem  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Sprawdź, czy replikacja systemu SAP HANA jest zsynchronizowana

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Zalecamy gruntowną weryfikację konfiguracji klastra SAP HANA, wykonując testy opisane w [ha dla SAP HANA na maszynach wirtualnych platformy Azure w witrynie SLES](./sap-hana-high-availability.md#test-the-cluster-setup) i w [scenariuszu zoptymalizowanego pod kątem wydajności w zakresie replikacji SLES](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster).

3. Sprawdź konfigurację klastra dla scenariusza awarii, gdy węzeł utraci dostęp do udziału NFS ( `/hana/shared` ).  

   Agenci zasobów SAP HANA zależą od plików binarnych przechowywanych w programie w `/hana/shared` celu wykonywania operacji w trybie failover. System plików `/hana/shared` jest instalowany za pośrednictwem systemu plików NFS w przedstawionej konfiguracji. Test, który można wykonać, polega na utworzeniu tymczasowej reguły zapory w celu zablokowania dostępu do `/hana/shared` woluminu ANF na jednej z maszyn wirtualnych lokacji głównej. To podejście sprawdza, czy klaster przejdzie w tryb failover, jeśli dostęp do programu `/hana/shared` zostanie utracony w aktywnej lokacji replikacji systemu.  

   **Oczekiwany wynik**: po zablokowaniu dostępu do `/hana/shared` woluminu ANF na jednej z maszyn wirtualnych lokacji głównej operacja monitorowania, która wykonuje operację odczytu/zapisu w systemie plików, zakończy się niepowodzeniem, ponieważ nie może uzyskać dostępu do systemu plików i spowoduje wyzwolenie trybu failover zasobów platformy Hana. Ten sam wynik jest oczekiwany, gdy węzeł HANA utraci dostęp do udziału NFS.  
     
   Stan zasobów klastra można sprawdzić, wykonując `crm_mon` lub `crm status` . Stan zasobu przed rozpoczęciem testu:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Aby symulować niepowodzenie dla `/hana/shared` , najpierw Potwierdź adres IP `/hana/shared` woluminu ANF w lokacji głównej. Możesz to zrobić, uruchamiając `df -kh|grep /hana/shared` . 
   Następnie skonfiguruj tymczasową regułę zapory, aby zablokować dostęp do adresu IP `/hana/shared` woluminu ANF przez wykonanie następującego polecenia na jednej z maszyn wirtualnych lokacji replikacji podstawowej platformy Hana.
   W tym przykładzie polecenie zostało wykonane na platformie Hana-S1-DB1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Zasoby klastra zostaną zmigrowane do innej lokacji replikacji systemu HANA.    
              
   Jeśli ustawisz AUTOMATED_REGISTER = "false", musisz skonfigurować SAP HANA replikację systemu w lokacji dodatkowej. W takim przypadku można wykonać te polecenia, aby ponownie skonfigurować SAP HANA jako pomocniczy.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Stan zasobów po teście: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* [Woluminy NFS 4.1 w usłudze Azure NetApp Files dla platformy SAP HANA](./hana-vm-operations-netapp.md)
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha].