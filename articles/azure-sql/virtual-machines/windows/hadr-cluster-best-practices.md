---
title: Najlepsze rozwiązania dotyczące konfiguracji klastra
description: Zapoznaj się z obsługiwanymi konfiguracjami klastrów w przypadku konfigurowania opcji wysokiej dostępności i odzyskiwania po awarii (HADR cluster) dla SQL Server na platformie Azure Virtual Machines, takich jak obsługiwane kworum lub Opcje routingu połączeń.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 4ab4e40e1dd4bbaf9ae73ab545285f5ae6261e27
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201774"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Najlepsze rozwiązania dotyczące konfiguracji klastra (SQL Server w usłudze Azure Virtual Machines)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Klaster jest używany w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (HADR cluster) z SQL Server na platformie Azure Virtual Machines. 

W tym artykule przedstawiono najlepsze rozwiązania w zakresie konfiguracji klastra dla [wystąpień klastra trybu failover (FCIS)](failover-cluster-instance-overview.md) i [grupy dostępności](availability-group-overview.md) , gdy są używane z SQL Server na maszynach wirtualnych platformy Azure. 


## <a name="networking"></a>Sieć

Użyj pojedynczej karty sieciowej na serwer (węzeł klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej platformy Azure. Raport weryfikacji klastra ostrzega o tym, że węzły są dostępne tylko w jednej sieci. To ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej platformy Azure.

### <a name="tuning-failover-cluster-network-thresholds"></a>Dostrajanie progów sieci klastra trybu failover

W przypadku uruchamiania węzłów klastra trybu failover systemu Windows na maszynach wirtualnych platformy Azure z funkcją SQL Server AlwaysOn zaleca się zmianę ustawienia klastra na bardziej swobodny stan monitorowania.  Dzięki temu klaster jest znacznie bardziej stabilny i niezawodny.  Aby uzyskać szczegółowe informacje na ten temat, zobacz [IaaS with SQL AlwaysOn-dostrajania sieci klastra trybu failover](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Kworum

Mimo że klaster z dwoma węzłami będzie działać bez [zasobów kworum](/windows-server/storage/storage-spaces/understand-quorum), klienci są ściśle zobowiązani do korzystania z zasobów kworum w celu zapewnienia obsługi produkcyjnej. Sprawdzenie poprawności klastra nie spowoduje przekazanie żadnego klastra bez zasobu kworum. 

Technicznie klaster z trzema węzłami może przetrwać utratę jednego węzła (w dół do dwóch węzłów) bez zasobu kworum. Jednak gdy klaster nie działa w dwóch węzłach, istnieje ryzyko, że zasoby klastrowane przechodzą w tryb offline w przypadku niepowodzenia lub komunikacji między węzłami.

Skonfigurowanie zasobu kworum umożliwi kontynuowanie pracy klastra w trybie online z tylko jednym węzłem w trybie online.

Poniższa tabela zawiera listę opcji kworum dostępnych w kolejności zalecanej do użycia z maszyną wirtualną platformy Azure z wybranym monitorem dysku: 


||[Monitor dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Monitor w chmurze](/windows-server/failover-clustering/deploy-cloud-witness)  |[Monitor udziału plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Obsługiwany system operacyjny**| Wszystko |Windows Server 2016 +| Wszystko|


### <a name="disk-witness"></a>Monitor dysku

Monitor dysku to niewielki dysk klastrowany w grupie magazynów dostępnego klastra. Ten dysk jest wysoce dostępny i może przechodzić w tryb failover między węzłami. Zawiera kopię bazy danych klastra o domyślnym rozmiarze, który jest zwykle mniejszy niż 1 GB. Monitor dysku jest preferowaną opcją kworum dla dowolnego klastra korzystającego z udostępnionych dysków platformy Azure (lub dowolnego rozwiązania udostępnionego na dysku, takiego jak udostępniona magistrala SCSI, iSCSI lub Fibre Channel).  Nie można użyć klastrowanego woluminu udostępnionego jako monitora dysku.

Skonfiguruj dysk udostępniony platformy Azure jako monitor dysku. 

Aby rozpocząć, zobacz [Konfigurowanie monitora dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Obsługiwany system operacyjny**: wszystkie   


### <a name="cloud-witness"></a>Monitor w chmurze

Monitor w chmurze to typ monitora kworum klastra trybu failover, który używa Microsoft Azure, aby zapewnić głos na kworum klastra. Domyślny rozmiar wynosi około 1 MB i zawiera tylko sygnaturę czasową. Monitor w chmurze jest idealnym rozwiązaniem w przypadku wdrożeń w wielu lokacjach, wielu strefach i wielu regionach.

Aby rozpocząć, zobacz [Configure a Cloud monitor](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Obsługiwane systemy operacyjne**: Windows Server 2016 i nowsze   


### <a name="file-share-witness"></a>Monitor udziału plików

Monitor udziału plików to udział plików SMB, który jest zazwyczaj konfigurowany na serwerze plików z systemem Windows Server. Przechowuje informacje o klastrach w pliku monitora. log, ale nie przechowuje kopii bazy danych klastra. Na platformie Azure można skonfigurować udział plików na oddzielnej maszynie wirtualnej.

Aby rozpocząć, zobacz [Konfigurowanie monitora udziału plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Obsługiwane systemy operacyjne**: Windows Server 2012 i nowsze   

## <a name="connectivity"></a>Łączność

W tradycyjnym lokalnym środowisku sieciowym wystąpienie klastra trybu failover SQL Server jest jednym wystąpieniem SQL Server uruchomione na pojedynczym komputerze. Ponieważ wystąpienie klastra trybu failover jest przełączane z węzła do węzła, nazwa sieci wirtualnej (VNN) dla wystąpienia zapewnia ujednolicony punkt połączenia i umożliwia aplikacjom łączenie się z wystąpieniem SQL Server bez znajomości węzła, który jest obecnie aktywny. Gdy nastąpi przejście w tryb failover, nazwa sieci wirtualnej jest zarejestrowana w nowym aktywnym węźle po jego uruchomieniu. Ten proces jest niewidoczny dla klienta lub aplikacji, która nawiązuje połączenie z SQL Server, co minimalizuje przestoje środowiska klienta lub aplikacji w trakcie awarii. Podobnie odbiornik grupy dostępności używa VNN do kierowania ruchu do odpowiedniej repliki. 

Użyj VNN z Azure Load Balancer lub nazwy sieci rozproszonej (DNN), aby skierować ruch do VNN wystąpienia klastra trybu failover z SQL Server na maszynach wirtualnych platformy Azure lub zamienić istniejący odbiornik VNN w grupie dostępności. 


Poniższa tabela zawiera porównanie obsługi połączeń HADR cluster: 

| |**Nazwa Virtual Network (VNN)**  |**Nazwa sieci rozproszonej (DNN)**  |
|---------|---------|---------|
|**Minimalna wersja systemu operacyjnego**| Wszystko | Windows Server 2016 |
|**Minimalna wersja SQL Server** |Wszystko |SQL Server 2019 ZASTOSUJESZ pakietu CU2 (dla FCI)<br/> SQL Server 2019 CU8 (for AG)|
|**Obsługiwane rozwiązanie HADR Cluster** | Wystąpienie klastra trybu failover <br/> Grupa dostępności | Wystąpienie klastra trybu failover <br/> Grupa dostępności|


### <a name="virtual-network-name-vnn"></a>Nazwa sieci wirtualnej (VNN)

Punkt dostępu wirtualnego adresu IP działa inaczej na platformie Azure, dlatego należy skonfigurować [Azure Load Balancer](../../../load-balancer/index.yml) , aby kierować ruch do adresu IP węzłów FCI lub odbiornika grupy dostępności. W przypadku usługi Azure Virtual Machines moduł równoważenia obciążenia przechowuje adres IP VNN, na którym bazują klastrowane zasoby SQL Server. Moduł równoważenia obciążenia dystrybuuje przepływy przychodzące, które docierają do frontonu, a następnie kieruje ten ruch do wystąpień zdefiniowanych przez pulę zaplecza. Przepływ ruchu można skonfigurować przy użyciu zasad równoważenia obciążenia i sond kondycji. W SQL Server FCI wystąpienia puli zaplecza są maszynami wirtualnymi platformy Azure z systemem SQL Server. 

W przypadku korzystania z modułu równoważenia obciążenia występuje niewielkie opóźnienie pracy awaryjnej, ponieważ sonda kondycji przeprowadza sprawdzanie aktywności co 10 sekund domyślnie. 

Aby rozpocząć, Dowiedz się, jak skonfigurować Azure Load Balancer dla [wystąpienia klastra trybu failover](failover-cluster-instance-vnn-azure-load-balancer-configure.md) lub [grupy dostępności](availability-group-vnn-azure-load-balancer-configure.md)

**Obsługiwany system operacyjny**: wszystkie   
**Obsługiwana wersja programu SQL**: wszystkie   
**Obsługiwane rozwiązanie HADR Cluster**: wystąpienie klastra trybu failover i Grupa dostępności   


### <a name="distributed-network-name-dnn"></a>Nazwa sieci rozproszonej (DNN)

Nazwa sieci rozproszonej to nowa funkcja platformy Azure dla SQL Server 2019. DNN zapewnia alternatywny sposób, w jaki SQL Server klienci mogą łączyć się z wystąpieniem klastra trybu failover SQL Server lub grupą dostępności bez użycia modułu równoważenia obciążenia. 

Po utworzeniu zasobu DNN klaster wiąże nazwę DNS z adresami IP wszystkich węzłów w klastrze. Klient SQL podejmie próbę nawiązania połączenia z każdym adresem IP na tej liście, aby znaleźć zasób, z którym ma zostać nawiązane połączenie.  Aby przyspieszyć ten proces, należy określić `MultiSubnetFailover=True` w parametrach połączenia. To ustawienie instruuje dostawcę, aby wypróbować wszystkie adresy IP równolegle, dlatego klient może połączyć się z FCI lub odbiornikiem natychmiast. 

Gdy jest to możliwe, zaleca się użycie rozproszonej nazwy sieciowej, ponieważ: 
- Kompleksowe rozwiązanie jest bardziej niezawodne, ponieważ nie trzeba już obsługiwać zasobów modułu równoważenia obciążenia. 
- Wyeliminowanie sond modułu równoważenia obciążenia minimalizuje czas pracy w trybie failover. 
- DNN upraszcza inicjowanie obsługi administracyjnej wystąpienia klastra trybu failover lub odbiornika grupy dostępności i zarządzanie nim przy użyciu SQL Server na maszynach wirtualnych platformy Azure. 

Większość SQL Server funkcje działają w sposób przezroczysty z FCI i grupami dostępności podczas korzystania z DNN, ale istnieją pewne funkcje, które mogą wymagać szczególnych uwagi. Aby dowiedzieć się więcej, zobacz Współdziałanie [FCI i DNN](failover-cluster-instance-dnn-interoperability.md) oraz [współdziałanie z usługą AG i DNN](availability-group-dnn-interoperability.md) . 

Aby rozpocząć, zapoznaj się z tematem Konfigurowanie zasobu rozproszonej nazwy sieciowej dla [wystąpienia klastra trybu failover](failover-cluster-instance-distributed-network-name-dnn-configure.md) lub [grupy dostępności](availability-group-distributed-network-name-dnn-listener-configure.md)

**Obsługiwane systemy operacyjne**: Windows Server 2016 i nowsze   
**Obsługiwana wersja programu SQL**: SQL Server 2019 ZASTOSUJESZ pakietu CU2 (FCI) i SQL Server 2019 CU8 (AG)   
**Obsługiwane rozwiązanie HADR Cluster**: wystąpienie klastra trybu failover i Grupa dostępności   


## <a name="limitations"></a>Ograniczenia

Podczas pracy z FCI lub grupami dostępności oraz SQL Server na platformie Azure Virtual Machines należy wziąć pod uwagę następujące ograniczenia. 

### <a name="msdtc"></a>ZNAJDUJĄC 

Usługa Azure Virtual Machines obsługuje usługę Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem na udostępnionych woluminach klastra (CSV) i [Usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-overview.md) lub na SQL Server maszynach wirtualnych korzystających z dysków udostępnionych na platformie Azure. 

Na platformie Azure Virtual Machines usługa MSDTC nie jest obsługiwana w przypadku systemu Windows Server 2016 lub starszego z klastrowanymi woluminami udostępnionymi, ponieważ:

- Nie można skonfigurować klastrowanego zasobu usługi MSDTC do korzystania z magazynu udostępnionego. W systemie Windows Server 2016, jeśli utworzysz zasób MSDTC, nie będzie widoczny żaden magazyn udostępniony dostępny do użycia, nawet jeśli magazyn jest dostępny. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowa usługa równoważenia obciążenia nie obsługuje portów RPC.


## <a name="next-steps"></a>Następne kroki

Po ustaleniu odpowiednich najlepszych rozwiązań dla Twojego rozwiązania Zacznij od [przygotowania SQL Server maszyny wirtualnej do FCI](failover-cluster-instance-prepare-vm.md) lub przez utworzenie grupy dostępności przy użyciu [Azure Portal](availability-group-azure-portal-configure.md), [interfejsu wiersza polecenia platformy Azure/programu PowerShell](./availability-group-az-commandline-configure.md)lub [szablonów szybkiego startu platformy Azure](availability-group-quickstart-template-configure.md).
