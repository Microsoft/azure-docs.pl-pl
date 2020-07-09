---
title: Najlepsze rozwiązania dotyczące konfiguracji klastra
description: Zapoznaj się z obsługiwanymi konfiguracjami klastrów w przypadku konfigurowania opcji wysokiej dostępności i odzyskiwania po awarii (HADR cluster) dla SQL Server na platformie Azure Virtual Machines, takich jak obsługiwane kworum lub Opcje routingu połączeń.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: d20ac5964ef70618d4d7dc2d4a7fe7d7d01284ce
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965645"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Najlepsze praktyki dotyczące konfiguracji klastra (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Klaster jest używany w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (HADR cluster) z SQL Server na platformie Azure Virtual Machines. 

W tym artykule przedstawiono najlepsze rozwiązania w zakresie konfiguracji klastra dla [wystąpień klastra trybu failover (FCIS)](failover-cluster-instance-overview.md) i [grupy dostępności](availability-group-overview.md) , gdy są używane z SQL Server na maszynach wirtualnych platformy Azure. 


## <a name="networking"></a>Networking

Użyj pojedynczej karty sieciowej na serwer (węzeł klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej platformy Azure. Raport weryfikacji klastra ostrzega o tym, że węzły są dostępne tylko w jednej sieci. To ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej platformy Azure.

## <a name="quorum"></a>Kworum

Mimo że klaster z dwoma węzłami będzie działać bez [zasobów kworum](/windows-server/storage/storage-spaces/understand-quorum), klienci są ściśle zobowiązani do korzystania z zasobów kworum w celu zapewnienia obsługi produkcyjnej. Sprawdzenie poprawności klastra nie spowoduje przekazanie żadnego klastra bez zasobu kworum. 

Technicznie klaster z trzema węzłami może przetrwać utratę jednego węzła (w dół do dwóch węzłów) bez zasobu kworum. Ale po przejściu klastra do dwóch węzłów istnieje ryzyko uruchomienia w programie: 

- **Partycja w miejscu** (Podziel mózg): węzły klastra są rozdzielone w sieci z powodu problemu z serwerem, kartą sieciową lub przełącznikiem. 
- **Partycja w czasie** (Amnesia): węzeł dołącza lub ponownie dołącza do klastra, a następnie próbuje przejąć bezprawną własność grupy klastrów lub roli klastra. 

Zasób kworum chroni klaster przed każdym z tych problemów. 

Aby skonfigurować zasób kworum przy użyciu SQL Server na maszynach wirtualnych platformy Azure, można użyć następujących typów monitora: 


||[Monitor dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Monitor w chmurze](/windows-server/failover-clustering/deploy-cloud-witness)  |[Monitor udziału plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Obsługiwane systemy operacyjne**| Wszystko |Windows Server 2016 +| Windows Server 2012 +|
|**Obsługiwana wersja SQL Server**|SQL Server 2019|SQL Server 2016 +|SQL Server 2016 +|



### <a name="disk-witness"></a>Monitor dysku

Monitor dysku to niewielki dysk klastrowany w grupie magazynów dostępnego klastra. Ten dysk jest wysoce dostępny i może przechodzić w tryb failover między węzłami. Zawiera kopię bazy danych klastra o domyślnym rozmiarze, który jest zwykle mniejszy niż 1 GB. 

Skonfiguruj dysk udostępniony platformy Azure jako monitor dysku. 

Aby rozpocząć, zobacz [Konfigurowanie monitora dysku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Obsługiwany system operacyjny**: wszystkie    
**Obsługiwana wersja programu SQL**: SQL Server 2019   


### <a name="cloud-witness"></a>Monitor w chmurze

Monitor w chmurze to typ monitora kworum klastra trybu failover, który używa Microsoft Azure, aby zapewnić głos na kworum klastra. Domyślny rozmiar wynosi około 1 MB i zawiera tylko sygnaturę czasową. Monitor w chmurze jest idealnym rozwiązaniem w przypadku wdrożeń w wielu lokacjach, wielu strefach i wielu regionach.

Aby rozpocząć, zobacz [Configure a Cloud monitor](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**Obsługiwane systemy operacyjne**: Windows Server 2016 i nowsze   
**Obsługiwana wersja programu SQL**: SQL Server 2016 i nowsze     


### <a name="file-share-witness"></a>Monitor udziału plików

Monitor udziału plików to udział plików SMB, który jest zazwyczaj konfigurowany na serwerze plików z systemem Windows Server. Przechowuje informacje o klastrach w pliku monitora. log, ale nie przechowuje kopii bazy danych klastra. Na platformie Azure można skonfigurować [udział plików platformy Azure](../../../storage/files/storage-how-to-create-file-share.md) do użycia jako monitor udostępniania plików lub użyć udziału plików na oddzielnej maszynie wirtualnej.

Jeśli zamierzasz użyć innego udziału plików platformy Azure, możesz go zainstalować przy użyciu tego samego procesu, który jest używany do [instalowania udziału plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Aby rozpocząć, zobacz [Konfigurowanie monitora udziału plików](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**Obsługiwane systemy operacyjne**: Windows Server 2012 i nowsze   
**Obsługiwana wersja programu SQL**: SQL Server 2016 i nowsze   


## <a name="connectivity"></a>Łączność

W tradycyjnym lokalnym środowisku sieciowym wystąpienie klastra trybu failover SQL Server jest jednym wystąpieniem SQL Server uruchomione na pojedynczym komputerze. Ponieważ wystąpienie klastra trybu failover jest przełączane z węzła do węzła, nazwa sieci wirtualnej (VNN) dla wystąpienia zapewnia ujednolicony punkt połączenia i umożliwia aplikacjom łączenie się z wystąpieniem SQL Server bez znajomości węzła, który jest obecnie aktywny. Gdy nastąpi przejście w tryb failover, nazwa sieci wirtualnej jest zarejestrowana w nowym aktywnym węźle po jego uruchomieniu. Ten proces jest niewidoczny dla klienta lub aplikacji, która nawiązuje połączenie z SQL Server, co minimalizuje przestoje środowiska klienta lub aplikacji w trakcie awarii. 

Użyj VNN z Azure Load Balancer lub nazwy sieci rozproszonej (DNN), aby kierować ruch do VNN wystąpienia klastra trybu failover z SQL Server na maszynach wirtualnych platformy Azure. Funkcja DNN jest obecnie dostępna tylko dla SQL Server 2019 ZASTOSUJESZ pakietu CU2 i nowszych na maszynie wirtualnej z systemem Windows Server 2016 (lub nowszym). 

Poniższa tabela zawiera porównanie obsługi połączeń HADR cluster: 

| |**Nazwa sieci wirtualnej (VNN)**  |**Nazwa sieci rozproszonej (DNN)**  |
|---------|---------|---------|
|**Minimalna wersja systemu operacyjnego**| Windows Server 2012 | Windows Server 2016|
|**Minimalna wersja SQL Server** |SQL Server 2012 |SQL Server 2019 ZASTOSUJESZ pakietu CU2|
|**Obsługiwane rozwiązanie HADR Cluster** | Wystąpienie klastra trybu failover <br/> Grupa dostępności | Wystąpienie klastra trybu failover|


### <a name="virtual-network-name-vnn"></a>Nazwa sieci wirtualnej (VNN)

Punkt dostępu wirtualnego adresu IP działa inaczej na platformie Azure, dlatego należy skonfigurować [Azure Load Balancer](../../../load-balancer/index.yml) , aby kierować ruch do adresu IP węzłów FCI. W przypadku usługi Azure Virtual Machines moduł równoważenia obciążenia przechowuje adres IP VNN, na którym bazują klastrowane zasoby SQL Server. Moduł równoważenia obciążenia dystrybuuje przepływy przychodzące, które docierają do frontonu, a następnie kieruje ten ruch do wystąpień zdefiniowanych przez pulę zaplecza. Przepływ ruchu można skonfigurować przy użyciu zasad równoważenia obciążenia i sond kondycji. W SQL Server FCI wystąpienia puli zaplecza są maszynami wirtualnymi platformy Azure z systemem SQL Server. 

W przypadku korzystania z modułu równoważenia obciążenia występuje niewielkie opóźnienie pracy awaryjnej, ponieważ sonda kondycji przeprowadza sprawdzanie aktywności co 10 sekund domyślnie. 

Aby rozpocząć, Dowiedz się, jak [skonfigurować Azure Load Balancer dla FCI](hadr-vnn-azure-load-balancer-configure.md). 

**Obsługiwane systemy operacyjne**: Windows Server 2012 i nowsze   
**Obsługiwana wersja programu SQL**: SQL Server 2012 i nowsze   
**Obsługiwane rozwiązanie HADR Cluster**: wystąpienie klastra trybu failover i Grupa dostępności 


### <a name="distributed-network-name-dnn"></a>Nazwa sieci rozproszonej (DNN)

Nazwa sieci rozproszonej to nowa funkcja platformy Azure dla SQL Server 2019 ZASTOSUJESZ pakietu CU2. DNN zapewnia alternatywny sposób nawiązywania połączenia z wystąpieniem klastra trybu failover SQL Server przez SQL Server klientów bez użycia modułu równoważenia obciążenia. 

Po utworzeniu zasobu DNN klaster wiąże nazwę DNS z adresami IP wszystkich węzłów w klastrze. Klient SQL podejmie próbę nawiązania połączenia z każdym adresem IP na tej liście, aby znaleźć węzeł, w którym jest aktualnie uruchomione wystąpienie klastra trybu failover. Aby przyspieszyć ten proces, należy określić `MultiSubnetFailover=True` w parametrach połączenia. To ustawienie instruuje dostawcę, aby wypróbować wszystkie adresy IP równolegle, dlatego klient może natychmiast połączyć się z FCI. 

Gdy jest to możliwe, zaleca się użycie rozproszonej nazwy sieciowej, ponieważ: 
- Kompleksowe rozwiązanie jest bardziej niezawodne, ponieważ nie trzeba już obsługiwać zasobów modułu równoważenia obciążenia. 
- Wyeliminowanie sond modułu równoważenia obciążenia minimalizuje czas pracy w trybie failover. 
- DNN upraszcza obsługę administracyjną wystąpienia klastra trybu failover i zarządzanie nim za pomocą SQL Server na maszynach wirtualnych platformy Azure. 

Większość SQL Server funkcje działają w sposób przezroczysty z FCI. W takich przypadkach można po prostu zastąpić istniejącą nazwę DNS VNN nazwą DNS DNN lub ustawić wartość DNN przy użyciu istniejącej nazwy DNS VNN. Niektóre składniki po stronie serwera wymagają jednak aliasu sieciowego, który mapuje nazwę VNN na nazwę DNN. Określone przypadki mogą wymagać jawnego użycia nazwy DNS DNN, na przykład podczas definiowania niektórych adresów URL w konfiguracji po stronie serwera. 

Aby rozpocząć, Dowiedz się, jak [skonfigurować zasób DNN dla FCI](hadr-distributed-network-name-dnn-configure.md). 

**Obsługiwane systemy operacyjne**: Windows Server 2016 i nowsze   
**Obsługiwana wersja programu SQL**: SQL Server 2019 i nowsze   
**Obsługiwane rozwiązanie HADR Cluster**: tylko wystąpienie klastra trybu failover


## <a name="limitations"></a>Ograniczenia

Podczas pracy z FCI lub grupami dostępności oraz SQL Server na platformie Azure Virtual Machines należy wziąć pod uwagę następujące ograniczenia. 

### <a name="msdtc"></a>ZNAJDUJĄC 
Usługa Azure Virtual Machines obsługuje usługę Microsoft Distributed Transaction Coordinator (MSDTC) w systemie Windows Server 2019 z magazynem w udostępnionych woluminach klastra (CSV) i [Usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-standard-overview.md).

Na platformie Azure Virtual Machines usługa MSDTC nie jest obsługiwana w przypadku systemu Windows Server 2016 lub starszego, ponieważ:

- Nie można skonfigurować klastrowanego zasobu usługi MSDTC do korzystania z magazynu udostępnionego. W systemie Windows Server 2016, jeśli utworzysz zasób MSDTC, nie będzie widoczny żaden magazyn udostępniony dostępny do użycia, nawet jeśli magazyn jest dostępny. Ten problem został rozwiązany w systemie Windows Server 2019.
- Podstawowa usługa równoważenia obciążenia nie obsługuje portów RPC.


## <a name="next-steps"></a>Następne kroki

Po ustaleniu odpowiednich najlepszych rozwiązań dla rozwiązania Zacznij od [przygotowania SQL Server maszyny wirtualnej do FCI](failover-cluster-instance-prepare-vm.md). Możesz również utworzyć grupę dostępności przy użyciu [interfejsu wiersza polecenia platformy Azure](availability-group-az-cli-configure.md)lub [szablonów szybkiego startu platformy Azure](availability-group-quickstart-template-configure.md). 

