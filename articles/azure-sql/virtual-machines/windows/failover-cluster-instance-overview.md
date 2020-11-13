---
title: Wystąpienia klastra trybu failover
description: Dowiedz się więcej o wystąpieniach klastra trybu failover (FCIs) z SQL Server na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5845a3bdc4b86fbbe44c92779e5aae95044eb6b2
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556374"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Wystąpienia klastra trybu failover z SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule przedstawiono różnice dotyczące funkcji podczas pracy z wystąpieniami klastra trybu failover (FCI) w celu SQL Server na platformie Azure Virtual Machines. 

## <a name="overview"></a>Omówienie

SQL Server na maszynach wirtualnych platformy Azure korzysta z funkcji Windows Server Failover Clustering (WSFC), aby zapewnić lokalną wysoką dostępność za pośrednictwem nadmiarowości na poziomie wystąpienia serwera: wystąpienie klastra trybu failover. FCI to pojedyncze wystąpienie SQL Server zainstalowane w usłudze WSFC (lub po prostu klastra) i, prawdopodobnie w wielu podsieciach. W sieci FCI wydaje się wystąpienie SQL Server uruchomione na pojedynczym komputerze. Jednak FCI zapewnia tryb failover z jednego węzła usługi WSFC na inny, jeśli bieżący węzeł stał się niedostępny.

W pozostałej części artykułu nałożono różnice między wystąpieniami klastra trybu failover, gdy są one używane z SQL Server na maszynach wirtualnych platformy Azure. Aby dowiedzieć się więcej na temat technologii klaster trybu failover, zobacz: 

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Kworum

Wystąpienia klastra trybu failover z SQL Server w usłudze Azure Virtual Machines support przy użyciu monitora dysku, monitora w chmurze lub monitora udostępniania plików dla kworum klastra.

Aby dowiedzieć się więcej, zobacz [najlepsze rozwiązania dotyczące kworum z maszynami wirtualnymi SQL Server na platformie Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Magazyn

W tradycyjnych lokalnych środowiskach klastrowanych klaster trybu failover systemu Windows używa sieci magazynowania (SAN), która jest dostępna w obu węzłach jako magazyn udostępniony. Pliki SQL Server są hostowane w magazynie udostępnionym, a tylko aktywny węzeł może uzyskać do nich dostęp jednocześnie. 

SQL Server na maszynach wirtualnych platformy Azure oferuje różne opcje jako rozwiązanie magazynu udostępnionego do wdrożenia SQL Server wystąpienia klastra trybu failover: 

||[Dyski udostępnione platformy Azure](../../../virtual-machines/windows/disks-shared.md)|[Udziały plików w warstwie Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Minimalna wersja systemu operacyjnego**| Wszystko |Windows Server 2012|Windows Server 2016|
|**Minimalna wersja SQL Server**|Wszystko|SQL Server 2012|SQL Server 2016|
|**Obsługiwana dostępność maszyny wirtualnej** |Zestawy dostępności z grupami umieszczania zbliżeniowego |Zestawy dostępności i strefy dostępności|Zestawy dostępności |
|**Obsługuje FileStream**|Tak|Nie|Tak |
|**Pamięć podręczna Azure Blob**|Nie|Nie|Tak|

W pozostałej części tej sekcji wymieniono zalety i ograniczenia dotyczące poszczególnych opcji magazynów dostępnych dla SQL Server na maszynach wirtualnych platformy Azure. 

### <a name="azure-shared-disks"></a>Dyski udostępnione platformy Azure

[Usługa Azure Shared disks](../../../virtual-machines/windows/disks-shared.md) jest funkcją usługi [Azure Managed disks](../../../virtual-machines/managed-disks-overview.md). Klaster trybu failover systemu Windows Server obsługuje używanie dysków udostępnionych platformy Azure z wystąpieniem klastra trybu failover. 

**Obsługiwany system operacyjny** : wszystkie   
**Obsługiwana wersja programu SQL** : wszystkie     

**Zalety** : 
- Przydatne w przypadku aplikacji, które chcą migrować do platformy Azure, zachowując swoją architekturę wysokiej dostępności i odzyskiwania po awarii (HADR cluster) zgodnie z oczekiwaniami. 
- Program może migrować klastrowane aplikacje na platformę Azure, ponieważ jest to spowodowane obsługą trwałych rezerwacji SCSI (SCSI PR). 
- Obsługuje udostępnione usługi Azure SSD w warstwie Premium i Azure Ultra Disk Storage.
- Do utworzenia udostępnionej puli magazynów można użyć jednego udostępnionego dysku lub rozdzielić wiele dysków udostępnionych. 
- Obsługuje funkcję FILESTREAM.


**Ograniczenia** : 
- Maszyny wirtualne muszą być umieszczone w tym samym zestawie dostępności i w grupie umieszczania sąsiedztwa.
- Strefy dostępności nie są obsługiwane.
- Buforowanie dysków SSD w warstwie Premium nie jest obsługiwane.
 
Aby rozpocząć, zobacz [SQL Server wystąpienia klastra trybu failover z dyskami udostępnionymi platformy Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Bezpośrednie miejsca do magazynowania

[Bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) to funkcja systemu Windows Server, która jest obsługiwana z klastrem trybu failover w usłudze Azure Virtual Machines. Zapewnia wirtualną sieć SAN opartą na oprogramowaniu.

**Obsługiwane systemy operacyjne** : Windows Server 2016 i nowsze   
**Obsługiwana wersja programu SQL** : SQL Server 2016 i nowsze   


**Korzysta** 
- Wystarczająca przepustowość sieci umożliwia niezawodne i wysoce wydajne rozwiązanie magazynu udostępnionego. 
- Obsługuje pamięć podręczną usługi Azure Blob, więc odczyty mogą być obsługiwane lokalnie z pamięci podręcznej. (Aktualizacje są replikowane jednocześnie do obu węzłów). 
- Obsługuje funkcję FileStream. 

**Limity**
- Dostępne tylko dla systemu Windows Server 2016 i nowszych. 
- Strefy dostępności nie są obsługiwane.
- Wymaga tej samej pojemności dysku podłączonej do obu maszyn wirtualnych. 
- Wysoka przepustowość sieci jest wymagana w celu zapewnienia wysokiej wydajności z powodu trwającej replikacji dysków. 
- Wymaga większego rozmiaru maszyny wirtualnej i podwójnej opłaty za magazyn, ponieważ magazyn jest dołączony do każdej maszyny wirtualnej. 

Aby rozpocząć, zobacz [SQL Server wystąpienia klastra trybu failover z bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Udział plików w warstwie Premium

[Udziały plików w warstwie Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) są funkcją [Azure Files](../../../storage/files/index.yml). Udziały plików w warstwie Premium są oparte na dyskach SSD i stale mają małe opóźnienia. Są one w pełni obsługiwane do użycia z wystąpieniami klastra trybu failover dla SQL Server 2012 lub nowszych w systemie Windows Server 2012 lub nowszym. Udziały plików w warstwie Premium zapewniają większą elastyczność, ponieważ można zmieniać rozmiar i skalować udział plików bez przestojów.

**Obsługiwane systemy operacyjne** : Windows Server 2012 i nowsze   
**Obsługiwana wersja programu SQL** : SQL Server 2012 i nowsze   

**Korzysta** 
- Tylko udostępnione rozwiązanie magazynu dla maszyn wirtualnych rozprzestrzenia się w wielu strefach dostępności. 
- W pełni zarządzany system plików z opóźnieniami jednocyfrowymi i wydajnością operacji we/wy. 

**Limity**
- Dostępne tylko dla systemu Windows Server 2012 i nowszych. 
- FileStream nie jest obsługiwany. 


Aby rozpocząć, zobacz [SQL Server wystąpienia klastra trybu failover z udziałem plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

Istnieją rozwiązania klastrów partnerskich z obsługiwaną przestrzenią dyskową. 

**Obsługiwany system operacyjny** : wszystkie   
**Obsługiwana wersja programu SQL** : wszystkie   

Jeden przykład używa oprogramowanie SIOS DataKeeper jako magazynu. Aby uzyskać więcej informacji, zapoznaj się z wpisem w blogu Cluster-in [trybu failover i oprogramowanie SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI i ExpressRoute

Możesz również uwidocznić magazyn udostępnionych bloków docelowych iSCSI za pośrednictwem usługi Azure ExpressRoute. 

**Obsługiwany system operacyjny** : wszystkie   
**Obsługiwana wersja programu SQL** : wszystkie   

Na przykład NetApp prywatny Storage (NPS) ujawnia obiekt docelowy iSCSI za pośrednictwem ExpressRoute z Equinixem do maszyn wirtualnych platformy Azure.

W przypadku udostępnionych rozwiązań magazynu i replikacji danych od partnerów firmy Microsoft należy skontaktować się z dostawcą, aby uzyskać wszelkie problemy związane z dostępem do danych w trybie failover.

## <a name="connectivity"></a>Łączność

Wystąpienia klastra trybu failover z SQL Server na platformie Azure Virtual Machines użyć [nazwy sieci rozproszonej (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) lub [nazwy sieci wirtualnej (VNN) z Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) do kierowania ruchu do wystąpienia SQL Server, niezależnie od tego, który węzeł jest aktualnie właścicielem zasobów klastra. Istnieją dodatkowe zagadnienia dotyczące korzystania z określonych funkcji i DNN z SQL Server FCI. Aby dowiedzieć się więcej, zobacz [DNN współdziałanie z usługą SQL Server FCI](failover-cluster-instance-dnn-interoperability.md) . 

Aby uzyskać więcej informacji na temat opcji łączności klastra, zobacz [Route HADR Cluster Connections to SQL Server na maszynach wirtualnych platformy Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Ograniczenia

Należy wziąć pod uwagę następujące ograniczenia dotyczące wystąpień klastra trybu failover z SQL Server na platformie Azure Virtual Machines. 

### <a name="lightweight-extension-support"></a>Uproszczona obsługa rozszerzeń   

W tej chwili SQL Server wystąpienia klastra trybu failover w usłudze Azure Virtual Machines są obsługiwane tylko przy użyciu [uproszczonego trybu zarządzania](sql-server-iaas-agent-extension-automate-management.md#management-modes) rozszerzenia agenta SQL Server IaaS. Aby zmienić tryb pełnego rozszerzenia na lekki, Usuń zasób **maszyny wirtualnej SQL** dla odpowiednich maszyn wirtualnych, a następnie zarejestruj je przy użyciu rozszerzenia agenta SQL IaaS w trybie uproszczonym. Po usunięciu zasobu **maszyny wirtualnej SQL** przy użyciu Azure Portal wyczyść pole wyboru obok odpowiedniej maszyny wirtualnej, aby uniknąć usunięcia maszyny wirtualnej. 

Pełne rozszerzenie obsługuje takie funkcje, jak automatyczne tworzenie kopii zapasowych, stosowanie poprawek i zaawansowane zarządzanie portalem. Te funkcje nie będą działały w przypadku SQL Server maszyn wirtualnych zarejestrowanych w trybie uproszczonego zarządzania.

### <a name="msdtc"></a>ZNAJDUJĄC 

Usługa Azure Virtual Machines obsługuje usługę Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem na udostępnionych woluminach klastra (CSV) i [Usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-overview.md) lub na SQL Server maszynach wirtualnych korzystających z dysków udostępnionych na platformie Azure. 

Na platformie Azure Virtual Machines usługa MSDTC nie jest obsługiwana w przypadku systemu Windows Server 2016 lub starszego z klastrowanymi woluminami udostępnionymi, ponieważ:

- Nie można skonfigurować klastrowanego zasobu usługi MSDTC do korzystania z magazynu udostępnionego. W systemie Windows Server 2016, jeśli utworzysz zasób MSDTC, nie będzie widoczny żaden magazyn udostępniony dostępny do użycia, nawet jeśli magazyn jest dostępny. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowa usługa równoważenia obciążenia nie obsługuje portów RPC.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi konfiguracji klastra](hadr-cluster-best-practices.md), a następnie możesz [przygotować maszynę wirtualną SQL Server dla FCI](failover-cluster-instance-prepare-vm.md). 

Aby uzyskać więcej informacji, zobacz: 

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)