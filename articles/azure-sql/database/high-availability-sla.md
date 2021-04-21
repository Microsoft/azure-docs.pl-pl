---
title: Wysoka dostępność
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Dowiedz się więcej na temat Azure SQL Database i SQL Managed Instance wysokiej dostępności usługi
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
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781743"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Wysoka dostępność dla Azure SQL Database i SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Celem architektury wysokiej dostępności w usługach Azure SQL Database i SQL Managed Instance jest zagwarantowanie, że baza danych jest uruchomiona przez co najmniej 99,99% czasu (aby uzyskać więcej informacji na temat określonej umowy SLA dla różnych warstw, zapoznaj się z umowami SLA dla usług Azure SQL Database i [SQL Managed Instance)](https://azure.microsoft.com/support/legal/sla/sql-database/)bez martwienia się o wpływ operacji konserwacji i przerw w pracy. Platforma Azure automatycznie obsługuje krytyczne zadania obsługi, takie jak stosowanie poprawek, kopie zapasowe, uaktualnienia systemu Windows i programu Azure SQL, a także nieplanowane zdarzenia, takie jak podstawowy sprzęt, oprogramowanie lub awarie sieci.  Gdy bazowa baza danych w Azure SQL Database zostanie poprawiona lub przestój zostanie poprawiony, przestój nie jest zauważalny w przypadku korzystania z logiki ponawiania [prób](develop-overview.md#resiliency) w aplikacji. SQL Database i SQL Managed Instance można szybko odzyskać nawet w najbardziej krytycznych okolicznościach, zapewniając, że dane są zawsze dostępne.

Rozwiązanie o wysokiej dostępności ma na celu zapewnienie, że zatwierdzone dane nigdy nie zostaną utracone z powodu awarii, że operacje konserwacji nie wpływają na obciążenie i że baza danych nie będzie single point of failure w architekturze oprogramowania. Nie ma żadnych okien obsługi ani przestojów, które powinny wymagać zatrzymania obciążenia podczas uaktualnienia lub konserwacji bazy danych.

Istnieją dwa modele architektury wysokiej dostępności:

- **Standardowy model dostępności** oparty na rozdzieleniu zasobów obliczeniowych i magazynu.  Opiera się on na wysokiej dostępności i niezawodności warstwy magazynowania zdalnego. Ta architektura jest ukierunkowana na aplikacje biznesowe ukierunkowane na budżet, które mogą tolerować pewne pogorszenie wydajności podczas działań konserwacyjnych.
- **Model dostępności Premium** oparty na klastrze procesów aparatu bazy danych. Opiera się on na tym, że zawsze istnieje kworum dostępnych węzłów aparatu bazy danych. Ta architektura jest przeznaczony dla aplikacji o kluczowym znaczeniu z wysoką wydajnością operacji we/wy, wysoką szybkością transakcji i gwarancją minimalnego wpływu na wydajność obciążenia podczas działań konserwacyjnych.

SQL Database i SQL Managed Instance działają w najnowszej stabilnej wersji aparatu bazy danych programu SQL Server i systemu operacyjnego Windows, a większość użytkowników nie zauważy, że uaktualnienia są wykonywane w sposób ciągły.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Dostępność lokalnie nadmiarowa warstwy Ogólnego przeznaczenia podstawowa, standardowa i Ogólnego przeznaczenia

Warstwy usług Podstawowa, Standardowa i Ogólnego przeznaczenia wykorzystują standardową architekturę dostępności zarówno dla zasobów obliczeniowych bez serwera, jak i aprowowanych. Na poniższej ilustracji przedstawiono cztery różne węzły z oddzielnymi warstwami obliczeniowymi i warstwami magazynu.

![Rozdzielenie zasobów obliczeniowych i magazynu](./media/high-availability-sla/general-purpose-service-tier.png)

Standardowy model dostępności zawiera dwie warstwy:

- Bez stanowa warstwa obliczeniowa, która uruchamia proces i zawiera tylko dane przejściowe i buforowane, takie jak tempDB, modelowe bazy danych na dołączonym dysku SSD oraz pamięć podręczna planu, pula buforów i pula magazynu kolumn w `sqlservr.exe` pamięci. Ten węzeł bez stanowy jest obsługiwany przez usługę Azure Service Fabric, która inicjuje , kontroluje kondycję węzła i w razie potrzeby wykonuje pracę w trybu `sqlservr.exe` failover w innym węźle.
- Stanowa warstwa danych z plikami bazy danych (mdf/.ldf), które są przechowywane w usłudze Azure Blob Storage. Usługa Azure Blob Storage ma wbudowaną funkcję dostępności i nadmiarowości danych. Gwarantuje to, że każdy rekord w pliku dziennika lub na stronie w pliku danych zostanie zachowany, nawet jeśli `sqlservr.exe` proces ulega awarii.

Za każdym razem, gdy aparat bazy danych lub system operacyjny zostanie uaktualniony lub zostanie wykryty błąd, usługa Azure Service Fabric przeniesie proces bez stanowy do innego bez stanowego węzła obliczeniowego o wystarczającej `sqlservr.exe` wolnej pojemności. Przeniesienie nie wpływa na dane w usłudze Azure Blob Storage, a pliki danych/dziennika są dołączane do nowo zainicjowanego `sqlservr.exe` procesu. Ten proces gwarantuje dostępność na poziomie 99,99%, ale duże obciążenie może doświadczyć spadku wydajności podczas przejścia, ponieważ nowy proces rozpoczyna się od `sqlservr.exe` zimnej pamięci podręcznej.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Ogólnego przeznaczenia usługi strefowo nadmiarowej (wersja zapoznawcza)

Konfiguracja strefowo nadmiarowa dla warstwy usługi ogólnego przeznaczenia jest oferowana zarówno dla zasobów obliczeniowych bez serwera, jak i aprowowanych. Ta konfiguracja korzysta z [Strefy dostępności platformy Azure](../../availability-zones/az-overview.md) do replikowania baz   danych w wielu lokalizacjach fizycznych w regionie świadczenia usługi Azure.Wybierając nadmiarowość strefową, można sprawić, że nowe i istniejące bez serwera oraz aprowowane pojedyncze bazy danych i elastyczne pule ogólnego przeznaczenia będą odporne na znacznie większy zestaw awarii, w tym katastrofalne awarie centrum danych, bez żadnych zmian logiki aplikacji.

Konfiguracja strefowo nadmiarowa dla warstwy ogólnego przeznaczenia ma dwie warstwy:  

- Stanowa warstwa danych z plikami bazy danych (mdf/.ldf) przechowywanymi w magazynie ZRS (magazyn strefowo nadmiarowy). Przy [użyciu magazynu ZRS](../../storage/common/storage-redundancy.md) pliki danych i dziennika są synchronicznie kopiowane do trzech fizycznie odizolowanych stref dostępności platformy Azure.
- Bez stateless compute layer that runs the sqlservr.exe process and contains only transient and cached data, such as TempDB, model databases on the attached SSD, and plan cache, buffer pool, and columnstore pool in memory. Ten węzeł bez stanowy jest obsługiwany przez usługę Azure Service Fabric, która inicjuje sqlservr.exe, kontroluje kondycję węzła i w razie potrzeby wykonuje pracę w Service Fabric w innym węźle. W przypadku strefowo nadmiarowych bez serwera i aprowowanych baz danych ogólnego przeznaczenia węzły z zapasową pojemnością są łatwo dostępne w innych Strefy dostępności do trybu failover.

Strefowo nadmiarowa wersja architektury wysokiej dostępności dla warstwy usługi ogólnego przeznaczenia jest zilustrowana na poniższym diagramie:

![Konfiguracja strefowo nadmiarowa ogólnego przeznaczenia](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Konfiguracja strefowo nadmiarowa jest dostępna tylko po wybraniu sprzętu obliczeniowego Gen5. Ta funkcja nie jest dostępna w SQL Managed Instance. Konfiguracja strefowo nadmiarowa dla warstwy ogólnego przeznaczenia bez serwera i aprowizowana jest dostępna tylko w następujących regionach: Wschodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, Australia Wschodnia, Japonia Wschodnia, Południowe Zjednoczone Królestwo i Francja Środkowa.

> [!NOTE]
> Ogólnego przeznaczenia baz danych o rozmiarze 80 rdzeni wirtualnych mogą doświadczać spadku wydajności w przypadku konfiguracji strefowo nadmiarowej. Ponadto operacje, takie jak tworzenie kopii zapasowej, przywracanie, kopiowanie bazy danych, konfigurowanie relacji geograficznego drowania i dół bazy danych strefowo nadmiarowej z programu Krytyczne dla działania firmy do Ogólnego przeznaczenia, mogą mieć mniejszą wydajność w przypadku pojedynczych baz danych większych niż 1 TB. Aby uzyskać więcej [informacji, zapoznaj](single-database-scale.md) się z naszą dokumentacją dotyczącą opóźnień w skalowaniu bazy danych.
> 
> [!NOTE]
> Wersja zapoznawcza nie jest objęta wystąpieniem zarezerwowanym

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Dostępność lokalnie nadmiarowa w warstwie Krytyczne dla działania firmy Premium i Krytyczne dla działania firmy usługi

Warstwy usług Premium i Krytyczne dla działania firmy wykorzystują model dostępności Premium, który integruje zasoby obliczeniowe (proces) i magazyn (lokalnie dołączony dysk SSD) w `sqlservr.exe` jednym węźle. Wysoka dostępność jest osiągana przez replikowanie zasobów obliczeniowych i magazynu do dodatkowych węzłów tworzących klaster z trzema lub czterema węzłami.

![Klaster węzłów aparatu bazy danych](./media/high-availability-sla/business-critical-service-tier.png)

Pliki bazowych baz danych (mdf/.ldf) są umieszczane w dołączonym magazynie SSD, aby zapewnić bardzo małe opóźnienia operacji we/wy obciążenia. Wysoka dostępność jest implementowane przy użyciu technologii podobnej do SQL Server [zawsze wł. grup dostępności.](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Klaster zawiera jedną replikę podstawową, która jest dostępna dla obciążeń klientów do odczytu i zapisu, oraz maksymalnie trzy repliki pomocnicze (obliczenia i magazyn) zawierające kopie danych. Węzeł podstawowy stale wypycha zmiany do węzłów pomocniczych w kolejności i zapewnia synchronizację danych z co najmniej jedną repliką pomocniczą przed zatwierdzeniem każdej transakcji. Ten proces gwarantuje, że jeśli węzeł podstawowy z jakiegoś powodu ulega awarii, zawsze istnieje w pełni zsynchronizowany węzeł do pracy awaryjnej. Trybu failover jest inicjowany przez usługę Azure Service Fabric. Gdy replika pomocnicza stanie się nowym węzłem podstawowym, tworzona jest inna replika pomocnicza, aby upewnić się, że klaster ma wystarczającą ilość węzłów (zestaw kworum). Po zakończeniu pracy w Azure SQL zostaną automatycznie przekierowane do nowego węzła podstawowego.

Dodatkowo model dostępności Premium oferuje możliwość przekierowywania połączeń tylko do odczytu Azure SQL do jednej z replik pomocniczych. Ta funkcja nosi nazwę [skalowania odczytu w zewnątrz.](read-scale-out.md) Zapewnia 100% dodatkową pojemność obliczeniową bez dodatkowych opłat za operacje tylko do odczytu od obciążenia, takie jak obciążenia analityczne, z repliki podstawowej.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Dostępność strefowo nadmiarowa w warstwie Krytyczne dla działania firmy Premium i Krytyczne dla działania firmy usługi 

Domyślnie klaster węzłów dla modelu dostępności Premium jest tworzony w tym samym centrum danych. Wraz z wprowadzeniem [Strefy dostępności platformy Azure](../../availability-zones/az-overview.md)program SQL Database umieszczać różne repliki bazy danych Krytyczne dla działania firmy różnych strefach dostępności w tym samym regionie. Aby wyeliminować single point of failure, pierścień sterowania jest również duplikowany w wielu strefach jako trzy pierścienie bramy. Routing do określonego pierścienia bramy [](../../traffic-manager/traffic-manager-overview.md) jest kontrolowany przez Azure Traffic Manager (ATM). Ponieważ konfiguracja strefowo nadmiarowa w warstwach premium lub Krytyczne dla działania firmy nie tworzy dodatkowej nadmiarowości bazy danych, można ją włączyć bez dodatkowych kosztów. Wybierając konfigurację strefowo nadmiarową, można ustawić bazy danych premium lub Krytyczne dla działania firmy odporne na znacznie większy zestaw awarii, w tym katastrofalne awarie centrum danych, bez żadnych zmian w logice aplikacji. Możesz również przekonwertować wszystkie istniejące bazy danych lub pule w Krytyczne dla działania firmy Premium na konfigurację strefowo nadmiarową.

Ze względu na to, że strefowo nadmiarowe bazy danych mają repliki w różnych centrach danych z pewnymi odległościami między nimi, zwiększone opóźnienie sieci może zwiększyć czas zatwierdzania, a tym samym wpłynąć na wydajność niektórych obciążeń OLTP. Zawsze możesz wrócić do konfiguracji z jedną strefą, wyłączając ustawienie nadmiarowości strefy. Ten proces jest operacją online podobną do regularnego uaktualniania warstwy usługi. Po zakończeniu procesu baza danych lub pula są migrowane z pierścienia strefowo nadmiarowego do pierścienia pojedynczej strefy lub odwrotnie.

> [!IMPORTANT]
> W przypadku korzystania z warstwy Krytyczne dla działania firmy, konfiguracja strefowo nadmiarowa jest dostępna tylko w przypadku wyboru sprzętu obliczeniowego gen5. Aby uzyskać aktualne informacje o regionach, które obsługują strefowo nadmiarowe bazy danych, zobacz [Obsługa usług według regionów.](../../availability-zones/az-region.md)

> [!NOTE]
> Ta funkcja nie jest dostępna w SQL Managed Instance.

Strefowo nadmiarowa wersja architektury wysokiej dostępności jest zilustrowana na poniższym diagramie:

![architektura wysokiej dostępności strefowo nadmiarowa](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Dostępność warstwy usługi Hiperskala

Architektura warstwy usługi Hiperskala jest opisana w tesłudze Architektura funkcji [rozproszonych](./service-tier-hyperscale.md#distributed-functions-architecture) i jest obecnie dostępna tylko dla SQL Database, a nie SQL Managed Instance.

![Architektura funkcjonalna hiperskali](./media/high-availability-sla/hyperscale-architecture.png)

Model dostępności w warstwie Hiperskala obejmuje cztery warstwy:

- Bez stateless compute layer that runs the processes and contains only transient and cached data, such such `sqlservr.exe` non-covering RBPEX cache, TempDB, model database, etc. on the attached SSD, and plan cache, buffer pool, and columnstore pool in memory. Ta warstwa bez stanowa zawiera podstawową replikę obliczeniową i opcjonalnie kilka pomocniczych replik obliczeniowych, które mogą służyć jako obiekty docelowe trybu failover.
- Warstwa magazynu bez stanowego, która jest tworzymy na serwerach stronicowych. Ta warstwa to aparat magazynu rozproszonego dla procesów `sqlservr.exe` uruchomionych w replikach obliczeniowych. Każdy serwer stron zawiera tylko dane przejściowe i buforowane, takie jak pokrycie pamięci podręcznej RBPEX na dołączonym dysku SSD i stron danych buforowanych w pamięci. Każdy serwer stron ma sparowany serwer stron w konfiguracji aktywne-aktywne, aby zapewnić równoważenie obciążenia, nadmiarowość i wysoką dostępność.
- Warstwa magazynu dziennika transakcji stanowych, która jest postać węzła obliczeniowego, w którym działa proces usługi Dziennika, strefa docelowa dziennika transakcji i magazyn długoterminowy dziennika transakcji. Strefa docelowa i magazyn długoterminowy korzystają z [](../../storage/common/storage-redundancy.md) usługi Azure Storage, która zapewnia dostępność i nadmiarowość dziennika transakcji, zapewniając trwałość danych dla zatwierdzonej transakcji.
- Warstwa magazynu danych stanowych z plikami bazy danych (mdf/.ndf), które są przechowywane w usłudze Azure Storage i aktualizowane przez serwery stronicowanie. Ta warstwa korzysta z funkcji dostępności [i nadmiarowości danych](../../storage/common/storage-redundancy.md) usługi Azure Storage. Gwarantuje to, że każda strona w pliku danych zostanie zachowana nawet w przypadku awarii procesów w innych warstwach architektury hiperskali lub awarii węzłów obliczeniowych.

Węzły obliczeniowe we wszystkich warstwach hiperskali działają w usłudze Azure Service Fabric, która kontroluje kondycję każdego węzła i w razie potrzeby wykonuje tryb failover w dostępnych węzłach w dobrej kondycji.

Aby uzyskać więcej informacji na temat wysokiej dostępności w hiperskali, zobacz Database High Availability in Hiperscale (Wysoka [dostępność bazy danych w hiperskali).](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>przyspieszone odzyskiwanie bazy danych (ADR)

[przyspieszone odzyskiwanie bazy danych (ADR)](../accelerated-database-recovery.md) to nowa funkcja aparatu bazy danych, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji. Reguły ADR są obecnie dostępne Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testowanie odporności aplikacji na błędy

Wysoka dostępność to podstawowa część usługi SQL Database i platformy SQL Managed Instance, która działa w sposób przejrzysty dla aplikacji bazy danych. Jednak wiemy, że można sprawdzić, jak automatyczne operacje trybu failover inicjowane podczas planowanych lub nieplanowanych zdarzeń wpływają na aplikację przed wdrożeniem jej w środowisku produkcyjnym. Możesz ręcznie wyzwolić trybu failover, wywołując specjalny interfejs API w celu ponownego uruchomienia bazy danych, elastycznej puli lub wystąpienia zarządzanego. W przypadku strefowo nadmiarowej bazy danych lub bazy danych Ogólnego przeznaczenia lub elastycznej puli strefowo nadmiarowej wywołanie interfejsu API spowoduje przekierowywanie połączeń klientów do nowej podstawowej bazy danych w strefie dostępności innej niż strefa dostępności starej puli podstawowej. Dlatego oprócz testowania wpływu trybu failover na istniejące sesje bazy danych można również sprawdzić, czy zmienia on wydajność na wszystkich urządzeniach z powodu zmian opóźnienia sieci. Ponieważ operacja ponownego uruchamiania jest uciążliwa i duża liczba z nich może być obciążeniem platformy, co 15 minut dozwolone jest tylko jedno wywołanie trybu failover dla każdej bazy danych, elastycznej puli lub wystąpienia zarządzanego.

Trybu failover można zainicjować przy użyciu programu PowerShell, interfejsu API REST lub interfejsu wiersza polecenia platformy Azure:

|Typ wdrożenia|PowerShell|Interfejs API REST| Interfejs wiersza polecenia platformy Azure|
|:---|:---|:---|:---|
|baza danych|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Trybu failover bazy danych](/rest/api/sql/databases/failover)|[Az rest może](/cli/azure/reference-index#az_rest) służyć do wywoływania wywołania interfejsu API REST z interfejsu wiersza polecenia platformy Azure|
|Pula elastyczna|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Trybu failover puli elastycznej](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[Az rest może](/cli/azure/reference-index#az_rest) służyć do wywoływania wywołania interfejsu API REST z interfejsu wiersza polecenia platformy Azure|
|Wystąpienie zarządzane|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Wystąpienia zarządzane — failover](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> Polecenie Trybu failover nie jest dostępne dla replik pomocniczych dostępnych do odczytu baz danych w chmurze w chmurze.

## <a name="conclusion"></a>Podsumowanie

Azure SQL Database i Azure SQL Managed Instance oferują wbudowane rozwiązanie wysokiej dostępności, które jest głęboko zintegrowane z platformą Azure. Jest ona zależna od usługi Service Fabric wykrywania awarii i odzyskiwania danych, od usługi Azure Blob Storage w celu ochrony danych oraz od usługi Strefy dostępności w celu większej odporności na uszkodzenia (jak wspomniano wcześniej w dokumencie, który nie ma jeszcze zastosowania do usługi Azure SQL Managed Instance). Ponadto SQL Database i SQL Managed Instance technologii zawsze wł. grupy dostępności z wystąpienia SQL Server replikacji i trybu failover. Połączenie tych technologii umożliwia aplikacjom pełne korzystanie z zalet modelu magazynu mieszanego i obsługę najbardziej wymagających sla sla.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [więcej o Strefy dostępności platformy Azure](../../availability-zones/az-overview.md)
- Dowiedz się [więcej o Service Fabric](../../service-fabric/service-fabric-overview.md)
- Dowiedz się [więcej o Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Dowiedz [się, jak zainicjować ręczne tryb failover na SQL Managed Instance](../managed-instance/user-initiated-failover.md)
- Aby uzyskać więcej opcji wysokiej dostępności i odzyskiwania po awarii, [zobacz Business Continuity (Ciągłość działania)](business-continuity-high-availability-disaster-recover-hadr-overview.md)
