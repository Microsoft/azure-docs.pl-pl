---
title: Wysoka dostępność i odzyskiwanie po awarii dla SQL Server | Microsoft Docs
description: Omówienie różnych typów strategii HADR Cluster dla SQL Server uruchomionych na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: eb61064e3dd7b5f6201bd80fe5d7ca08dea42598
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342939"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-on-azure-virtual-machines"></a>Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Microsoft SQL Server w usłudze Azure Virtual Machines może pomóc obniżyć koszty rozwiązania bazy danych o wysokiej dostępności i odzyskiwania po awarii (HADR cluster). Większość SQL Server rozwiązań HADR Cluster są obsługiwane na maszynach wirtualnych (VM) zarówno jako rozwiązanie platformy Azure, jak i jako rozwiązania hybrydowe. W rozwiązaniu opartym na platformie Azure cały system HADR Cluster jest uruchamiany na platformie Azure. W konfiguracji hybrydowej część rozwiązania działa na platformie Azure, a druga część działa lokalnie w organizacji. Elastyczność środowiska platformy Azure umożliwia przechodzenie częściowo lub całkowicie na platformę Azure w celu spełnienia wymagań budżetowych i HADR Cluster w zakresie systemów baz danych SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Zrozumienie potrzeb rozwiązania HADR Cluster
Jest to konieczne, aby upewnić się, że system bazy danych posiada HADR Cluster funkcje wymagane przez umowę dotyczącą poziomu usług (SLA). Fakt, że platforma Azure udostępnia mechanizmy wysokiej dostępności, takie jak korygowanie usług w chmurze i wykrywanie odzyskiwania po awarii dla maszyn wirtualnych, nie gwarantuje, że można spełnić wymagania umowy SLA. Te mechanizmy chronią wysoką dostępność maszyn wirtualnych, ale nie zapewniają wysokiej dostępności SQL Server uruchomionych wewnątrz maszyn wirtualnych. Wystąpienie SQL Server może się nie powieść, gdy maszyna wirtualna jest w trybie online i jest w dobrej kondycji. Ponadto nawet mechanizmy wysokiej dostępności zapewniane przez platformę Azure pozwalają na przestoje maszyn wirtualnych ze względu na zdarzenia, takie jak odzyskiwanie z błędów oprogramowania lub sprzętu i uaktualnień systemu operacyjnego.

Ponadto magazyn Geograficznie nadmiarowy (GRS) na platformie Azure, który jest implementowany przy użyciu funkcji replikacji geograficznej, może nie być odpowiednim rozwiązaniem do odzyskiwania po awarii dla baz danych. Ponieważ replikacja geograficzna wysyła dane asynchronicznie, ostatnie aktualizacje mogą zostać utracone w przypadku awarii. Więcej informacji dotyczących ograniczeń replikacji geograficznej znajduje się w sekcji [replikacja geograficzna nie jest obsługiwana w przypadku plików danych i dziennika na oddzielnych dyskach](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Architektury wdrażania HADR Cluster
SQL Server technologie HADR Cluster obsługiwane przez platformę Azure obejmują:

* [Zawsze włączone grupy dostępności](https://technet.microsoft.com/library/hh510230.aspx)
* [Zawsze włączone wystąpienia klastra trybu failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Wysyłanie dziennika](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server tworzenia kopii zapasowych i przywracania za pomocą usługi Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx)
* [Dublowanie bazy danych](https://technet.microsoft.com/library/ms189852.aspx) — przestarzałe w SQL Server 2016

Istnieje możliwość łączenia technologii w celu zaimplementowania rozwiązania SQL Server, które ma zarówno możliwość odzyskiwania o wysokiej dostępności, jak i w razie awarii. W zależności od używanej technologii wdrożenie hybrydowe może wymagać tunelu sieci VPN w sieci wirtualnej platformy Azure. W poniższych sekcjach przedstawiono niektóre z przykładowych architektur wdrożenia.

## <a name="azure-only-high-availability-solutions"></a>Tylko platforma Azure: rozwiązania wysokiej dostępności

Możesz mieć rozwiązanie o wysokiej dostępności dla SQL Server na poziomie bazy danych z zawsze włączonymi grupami dostępności — nazwanymi grupami dostępności. Możesz również utworzyć rozwiązanie wysokiej dostępności na poziomie wystąpienia z zawsze włączonymi wystąpieniami klastrów trybu failover — wystąpienia klastra trybu failover. W celu zapewnienia dodatkowej nadmiarowości można utworzyć nadmiarowość na obu poziomach przez utworzenie grup dostępności w wystąpieniach klastra trybu failover. 

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności działające na maszynach wirtualnych platformy Azure w tym samym regionie zapewniają wysoką dostępność. Musisz skonfigurować maszynę wirtualną kontrolera domeny, ponieważ klaster trybu failover systemu Windows wymaga domeny Active Directory.<br/><br/> Aby zapewnić wyższą nadmiarowość i dostępność, maszyny wirtualne platformy Azure można wdrażać w różnych [strefach dostępności](../../../availability-zones/az-overview.md) , zgodnie z opisem w temacie [Omówienie grupy dostępności](availability-group-overview.md). Jeśli SQL Server maszyny wirtualne w grupie dostępności są wdrożone w strefach dostępności, Użyj usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/load-balancer-standard-overview.md) dla odbiornika, zgodnie z opisem w poniższych artykułach — szablon [interfejsu wiersza polecenia usługi Azure SQL Virtual Machine CLI](availability-group-az-cli-configure.md)  &  [Azure](availability-group-quickstart-template-configure.md).<br/> ![Grupy dostępności](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](availability-group-azure-marketplace-template-configure.md). |
| **Wystąpienia klastra trybu failover** |Wystąpienia klastra trybu failover (FCI), które wymagają magazynu udostępnionego, można utworzyć na 4 różne sposoby.<br/><br/>1. klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure z dołączonym magazynem przy użyciu [systemu Windows Server 2016 bezpośrednie miejsca do magazynowania \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) w celu zapewnienia wirtualnej sieci San opartej na oprogramowaniu.<br/><br/> 2. klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure korzysta z [udziału plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md). Udziały plików w warstwie Premium to udziały plików o dużych opóźnieniach, które są w pełni obsługiwane w przypadku wystąpienia klastra trybu failover.<br/><br/>3. klaster trybu failover z dwoma węzłami działający na maszynach wirtualnych platformy Azure z magazynem obsługiwanym przez rozwiązanie do obsługi klastrowania innej firmy. Aby zapoznać się z konkretnym przykładem, który używa oprogramowanie SIOS DataKeeper, zobacz [wysoka dostępność udziału plików przy użyciu klastrów trybu failover i usługi Microsoft oprogramowanie SIOS DataKeeper oprogramowania](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. klaster trybu failover z dwoma węzłami uruchomiony na maszynach wirtualnych platformy Azure ze współdzielonym magazynem blokowym zdalnego interfejsu iSCSI za pośrednictwem usługi ExpressRoute. Na przykład NetApp prywatny Storage (NPS) ujawnia obiekt docelowy iSCSI za pośrednictwem ExpressRoute z Equinixem do maszyn wirtualnych platformy Azure.<br/><br/>W przypadku rozwiązań udostępnionego magazynu i replikacji danych innych firm należy skontaktować się z dostawcą w celu uzyskania wszelkich problemów związanych z dostępem do danych w trybie failover.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Tylko platforma Azure: rozwiązania do odzyskiwania po awarii
Możesz mieć rozwiązanie do odzyskiwania po awarii dla baz danych SQL Server na platformie Azure przy użyciu grup dostępności, dublowania baz danych lub tworzenia kopii zapasowych i przywracania za pomocą obiektów blob magazynu.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności działające w wielu centrach danych na maszynach wirtualnych platformy Azure na potrzeby odzyskiwania po awarii. To rozwiązanie międzyregionowe chroni przed ukończeniem przestoju lokacji. <br/> ![Grupy dostępności](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>W obrębie regionu wszystkie repliki powinny znajdować się w tej samej usłudze w chmurze i tej samej sieci wirtualnej. Ponieważ każdy region będzie miał oddzielną sieć wirtualną, te rozwiązania wymagają łączności wirtualnej z siecią wirtualną. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia między sieciami wirtualnymi przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Aby uzyskać szczegółowe instrukcje, zobacz [Konfigurowanie grupy dostępności SQL Server na platformie Azure Virtual Machines w różnych regionach](availability-group-manually-configure-multiple-regions.md).|
| **Dublowanie bazy danych** |Podmiot zabezpieczeń i duplikaty oraz serwery działające w różnych centrach danych na potrzeby odzyskiwania po awarii. Należy wdrożyć przy użyciu certyfikatów serwera. Funkcja dublowania baz danych SQL Server nie jest obsługiwana w przypadku SQL Server 2008 ani SQL Server 2008 R2 na maszynie wirtualnej platformy Azure. <br/>![Dublowanie bazy danych](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage** |Produkcyjne bazy danych z kopii zapasowej bezpośrednio do magazynu obiektów BLOB w innym centrum danych na potrzeby odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikowanie SQL Server i przełączenie w tryb failover na platformie Azure za pomocą Azure Site Recovery** |SQL Server produkcyjne jednego centrum danych platformy Azure zreplikowane bezpośrednio do usługi Azure Storage różnych centrów danych platformy Azure na potrzeby odzyskiwania po awarii.<br/>![Replikacja przy użyciu Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona SQL Server przy użyciu SQL Server odzyskiwania po awarii i Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrydowe IT: rozwiązania do odzyskiwania po awarii
Możesz mieć rozwiązanie do odzyskiwania po awarii dla baz danych SQL Server w środowisku IT hybrydowym, korzystając z grup dostępności, dublowania baz danych, wysyłania dzienników i tworzenia kopii zapasowych i przywracania w usłudze Azure blog Storage.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Niektóre repliki dostępności działające na maszynach wirtualnych platformy Azure i innych replikach działających lokalnie na potrzeby odzyskiwania po awarii między lokacjami. Witryna produkcyjna może być lokalna lub w centrum danych platformy Azure.<br/>![Grupy dostępności](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Ponieważ wszystkie repliki dostępności muszą znajdować się w tym samym klastrze trybu failover, klaster musi obejmować obie sieci (klaster trybu failover z jedną podsiecią). Ta konfiguracja wymaga połączenia sieci VPN między platformą Azure i siecią lokalną.<br/><br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii.<br/><br/>Aby dodać replikę Azure do istniejącej grupy dostępności zawsze włączone, można użyć Kreatora dodawania repliki w programie SSMS. Aby uzyskać więcej informacji, zobacz Samouczek: zwiększanie grupy dostępności zawsze włączone na platformie Azure. |
| **Dublowanie bazy danych** |Jeden partner działający na maszynie wirtualnej platformy Azure i inne działające lokalnie na potrzeby odzyskiwania po awarii między lokacjami przy użyciu certyfikatów serwera. Partnerzy nie muszą znajdować się w tej samej domenie Active Directory i nie są wymagane żadne połączenia sieci VPN.<br/>![Dublowanie bazy danych](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Inny scenariusz dublowania bazy danych obejmuje jednego partnera działającego na maszynie wirtualnej platformy Azure i inne działające lokalnie w tej samej domenie Active Directory na potrzeby odzyskiwania po awarii między lokacjami. Wymagane jest [połączenie sieci VPN między siecią wirtualną platformy Azure a siecią lokalną](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) .<br/><br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii. Funkcja dublowania baz danych SQL Server nie jest obsługiwana w przypadku SQL Server 2008 ani SQL Server 2008 R2 na maszynie wirtualnej platformy Azure. |
| **Wysyłanie dziennika** |Jeden serwer działający na maszynie wirtualnej platformy Azure i inne działające lokalnie na potrzeby odzyskiwania po awarii między lokacjami. Wysyłanie dziennika zależy od udostępniania plików systemu Windows, dlatego wymagane jest połączenie sieci VPN między siecią wirtualną platformy Azure i siecią lokalną.<br/>![Wysyłanie dziennika](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage** |Lokalne bazy danych produkcyjnych są archiwizowane bezpośrednio w usłudze Azure Blob Storage na potrzeby odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikowanie SQL Server i przełączanie w tryb failover na platformie Azure za pomocą Azure Site Recovery** |Lokalne SQL Server produkcyjne zreplikowane bezpośrednio do usługi Azure Storage w celu odzyskiwania po awarii.<br/>![Replikacja przy użyciu Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona SQL Server przy użyciu SQL Server odzyskiwania po awarii i Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezpłatna replika DR na platformie Azure

Jeśli masz program [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), możesz zaimplementować plany hybrydowego odzyskiwania po awarii za pomocą SQL Server bez ponoszenia dodatkowych kosztów licencjonowania dla pasywnego wystąpienia usługi Dr.

Na poniższej ilustracji Instalator używa SQL Server uruchomionego na maszynie wirtualnej platformy Azure korzystającej z 12 rdzeni jako repliki odzyskiwania po awarii dla lokalnego wdrożenia SQL Server przy użyciu 12 rdzeni. W przeszłości należy uzyskać licencję na 12 rdzeni SQL Server lokalnego i wdrożenia Virtual Machines platformy Azure. Nowe korzyści oferują zalety pasywnej repliki działającej na maszynie wirtualnej platformy Azure. Teraz należy uzyskać licencję na 12 rdzeni SQL Server działających lokalnie, o ile kryteria odzyskiwania po awarii dla repliki pasywnej na platformie Azure Virtual Machines są spełnione.

![Bezpłatna replika DR na platformie Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Aby uzyskać więcej informacji, zapoznaj się z [postanowieniami licencyjnymi](https://www.microsoft.com/licensing/product-licensing/products)dotyczącymi produktów. 

Aby włączyć tę korzyść, przejdź do [zasobu maszyny wirtualnej SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), wybierz opcję **Konfiguruj** w obszarze Ustawienia, a następnie wybierz opcję **odzyskiwanie awaryjne** w obszarze **licencja SQL Server**. Zaznacz pole wyboru, aby potwierdzić, że ta SQL Server maszyna wirtualna będzie używana jako replika pasywna, a następnie wybierz pozycję **Zastosuj** , aby zapisać ustawienia. 

![Konfigurowanie repliki odzyskiwania po awarii na platformie Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Ważne zagadnienia dotyczące SQL Server HADR Cluster na platformie Azure
Maszyny wirtualne, magazyn i sieci platformy Azure mają różne cechy operacyjne niż lokalna, niezwirtualizowana infrastruktura IT. Pomyślne wdrożenie rozwiązania HADR Cluster SQL Server na platformie Azure wymaga zrozumienia tych różnic i zaprojektowania rozwiązania w celu ich dopasowania.

### <a name="high-availability-nodes-in-an-availability-set"></a>Węzły wysokiej dostępności w zestawie dostępności
Zestawy dostępności na platformie Azure umożliwiają umieszczenie węzłów o wysokiej dostępności w oddzielnych domenach błędów (domenami błędów) i domenach aktualizacji. Każda maszyna wirtualna w zestawie dostępności ma przypisaną domenę aktualizacji i domenę błędów z odpowiedniej platformy Azure. Ta konfiguracja w centrum danych gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna i spełnia warunki umowy SLA platformy Azure o wartości 99,95%. Aby skonfigurować konfigurację wysokiej dostępności, umieść wszystkie uczestniczące maszyny wirtualne SQL w tym samym zestawie dostępności, aby uniknąć utraty aplikacji lub danych podczas zdarzenia konserwacji. Tylko węzły w tej samej usłudze w chmurze mogą uczestniczyć w tym samym zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Manage the availability of virtual machines](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością maszyn wirtualnych).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Węzły wysokiej dostępności w strefie dostępności
Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Fizyczne rozdzielenie Strefy dostępności w regionie chroni aplikacje i dane przed awariami centrów danych, co zapewnia dostępność co najmniej jednej maszyny wirtualnej i spełnia warunki umowy SLA na 99,99%. Aby skonfigurować wysoką dostępność, umieść uczestniczące maszyny wirtualne SQL w obszarze rozmieszczonym w obszarze dostępne Strefy dostępności w regionie. Będzie dostępnych dodatkowych opłat za transfer danych między MASZYNami wirtualnymi między strefami dostępności. Aby uzyskać więcej informacji, zobacz [strefy dostępności](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Zachowanie klastra trybu failover w sieci platformy Azure
Usługa DHCP niezgodna ze standardem RFC na platformie Azure może spowodować niepowodzenie tworzenia niektórych konfiguracji klastra trybu failover, ponieważ nazwa sieci klastra ma przypisany zduplikowany adres IP, na przykład ten sam adres IP, co jeden z węzłów klastra. Jest to problem występujący podczas implementowania grup dostępności, które są zależne od funkcji klastra trybu failover systemu Windows.

Zastanów się nad scenariuszem tworzenia klastra z dwoma węzłami i przełączeniem w tryb online:

1. Klaster jest dostępny w trybie online, a następnie WĘZEŁ1 żąda dynamicznego przypisanego adresu IP dla nazwy sieci klastra.
2. Usługa DHCP nie otrzymuje adresu IP innego niż NODE1's własny adres IP, ponieważ usługa DHCP rozpoznaje, że żądanie pochodzi od samego WĘZEŁ1u.
3. System Windows wykrywa, że zduplikowany adres jest przypisany do WĘZEŁ1 i do nazwy sieciowej klastra trybu failover, a domyślna grupa klastrów nie przejdzie w tryb online.
4. Domyślna grupa klastra przenosi do WĘZEŁ2, która traktuje NODE1's adres IP jako adres IP klastra i przywraca domyślną grupę klastra w trybie online.
5. Gdy WĘZEŁ2 próbuje nawiązać połączenie z WĘZEŁ1, pakiety kierowane w WĘZEŁ1 nigdy nie opuszczą WĘZEŁ2, ponieważ rozwiązuje on adres IP NODE1's do samego siebie. Usługa WĘZEŁ2 nie może nawiązać połączenia z WĘZEŁ1, a następnie utraci kworum i zamyka klaster.
6. W międzyczasie WĘZEŁ1 może wysyłać pakiety do WĘZEŁ2, ale WĘZEŁ2 nie może odpowiedzieć. WĘZEŁ1 utraci kworum i zamyka klaster.

Ten scenariusz można uniknąć, przypisując nieużywany statyczny adres IP, taki jak 169.254.1.1, do nazwy sieciowej klastra w celu przełączenia nazwy sieciowej klastra do trybu online. Aby uprościć ten proces, zobacz [Konfigurowanie klastra trybu failover systemu Windows na platformie Azure dla grup dostępności](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](availability-group-azure-marketplace-template-configure.md).

### <a name="availability-group-listener-support"></a>Obsługa odbiornika grupy dostępności
Odbiorniki grup dostępności są obsługiwane na maszynach wirtualnych platformy Azure z systemem Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016. Jest to możliwe dzięki użyciu punktów końcowych z równoważeniem obciążenia, które są włączone na maszynach wirtualnych platformy Azure, które są węzłami grupy dostępności. Należy wykonać specjalne czynności konfiguracyjne, aby detektory działały zarówno dla aplikacji klienckich działających na platformie Azure, jak i działających lokalnie.

Dostępne są dwie główne opcje konfigurowania odbiornika: zewnętrzne (publiczne) lub wewnętrzne. Zewnętrzny (Publiczny) odbiornik używa modułu równoważenia obciążenia połączonego z Internetem i jest skojarzony z publicznym wirtualnym adresem IP (VIP), który jest dostępny za pośrednictwem Internetu. Odbiornik wewnętrzny używa wewnętrznego modułu równoważenia obciążenia i obsługuje tylko klientów w ramach tego samego Virtual Network. Dla dowolnego typu modułu równoważenia obciążenia należy włączyć bezpośredni zwrot serwera. 

Jeśli grupa dostępności obejmuje wiele podsieci platformy Azure (na przykład wdrożenie przekraczające regiony platformy Azure), parametry połączenia klienta muszą zawierać wartość "**MultiSubnetFailover = true**". Powoduje to łączenie równoległych prób z replikami w różnych podsieciach. Aby uzyskać instrukcje dotyczące konfigurowania odbiornika, zobacz.

* [Skonfiguruj odbiornik ILB dla grup dostępności na platformie Azure](availability-group-listener-powershell-configure.md).
* [Skonfiguruj odbiornik zewnętrzny dla grup dostępności na platformie Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Można nadal łączyć się z każdą repliką dostępności oddzielnie, łącząc się bezpośrednio z wystąpieniem usługi. Ponadto, ponieważ grupy dostępności są zgodne z poprzednimi wersjami klientów funkcji dublowania baz danych, można łączyć się z replikami dostępności, takimi jak partnerzy funkcji dublowania baz danych, o ile repliki są skonfigurowane podobnie jak w przypadku dublowania baz danych:

* Jedna replika podstawowa i jedna replika pomocnicza
* Replika pomocnicza jest skonfigurowana jako niemożliwa do odczytu (opcja**pomocnicza do odczytu** jest ustawiona na wartość **nie**)

Oto przykładowe parametry połączenia klienta, które odpowiadają tej konfiguracji podobnej do dublowania bazy danych, przy użyciu ADO.NET lub SQL Server Native Client poniżej:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Aby uzyskać więcej informacji na temat łączności klienta, zobacz:

* [Używanie słów kluczowych parametrów połączenia z SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Łączenie klientów z sesją dublowania baz danych (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Łączenie z odbiornikiem grupy dostępności w hybrydowym](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Odbiorniki grup dostępności, łączność z klientem i tryb failover aplikacji (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Używanie parametrów połączenia dublowania baz danych z grupami dostępności](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Opóźnienie sieci w hybrydowym
Należy wdrożyć rozwiązanie HADR Cluster z założeniem, że mogą być okresy o wysokim opóźnieniu sieci między siecią lokalną i platformą Azure. Podczas wdrażania replik na platformie Azure należy używać zatwierdzania asynchronicznego zamiast zatwierdzania synchronicznego dla trybu synchronizacji. Podczas wdrażania serwerów dublowania baz danych zarówno lokalnie, jak i na platformie Azure, użyj trybu wysokiej wydajności, a nie trybu wysokiego poziomu zabezpieczeń.

### <a name="geo-replication-support"></a>Obsługa replikacji geograficznej
Replikacja geograficzna w usłudze Azure disks nie obsługuje plików danych i plików dziennika tej samej bazy danych, które mają być przechowywane na oddzielnych dyskach. GRS replikuje zmiany na każdym dysku niezależnie i asynchronicznie. Ten mechanizm gwarantuje kolejność zapisu w ramach jednego dysku w kopii z replikacją geograficzną, ale nie między kopiami replikowanymi geograficznie wielu dysków. W przypadku skonfigurowania bazy danych do przechowywania pliku danych i jego pliku dziennika na oddzielnych dyskach odzyskane dyski po awarii mogą zawierać bardziej aktualną kopię pliku danych niż plik dziennika, który dzieli dziennik zapisu w SQL Server i właściwości KWASów transakcji. Aby wyłączyć replikację geograficzną na koncie magazynu, należy zachować wszystkie pliki danych i dziennika dla danej bazy danych na tym samym dysku. Jeśli konieczne jest użycie więcej niż jednego dysku ze względu na rozmiar bazy danych, należy wdrożyć jedno z wymienionych powyżej rozwiązań odzyskiwania po awarii w celu zapewnienia nadmiarowości danych.

## <a name="next-steps"></a>Następne kroki
Jeśli musisz utworzyć maszynę wirtualną platformy Azure z SQL Server, zobacz [Inicjowanie obsługi SQL Server maszyny wirtualnej na platformie Azure](create-sql-vm-portal.md).

Aby uzyskać najlepszą wydajność SQL Server uruchamiania na maszynie wirtualnej platformy Azure, zapoznaj się ze wskazówkami dotyczącymi [najlepszych rozwiązań dotyczących wydajności SQL Server na platformie azure Virtual Machines](performance-guidelines-best-practices.md).

Aby poznać inne tematy związane z uruchamianiem SQL Server na maszynach wirtualnych platformy Azure, zobacz [SQL Server na platformie azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

### <a name="other-resources"></a>Inne zasoby
* [Instalowanie nowego lasu Active Directory na platformie Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Tworzenie klastra trybu failover dla grup dostępności na maszynie wirtualnej platformy Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

