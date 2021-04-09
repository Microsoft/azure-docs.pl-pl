---
title: Wdrażanie oprogramowania SAP BusinessObjects BI platform na platformie Azure | Microsoft Docs
description: Planowanie, wdrażanie i Konfigurowanie platformy SAP BusinessObjects BI na platformie Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505906"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Przewodnik planowania i implementacji platformy SAP BusinessObjects BI na platformie Azure

## <a name="overview"></a>Omówienie

Celem tego przewodnika jest przedstawienie wytycznych dotyczących planowania, wdrażania i konfigurowania platformy SAP BusinessObjects BI, znanej również jako platforma SAP BOBI na platformie Azure. Ten przewodnik jest przeznaczony do obsługi typowych usług i funkcji platformy Azure, które są istotne dla platformy SAP BOBI. Ten przewodnik nie jest wyczerpującą listą wszystkich możliwych opcji konfiguracji. Obejmuje to rozwiązania typowe dla typowych scenariuszy wdrażania.

Ten przewodnik nie jest przeznaczony do zastępowania standardowej instalacji platformy SAP BOBI i podręczników administracyjnych, systemu operacyjnego ani żadnej dokumentacji bazy danych.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planowanie i wdrażanie platformy SAP BusinessObjects BI na platformie Azure

Microsoft Azure oferuje szeroką gamę usług, w tym obliczenia, magazyn, Sieć i wiele innych firm do kompilowania aplikacji bez długotrwałych cykli zakupów. Usługa Azure Virtual Machines umożliwia firmom wdrażanie zasobów obliczeniowych na żądanie i skalowalności dla różnych aplikacji SAP, takich jak aplikacje oparte na oprogramowaniu SAP NetWeaver, SAP Hybris i SAP BusinessObjects BI platform, na podstawie ich potrzeb biznesowych. Platforma Azure obsługuje również łączność obejmującą wiele lokalizacji, dzięki czemu firmy mogą integrować maszyny wirtualne platformy Azure z domenami lokalnymi, chmurami prywatnymi i system SAP w poziomie.

Ten dokument zawiera wskazówki dotyczące planowania i wdrażania programu SAP BusinessObjects BI platform na platformie Azure. Uzupełnia on dokumentację instalacji SAP i uwagi SAP, które reprezentują podstawowe zasoby dotyczące instalacji i wdrożeń oprogramowania SAP BOBI.

### <a name="architecture-overview"></a>Omówienie architektury

Platforma SAP BusinessObjects BI to samodzielny system, który może istnieć na jednej maszynie wirtualnej platformy Azure lub może być skalowany w klastrze wielu Virtual Machines platformy Azure, na których działają różne składniki. Platforma SAP BOBI składa się z sześciu warstw koncepcyjnych: warstwy klienta, warstwy sieci Web, warstwy zarządzania, warstwy magazynowania, warstwy przetwarzania i warstwy danych. (Aby uzyskać więcej informacji na temat każdej warstwy, zobacz Przewodnik administratora programu [SAP BusinessObjects Business Intelligence platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Help Portal). Poniżej znajdują się szczegóły wysokiego poziomu dla każdej warstwy:

- **Warstwa klienta:** Zawiera wszystkie aplikacje klienckie komputerów stacjonarnych, które współpracują z platformą analizy biznesowej w celu zapewnienia różnego rodzaju funkcji raportowania, analitycznych i administracyjnych.
- **Warstwa sieci Web:** Zawiera aplikacje sieci Web wdrożone na serwerach aplikacji sieci Web JAVA. Aplikacje sieci Web udostępniają użytkownikom końcowym funkcje platformy analizy biznesowej za pomocą przeglądarki sieci Web.
- **Warstwa zarządzania:** Koordynuje i kontroluje wszystkie składniki, które udostępniają platformę analizy biznesowej. Obejmuje on centralny serwer zarządzania (CMS) oraz serwer zdarzeń i skojarzone usługi
- **Warstwa magazynowania:** Jest on odpowiedzialny za obsługę plików, takich jak dokumenty i raporty. Obsługuje ona również buforowanie raportów w celu zapisywania zasobów systemowych, gdy są dostępne raporty dostępu użytkowników.
- **Warstwa przetwarzania:** Analizuje ona dane i tworzy raporty i inne typy wyjściowe. Jest to jedyna warstwa, która uzyskuje dostęp do baz danych, które zawierają dane raportu.
- **Warstwa danych:** Składa się z serwerów baz danych, które obsługują systemową bazę danych systemu CMS i magazyn danych inspekcji.

Platforma SAP BI składa się z kolekcji serwerów uruchomionych na jednym lub wielu hostach. Należy wybrać odpowiednią strategię wdrażania w oparciu o wielkość, potrzebę biznesową i typ środowiska. W przypadku małych instalacji, takich jak programowanie lub testowanie, można użyć pojedynczej maszyny wirtualnej platformy Azure dla serwera aplikacji sieci Web, serwera bazy danych i wszystkich serwerów platformy analizy biznesowej. W przypadku korzystania z oferty "DBaaS (Database as a Service") na platformie Azure serwer bazy danych zostanie uruchomiony niezależnie od innych składników. W przypadku średnich i dużych instalacji można korzystać z serwerów z wieloma maszynami wirtualnymi platformy Azure.

Na poniższej ilustracji przedstawiono architekturę wdrożenia dużej skali platformy SAP BOBI na maszynach wirtualnych platformy Azure, gdzie każdy składnik jest dystrybuowany i umieszczany w zestawach dostępności, które mogą utrzymywać pracę w trybie failover w przypadku przerwania działania usługi.

![Architektura platformy SAP BusinessObjects BI na platformie Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Szczegóły architektury

- Moduł równoważenia obciążenia

  W przypadku wdrażania z wieloma wystąpieniami w programie SAP BOBI serwery aplikacji sieci Web (lub warstwy sieci Web) są uruchomione na co najmniej dwóch hostach. Aby równomiernie rozłożyć obciążenie użytkownikami między serwerami sieci Web, można użyć modułu równoważenia obciążenia między użytkownikami końcowymi i serwerami sieci Web. Na platformie Azure Możesz użyć [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) lub [Application Gateway platformy Azure](../../../application-gateway/overview.md) do zarządzania ruchem do serwerów sieci Web.

- Serwery aplikacji sieci Web

  Serwer sieci Web obsługuje aplikacje sieci Web platformy SAP BOBI, takie jak CMC i konsola uruchamiania usługi BI. Aby uzyskać wysoką dostępność serwera sieci Web, należy wdrożyć co najmniej dwa serwery aplikacji sieci Web w celu zarządzania nadmiarowością i równoważeniem obciążenia. Na platformie Azure te serwery aplikacji sieci Web mogą być umieszczane w zestawach dostępności lub strefach dostępności w celu zapewnienia lepszej dostępności.

  Tomcat to domyślna aplikacja sieci Web dla platformy SAP BI. Aby uzyskać wysoką dostępność Tomcat, Włącz replikację sesji przy użyciu [statycznego interceptora członkostwa](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) na platformie Azure. Gwarantuje to, że użytkownik może uzyskać dostęp do aplikacji sieci Web SAP BI nawet wtedy, gdy usługa Tomcat jest zakłócona.

  > [!Important]
  > Domyślnie Tomcat używa protokołu IP multiemisji i portu dla klastra, co nie jest obsługiwane na platformie Azure (Uwaga: SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Serwery usługi BI platform

  Serwery z platformą BI obejmują wszystkie usługi, które są częścią aplikacji SAP BOBI (warstwa zarządzania, warstwa przetwarzania i warstwa magazynowania). Gdy serwer sieci Web odbiera żądanie, wykrywa każdy serwer platformy analizy biznesowej (w tym wszystkie serwery CMS w klastrze) i automatycznie równoważy obciążenie ich żądaniami. W przypadku awarii jednego z hostów platformy BI serwer sieci Web automatycznie wysyła żądania do innego hosta.

  Aby uzyskać wysoką dostępność lub nadmiarowość dla platformy analizy biznesowej, należy wdrożyć aplikację w co najmniej dwóch maszynach wirtualnych platformy Azure. Na podstawie rozmiaru można skalować swoją platformę analizy biznesowej tak, aby była uruchamiana na większej liczbie maszyn wirtualnych platformy Azure.

- Serwer repozytorium plików (FRS)

  Serwer repozytorium plików zawiera wszystkie raporty i inne dokumenty analizy biznesowej, które zostały utworzone. W przypadku wdrażania z wieloma wystąpieniami serwery usługi BI platform są uruchomione na wielu maszynach wirtualnych, a każda maszyna wirtualna powinna mieć dostęp do tych raportów i innych dokumentów analizy biznesowej. Dlatego system plików musi być współużytkowany na wszystkich serwerach usługi BI platform.

  Na platformie Azure Możesz użyć [plików usługi Azure Premium](../../../storage/files/storage-files-introduction.md) lub [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) dla serwera repozytorium plików. Obie te usługi platformy Azure mają wbudowaną nadmiarowość.

  > [!Important]
  > Protokół SMB dla Azure Files jest ogólnie dostępny, ale obsługa protokołu NFS dla Azure Files jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna NFS 4,1 dla Azure Files jest teraz w wersji zapoznawczej](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- Baza danych inspekcji & CMS
  
  Platforma SAP BOBI wymaga bazy danych do przechowywania danych systemowych, która jest określana jako baza danych CMS. Służy do przechowywania informacji o platformie analizy biznesowej, takich jak użytkownik, serwer, folder, dokument, konfiguracja i szczegóły uwierzytelniania.

  Platforma Azure oferuje usługę [MySQL Database](https://azure.microsoft.com/en-us/services/mysql/) i bazę danych usługi [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) jako usługa (DBaaS), która może być używana dla bazy danych CMS i bazy danych inspekcji. Ponieważ jest to oferta PaaS, klienci nie muszą martwić się o działania, dostępność i konserwację baz danych. Klient może również wybrać własną bazę danych dla repozytorium CMS i inspekcji na podstawie ich potrzeb firmy.

## <a name="support-matrix"></a>Tabela obsługi

W tej sekcji opisano obsługę różnych składników SAP BOBI, takich jak wersja platformy SAP BusinessObjects BI platform, system operacyjny i bazy danych na platformie Azure.

### <a name="sap-businessobjects-bi-platform"></a>Platforma SAP BusinessObjects BI

Usługa Azure Infrastructure as (IaaS) umożliwia wdrażanie i Konfigurowanie platformy SAP BusinessObjects BI w usłudze Azure COMPUTE. Obsługuje ona następujące wersje platformy SAP BOBI —

- Oprogramowanie SAP BusinessObjects BI platform 4,3
- SAP BusinessObjects BI platform 4,2 SP04 +
- SAP BusinessObjects BI platform 4,1 SP05 +

Platforma SAP BI działa w różnych systemach operacyjnych i bazach danych. Możliwość obsługi platformy SAP BOBI między systemem operacyjnym i wersją bazy danych znajduje się w [macierzy dostępności produktów](https://apps.support.sap.com/sap/support/pam) dla oprogramowania SAP BOBI.

### <a name="operating-system"></a>System operacyjny

Platforma Azure obsługuje następujące systemy operacyjne dla wdrożenia programu SAP BusinessObjects BI platform.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (olu)

Wersja systemu operacyjnego wymieniona w [macierzy dostępności produktu (PAM) dla platformy SAP BUSINESSOBJECTS BI](https://support.sap.com/pam) jest obsługiwana, o ile są one zgodne do uruchamiania w infrastrukturze platformy Azure.

### <a name="databases"></a>Bazy danych

Platforma analizy biznesowej wymaga bazy danych dla magazynu danych CMS i inspekcji, który można zainstalować na wszystkich obsługiwanych bazach danych, które są wymienione w [macierzy dostępności produktu SAP](https://support.sap.com/pam) , która obejmuje następujące elementy:

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (obsługiwana baza danych tylko dla platformy SAP BOBI w systemie Windows)

  Jest to w pełni zarządzany aparat bazy danych SQL Server w oparciu o najnowszą stabilną wersję Enterprise SQL Server. Usługa Azure SQL Database obsługuje większość funkcji zarządzania bazami danych, takich jak uaktualnianie, stosowanie poprawek i monitorowanie bez zaangażowania użytkowników. Za pomocą usługi Azure SQL Database można utworzyć warstwę magazynów danych o wysokiej dostępności i wysokiej wydajności dla aplikacji i rozwiązań na platformie Azure. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) .

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (przestrzegaj tych samych wytycznych dotyczących zgodności jak wspomniano w przypadku programu MySQL AB w usłudze SAP PAM)

  Jest to usługa relacyjnej bazy danych obsługiwana przez program MySQL Community Edition. Jest to w pełni zarządzana oferta bazy danych jako usługa (DBaaS), która może obsługiwać zadania o znaczeniu krytycznym z przewidywalną wydajnością i dynamiczną skalowalnością. Ma wbudowaną funkcję wysokiej dostępności, automatyczne wykonywanie kopii zapasowych, stosowanie poprawek oprogramowania, automatyczne wykrywanie błędów oraz przywracanie do punktu w czasie przez maksymalnie 35 dni, co znacznie zmniejsza zadania operacyjne. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [Azure Database for MySQL](../../../mysql/overview.md) .

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (w przypadku wersji i ograniczeń, sprawdź uwagi SAP Uwaga [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

W tym dokumencie przedstawiono wskazówki dotyczące wdrażania **platformy SAP BOBI w systemie Windows przy użyciu platformy Azure SQL Database** i **oprogramowania SAP BOBI w systemie Linux z Azure Database for MySQL**. Jest to również nasze zalecane podejście do uruchamiania platformy SAP BusinessObjects BI na platformie Azure.

## <a name="sizing"></a>Ustalanie rozmiaru

Ustalanie wielkości jest procesem określania wymagań sprzętowych w celu wydajnego uruchamiania aplikacji. W przypadku platformy SAP BOBI należy wykonać zmiany rozmiaru przy użyciu narzędzia do ustalania rozmiaru SAP o nazwie [Quick Sizeer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). Narzędzie dostarcza SAP w oparciu o dane wejściowe, które następnie należy zamapować na certyfikowane typy maszyn wirtualnych platformy Azure dla SAP. Uwaga dotycząca oprogramowania SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) zawiera listę obsługiwanych produktów SAP i typów maszyn wirtualnych platformy Azure wraz z punktami SAP. Aby uzyskać więcej informacji na temat określania rozmiarów, sprawdź [Przewodnik dotyczący ustalania wielkości usługi SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Dla potrzeb magazynu dla platformy SAP BOBI platforma Azure oferuje różne typy [Managed disks](../../managed-disks-overview.md). W przypadku katalogu instalacyjnego SAP BOBI zaleca się używanie dysku zarządzanego w warstwie Premium i bazy danych, która jest uruchamiana na maszynach wirtualnych, postępuj zgodnie ze wskazówkami podanymi w temacie [wdrażanie systemu DBMS dla obciążeń SAP](dbms_guide_general.md).

Platforma Azure obsługuje dwie DBaaS oferty dla warstwy danych platformy SAP BOBI — [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (aplikacja analizy biznesowej działająca w systemie Windows) i [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (aplikacja analizy biznesowej działa w systemach Linux i Windows). Na podstawie wyniku zmiany wielkości można wybrać model kupowania, który najlepiej odpowiada Twoim potrzebom.

> [!Tip]
> Aby uzyskać informacje na temat szybkiego ustalania wielkości, należy wziąć pod uwagę 800 punktów SAP = 1 vCPU podczas mapowania wyniku punktu usługi SAP BOBI platform bazy danych platformy Azure Database as a Service (Azure SQL Database lub Azure Database for MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Ustalanie rozmiarów modeli dla usługi Azure SQL Database

Azure SQL Database oferuje następujące trzy modele zakupu:

- Oparty na rdzeniach wirtualnych

  Pozwala wybrać liczbę rdzeni wirtualnych, ilość pamięci oraz ilość i szybkość magazynu. Model zakupu oparty na rdzeń wirtualny umożliwia również korzystanie z [Korzyść użycia hybrydowego platformy Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów. Ten model jest odpowiedni dla klienta, który zapewnia elastyczność, kontrolę i przezroczystość.

  W modelu rdzeń wirtualny są oferowane trzy [Opcje warstwy usług](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) , które obejmują Ogólnego przeznaczenia, krytyczne dla działania firmy i skalowanie. Warstwa usług definiuje architekturę magazynu, miejsce, limity we/wy i opcje ciągłości działania związane z dostępnością i odzyskiwaniem po awarii. Poniżej znajdują się szczegółowe informacje na temat każdej opcji warstwy usług —

  1. **Ogólnego przeznaczenia** warstwy usług najlepiej nadaje się do obciążeń firmowych. Oferuje zorientowane na budżet, zrównoważone i skalowalne Opcje obliczeniowe i magazynowe. Aby uzyskać więcej informacji, zobacz [Opcje zasobów i limity](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Krytyczne dla działania firmy** warstwa usług oferuje aplikacjom biznesowym największą odporność na błędy przy użyciu kilku izolowanych replik i zapewnia największą wydajność operacji we/wy na replikę bazy danych. Aby uzyskać więcej informacji, zobacz [Opcje zasobów i limity](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. Warstwa usługi do **skalowania** jest Najlepsza w przypadku obciążeń firmowych mających wysoce skalowalny magazyn i wymagania dotyczące skalowania w poziomie. Zapewnia wyższą odporność na błędy, umożliwiając konfigurację wielu izolowanych replik baz danych. Aby uzyskać więcej informacji, zobacz [Opcje zasobów i limity](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- Oparty na jednostkach DTU

  Model zakupu oparty na jednostkach DTU oferuje mieszankę zasobów obliczeniowych, pamięci i operacji we/wy w trzech warstwach usług, aby obsługiwać lekkie i duże obciążenia baz danych. Rozmiary obliczeniowe w poszczególnych warstwach zapewniają inną kombinację tych zasobów, do których można dodać dodatkowe zasoby magazynu. Jest to najlepiej dostosowane do klientów, którzy chcą mieć proste, wstępnie skonfigurowane opcje zasobów.

  [Warstwy usług](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) w modelu zakupu opartego na jednostkach DTU są zróżnicowane według zakresu wielkości obliczeniowej ze stałą ilością dołączonego magazynu, stałego okresu przechowywania kopii zapasowych i ustalonej ceny.

- Praca bezserwerowa

  Model bezserwerowy automatycznie skaluje obliczenia na podstawie zapotrzebowania na obciążenia i rachunki dla ilości użytych obliczeń na sekundę. Warstwa obliczeniowa bezserwerowa automatycznie wstrzymuje bazy danych w trakcie okresów nieaktywnych, gdy są naliczane opłaty za magazyn, i automatycznie wznawia bazy danych po powrocie działania. Aby uzyskać więcej informacji, zobacz [Opcje zasobów i limity](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  Jest to bardziej odpowiednie dla sporadycznego, nieprzewidywalnego użycia z niskim średnim wykorzystaniem obliczeń w czasie. Ten model może być używany dla nieprodukcyjnego wdrożenia SAP BOBI.

> [!Note]
> W przypadku oprogramowania SAP BOBI można korzystać z modelu opartego na rdzeń wirtualny i wybierać Ogólnego przeznaczenia lub Krytyczne dla działania firmy warstwy usług w zależności od potrzeb firmy.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Ustalanie rozmiarów modeli usługi Azure Database for MySQL

Azure Database for MySQL zawiera trzy różne warstwy cenowe. Są one zróżnicowane według ilości obliczeń w rdzeni wirtualnych, pamięci na rdzeń wirtualny i technologii magazynowania używanej do przechowywania daty. Poniżej znajdują się szczegółowe informacje na temat opcji i więcej informacji na temat różnych atrybutów, odnoszące się do [warstwy cenowej](../../../mysql/concepts-pricing-tiers.md) Azure Database for MySQL.

- Podstawowa

  Jest on używany do obciążeń docelowych, które wymagają lekkich obliczeń i wydajności operacji we/wy.

- Ogólnego przeznaczenia

  Jest to odpowiednie dla większości obciążeń firmowych, które wymagają zrównoważonych obliczeń i pamięci dzięki skalowalnej przepływności we/wy.

- Optymalizacja pod kątem pamięci

  W przypadku obciążeń baz danych o wysokiej wydajności, które wymagają wydajności w pamięci w celu przyspieszenia przetwarzania transakcji i wyższego współbieżności.

> [!Note]
> W przypadku oprogramowania SAP BOBI najlepiej używać Ogólnego przeznaczenia lub zoptymalizowanej pod kątem pamięci warstwy cenowej na podstawie obciążenia biznesowego.

## <a name="azure-resources"></a>Zasoby platformy Azure

### <a name="choosing-regions"></a>Wybieranie regionów

Region świadczenia usługi Azure to jedna lub kolekcja centrów danych, które zawierają infrastrukturę do uruchamiania i hostowania różnych usług platformy Azure. Ta infrastruktura obejmuje dużą liczbę węzłów, które działają jako węzły obliczeniowe lub węzły magazynu, lub uruchamiają funkcje sieciowe. Nie wszystkie regiony oferują te same usługi.

Platforma SAP BI zawiera różne składniki, które mogą wymagać określonych typów maszyn wirtualnych, takich jak Azure Files lub Azure NetApp Files lub baza danych jako usługa (DBaaS) dla warstwy danych, która może być niedostępna w określonych regionach. Dokładne informacje o typach maszyn wirtualnych, typach usługi Azure Storage lub innych usługach platformy Azure można znaleźć w obszarze [produkty dostępne według regionów](https://azure.microsoft.com/en-us/global-infrastructure/services/) . Jeśli masz już uruchomione systemy SAP na platformie Azure, prawdopodobnie masz określony region. W takim przypadku należy najpierw zbadać, czy niezbędne usługi są dostępne w tych regionach, aby określić architekturę platformy SAP BI.

### <a name="availability-zones"></a>Strefy dostępności

Strefy dostępności są fizycznie oddzielone lokalizacjami w regionie świadczenia usługi Azure. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależną moc, chłodzenie i sieci.

Aby zapewnić wysoką dostępność dla każdej warstwy dla platformy SAP BI, możesz rozpowszechnić maszyny wirtualne w strefie dostępności, implementując platformę wysokiej dostępności, która zapewnia najlepszą umowę SLA na platformie Azure. W przypadku umowy SLA maszyny wirtualnej na platformie Azure Zapoznaj się z najnowszą wersją [umowy SLA maszyny wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

W przypadku warstwy danych usługa Azure Database as Service (DBaaS) domyślnie oferuje strukturę wysokiej dostępności. Wystarczy wybrać region i usługę nieodłączną dostępność, nadmiarowość i możliwości odporności, aby ograniczyć przestoje bazy danych z planowanych i nieplanowanych awarii, bez konieczności konfigurowania dodatkowych składników. Aby uzyskać więcej informacji na temat umowy SLA dla obsługiwanej oferty DBaaS na platformie Azure, sprawdź [wysoką dostępność w Azure Database for MySQL](../../../mysql/concepts-high-availability.md) i [wysoką dostępność dla Azure SQL Database](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Zestawy dostępności

Zestaw dostępności to logiczna funkcja grupowania na potrzeby izolowania zasobów maszyn wirtualnych od siebie podczas wdrażania. Platforma Azure zapewnia, że maszyny wirtualne, które są umieszczane w zestawie dostępności, działają na wielu serwerach fizycznych, stojakach obliczeniowych, jednostkach magazynowych i przełącznikach sieciowych. Jeśli wystąpi awaria sprzętu lub oprogramowania, wpływ dotyczy tylko podzbioru maszyn wirtualnych, a całe rozwiązanie będzie działać. W związku z tym gdy maszyny wirtualne są umieszczane w zestawach dostępności, Kontroler sieci szkieletowej platformy Azure dystrybuuje maszyny wirtualne w różnych domenach [błędów](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) i [uaktualnień](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) , aby uniemożliwić dostęp wszystkich maszyn wirtualnych ze względu na konserwację lub awarię infrastruktury w ramach jednej domeny błędów.

Platforma SAP BI zawiera wiele różnych składników i podczas projektowania architektury trzeba upewnić się, że każdy składnik jest odporny na jakiekolwiek zakłócenia. Można to osiągnąć przez umieszczenie maszyn wirtualnych platformy Azure dla każdego składnika w ramach zestawów dostępności. Należy pamiętać, że w przypadku miksowania maszyn wirtualnych różnych rodzin maszyn wirtualnych w ramach jednego zestawu dostępności mogą wystąpić problemy, które uniemożliwiają dołączenie określonego typu maszyn wirtualnych do tego zestawu dostępności. W związku z tym należy mieć oddzielny zestaw dostępności dla aplikacji sieci Web, aplikacji analizy biznesowej dla platformy SAP BI, która została wyróżniona w omówieniu architektury.

Ponadto liczba domen aktualizacji i błędów, które mogą być używane przez zestaw dostępności platformy Azure w ramach jednostki skalowania platformy Azure, jest ograniczona. Dlatego jeśli dodajesz maszyny wirtualne do jednego zestawu dostępności, co najmniej dwie maszyny wirtualne będą ostatecznie kończyć się w tej samej domenie błędów lub aktualizacji. Aby uzyskać więcej informacji, zobacz sekcję [zestawy dostępności platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) w dokumencie planowanie i wdrażanie dla oprogramowania SAP na maszynach wirtualnych platformy Azure.

Aby zrozumieć koncepcję zestawów dostępności platformy Azure i sposób, w jaki zestawy dostępności odnoszą się do domen błędów i uaktualnień, przeczytaj artykuł [Zarządzanie dostępnością](../../availability.md) .

> [!Important]
> Pojęcia dotyczące Strefy dostępności platformy Azure i zestawów dostępności platformy Azure wykluczają się wzajemnie. Oznacza to, że można wdrożyć parę lub wiele maszyn wirtualnych w określonej strefie dostępności lub w zestawie dostępności platformy Azure. Ale nie obu.

### <a name="virtual-machines"></a>Maszyny wirtualne

Azure Virtual Machine to oferta usługi, która umożliwia wdrażanie niestandardowych obrazów na platformie Azure jako wystąpieniach typu "infrastruktura jako usługa" (IaaS). Upraszcza to utrzymywanie i obsługę aplikacji przez zapewnienie obliczeniowych i magazynów na żądanie na potrzeby hostowania, skalowania i zarządzania aplikacją sieci Web i połączonymi aplikacjami.

Platforma Azure oferuje różne maszyny wirtualne dla wszystkich potrzeb aplikacji. Jednak w przypadku obciążeń SAP system Azure zawężał wybór do różnych rodzin maszyn wirtualnych, które są odpowiednie dla obciążenia SAP i bardziej szczegółowego obciążenia SAP HANA. Aby uzyskać więcej szczegółowych informacji, sprawdź, [jakie oprogramowanie SAP jest obsługiwane dla wdrożeń platformy Azure](sap-supported-product-on-azure.md).

W zależności od wielkości platformy SAP BI należy zamapować wymaganie na maszynę wirtualną platformy Azure, która jest obsługiwana na platformie Azure dla produktu SAP. Test SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) to dobry punkt wyjścia zawierający listę obsługiwanych typów maszyn wirtualnych platformy Azure dla produktów SAP w systemach Windows i Linux. Należy również pamiętać, że oprócz wyboru czysto obsługiwanych typów maszyn wirtualnych należy sprawdzić, czy te typy maszyn wirtualnych są dostępne w określonym regionie. Dostępność typu maszyny wirtualnej można sprawdzić na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/) . Aby wybrać model cenowy, możesz odnieść się do [usługi Azure Virtual Machines dla obciążenia SAP](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Storage

Azure Storage to usługa w chmurze zarządzana przez platformę Azure, która zapewnia magazyn o wysokiej dostępności, bezpieczny, trwały, skalowalny i nadmiarowy. Niektóre typy magazynów mają ograniczone zastosowanie do scenariuszy SAP. Niektóre typy magazynów platformy Azure są dobrze dopasowane lub zoptymalizowane pod kątem określonych scenariuszy obciążeń SAP. Aby uzyskać więcej informacji, zapoznaj się z tematem [Azure Storage typy dla przewodnika obciążeń SAP](planning-guide-storage.md) , ponieważ wyróżnia on różne opcje magazynu odpowiednie dla oprogramowania SAP.

Usługa Azure Storage ma różne typy magazynów dostępne dla klientów i szczegółowe informacje na ten temat można znaleźć w artykule [jakie typy dysków są dostępne na platformie Azure?](../../disks-types.md). Platforma SAP BOBI używa następującej usługi Azure Storage do kompilowania aplikacji —

- Dyski zarządzane przez platformę Azure

  Jest to wolumin magazynu na poziomie bloku, który jest zarządzany przez platformę Azure. W przypadku instalacji na maszynach wirtualnych platformy Azure można używać dysków dla serwerów aplikacji i baz danych platformy SAP BOBI. Dostępne są różne typy [Managed disks platformy Azure](../../managed-disks-overview.md) , ale zalecane jest używanie [dysków SSD Premium](../../disks-types.md#premium-ssd) dla aplikacji i bazy danych platformy SAP BOBI.

  W poniższym przykładzie dysków SSD Premium jest używany dla katalogu instalacji platformy BOBI. W przypadku bazy danych zainstalowanej na maszynie wirtualnej można użyć usługi Managed disks w celu uzyskania danych i ilości dziennika zgodnie z wytycznymi. Bazy danych CMS i Audit są zwykle małe i nie mają tych samych wymagań dotyczących wydajności magazynu, co w przypadku innych baz danych programu SAP OLTP/OLAP.

- Pliki lub Azure NetApp Files platformy Azure Premium

  W przypadku platformy SAP BOBI serwer repozytorium plików (FRS) odnosi się do katalogów dysków, w których są przechowywane zawartości, takie jak raporty, miejsca i połączenia, które są używane przez wszystkie serwery aplikacji tego systemu. [Pliki usługi Azure Premium](../../../storage/files/storage-files-introduction.md) lub [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) Storage mogą służyć jako współużytkowany system plików dla aplikacji SAP BOBI Applications FRS. Ponieważ ta oferta magazynu nie jest dostępna we wszystkich regionach, zapoznaj się z informacjami na temat [produktów dostępnych według regionów](https://azure.microsoft.com/en-us/global-infrastructure/services/) , aby dowiedzieć się więcej o aktualnych informacjach.

  Jeśli usługa jest niedostępna w Twoim regionie, można utworzyć serwer systemu plików NFS, za pomocą którego można udostępnić system plików aplikacji SAP BOBI. Należy również wziąć pod uwagę jego wysoką dostępność.

![Układ magazynu oprogramowania SAP BusinessObjects BI platform na platformie Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Sieć

SAP BOBI to platforma raportowania i analizy analizy biznesowej, która nie przechowuje żadnych danych biznesowych. Dlatego system jest połączony z innymi serwerami baz danych z lokalizacji, w którym pobiera wszystkie dane, i zapewnia wgląd użytkownikom. Platforma Azure udostępnia infrastrukturę sieciową, która umożliwia mapowanie wszystkich scenariuszy, które mogą być realizowane przy użyciu platformy SAP BI, takich jak łączenie się z systemem lokalnym, systemami w różnych sieciach wirtualnych i innych. Aby uzyskać więcej informacji, sprawdź [Microsoft Azure sieci dla obciążeń SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

W przypadku oferty typu "baza danych jako usługa" nowo utworzona baza danych (Azure SQL Database lub Azure Database for MySQL) ma zaporę, która blokuje wszystkie połączenia zewnętrzne. Aby zezwolić na dostęp do usługi DBaaS z maszyn wirtualnych w usłudze BI platform, należy określić co najmniej jedną regułę zapory na poziomie serwera, aby umożliwić dostęp do serwera DBaaS. Aby uzyskać więcej informacji, zobacz sekcję [reguły zapory](../../../mysql/concepts-firewall-rules.md) dla Azure Database for MySQL i [kontroli dostępu do sieci](../../../azure-sql/database/network-access-controls-overview.md) dla usługi Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie oprogramowania SAP BusinessObjects BI platform w systemie Linux](businessobjects-deployment-guide-linux.md)
- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP](planning-guide.md)
- [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP](deployment-guide.md)
- [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP](./dbms_guide_general.md)