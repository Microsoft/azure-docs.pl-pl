---
title: Klastrowanie wystąpienia SAP ASCS/SCS w usłudze WSFC przy użyciu dysku udostępnionego na platformie Azure | Microsoft Docs
description: Dowiedz się, jak Klastrować wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c580e44cc827de46c7464ba5f316e6c515de2940
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977990"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udostępnionego dysku klastra na platformie Azure

> ![System operacyjny Windows][Logo_Windows] Windows
>

Klaster pracy awaryjnej systemu Windows Server to podstawa instalacji oprogramowania SAP ASCS/SCS o wysokiej dostępności w systemie Windows.

Klaster trybu failover to grupa z 1 + n-niezależnymi serwerami (węzły), które współpracują ze sobą w celu zwiększenia dostępności aplikacji i usług. Jeśli wystąpi awaria węzła, klaster trybu failover systemu Windows Server oblicza liczbę błędów, które mogą wystąpić i nadal utrzymuje klaster w dobrej kondycji, aby zapewnić aplikacje i usługi. Możesz wybrać inny tryb kworum, aby osiągnąć klaster trybu failover.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem wykonywania zadań z tego artykułu zapoznaj się z następującym artykułem:

* [Architektura Azure Virtual Machines wysoka dostępność i scenariusze dla oprogramowania SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Klaster trybu failover z systemem Windows Server na platformie Azure

Klaster trybu failover systemu Windows Server z usługą Azure Virtual Machines wymaga dodatkowych kroków konfiguracyjnych. Podczas tworzenia klastra należy ustawić kilka adresów IP i nazw hostów wirtualnych dla wystąpienia SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Rozpoznawanie nazw na platformie Azure i nazwa hosta wirtualnego klastra

Platforma Azure Cloud Platform nie oferuje opcji konfigurowania wirtualnych adresów IP, takich jak zmiennoprzecinkowe adresy IP. Do skonfigurowania wirtualnego adresu IP w celu uzyskania dostępu do zasobu klastra w chmurze potrzebne jest alternatywne rozwiązanie. 

Usługa Azure Load Balancer udostępnia *wewnętrzny moduł równoważenia obciążenia* dla platformy Azure. W przypadku wewnętrznego modułu równoważenia obciążenia klienci docierają do klastra za pośrednictwem wirtualnego adresu IP klastra. 

Wdróż wewnętrzny moduł równoważenia obciążenia w grupie zasobów zawierającej węzły klastra. Następnie należy skonfigurować wszystkie wymagane reguły przekazywania portów przy użyciu portów sondy wewnętrznego modułu równoważenia obciążenia. Klienci mogą łączyć się za pośrednictwem nazwy hosta wirtualnego. Serwer DNS rozpoznaje adres IP klastra, a wewnętrzny moduł równoważenia obciążenia obsługuje przekazywanie portów do aktywnego węzła klastra.

![Rysunek 1. Konfiguracja klastra trybu failover systemu Windows na platformie Azure bez dysku udostępnionego][sap-ha-guide-figure-1001]

_Konfiguracja klastra trybu failover systemu Windows Server na platformie Azure bez dysku udostępnionego_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA z udostępnionymi dyskami klastra
W systemie Windows wystąpienie SAP ASCS/SCS zawiera usługi SAP Central Services, serwer komunikatów SAP, procesy serwera w kolejce i globalne pliki hosta SAP. Pliki hosta globalnego SAP przechowują pliki centralne dla całego systemu SAP.

Wystąpienie SAP ASCS/SCS ma następujące składniki:

* Usługi SAP Central:
    * Dwa procesy, serwer wiadomości i kolejki oraz \<ASCS/SCS virtual host name> , które są używane do uzyskiwania dostępu do tych dwóch procesów.
    * Struktura pliku: S:\usr\sap \\ &lt; SID &gt; \ ASCS/SCS\<instance number\>


* Pliki hosta globalnego SAP:
  * Struktura pliku: S:\usr\sap \\ &lt; SID &gt; \SYS \. ..
  * Udział plików sapmnt, który umożliwia dostęp do tych globalnych S:\usr\sap \\ &lt; SID &gt; \SYS \. .. plików przy użyciu następującej ścieżki UNC:

    \\\\<ASCS/SCS nazwa hosta wirtualnego \> \Sapmnt \\ &lt; Identyfikator SID &gt; \SYS \. ..


![Rysunek 2. procesy, struktura plików i globalny udział plików sapmnt w wystąpieniu SAP ASCS/SCS][sap-ha-guide-figure-8001]

_Procesy, struktura plików i sapmnt Host globalny udział plików w wystąpieniu SAP ASCS/SCS_

W przypadku ustawienia wysokiej dostępności są dostępne wystąpienia oprogramowania SAP ASCS/SCS. Do umieszczenia plików hosta globalnego SAP ASCS/SCS i SAP są używane *klastrowane dyski udostępnione* (Drives, w naszym przykładzie).

![Rysunek 3. Architektura SAP ASCS/SCS z udostępnionym dyskiem][sap-ha-guide-figure-8002]

_Architektura architektury SAP ASCS/SCS z udostępnionym dyskiem_


W przypadku architektury replikacji serwera w kolejce 1:
* Ta sama \<ASCS/SCS virtual host name> jest używana do uzyskiwania dostępu do procesów serwera komunikatów SAP i kolejki oraz plików hosta globalnego SAP za pośrednictwem udziału plików sapmnt.
* Ten sam dysk udostępniony dysku S jest współużytkowany między nimi.  

Przy użyciu architektury replikacja serwera z kolejki 2: 
* Ta sama \<ASCS/SCS virtual host name> służy do uzyskiwania dostępu do procesu serwera komunikatów SAP oraz plików hosta globalnego SAP za pośrednictwem udziału plików sapmnt.
* Ten sam dysk udostępniony dysku S jest współużytkowany między nimi.
* Istnieje oddzielny \<ERS virtual host name> dostęp do procesu serwera w kolejce  

![Rysunek 4. architektura oprogramowania SAP ASCS/SCS z udostępnionym dyskiem][sap-ha-guide-figure-8003]

_Architektura architektury SAP ASCS/SCS z udostępnionym dyskiem_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Udostępniony dysk i do kolejki serwer replikacji 

1. Udostępniony dysk jest obsługiwany z architekturą serwerowej replikacji 1, w której znajduje się wystąpienie serwera replikacji z kolejki (wykres WYWOŁUJĄCYCH):   

   - nie jest klastrowane
   - używa `localhost` nazwy
   - jest wdrożony na dyskach lokalnych na każdym z węzłów klastra

2. Udostępniony dysk jest również obsługiwany w przypadku architektury replikacji serwera w kolejce 2 (ERS2).  

   - jest klastrowane
   - używa dedykowanej nazwy hosta wirtualnego/sieci
   - adres IP wirtualnej nazwy hosta wykres WYWOŁUJĄCYCH musi być skonfigurowany w wewnętrznej Load Balancer platformy Azure, a także do (A) SCS adresu IP
   - Program jest wdrażany na **dyskach lokalnych** w poszczególnych węzłach klastra, dlatego nie ma potrzeby udostępniania dysku

   > [!TIP]
   > Więcej informacji na temat kolejki serwer replikacji 1 i 2 (ERS1 i ERS2) można znaleźć tutaj:  
   > [Kolejkowania serwera replikacji w klastrze trybu failover firmy Microsoft](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Nowy Replikator kolejki w środowiskach klastra trybu failover](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Opcje dysku udostępnionego na platformie Azure dla obciążeń SAP

Dostępne są dwie opcje dysku udostępnionego w klastrze trybu failover systemu Windows na platformie Azure:

- [Udostępnione dyski platformy Azure](../../windows/disks-shared.md) — funkcja umożliwiająca dołączenie dysku zarządzanego platformy Azure do wielu maszyn wirtualnych jednocześnie. 
- Tworzenie dublowanego magazynu, który symuluje udostępniony magazyn klastra, przy użyciu oprogramowania [oprogramowanie SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster) 

Podczas wybierania technologii dla dysku udostępnionego należy pamiętać o następujących kwestiach:

**Dysk udostępniony platformy Azure dla obciążeń SAP**
- Umożliwia dołączenie dysku zarządzanego platformy Azure do wielu maszyn wirtualnych jednocześnie bez konieczności utrzymania i działania dodatkowego oprogramowania 
- Będziesz działać z pojedynczym dyskiem udostępnionym platformy Azure w jednym klastrze magazynu. Ma to wpływ na niezawodność rozwiązania SAP.
- Obecnie jedynym obsługiwanym wdrożeniem jest usługa Azure Shared Premium Disk w zestawie dostępności. Dysk udostępniony platformy Azure nie jest obsługiwany w ramach wdrażania strefowo.     
- Upewnij się, że dysk w warstwie Premium systemu Azure ma minimalny rozmiar dysku określony w [SSD w warstwie Premium zakresy](../../windows/disks-shared.md#disk-sizes) , aby umożliwić dołączenie do wymaganej liczby maszyn wirtualnych jednocześnie (zazwyczaj 2 w przypadku klastra trybu failover systemu Windows w systemie SAP ASCS). 
- Usługa Azure Shared Disk nie jest obsługiwana w przypadku obciążeń SAP, ponieważ nie obsługuje wdrożenia w zestawie dostępności ani w ramach wdrażania strefowego.  
 
**OPROGRAMOWANIE SIOS**
- Rozwiązanie oprogramowanie SIOS zapewnia synchroniczną replikację danych w czasie rzeczywistym między dwoma dyskami
- W przypadku rozwiązania oprogramowanie SIOS działającego z dwoma dyskami zarządzanymi i w przypadku korzystania z zestawów dostępności lub stref dostępności dyski zarządzane będą przechowywane w różnych klastrach magazynu. 
- Wdrożenie w strefach dostępności jest obsługiwane
- Wymaga instalacji i współpracy oprogramowania innych firm, które trzeba zakupić dodatkowo

### <a name="shared-disk-using-azure-shared-disk"></a>Udostępniony dysk korzystający z dysku udostępnionego platformy Azure

Firma Microsoft oferuje [dyski udostępnione platformy Azure](../../windows/disks-shared.md), które mogą służyć do implementowania wysokiej dostępności SAP ASCS/SCS z użyciem opcji dysku udostępnionego.

#### <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

Obecnie można używać dysków SSD w warstwie Premium platformy Azure jako dysku udostępnionego platformy Azure dla wystąpienia SAP ASCS/SCS. Obecnie są stosowane następujące ograniczenia:

-  [Usługa Azure Ultra Disk](../../disks-types.md#ultra-disk) nie jest obsługiwana jako dysk udostępniony platformy Azure dla obciążeń SAP. Obecnie nie jest możliwe umieszczenie maszyn wirtualnych platformy Azure przy użyciu usługi Azure Ultra Disk w zestawie dostępności
-  [Dysk udostępniony platformy Azure](../../windows/disks-shared.md) z dyskami SSD w warstwie Premium jest obsługiwany tylko w przypadku maszyn wirtualnych w zestawie dostępności. Nie jest on obsługiwany w Strefy dostępności wdrożenia. 
-  Wartość dysku udostępnionego na platformie Azure [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) określa, ile węzłów klastra może używać dysku udostępnionego. Zwykle w przypadku wystąpienia SAP ASCS/SCS można skonfigurować dwa węzły w klastrze trybu failover systemu Windows, w związku z czym wartość dla parametru `maxShares` musi być równa 2.
-  Wszystkie maszyny wirtualne klastra SAP ASCS/SCS muszą zostać wdrożone w tej samej [grupie umieszczania usługi Azure zbliżeniowe](../../windows/proximity-placement-groups.md).   
   Mimo że można wdrożyć maszyny wirtualne klastra systemu Windows w zestawie dostępności z dyskiem udostępnionym platformy Azure bez PPG, usługa PPG zapewni zamknięcie fizycznej bliskości udostępnionych dysków platformy Azure i maszyn wirtualnych klastra, co pozwala na wolniejsze opóźnienia między maszynami wirtualnymi i warstwą magazynu.    

Aby uzyskać więcej szczegółowych informacji na temat ograniczeń dotyczących dysku udostępnionego platformy Azure, zobacz sekcję [ograniczenia](../../linux/disks-shared.md#limitations) dotyczące usługi Azure Shared Disk.

> [!IMPORTANT]
> Podczas wdrażania klastra trybu failover systemu Windows z systemem SAP ASCS/SCS przy użyciu udostępnionego dysku platformy Azure należy pamiętać, że wdrożenie będzie działać z jednym udostępnionym dyskiem w jednym klastrze magazynu. Wpłynie to na wystąpienie oprogramowania SAP ASCS/SCS w przypadku problemów z klastrem magazynu, w którym wdrożono dysk udostępniony platformy Azure.    

> [!TIP]
> Przed zaplanowaniem wdrożenia SAP zapoznaj się z [przewodnikiem planowania platformy SAP NetWeaver w systemie Azure](./planning-guide.md) i [przewodniku usługi Azure Storage dla obciążeń SAP](./planning-guide-storage.md) .

### <a name="supported-os-versions"></a>Obsługiwane wersje systemu operacyjnego

Obsługiwane są zarówno systemy Windows Server 2016, jak i 2019 (Użyj najnowszych obrazów centrum danych).

Zdecydowanie zalecamy korzystanie z **systemu Windows Server 2019 Datacenter**w taki sposób, jak:
- Usługa klastra pracy awaryjnej systemu Windows 2019 jest uwzględniana przez platformę Azure
- Dodano integrację i świadomość konserwacji hosta platformy Azure oraz udoskonalone środowisko monitorowania dla zdarzeń planu platformy Azure.
- Możliwe jest użycie rozproszonej nazwy sieciowej (jest to opcja domyślna). W związku z tym nie ma potrzeby posiadania dedykowanego adresu IP dla nazwy sieci klastra. Ponadto nie ma potrzeby konfigurowania tego adresu IP w wewnętrznej Load Balancer platformy Azure. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Dyski udostępnione na platformie Azure z oprogramowanie SIOS DataKeeper

Inna opcja dla dysku udostępnionego polega na użyciu oprogramowania oprogramowanie SIOS DataKeeper klastra, aby utworzyć dublowany magazyn, który symuluje magazyn udostępniony klastra. Rozwiązanie oprogramowanie SIOS zapewnia synchroniczną replikację danych w czasie rzeczywistym.

Aby utworzyć zasób dysku udostępnionego dla klastra:

1. Dołącz dodatkowy dysk do każdej maszyny wirtualnej w konfiguracji klastra systemu Windows.
2. Uruchom oprogramowanie SIOS DataKeeper Cluster Edition w obu węzłach maszyn wirtualnych.
3. Skonfiguruj program oprogramowanie SIOS DataKeeper Cluster w taki sposób, aby replikować zawartość dodatkowego woluminu dołączonego do dysku ze źródłowej maszyny wirtualnej do dodatkowego woluminu dołączonego do dysku docelowej maszyny wirtualnej. OPROGRAMOWANIE SIOS DataKeeper abstrakcyjne źródłowe i docelowe woluminy lokalne, a następnie prezentuje je do klastra trybu failover systemu Windows Server jako jednego dysku udostępnionego.

Uzyskaj więcej informacji na temat [oprogramowanie SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Rysunek 5. Konfiguracja klastra trybu failover z systemem Windows Server na platformie Azure z usługą oprogramowanie SIOS DataKeeper][sap-ha-guide-figure-1002]

_Konfiguracja klastra trybu failover systemu Windows na platformie Azure z usługą oprogramowanie SIOS DataKeeper_

> [!NOTE]
> Dyski udostępnione nie są potrzebne, aby zapewnić wysoką dostępność w przypadku niektórych produktów DBMS, takich jak SQL Server. SQL Server funkcja AlwaysOn replikuje dane i pliki dziennika systemu DBMS z dysku lokalnego jednego węzła klastra do dysku lokalnego innego węzła klastra. W takim przypadku Konfiguracja klastra systemu Windows nie wymaga dysku udostępnionego.
>

## <a name="next-steps"></a>Następne kroki

* [Przygotowanie infrastruktury platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Instalowanie oprogramowania SAP NetWeaver HA na klastrze trybu failover systemu Windows i dysku udostępnionego dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności dla oprogramowania SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md