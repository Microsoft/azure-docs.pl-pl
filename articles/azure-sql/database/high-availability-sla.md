---
title: Wysoka dostępność
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Dowiedz się więcej o możliwościach i funkcjach wysokiej dostępności usługi wystąpienia zarządzanego Azure SQL Database przez program SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 1c210eab0332d01fc6514edc790d729172ed2174
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889063"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Wysoka dostępność dla Azure SQL Database i wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Celem architektury wysokiej dostępności w Azure SQL Database i wystąpieniu zarządzanym SQL jest zagwarantowanie, że baza danych jest uruchomiona co najmniej 99,99% czasu (Aby uzyskać więcej informacji dotyczących określonej umowy SLA dla różnych warstw, zapoznaj się z umową [SLA dla Azure SQL Database i wystąpienia zarządzanego SQL](https://azure.microsoft.com/support/legal/sla/sql-database/)), bez zakłócania wpływu operacji konserwacji i przestojów. Platforma Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak stosowanie poprawek, kopii zapasowych, uaktualnienia systemu Windows i platformy Azure SQL, a także niezaplanowanych zdarzeń, takich jak sprzętowe, programowe lub awarie sieci.  Gdy bazowa baza danych w Azure SQL Database jest zastosowana lub przejdzie do trybu failover, przestoje nie będzie zauważalne w przypadku zastosowania [logiki ponawiania prób](develop-overview.md#resiliency) w aplikacji. Wystąpienia zarządzane SQL Database i SQL mogą szybko odzyskać nawet w najbardziej krytycznych okolicznościach, dzięki czemu dane są zawsze dostępne.

Rozwiązanie wysokiej dostępności zostało zaprojektowane z myślą o zapewnieniu, że przekazane dane nigdy nie zostaną utracone z powodu niepowodzeń, że operacje konserwacji nie wpłyną na obciążenie, a baza danych nie będzie single point of failure w architekturze oprogramowania. Nie ma okien obsługi ani przestojów, które powinny wymagać zatrzymania obciążenia, gdy baza danych jest uaktualniana lub utrzymywana.

Istnieją dwa modele architektoniczne wysokiej dostępności:

- **Standardowy model dostępności** oparty na rozdzieleniu zasobów obliczeniowych i magazynu.  Zależy to od wysokiej dostępności i niezawodności zdalnej warstwy magazynowania. Ta architektura ukierunkowana na aplikacje biznesowe zorientowane na budżet, które mogą tolerować spadek wydajności podczas aktywności konserwacyjnej.
- **Model dostępności Premium** oparty na klastrze procesów aparatu bazy danych. Opiera się to na faktach, że zawsze jest kworum dostępnych węzłów aparatu bazy danych. Ta architektura ukierunkowana na aplikacje o znaczeniu strategicznym o wysokiej wydajności operacji we/wy, wysoka liczba transakcji i gwarantuje minimalny wpływ na wydajność podczas aktywności konserwacyjnej.

SQL Database i wystąpienie zarządzane SQL są uruchamiane w najnowszej stabilnej wersji aparatu bazy danych SQL Server i systemu operacyjnego Windows, a większość użytkowników nie zauważy, że uaktualnienia są wykonywane w sposób ciągły.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Warstwa usługi podstawowa, standardowa i Ogólnego przeznaczenia lokalnie nadmiarowa dostępność

Warstwy usługi Basic, standard i Ogólnego przeznaczenia korzystają ze standardowej architektury dostępności zarówno dla obliczeń bezserwerowych, jak i zasobów. Poniższy rysunek przedstawia cztery różne węzły z oddzielnymi warstwami obliczeniowymi i magazynowymi.

![Rozdzielenie zasobów obliczeniowych i magazynu](./media/high-availability-sla/general-purpose-service-tier.png)

Standardowy model dostępności obejmuje dwie warstwy:

- Warstwa obliczeń bezstanowych, która uruchamia `sqlservr.exe` proces i zawiera tylko dane przejściowe i buforowane, takie jak tempdb, modelować bazy danych na podłączonym dysku SSD, oraz pamięć podręczną planu, pulę buforów i pulę magazynu kolumn w pamięci. Ten bezstanowy węzeł jest obsługiwany przez usługę Azure Service Fabric, która inicjuje `sqlservr.exe` , steruje kondycją węzła i przeprowadza przejście w tryb failover do innego węzła w razie potrzeby.
- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage ma wbudowaną funkcję dostępności i nadmiarowości danych. Gwarantuje, że każdy rekord w pliku dziennika lub stronie w pliku danych zostanie zachowany nawet w przypadku `sqlservr.exe` awarii procesu.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryta awaria, usługa Azure Service Fabric przeniesie proces bezstanowy `sqlservr.exe` do innego bezstanowego węzła obliczeniowego z wystarczającą ilością wolnego miejsca. Przeniesienie nie ma wpływ na dane w usłudze Azure Blob Storage, a pliki danych/dziennika są dołączone do nowo zainicjowanego `sqlservr.exe` procesu. Ten proces gwarantuje dostępność na 99,99%, ale duże obciążenie może spowodować spadek wydajności podczas przejścia, ponieważ nowy proces rozpoczyna się od `sqlservr.exe` zimnej pamięci podręcznej.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Ogólnego przeznaczenia nadmiarowa dostępność strefy warstwy usług (wersja zapoznawcza)

Konfiguracja nadmiarowa strefy dla warstwy usług ogólnego przeznaczenia wykorzystuje [strefy dostępności platformy Azure](../../availability-zones/az-overview.md)   do replikowania baz danych w wielu lokalizacjach fizycznych w regionie świadczenia usługi Azure.Wybranie nadmiarowości strefy pozwala utworzyć nowe i istniejące pojedyncze bazy danych ogólnego przeznaczenia, które są odporne na znacznie większy zestaw błędów, w tym katastrofalne przestoje w centrum, bez wprowadzania żadnych zmian w logice aplikacji.

Konfiguracja nadmiarowa strefy dla warstwy ogólnego zastosowania ma dwie warstwy:  

- Warstwa danych stanowych z plikami bazy danych (. mdf/. ldf), które są przechowywane w ZRS PFS ( [udział plików magazynu](../../storage/files/storage-how-to-create-file-share.md)Strefowo nadmiarowego w warstwie Premium. Korzystanie z [magazynu Strefowo nadmiarowego](../../storage/common/storage-redundancy.md) pliki danych i dziennika są synchronicznie kopiowane w ramach trzech fizycznie odizolowanych stref dostępności platformy Azure.
- Warstwa obliczeń bezstanowych, która uruchamia proces sqlservr.exe i zawiera tylko dane przejściowe i buforowane, takie jak TempDB, modeluje bazy danych na dołączonym dysku SSD, oraz pamięć podręczną planu, pulę buforów i pulę magazynu kolumn w pamięci. Ten bezstanowy węzeł jest obsługiwany przez usługę Azure Service Fabric, która inicjuje sqlservr.exe, steruje kondycją węzła i wykonuje przejście w tryb failover do innego węzła w razie potrzeby. W przypadku strefowo nadmiarowych baz danych ogólnego przeznaczenia węzły o pojemności zapasowej są łatwo dostępne w innych Strefy dostępności do pracy w trybie failover.

Strefa z nadmiarową wersją architektury wysokiej dostępności dla warstwy usług ogólnego przeznaczenia przedstawiono na poniższym diagramie:

![Konfiguracja nadmiarowa strefy dla ogólnego przeznaczenia](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Konfiguracja nadmiarowa strefy jest dostępna tylko wtedy, gdy wybrano sprzęt obliczeniowy 5 rdzeń. Ta funkcja jest niedostępna w wystąpieniu zarządzanym SQL. Konfiguracja nadmiarowa strefy dla warstwy ogólnego przeznaczenia jest dostępna tylko w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, Australia Wschodnia, Japonia Wschodnia, Południowe Zjednoczone Królestwo i Francja środkowa.

> [!NOTE]
> Ogólnego przeznaczenia bazy danych o rozmiarze 80 rdzeń wirtualny mogą powodować spadek wydajności dzięki konfiguracji nadmiarowej strefy. Ponadto operacje, takie jak tworzenie kopii zapasowej, przywracanie, Kopiowanie bazy danych i Konfigurowanie relacji Geo-DR mogą mieć mniejszą wydajność dla wszystkich baz danych większych niż 1 TB. 
> 
> [!NOTE]
> Wersja zapoznawcza nie jest objęta wystąpieniem zarezerwowanym

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Warstwa usług premium i Krytyczne dla działania firmy lokalnie nadmiarowa dostępność

Warstwy usług premium i Krytyczne dla działania firmy wykorzystują model dostępności Premium, który integruje zasoby obliczeniowe ( `sqlservr.exe` procesy) i magazyn (lokalnie dołączony dysk SSD) w jednym węźle. Wysoka dostępność jest osiągana przez replikowanie zarówno zasobów obliczeniowych, jak i magazynu do dodatkowych węzłów tworzących klaster z trzema czterema węzłami.

![Klaster węzłów aparatu bazy danych](./media/high-availability-sla/business-critical-service-tier.png)

Bazowe pliki bazy danych (. mdf/. ldf) są umieszczane w podłączonym magazynie SSD w celu zapewnienia bardzo małych opóźnień we/wy dla obciążenia. Wysoka dostępność jest implementowana przy użyciu technologii podobnego do SQL Server [zawsze włączonymi grupami dostępności](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klaster zawiera jedną replikę podstawową, która jest dostępna do obsługi obciążeń klientów odczytu i zapisu, oraz do trzech replik pomocniczych (obliczeniowych i magazynowych) zawierających kopie danych. Węzeł podstawowy ciągle przekazuje zmiany do węzłów pomocniczych w kolejności i gwarantuje, że dane są synchronizowane z co najmniej jedną repliką pomocniczą przed zatwierdzeniem każdej transakcji. Ten proces gwarantuje, że jeśli węzeł podstawowy ulegnie awarii z jakiegokolwiek powodu, zawsze jest w pełni zsynchronizowany węzeł w celu przełączenia w tryb failover. Przełączenie w tryb failover jest inicjowane przez Service Fabric platformy Azure. Gdy replika pomocnicza zostanie nowym węzłem podstawowym, zostanie utworzona inna replika pomocnicza, aby upewnić się, że klaster ma wystarczającą liczbę węzłów (zestaw kworum). Po zakończeniu pracy w trybie failover połączenia SQL platformy Azure są automatycznie przekierowywane do nowego węzła podstawowego.

W ramach dodatkowych korzyści model dostępności Premium obejmuje możliwość przekierowania połączeń usługi Azure SQL tylko do odczytu do jednej z replik pomocniczych. Ta funkcja jest nazywana [skalowaniem do odczytu](read-scale-out.md). Zapewnia 100% dodatkowej pojemności obliczeniowej bez dodatkowej opłaty za magazyn operacji tylko do odczytu, na przykład obciążenia analityczne, z repliki podstawowej.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Strefa usług w warstwie Premium i Krytyczne dla działania firmy nadmiarowa dostępność 

Domyślnie klaster węzłów dla modelu dostępności Premium jest tworzony w tym samym centrum danych. Wprowadzając [strefy dostępności platformy Azure](../../availability-zones/az-overview.md), SQL Database mogą umieścić różne repliki bazy danych krytyczne dla działania firmy w różnych strefach dostępności w tym samym regionie. Aby wyeliminować single point of failure, pierścień kontrolny jest również duplikowany w wielu strefach jako trzy pierścienie bramy (GW). Routing do określonego pierścienia bramy jest kontrolowany przez [usługę Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Ponieważ konfiguracja nadmiarowa strefy w warstwach usług premium lub Krytyczne dla działania firmy nie powoduje utworzenia dodatkowej nadmiarowości bazy danych, możesz ją włączyć bez dodatkowych kosztów. Wybierając strefowo nadmiarową konfigurację, można sprawić, aby bazy danych Premium lub Krytyczne dla działania firmy odporne na znacznie większy zestaw błędów, w tym katastrofalne przerwy w działaniu, bez wprowadzania żadnych zmian w logice aplikacji. Istnieje również możliwość przekonwertowania wszelkich istniejących baz danych lub pul w warstwie Premium lub Krytyczne dla działania firmy na strefę nadmiarową.

Ze względu na to, że nadmiarowe bazy danych strefy mają repliki w różnych centrach, z odległości między nimi, zwiększone opóźnienie sieci może wydłużyć czas zatwierdzania i w ten sposób mieć wpływ na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji pojedynczej strefy, wyłączając ustawienie nadmiarowości strefy. Ten proces jest operacją online podobną do zwykłego uaktualnienia warstwy usług. Na końcu procesu baza danych lub Pula jest migrowana ze strefy nadmiarowego pierścień do pojedynczego pierścienia strefy lub odwrotnie.

> [!IMPORTANT]
> W przypadku korzystania z warstwy Krytyczne dla działania firmy konfiguracja nadmiarowa strefy jest dostępna tylko po wybraniu sprzętu obliczeniowego 5 rdzeń. Aby uzyskać aktualne informacje o regionach, które obsługują nadmiarowe bazy danych strefy, zobacz temat [Obsługa usług według regionów](../../availability-zones/az-region.md).

> [!NOTE]
> Ta funkcja jest niedostępna w wystąpieniu zarządzanym SQL.

Strefa o wysokiej dostępności nadmiarowa jest zilustrowana na poniższym diagramie:

![Strefa architektury wysokiej dostępności nadmiarowa](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Dostępność warstwy usługi w ramach skalowania

Architektura warstwy usług w ramach skalowania jest opisana w [architekturze funkcji rozproszonych](./service-tier-hyperscale.md#distributed-functions-architecture) i jest obecnie dostępna tylko dla SQL Database, a nie wystąpienia zarządzanego SQL.

![Skalowalna architektura funkcjonalna](./media/high-availability-sla/hyperscale-architecture.png)

Model dostępności w obszarze skalowanie obejmuje cztery warstwy:

- Bezstanowa warstwa obliczeniowa, która uruchamia `sqlservr.exe` procesy i zawiera tylko dane przejściowe i buforowane, takie jak nieobejmujący pamięci podręcznej RBPEX, tempdb, Modeling Database itp. na podłączonym dysku SSD, i planowanie pamięci podręcznej, puli buforów i puli magazynu kolumn w pamięci. Ta warstwa bezstanowa obejmuje podstawową replikę obliczeniową i opcjonalnie wiele pomocniczych replik obliczeniowych, które mogą być używane jako obiekty docelowe trybu failover.
- Bezstanowa warstwa magazynowania utworzona przez serwery stronicowania. Ta warstwa jest aparatem magazynu rozproszonego dla `sqlservr.exe` procesów uruchomionych w replikach obliczeniowych. Każdy serwer stron zawiera tylko dane przejściowe i buforowane, takie jak m.in. pamięć podręczna RBPEX na podłączonym dysku SSD, a strony danych w pamięci podręcznej. Każdy serwer stron ma sparowany serwer stronicowania w konfiguracji aktywne-aktywne, aby zapewnić Równoważenie obciążenia, nadmiarowość i wysoką dostępność.
- Warstwa magazynu dziennika transakcji stanowych utworzona przez węzeł obliczeniowy, na którym działa proces usługi log, strefa przeładunku dziennika transakcji oraz magazyn długoterminowy dziennika transakcji. Strefa docelowa i długoterminowy magazyn używają usługi Azure Storage, która zapewnia dostępność i [nadmiarowość](../../storage/common/storage-redundancy.md) dla dziennika transakcji, zapewniając trwałość danych dla zatwierdzonych transakcji.
- Stanowa warstwa magazynowania danych z plikami bazy danych (. mdf/. NDF), które są przechowywane w usłudze Azure Storage i są aktualizowane przez serwery stronicowania. Ta warstwa używa funkcji dostępności i [nadmiarowości](../../storage/common/storage-redundancy.md) danych usługi Azure Storage. Gwarantuje to, że każda Strona w pliku danych zostanie zachowana, nawet jeśli procesy w innych warstwach uległy awarii architektury ze skalą lub w przypadku awarii węzłów obliczeniowych.

Węzły obliczeniowe we wszystkich warstwach skalowania są uruchamiane na platformie Azure Service Fabric, która kontroluje kondycję każdego węzła i wykonuje przejścia w tryb failover do dostępnych węzłów w dobrej kondycji.

Aby uzyskać więcej informacji na temat wysokiej dostępności w ramach skalowania, zobacz [wysoka dostępność bazy danych w ramach skalowania](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Szybsze odzyskiwanie bazy danych (ADR)

[Szybsze odzyskiwanie bazy danych (ADR)](../accelerated-database-recovery.md) to nowa funkcja aparatu bazy danych, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji. Usługa ADR jest obecnie dostępna dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testowanie odporności błędów aplikacji

Wysoka dostępność to podstawowa część usługi SQL Database i platformy SQL Managed Instance, która działa w sposób przejrzysty dla aplikacji bazy danych. Jednak wiemy, że można sprawdzić, jak automatyczne operacje trybu failover inicjowane podczas planowanych lub nieplanowanych zdarzeń wpływają na aplikację przed wdrożeniem jej w środowisku produkcyjnym. Możesz ręcznie wyzwolić tryb failover, wywołując specjalny interfejs API w celu ponownego uruchomienia bazy danych, puli elastycznej lub wystąpienia zarządzanego. W przypadku strefowo nadmiarowej bazy danych lub puli elastycznej wywołanie interfejsu API spowoduje przekierowanie połączeń klientów do nowego elementu podstawowego w strefie dostępności innej niż strefa dostępności starego elementu podstawowego. W związku z tym oprócz testowania pracy w trybie failover wpływa na istniejące sesje baz danych, można również sprawdzić, czy zmienia ona kompleksową wydajność ze względu na zmiany opóźnienia sieci. Ponieważ operacja ponownego uruchomienia jest niepożądana, a duża liczba z nich może nałożyć na platformę, tylko jedno wywołanie trybu failover jest dozwolone co 15 minut dla każdej bazy danych, elastycznej puli lub wystąpienia zarządzanego.

Przejście w tryb failover można zainicjować przy użyciu programu PowerShell, interfejsu API REST lub wiersza polecenia platformy Azure:

|Typ wdrożenia|PowerShell|Interfejs API REST| Interfejs wiersza polecenia platformy Azure|
|:---|:---|:---|:---|
|baza danych|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Tryb failover bazy danych](/rest/api/sql/databases/failover)|[AZ REST](/cli/azure/reference-index#az-rest) może służyć do wywoływania interfejsu API REST z interfejsu wiersza polecenia platformy Azure|
|Pula elastyczna|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Tryb failover puli elastycznej](/rest/api/sql/elasticpools/failover)|[AZ REST](/cli/azure/reference-index#az-rest) może służyć do wywoływania interfejsu API REST z interfejsu wiersza polecenia platformy Azure|
|Wystąpienie zarządzane|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Wystąpienia zarządzane — tryb failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[AZ SQL mi tryb failover](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> Polecenie przełączenia w tryb failover nie jest dostępne do odczytu pomocniczych replik baz danych.

## <a name="conclusion"></a>Podsumowanie

Azure SQL Database i funkcja wystąpienia zarządzanego usługi Azure SQL to wbudowane rozwiązanie wysokiej dostępności, które jest głęboko zintegrowane z platformą Azure. Jest ona zależna od Service Fabric w przypadku wykrywania awarii i odzyskiwania, w usłudze Azure Blob Storage na potrzeby ochrony danych, a na Strefy dostępności w celu uzyskania większej odporności na uszkodzenia (jak wspomniano wcześniej w dokumencie nie dotyczy jeszcze wystąpienia zarządzanego Azure SQL). Ponadto SQL Database i wystąpienie zarządzane SQL wykorzystują technologię zawsze włączone z wystąpienia SQL Server na potrzeby replikacji i przełączania do trybu failover. Połączenie tych technologii pozwala aplikacjom w pełni wykorzystać zalety modelu magazynu mieszanego i obsługiwać najbardziej wymaganą umowy SLA.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [strefy dostępności platformy Azure](../../availability-zones/az-overview.md)
- Dowiedz się więcej o [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Dowiedz się [, jak zainicjować ręczną pracę awaryjną w wystąpieniu zarządzanym SQL](../managed-instance/user-initiated-failover.md)
- Aby uzyskać więcej opcji dotyczących wysokiej dostępności i odzyskiwania po awarii, zobacz [ciągłość działania firmy](business-continuity-high-availability-disaster-recover-hadr-overview.md)
