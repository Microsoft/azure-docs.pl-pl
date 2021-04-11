---
title: Wysoka dostępność, odzyskiwanie po awarii, ciągłość działania firmy
description: Dowiedz się więcej na temat opcji wysokiej dostępności, odzyskiwania po awarii (HADR cluster) i ciągłości biznesowej dostępnych dla SQL Server na maszynach wirtualnych platformy Azure, takich jak zawsze włączone grupy dostępności, wystąpienie klastra trybu failover, dublowanie bazy danych, dostarczanie dzienników i tworzenie kopii zapasowej & przywracanie do usługi Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: f4d870f458607ceb0f05812b5c0c066ce810448e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508320"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Ciągłość działania i HADR Cluster SQL Server na platformie Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ciągłość działalności biznesowej oznacza kontynuowanie działania firmy w przypadku awarii, planowania odzyskiwania i zapewnienia wysokiej dostępności danych. SQL Server w usłudze Azure Virtual Machines może pomóc obniżyć koszty rozwiązania bazy danych o wysokiej dostępności i odzyskiwania po awarii (HADR cluster). 

Większość SQL Server rozwiązań HADR Cluster jest obsługiwana na maszynach wirtualnych (VM) zarówno w przypadku rozwiązań opartych na platformie Azure, jak i hybrydowych. W rozwiązaniu opartym na platformie Azure cały system HADR Cluster jest uruchamiany na platformie Azure. W konfiguracji hybrydowej część rozwiązania działa na platformie Azure, a druga część działa lokalnie w organizacji. Elastyczność środowiska platformy Azure umożliwia przechodzenie częściowo lub całkowicie na platformę Azure w celu spełnienia wymagań budżetowych i HADR Cluster w zakresie systemów baz danych SQL Server.

W tym artykule porównano i rozróżnią się rozwiązania ciągłości działania dostępne dla SQL Server na maszynach wirtualnych platformy Azure. 

## <a name="overview"></a>Omówienie

Jest to konieczne, aby upewnić się, że system bazy danych ma funkcje HADR Cluster wymagane przez umowę dotyczącą poziomu usług (SLA). Fakt, że platforma Azure udostępnia mechanizmy wysokiej dostępności, takie jak korygowanie usług w chmurze i wykrywanie odzyskiwania po awarii dla maszyn wirtualnych, nie gwarantuje, że można spełnić warunki umowy SLA. Chociaż te mechanizmy zapewniają ochronę wysokiej dostępności maszyny wirtualnej, nie chronią one dostępności SQL Server uruchomionych wewnątrz maszyny wirtualnej. 

Wystąpienie SQL Server może się nie powieść, gdy maszyna wirtualna jest w trybie online i jest w dobrej kondycji. Nawet mechanizmy wysokiej dostępności zapewniane przez platformę Azure pozwalają na przestoje maszyn wirtualnych ze względu na zdarzenia, takie jak odzyskiwanie z błędów oprogramowania lub sprzętu i uaktualnień systemu operacyjnego.

Magazyn Geograficznie nadmiarowy (GRS) na platformie Azure jest zaimplementowany przy użyciu funkcji zwanej replikacją geograficzną. GRS może nie być odpowiednim rozwiązaniem do odzyskiwania po awarii dla baz danych. Ponieważ replikacja geograficzna wysyła dane asynchronicznie, ostatnie aktualizacje mogą zostać utracone w razie awarii. Więcej informacji na temat ograniczeń replikacji geograficznej znajduje się w sekcji [Obsługa replikacji geograficznej](#geo-replication-support) .

## <a name="deployment-architectures"></a>Architektury wdrażania
Platforma Azure obsługuje te technologie SQL Server w celu zapewnienia ciągłości działania:

* [Zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Wystąpienia klastrów trybu failover zawsze włączone (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Wysyłanie dziennika](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server wykonywania kopii zapasowych i przywracania za pomocą usługi Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Dublowanie bazy danych](/sql/database-engine/database-mirroring/database-mirroring-sql-server) — przestarzałe w SQL Server 2016

Możesz połączyć technologie, aby zaimplementować SQL Server rozwiązanie, które ma zarówno możliwości odzyskiwania o wysokiej dostępności, jak i w razie awarii. W zależności od używanej technologii wdrożenie hybrydowe może wymagać tunelu sieci VPN w sieci wirtualnej platformy Azure. W poniższych sekcjach przedstawiono kilka przykładów architektury wdrożenia.

## <a name="azure-only-high-availability-solutions"></a>Tylko platforma Azure: rozwiązania o wysokiej dostępności

Możesz mieć rozwiązanie o wysokiej dostępności dla SQL Server na poziomie bazy danych z zawsze włączonymi grupami dostępności. Możesz również utworzyć rozwiązanie wysokiej dostępności na poziomie wystąpienia z zawsze włączonymi wystąpieniami klastra trybu failover. Aby zapewnić dodatkową ochronę, można utworzyć nadmiarowość na obu poziomach przez utworzenie grup dostępności w wystąpieniach klastra trybu failover. 

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności działające na maszynach wirtualnych platformy Azure w tym samym regionie zapewniają wysoką dostępność. Musisz skonfigurować maszynę wirtualną kontrolera domeny, ponieważ klaster trybu failover systemu Windows wymaga domeny Active Directory.<br/><br/> Aby zapewnić większą nadmiarowość i dostępność, maszyny wirtualne platformy Azure można wdrażać w różnych [strefach dostępności](../../../availability-zones/az-overview.md) zgodnie z opisem w temacie [Omówienie grupy dostępności](availability-group-overview.md). Jeśli SQL Server maszyny wirtualne w grupie dostępności są wdrożone w strefach dostępności, użyj [Usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-overview.md) dla odbiornika, zgodnie z opisem w artykule [interfejs wiersza polecenia usługi Azure SQL VM](./availability-group-az-commandline-configure.md) i [Szablony szybkiego startu platformy Azure](availability-group-quickstart-template-configure.md) .<br/> ![Diagram przedstawiający "kontroler domeny" powyżej "klastra usługi WSFC" z "repliką podstawową", "replika pomocnicza" i "Monitor udostępniania plików".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](./availability-group-quickstart-template-configure.md). |
| **Wystąpienia klastra trybu failover** |Wystąpienia klastra trybu failover są obsługiwane na maszynach wirtualnych SQL Server. Ponieważ funkcja FCI wymaga magazynu udostępnionego, pięć rozwiązań będzie współpracowało z SQL Server na maszynach wirtualnych platformy Azure: <br/><br/> — Używanie [dysków udostępnionych platformy Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) dla systemu Windows Server 2019. Udostępnione dyski zarządzane są produktami platformy Azure, które umożliwiają równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Maszyny wirtualne w klastrze mogą odczytywać lub zapisywać na dołączonym dysku na podstawie rezerwacji wybranych przez aplikację klastrowaną za pośrednictwem trwałych rezerwacji SCSI (SCSI PR). SCSI PR to standardowe rozwiązanie do magazynowania, które jest używane przez aplikacje działające w sieci magazynowania (SAN) w środowisku lokalnym. Włączenie żądania ściągnięcia SCSI na dysku zarządzanym umożliwia migrowanie tych aplikacji na platformę Azure zgodnie z oczekiwaniami. <br/><br/>— Używanie [funkcji \( S2D \) bezpośrednie miejsca do magazynowania](failover-cluster-instance-storage-spaces-direct-manually-configure.md) w celu zapewnienia wirtualnej sieci San opartej na oprogramowaniu dla systemu Windows Server 2016 i nowszych.<br/><br/>— Korzystanie z [udziału plików w warstwie Premium](failover-cluster-instance-premium-file-share-manually-configure.md) dla systemu Windows Server 2012 i nowszych wersji. Udziały plików w warstwie Premium są oparte na dyskach SSD, mają ciągle małe opóźnienia i są w pełni obsługiwane do użycia z FCI.<br/><br/>— Używanie magazynu obsługiwanego przez rozwiązanie partnerskie na potrzeby klastrowania. Aby zapoznać się z konkretnym przykładem, który używa oprogramowanie SIOS DataKeeper, zapoznaj się z wpisem w blogu obsługa [klastrów trybu failover i usługi oprogramowanie SIOS](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>— Używanie magazynu bloków udostępnionych dla zdalnego obiektu docelowego iSCSI za pośrednictwem usługi Azure ExpressRoute. Na przykład NetApp prywatny Storage (NPS) ujawnia obiekt docelowy iSCSI za pośrednictwem ExpressRoute z Equinixem do maszyn wirtualnych platformy Azure.<br/><br/>W przypadku udostępnionych rozwiązań magazynu i replikacji danych od partnerów firmy Microsoft należy skontaktować się z dostawcą, aby uzyskać wszelkie problemy związane z dostępem do danych w trybie failover.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Tylko platforma Azure: rozwiązania do odzyskiwania po awarii
Możesz mieć rozwiązanie do odzyskiwania po awarii dla baz danych SQL Server na platformie Azure przy użyciu grup dostępności, dublowania baz danych lub tworzenia kopii zapasowych i przywracania za pomocą obiektów blob magazynu.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Repliki dostępności działające w wielu centrach danych na maszynach wirtualnych platformy Azure na potrzeby odzyskiwania po awarii. To rozwiązanie międzyregionowe pomaga chronić przed ukończeniem awarii lokacji. <br/> ![Diagram przedstawiający dwa regiony z "repliką podstawową" i "repliką pomocniczą" połączoną przez "asynchroniczne zatwierdzenie".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>W obrębie regionu wszystkie repliki powinny znajdować się w tej samej usłudze w chmurze i tej samej sieci wirtualnej. Ponieważ każdy region będzie mieć oddzielną sieć wirtualną, te rozwiązania wymagają połączenia między sieciami. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieciowego do sieci przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Szczegółowe instrukcje znajdują się w temacie [Configure a SQL Server Always On Availability Group w różnych regionach świadczenia usługi Azure](availability-group-manually-configure-multiple-regions.md).|
| **Dublowanie bazy danych** |Podmiot zabezpieczeń i duplikaty oraz serwery działające w różnych centrach danych na potrzeby odzyskiwania po awarii. Należy je wdrożyć przy użyciu certyfikatów serwera. Funkcja dublowania baz danych SQL Server nie jest obsługiwana dla SQL Server 2008 lub SQL Server 2008 R2 na maszynie wirtualnej platformy Azure. <br/>![Diagram, który pokazuje "podmiot zabezpieczeń" w jednym regionie podłączonym do "dublowania w innym regionie" o wysokiej wydajności ".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage** |Produkcyjne bazy danych z kopii zapasowej bezpośrednio do magazynu obiektów BLOB w innym centrum danych na potrzeby odzyskiwania po awarii.<br/>![Diagram przedstawiający "bazę danych" w jednym regionie wykonującej kopie zapasowe do "Blob Storage" w innym regionie.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie SQL Server na maszynach wirtualnych platformy Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikowanie SQL Server i przełączanie w tryb failover na platformie Azure za pomocą Azure Site Recovery** |Wystąpienie SQL Server produkcyjnej w jednym centrum danych platformy Azure zreplikowane bezpośrednio do usługi Azure Storage w innym wystawce Azure Portal na potrzeby odzyskiwania po awarii.<br/>![Diagram pokazujący "bazę danych" w jednym centrum usług Azure z użyciem "replikacji ASR" do odzyskiwania po awarii w innym centrum danych. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona SQL Server przy użyciu SQL Server odzyskiwania po awarii i Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrydowe IT: rozwiązania do odzyskiwania po awarii
Możesz mieć rozwiązanie do odzyskiwania po awarii dla baz danych SQL Server w hybrydowym środowisku IT przy użyciu grup dostępności, dublowania baz danych, wysyłania dzienników i tworzenia kopii zapasowych i przywracania w usłudze Azure Blob Storage.

| Technologia | Przykładowe architektury |
| --- | --- |
| **Grupy dostępności** |Niektóre repliki dostępności działające na maszynach wirtualnych platformy Azure i innych replikach działających lokalnie na potrzeby odzyskiwania po awarii między lokacjami. Witryna produkcyjna może być lokalna lub w centrum danych platformy Azure.<br/>![Grupy dostępności](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Ponieważ wszystkie repliki dostępności muszą znajdować się w tym samym klastrze trybu failover, klaster musi obejmować obie sieci (klaster trybu failover z jedną podsiecią). Ta konfiguracja wymaga połączenia sieci VPN między platformą Azure i siecią lokalną.<br/><br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii.|
| **Dublowanie bazy danych** |Jeden partner działający na maszynie wirtualnej platformy Azure i inne działające lokalnie na potrzeby odzyskiwania po awarii między lokacjami przy użyciu certyfikatów serwera. Partnerzy nie muszą znajdować się w tej samej domenie Active Directory i nie są wymagane żadne połączenia sieci VPN.<br/>![Dublowanie bazy danych](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Inny scenariusz dublowania bazy danych obejmuje jednego partnera działającego na maszynie wirtualnej platformy Azure i inne działające lokalnie w tej samej domenie Active Directory na potrzeby odzyskiwania po awarii między lokacjami. Wymagane jest [połączenie sieci VPN między siecią wirtualną platformy Azure a siecią lokalną](../../../vpn-gateway/tutorial-site-to-site-portal.md) .<br/><br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii. Funkcja dublowania baz danych SQL Server nie jest obsługiwana dla SQL Server 2008 lub SQL Server 2008 R2 na maszynie wirtualnej platformy Azure. |
| **Wysyłanie dziennika** |Jeden serwer działający na maszynie wirtualnej platformy Azure i inne działające lokalnie na potrzeby odzyskiwania po awarii między lokacjami. Wysyłanie dziennika zależy od udostępniania plików systemu Windows, dlatego wymagane jest połączenie sieci VPN między siecią wirtualną platformy Azure i siecią lokalną.<br/>![Wysyłanie dziennika](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>W celu pomyślnego przeprowadzenia odzyskiwania po awarii baz danych należy również zainstalować replikę kontrolera domeny w lokacji odzyskiwania po awarii. |
| **Tworzenie kopii zapasowych i przywracanie za pomocą usługi Azure Blob Storage** |Lokalne bazy danych produkcyjnych są archiwizowane bezpośrednio w usłudze Azure Blob Storage na potrzeby odzyskiwania po awarii.<br/>![Tworzenie kopii zapasowej i przywracanie](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych i przywracanie SQL Server na platformie Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replikowanie SQL Server i przełączanie w tryb failover na platformie Azure za pomocą Azure Site Recovery** |Lokalne wystąpienie SQL Server produkcyjne zreplikowane bezpośrednio do usługi Azure Storage na potrzeby odzyskiwania po awarii.<br/>![Replikacja przy użyciu Azure Site Recovery](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Aby uzyskać więcej informacji, zobacz [ochrona SQL Server przy użyciu SQL Server odzyskiwania po awarii i Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Bezpłatna replika DR na platformie Azure

Jeśli masz program [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), możesz zaimplementować plany hybrydowego odzyskiwania po awarii za pomocą SQL Server bez ponoszenia dodatkowych kosztów licencjonowania dla wystąpienia pasywnego odzyskiwania po awarii.

Na przykład możesz mieć dwie bezpłatne pasywne usługi pomocnicze, gdy wszystkie trzy repliki są hostowane na platformie Azure: 

![Dwie bezpłatne pasywne, gdy wszystko na platformie Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Lub można skonfigurować hybrydowe środowisko pracy awaryjnej, z licencjonowaną podstawową lokalną platformą, jedną bezpłatną pasywną dla HA, jedną bezpłatną pasywną dla lokalnego odzyskiwania po awarii i jedną bezpłatną pasywną dla DR na platformie Azure:

![Trzy bezpłatne pasywne, gdy środowisko jest hybrydowe z jedną podstawową repliką lokalną](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Aby uzyskać więcej informacji, zobacz [postanowienia dotyczące licencjonowania produktów](https://www.microsoft.com/licensing/product-licensing/products). 

Aby włączyć tę korzyść, przejdź do [zasobu maszyny wirtualnej SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Wybierz opcję **Konfiguruj** w obszarze **Ustawienia**, a następnie wybierz opcję **odzyskiwanie awaryjne** w obszarze **SQL Server licencji**. Zaznacz to pole wyboru, aby potwierdzić, że ta SQL Server maszyna wirtualna będzie używana jako replika pasywna, a następnie wybierz pozycję **Zastosuj** , aby zapisać ustawienia. 

![Konfigurowanie repliki odzyskiwania po awarii na platformie Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Ważne zagadnienia dotyczące SQL Server HADR Cluster na platformie Azure
Maszyny wirtualne, magazyn i sieci platformy Azure mają różne cechy operacyjne niż lokalna, niezwirtualizowana infrastruktura IT. Pomyślne wdrożenie rozwiązania HADR Cluster SQL Server na platformie Azure wymaga zrozumienia tych różnic i zaprojektowania rozwiązania w celu ich dopasowania.

### <a name="high-availability-nodes-in-an-availability-set"></a>Węzły wysokiej dostępności w zestawie dostępności
Zestawy dostępności na platformie Azure umożliwiają umieszczenie węzłów o wysokiej dostępności w oddzielnych domenach błędów i domenach aktualizacji. Platforma Azure przypisuje domenę aktualizacji i domenę błędów do każdej maszyny wirtualnej w zestawie dostępności. Ta konfiguracja w centrum danych gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji co najmniej jedna maszyna wirtualna będzie dostępna i spełnia warunki umowy SLA platformy Azure wynoszącą 99,95% czasu. 

Aby skonfigurować konfigurację wysokiej dostępności, należy umieścić wszystkie uczestniczące SQL Server maszyny wirtualne w tym samym zestawie dostępności, aby uniknąć utraty dostępu do aplikacji lub danych podczas zdarzenia konserwacji. Tylko węzły w tej samej usłudze w chmurze mogą uczestniczyć w tym samym zestawie dostępności. Aby uzyskać więcej informacji, zobacz [Manage the availability of virtual machines](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Zarządzanie dostępnością maszyn wirtualnych).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Węzły wysokiej dostępności w strefie dostępności
Strefy dostępności są unikatowymi lokalizacjami fizycznymi w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależną moc, chłodzenie i sieci. Fizyczne rozdzielenie stref dostępności w regionie pomaga chronić aplikacje i dane przed awariami centrów danych, co zapewnia, że co najmniej jedna maszyna wirtualna jest dostępna i spełnia warunki umowy SLA platformy Azure wynoszącą 99,99% czasu. 

Aby skonfigurować wysoką dostępność, należy umieścić udział SQL Server maszyn wirtualnych w różnych strefach dostępności w regionie. Są naliczane dodatkowe opłaty za transfer sieciowy do sieci między strefami dostępności. Aby uzyskać więcej informacji, zobacz [strefy dostępności](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Zachowanie klastra trybu failover w sieci platformy Azure
Usługa DHCP niezgodna ze standardem RFC na platformie Azure może spowodować niepowodzenie tworzenia niektórych konfiguracji klastra trybu failover. Ten błąd występuje, ponieważ nazwa sieci klastra ma przypisany zduplikowany adres IP, taki jak ten sam adres IP, co jeden z węzłów klastra. Jest to problem występujący w przypadku korzystania z grup dostępności, które są zależne od funkcji klastra trybu failover systemu Windows.

Zastanów się nad scenariuszem tworzenia klastra z dwoma węzłami i przełączeniem w tryb online:

1. Klaster przejdzie w tryb online, a następnie WĘZEŁ1 żąda dynamicznego przypisanego adresu IP dla nazwy sieci klastra.
2. Usługa DHCP nie udostępnia żadnego adresu IP innego niż NODE1's własny adres IP, ponieważ usługa DHCP rozpoznaje, że żądanie pochodzi od samego WĘZEŁ1a.
3. System Windows wykrywa, że zduplikowany adres jest przypisany do WĘZEŁ1 i do nazwy sieciowej klastra trybu failover, a domyślna grupa klastrów nie przejdzie w tryb online.
4. Domyślna grupa klastra przenosi do WĘZEŁ2. WĘZEŁ2 traktuje NODE1's adres IP jako adres IP klastra i przywraca domyślną grupę klastra w trybie online.
5. Gdy WĘZEŁ2 próbuje nawiązać połączenie z WĘZEŁ1, pakiety kierowane w WĘZEŁ1 nigdy nie opuszczą WĘZEŁ2, ponieważ rozwiązuje on adres IP NODE1's do samego siebie. Usługa WĘZEŁ2 nie może nawiązać połączenia z WĘZEŁ1, a następnie utraci kworum i zamyka klaster.
6. WĘZEŁ1 może wysyłać pakiety do WĘZEŁ2, ale WĘZEŁ2 nie może odpowiedzieć. WĘZEŁ1 utraci kworum i zamyka klaster.

Możesz uniknąć tego scenariusza, przypisując nieużywany statyczny adres IP do nazwy sieci klastra w celu przełączenia nazwy sieciowej klastra w tryb online. Na przykład można użyć adresu IP połączenia lokalnego, takiego jak 169.254.1.1. Aby uprościć ten proces, zobacz [Konfigurowanie klastra trybu failover systemu Windows na platformie Azure dla grup dostępności](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie grup dostępności na platformie Azure (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Obsługa odbiorników grup dostępności
Odbiorniki grup dostępności są obsługiwane na maszynach wirtualnych platformy Azure z systemem Windows Server 2012 lub nowszym. Jest to możliwe dzięki użyciu punktów końcowych z równoważeniem obciążenia, które są włączone na maszynach wirtualnych platformy Azure, które są węzłami grupy dostępności. Należy wykonać specjalne czynności konfiguracyjne, aby detektory działały zarówno dla aplikacji klienckich działających na platformie Azure, jak i działających lokalnie.

Dostępne są dwie główne opcje konfigurowania odbiornika: zewnętrzne (publiczne) lub wewnętrzne. Zewnętrzny (Publiczny) odbiornik używa modułu równoważenia obciążenia dostępnego z Internetu i jest skojarzony z publicznym wirtualnym adresem IP, który jest dostępny za pośrednictwem Internetu. Odbiornik wewnętrzny używa wewnętrznego modułu równoważenia obciążenia i obsługuje tylko klientów w ramach tej samej sieci wirtualnej. Dla dowolnego typu modułu równoważenia obciążenia należy włączyć bezpośredni zwrot serwera. 

Jeśli grupa dostępności obejmuje wiele podsieci platformy Azure (na przykład wdrożenie przekraczające regiony platformy Azure), ciąg połączenia klienta musi zawierać wartość `MultisubnetFailover=True` . Powoduje to łączenie równoległych prób z replikami w różnych podsieciach. Aby uzyskać instrukcje dotyczące konfigurowania odbiornika, zobacz [Konfigurowanie odbiornika ILB dla grup dostępności na platformie Azure](availability-group-listener-powershell-configure.md).


Można nadal łączyć się z każdą repliką dostępności oddzielnie, łącząc się bezpośrednio z wystąpieniem usługi. Ponadto, ponieważ grupy dostępności są zgodne z poprzednimi wersjami klientów funkcji dublowania baz danych, można łączyć się z replikami dostępności, takimi jak partnerzy funkcji dublowania baz danych, o ile repliki są skonfigurowane w podobny sposób do dublowania baz danych:

* Istnieje jedna replika podstawowa i jedna replika pomocnicza.
* Replika pomocnicza jest skonfigurowana jako nie do odczytu (opcja **pomocnicza do odczytu** jest ustawiona na wartość **no**).

Oto przykładowe parametry połączenia klienta, które odpowiadają tej konfiguracji podobnej do dublowania bazy danych, przy użyciu ADO.NET lub SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Aby uzyskać więcej informacji na temat łączności klienta, zobacz:

* [Używanie słów kluczowych parametrów połączenia z SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Łączenie klientów z sesją dublowania baz danych (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Łączenie z odbiornikiem grupy dostępności w hybrydowym](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Odbiorniki grup dostępności, łączność z klientem i tryb failover aplikacji (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Używanie Database-Mirroring parametrów połączenia z grupami dostępności](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Opóźnienie sieci w hybrydowym
Wdróż rozwiązanie HADR Cluster z założeniem, że mogą istnieć okresy wysokiego opóźnienia sieci między siecią lokalną a platformą Azure. Podczas wdrażania replik na platformie Azure Użyj zatwierdzenia asynchronicznego zamiast synchronicznego zatwierdzania w trybie synchronizacji. Wdrażając serwery dublowania baz danych zarówno lokalnie, jak i na platformie Azure, użyj trybu wysokiej wydajności, a nie trybu wysokiego poziomu zabezpieczeń.

### <a name="geo-replication-support"></a>Obsługa replikacji geograficznej
Replikacja geograficzna w usłudze Azure disks nie obsługuje plików danych i plików dziennika tej samej bazy danych, które mają być przechowywane na oddzielnych dyskach. GRS replikuje zmiany na każdym dysku niezależnie i asynchronicznie. Ten mechanizm gwarantuje kolejność zapisu w ramach jednego dysku w kopii z replikacją geograficzną, ale nie między kopiami replikowanymi geograficznie wielu dysków. W przypadku skonfigurowania bazy danych do przechowywania pliku danych i jego pliku dziennika na oddzielnych dyskach odzyskane dyski po awarii mogą zawierać bardziej aktualną kopię pliku danych niż plik dziennika, który dzieli dziennik zapisu w SQL Server oraz właściwości KWASów (niepodzielności, spójności, izolacji i trwałości) transakcji. 

Jeśli nie masz opcji wyłączenia replikacji geograficznej na koncie magazynu, Zachowaj wszystkie pliki danych i dziennika dla bazy danych na tym samym dysku. Jeśli musisz użyć więcej niż jednego dysku ze względu na rozmiar bazy danych, wdróż jedno z wymienionych wcześniej rozwiązań odzyskiwania po awarii w celu zapewnienia nadmiarowości danych.

## <a name="next-steps"></a>Następne kroki

Zdecyduj, czy [Grupa dostępności](availability-group-overview.md) lub [wystąpienie klastra trybu failover](failover-cluster-instance-overview.md) są najlepszym rozwiązaniem w zakresie ciągłości biznesowej dla Twojej firmy. Następnie zapoznaj się z [najlepszymi rozwiązaniami](hadr-cluster-best-practices.md) dotyczącymi konfigurowania środowiska pod kątem wysokiej dostępności i odzyskiwania po awarii.