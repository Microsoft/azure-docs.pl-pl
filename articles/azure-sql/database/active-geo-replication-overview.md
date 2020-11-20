---
title: Aktywna replikacja geograficzna
description: Użyj aktywnej replikacji geograficznej, aby utworzyć odczytane pomocnicze bazy danych poszczególnych baz danych w Azure SQL Database w tym samym lub różnych regionach centrum danych.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: c7a24dbe93bf0096e327804be07acc3f67d2f03b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985760"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Tworzenie i używanie aktywnej replikacji geograficznej — Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aktywna replikacja geograficzna to Azure SQL Database funkcja, która umożliwia tworzenie odczytanych pomocniczych baz danych poszczególnych baz danych na serwerze w tym samym lub innym centrum danych (region).

> [!NOTE]
> Aktywna replikacja geograficzna nie jest obsługiwana przez wystąpienie zarządzane usługi Azure SQL. W przypadku geograficznego trybu failover wystąpień wystąpienia zarządzanego SQL należy użyć [grup z obsługą trybu failover](auto-failover-group-overview.md).

Aktywna replikacja geograficzna została zaprojektowana jako rozwiązanie do ciągłej ciągłości biznesowej, które pozwala aplikacji na szybkie odzyskiwanie awaryjne poszczególnych baz danych w przypadku awarii regionalnej lub dużej skali. Jeśli włączono replikację geograficzną, aplikacja może zainicjować przejście w tryb failover do pomocniczej bazy danych w innym regionie świadczenia usługi Azure. Do czterech serwerów pomocniczych są obsługiwane w tym samym lub różnych regionach, a serwery pomocnicze mogą być również używane na potrzeby zapytań dostępu tylko do odczytu. Praca w trybie failover musi zostać zainicjowana ręcznie przez aplikację lub użytkownika. Po przejściu w tryb failover nowy element podstawowy ma inny punkt końcowy połączenia.

> [!NOTE]
> Aktywna replikacja geograficzna replikuje zmiany przez dziennik transakcji bazy danych przesyłania strumieniowego. Nie jest on związany z [replikacją transakcyjną](/sql/relational-databases/replication/transactional/transactional-replication), która replikuje zmiany przez wykonywanie poleceń DML (INSERT, Update i Delete).

Na poniższym diagramie przedstawiono typową konfigurację geograficznie nadmiarowej aplikacji w chmurze przy użyciu aktywnej replikacji geograficznej.

![Aktywna replikacja geograficzna](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL Database obsługuje również grupy autotrybu failover. Aby uzyskać więcej informacji, zobacz Korzystanie z [grup Autotryb failover](auto-failover-group-overview.md).

Jeśli z jakiegoś powodu nie powiedzie się podstawowa baza danych lub po prostu musi zostać przełączony w tryb offline, możesz zainicjować pracę w trybie failover w dowolnych pomocniczych bazach danych. Po aktywowaniu trybu failover do jednej z pomocniczych baz danych wszystkie inne serwery podrzędne są automatycznie łączone z nowym serwerem podstawowym.

Można zarządzać replikacją i trybem failover pojedynczej bazy danych lub zestawu baz danych na serwerze lub w puli elastycznej przy użyciu aktywnej replikacji geograficznej. Można to zrobić za pomocą polecenia:

- [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: pojedyncza baza danych](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: Pula elastyczna](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: pojedyncza baza danych lub Pula elastyczna](/sql/t-sql/statements/alter-database-azure-sql-database)
- [Interfejs API REST: pojedyncza baza danych](/rest/api/sql/replicationlinks)

Aktywna replikacja geograficzna wykorzystuje technologię [zawsze włączone](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) dla aparatu bazy danych, aby asynchronicznie replikować zatwierdzone transakcje z podstawowej bazy danych do pomocniczej bazy danych przy użyciu izolacji migawek. Grupy autotrybu failover zapewniają semantykę grupy na aktywnej replikacji geograficznej, ale jest używany ten sam mechanizm replikacji asynchronicznej. W dowolnym momencie pomocnicza baza danych może być nieco poza podstawową bazą danych, a dane pomocnicze nigdy nie mają transakcji częściowych. Nadmiarowość między regionami umożliwia aplikacjom szybkie odzyskiwanie po trwałej utracie całego centrum danych lub jego części z powodu klęsk żywiołowych, katastrofalnych błędów ludzkich lub złośliwych działań. Konkretne dane celu punktu odzyskiwania można znaleźć w temacie [Omówienie ciągłości działania firmy](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Jeśli wystąpi awaria sieci między dwoma regionami, ponawiamy próbę co 10 sekund, aby ponownie ustanowić połączenia.

> [!IMPORTANT]
> W celu zagwarantowania, że krytyczna zmiana podstawowej bazy danych jest replikowana na pomocniczą przed przełączeniem w tryb failover, można wymusić synchronizację, aby zapewnić replikację krytycznych zmian (na przykład aktualizacje haseł). Wymuszona synchronizacja ma wpływ na wydajność, ponieważ blokuje wątek wywołujący do momentu replikowania wszystkich zatwierdzonych transakcji. Aby uzyskać szczegółowe informacje, zobacz [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Aby monitorować opóźnienie replikacji między podstawową bazą danych i lokacją geograficzną, zobacz [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Na poniższej ilustracji przedstawiono przykład aktywnej replikacji geograficznej skonfigurowanej przy użyciu podstawowego w regionie Północno-środkowe stany USA i dodatkowe w regionie Południowo-środkowe stany USA.

![Relacja replikacji geograficznej](./media/active-geo-replication-overview/geo-replication-relationship.png)

Ponieważ pomocnicze bazy danych są odczytywane, mogą one służyć do odciążania obciążeń tylko do odczytu, takich jak zadania raportowania. W przypadku korzystania z aktywnej replikacji geograficznej można utworzyć pomocniczą bazę danych w tym samym regionie, w którym znajduje się podstawowa, ale nie zwiększa odporność aplikacji na błędy krytyczne. Jeśli używasz grup autotrybu failover, pomocnicza baza danych jest zawsze tworzona w innym regionie.

Oprócz odzyskiwania po awarii aktywnej replikacji geograficznej można użyć w następujących scenariuszach:

- **Migracja bazy danych**: można użyć aktywnej replikacji geograficznej w celu migrowania bazy danych z jednego serwera do drugiego w trybie online z minimalnym przestojem.
- **Uaktualnienia aplikacji**: można utworzyć dodatkową kopię zapasową jako nieudaną podczas uaktualniania aplikacji.

Aby osiągnąć prawdziwą ciągłość biznesową, Dodawanie nadmiarowości bazy danych między centrami elementów jest tylko częścią rozwiązania. Odzyskiwanie aplikacji (usługi) od końca do końca po katastrofalnym błędzie wymaga odzyskania wszystkich składników wchodzących w skład usługi i usług zależnych. Przykładowe składniki obejmują oprogramowanie klienta (na przykład przeglądarkę z niestandardowym językiem JavaScript), frontony sieci Web, magazyn i system DNS. Należy pamiętać, że wszystkie składniki są odporne na te same awarie i stają się dostępne w ramach celu czasu odzyskiwania (RTO) aplikacji. W związku z tym należy zidentyfikować wszystkie usługi zależne i zrozumieć gwarancje i funkcje, które zapewnia. Następnie należy wykonać odpowiednie czynności, aby upewnić się, że usługa działa w trybie failover usług, na których jest ona zależna. Aby uzyskać więcej informacji na temat projektowania rozwiązań na potrzeby odzyskiwania po awarii, zobacz [projektowanie rozwiązań w chmurze na potrzeby odzyskiwania po awarii przy użyciu aktywnej replikacji geograficznej](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Terminologia i możliwości związane z replikacją geograficzną

- **Automatyczna replikacja asynchroniczna**

  Pomocniczą bazę danych można utworzyć tylko przez dodanie do istniejącej bazy danych. Pomocniczą można utworzyć na dowolnym serwerze. Po utworzeniu pomocnicza baza danych zostanie wypełniona danymi skopiowanymi z podstawowej bazy danych. Ten proces jest nazywany umieszczaniem. Po utworzeniu pomocniczej bazy danych i umieszczeniu w niej aktualizacji podstawowa baza danych jest asynchronicznie replikowana do pomocniczej bazy danych. Replikacja asynchroniczna oznacza, że transakcje są zatwierdzane w podstawowej bazie danych, zanim zostaną zreplikowane do pomocniczej bazy danych.

- **Odczytane pomocnicze bazy danych**

  Aplikacja może uzyskać dostęp do pomocniczej bazy danych dla operacji tylko do odczytu przy użyciu tych samych lub różnych podmiotów zabezpieczeń używanych do uzyskiwania dostępu do podstawowej bazy danych. Pomocnicze bazy danych działają w trybie izolacji migawek, aby upewnić się, że replikacja aktualizacji podstawowego (dziennika powtarzania) nie zostanie opóźniona przez zapytania wykonane na pomocniczym.

> [!NOTE]
> Odtwarzanie dziennika jest opóźnione w pomocniczej bazie danych, jeśli istnieją aktualizacje schematu na serwerze podstawowym. Ten ostatni wymaga blokady schematu w pomocniczej bazie danych.

> [!IMPORTANT]
> Możesz użyć replikacji geograficznej, aby utworzyć pomocniczą bazę danych w tym samym regionie co podstawowy. Tej dodatkowej można użyć do równoważenia obciążenia obciążeń tylko do odczytu w tym samym regionie. Jednak pomocnicza baza danych w tym samym regionie nie zapewnia dodatkowej odporności na uszkodzenia i dlatego nie jest odpowiednim miejscem docelowym trybu failover na potrzeby odzyskiwania po awarii. Nie gwarantuje to również izolacji strefy dostępności. Użyj warstwy usługi krytycznej lub Premium z [konfiguracją nadmiarową stref](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) lub ogólnego przeznaczenia strefę usługi warstwy usług [nadmiarowa konfiguracja](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) , aby osiągnąć izolację strefy dostępności.
>

- **Planowana praca w trybie failover**

  Planowane przełączanie w tryb failover — role podstawowej i pomocniczej bazy danych po ukończeniu pełnej synchronizacji. Jest to operacja online, która nie powoduje utraty danych. Czas operacji zależy od rozmiaru dziennika transakcji na podstawowym serwerze, który należy synchronizować. Planowana praca w trybie failover jest zaprojektowana dla następujących scenariuszy: (a) do wykonywania operacji odzyskiwania po awarii w środowisku produkcyjnym, gdy utrata danych nie jest akceptowalna. (b) przeniesienie bazy danych do innego regionu; i (c) zwrócić bazę danych do regionu podstawowego po usunięciu awarii (powrót po awarii).

- **Nieplanowany tryb failover**

  Nieplanowane lub wymuszone przejście w tryb failover natychmiast przełącza pomocnicze do roli podstawowej bez synchronizacji z serwerem podstawowym. Wszystkie transakcje przekazane do podstawowego, ale nie zreplikowane do pomocniczej zostaną utracone. Ta operacja jest zaprojektowana jako metoda odzyskiwania w okresie przestoju, gdy podstawowa jest niedostępna, ale dostępność bazy danych musi być szybko przywrócona. Gdy pierwotny element podstawowy jest przywracany w trybie online, zostanie automatycznie ponownie nawiązane połączenie i stanie się nowym dodatkowym. Wszystkie niezsynchronizowane transakcje przed przełączeniem w tryb failover zostaną zachowane w pliku kopii zapasowej, ale nie zostaną zsynchronizowane z nowym podstawowym, aby uniknąć konfliktów. Te transakcje będą musiały zostać ręcznie scalone z najnowszą wersją podstawowej bazy danych.

- **Wiele elementów pomocniczych z możliwością odczytu**

  Dla każdego elementu podstawowego można utworzyć maksymalnie 4 pomocnicze bazy danych. Jeśli istnieje tylko jedna pomocnicza baza danych, a jej awaria nie powiedzie się, aplikacja zostanie narażona na większe ryzyko do momentu utworzenia nowej pomocniczej bazy danych. Jeśli istnieje wiele pomocniczych baz danych, aplikacja pozostaje chroniona, nawet jeśli jedna z pomocniczych baz danych ulegnie awarii. Dodatkowe pomocnicze serwery mogą być również używane do skalowania obciążeń tylko do odczytu.

  > [!NOTE]
  > Jeśli używasz aktywnej replikacji geograficznej w celu utworzenia aplikacji rozproszonej globalnie i musisz zapewnić dostęp tylko do odczytu do danych w więcej niż czterech regionach, możesz utworzyć pomocniczy pomocniczy (proces nazywany łańcuchem). W ten sposób można osiągnąć praktycznie nieograniczone skalowanie replikacji bazy danych. Ponadto łączenie łańcucha zmniejsza obciążenie podstawowej bazy danych. Ta wartość nie jest krótszym opóźnieniem replikacji w przypadku większości pomocniczych baz danych.

- **Replikacja geograficzna baz danych w puli elastycznej**

  Każda pomocnicza baza danych może uczestniczyć w puli elastycznej, a nie w żadnej puli elastycznej. Wybór puli dla każdej pomocniczej bazy danych jest oddzielny i nie zależy od konfiguracji żadnej innej pomocniczej bazy danych (podstawowej lub pomocniczej). Każda pula elastyczna jest zawarta w jednym regionie, w związku z czym wiele pomocniczych baz danych w tej samej topologii może nigdy nie współdzielić puli elastycznej.

- **Tryb failover kontrolowany przez użytkownika i powrót po awarii**

  Pomocnicza baza danych może zostać jawnie przełączona do roli głównej w dowolnym momencie przez aplikację lub użytkownika. W trakcie rzeczywistego przestoju należy użyć opcji "nieplanowane", która natychmiast promuje element pomocniczy jako podstawowy. Gdy odzyskanie pierwotne nie powiodło się i jest dostępne ponownie, system automatycznie oznaczy odzyskany podstawowy jako pomocniczy i przeniesie go na bieżąco z nowym serwerem podstawowym. Ze względu na asynchroniczną naturę replikacji, niewielka ilość danych może zostać utracona podczas nieplanowanych przełączeń w tryb failover, jeśli wystąpi awaria podstawowa przed replikowaniem najnowszych zmian do pomocniczej. W przypadku przełączenia podstawowego z wieloma wystąpieniami pomocniczymi system automatycznie ponownie konfiguruje relacje replikacji i łączy pozostałe pomocnicze serwery główne bez konieczności interwencji użytkownika. Po usunięciu awarii, która spowodowała przełączenia w tryb failover, może być wskazane zwrócenie aplikacji do regionu podstawowego. W tym celu należy wywołać polecenie trybu failover z opcją "planowana".

## <a name="preparing-secondary-database-for-failover"></a>Przygotowywanie pomocniczej bazy danych do pracy w trybie failover

Aby mieć pewność, że aplikacja będzie mogła natychmiast uzyskać dostęp do nowego elementu podstawowego po przejściu w tryb failover, upewnij się, że wymagania dotyczące uwierzytelniania dla serwera pomocniczego i bazy danych są prawidłowo skonfigurowane. Aby uzyskać szczegółowe informacje, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](active-geo-replication-security-configure.md). Aby zagwarantować zgodność po przejściu w tryb failover, należy się upewnić, że zasady przechowywania kopii zapasowych w pomocniczej bazie danych są zgodne z serwerem podstawowym. Te ustawienia nie są częścią bazy danych i nie są replikowane. Domyślnie dla elementu pomocniczego zostanie skonfigurowany domyślny okres przechowywania kopie wynoszący siedem dni. Aby uzyskać szczegółowe informacje, zobacz [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md).

> [!IMPORTANT]
> Jeśli baza danych jest członkiem grupy trybu failover, nie można zainicjować jej trybu failover za pomocą polecenia trybu failover replikacji geograficznej. Użyj polecenia trybu failover dla grupy. Jeśli musisz przełączyć się do trybu failover dla pojedynczej bazy danych, musisz najpierw usunąć ją z grupy trybu failover. Aby uzyskać szczegółowe informacje, zobacz  [grupy trybu failover](auto-failover-group-overview.md) .

## <a name="configuring-secondary-database"></a>Konfigurowanie pomocniczej bazy danych

Podstawowa i pomocnicza baza danych muszą mieć tę samą warstwę usług. Zdecydowanie zaleca się również utworzenie pomocniczej bazy danych z tą samą nadmiarowością magazynu kopii zapasowych i rozmiarem obliczeń (DTU lub rdzeni wirtualnych) jako podstawową. Jeśli podstawowa baza danych ma duże obciążenie zapisu, może nie być możliwe przeprowadzenie dodatkowej z mniejszym rozmiarem obliczeniowym. Spowoduje to opóźnienie opóźnienia na pomocniczej i potencjalną niedostępność pomocniczą. Aby wyeliminować te zagrożenia, aktywna replikacja geograficzna ogranicza stawkę dziennika transakcji głównej, jeśli jest to konieczne, aby umożliwić jej wychwycenie.

Inną konsekwencją niezrównoważonej konfiguracji dodatkowej jest to, że po przejściu w tryb failover wydajność aplikacji może ulec pogorszeniu z powodu niewystarczającej pojemności obliczeniowej nowego elementu podstawowego. W takim przypadku konieczne będzie skalowanie celu usługi bazy danych do niezbędnego poziomu, co może zająć dużo czasu i zasobów obliczeniowych, i będzie wymagało przejścia w tryb failover [o wysokiej dostępności](high-availability-sla.md) na końcu procesu skalowania w górę.

Jeśli zdecydujesz się na utworzenie pomocniczej o mniejszym rozmiarze obliczeń, wykres procentowy operacji we/wy dziennika w Azure Portal zapewnia dobry sposób oszacowania minimalnego rozmiaru obliczeniowego pomocniczego, który jest wymagany do utrzymania obciążenia replikacji. Na przykład jeśli podstawową bazą danych jest P6 (1000 jednostek DTU), a jej procent zapisu w dzienniku to 50%, pomocniczy musi mieć co najmniej P4 (500 jednostek DTU). Aby pobrać historyczne dane we/wy dziennika, użyj widoku [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) . Aby pobrać ostatnie dane zapisu dziennika z większą szczegółowością, która lepiej odzwierciedla krótkoterminowe wzrosty w współczynniku rejestrowania, użyj widoku [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) .

Ograniczanie szybkości dziennika transakcji na podstawowym ze względu na mniejszy rozmiar obliczeń na pomocniczym jest zgłaszane przy użyciu typu oczekiwania HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO, widocznego w widokach bazy danych [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) .

Domyślnie nadmiarowość magazynu kopii zapasowej pomocniczej jest taka sama jak w przypadku podstawowej bazy danych. Można skonfigurować dodatkową nadmiarowość magazynu kopii zapasowych. Kopie zapasowe są zawsze wykonywane w podstawowej bazie danych. Jeśli pomocnicza konfiguracja ma inną nadmiarowość magazynu kopii zapasowych, po przejściu do trybu failover w przypadku promowania pomocniczej bazy danych na serwerze podstawowym kopie zapasowe będą naliczane zgodnie z nadmiarowością magazynu wybraną w nowym podstawowym (powyższym poziomie). 

> [!NOTE]
> Szybkość dziennika transakcji na podstawowym może być ograniczona ze względu na niezwiązany z mniejszym rozmiarem obliczeń na pomocniczym. Ten rodzaj ograniczania może wystąpić nawet wtedy, gdy pomocniczy ma ten sam lub większy rozmiar obliczeniowy niż podstawowy. Aby uzyskać szczegółowe informacje, w tym typy oczekiwania dla różnych rodzajów ograniczania szybkości rejestrowania, zobacz temat [szybkość dziennika transakcji ładu](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Azure SQL Database konfigurowalnej nadmiarowości magazynu kopii zapasowych jest obecnie dostępna w publicznej wersji zapoznawczej w Brazylii Południowej, a ogólnie dostępna w regionie tylko Azja Wschodnia. Gdy źródłowa baza danych jest tworzona z użyciem lokalnie nadmiarowej lub nadmiarowej nadmiarowości magazynu kopii zapasowych, tworzenie pomocniczej bazy danych w innym regionie platformy Azure nie jest obsługiwane. 

Aby uzyskać więcej informacji na temat rozmiarów obliczeń SQL Database, zobacz [co to są SQL Database warstwy usług](purchasing-models.md).

## <a name="cross-subscription-geo-replication"></a>Replikacja geograficzna między subskrypcjami

Aby skonfigurować aktywną replikację geograficzną między dwiema bazami danych należącymi do różnych subskrypcji (w ramach tej samej dzierżawy lub nie), należy wykonać specjalną procedurę opisaną w tej sekcji.  Procedura jest oparta na poleceniach SQL i wymaga:

- Tworzenie uprzywilejowanego logowania na obu serwerach
- Dodanie adresu IP do listy dozwolonych klientów wykonujących zmiany na obu serwerach (na przykład adres IP hosta z uruchomioną SQL Server Management Studio).

Klient wykonujący zmiany wymaga dostępu sieciowego do serwera podstawowego. Mimo że ten sam adres IP klienta należy dodać do listy dozwolonych na serwerze pomocniczym, połączenie sieciowe z serwerem pomocniczym nie jest ściśle wymagane.

### <a name="on-the-master-of-the-primary-server"></a>Na serwerze głównym

1. Dodaj adres IP do listy dozwolonych klientów wykonujących zmiany (Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory](firewall-configure.md)).
1. Utwórz identyfikator logowania przeznaczony do konfigurowania aktywnej replikacji geograficznej (i Dostosuj poświadczenia zgodnie z wymaganiami):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Utwórz odpowiedniego użytkownika i przypisz go do roli DBManager:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Zanotuj identyfikator SID nowej nazwy logowania przy użyciu tego zapytania:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>W źródłowej bazie danych na serwerze podstawowym

1. Utwórz użytkownika na potrzeby tej samej nazwy logowania:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Dodaj użytkownika do roli db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Na serwerze pomocniczym

1. Dodaj adres IP klienta do listy dozwolonych w obszarze reguły zapory dla serwera pomocniczego. Sprawdź, czy dokładnie ten sam adres IP klienta, który został dodany do serwera podstawowego został również dodany do pomocniczej. Jest to wymagany krok, który należy wykonać przed uruchomieniem polecenia ALTER DATABASE ADD delokacja w celu zainicjowania replikacji geograficznej.

1. Utwórz taką samą nazwę logowania jak na serwerze podstawowym, używając tego samego hasła użytkownika i identyfikatora SID:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Utwórz odpowiedniego użytkownika i przypisz go do roli DBManager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Na serwerze głównym

1. Zaloguj się do serwera głównego na serwerze podstawowym przy użyciu nowej nazwy logowania.
1. Utwórz replikę pomocniczą źródłowej bazy danych na serwerze pomocniczym (w razie konieczności Dostosuj nazwę bazy danych i ServerName):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Po początkowej konfiguracji można usunąć użytkowników, logowania i reguły zapory.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Utrzymywanie synchronizacji poświadczeń i reguł zapory

Zalecamy używanie [reguł zapory adresów IP na poziomie bazy danych](firewall-configure.md) dla replikowanych geograficznie baz danych, dzięki czemu te reguły mogą być replikowane z bazą danych, aby zapewnić, że wszystkie pomocnicze bazy danych mają takie same reguły zapory adresów IP jak podstawowa. Takie podejście eliminuje konieczność ręcznego konfigurowania i utrzymywania reguł zapory na serwerach, na których znajdują się zarówno podstawowe, jak i pomocnicze bazy danych. Podobnie korzystanie z [użytkowników zawartej bazy danych](logins-create-manage.md) na potrzeby dostępu do danych gwarantuje, że zarówno podstawowa, jak i pomocnicza baza danych zawsze mają te same poświadczenia użytkownika, więc podczas pracy w trybie failover nie ma żadnych zakłóceń z powodu niezgodności z identyfikatorami logowania i hasłami. Po dodaniu [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)klienci mogą zarządzać dostępem użytkowników do podstawowych i pomocniczych baz danych, co eliminuje konieczność całkowitego zarządzania poświadczeniami w bazach danych.

## <a name="upgrading-or-downgrading-primary-database"></a>Uaktualnianie lub obniżanie podstawowej bazy danych

Możesz uaktualnić lub obniżyć podstawową bazę danych do innego rozmiaru obliczeniowego (w ramach tej samej warstwy usług, a nie między Ogólnego przeznaczenia i Krytyczne dla działania firmy) bez rozłączania pomocniczych baz danych. Podczas uaktualniania zalecamy najpierw uaktualnić pomocniczą bazę danych, a następnie uaktualnić podstawową. W przypadku obniżania poziomu, należy odwrócić kolejność: najpierw obniżyć poziom podstawowej bazy danych, a następnie obniżyć poziom pomocniczej bazy danych. W przypadku uaktualnienia lub obniżenia poziomu bazy danych do innej warstwy usługi to zalecenie jest wymuszane.

> [!NOTE]
> W przypadku utworzenia pomocniczej bazy danych jako części konfiguracji grupy trybu failover nie zaleca się obniżania poziomu pomocniczej bazy danych. Ma to na celu zapewnienie wystarczającej wydajności warstwy danych do przetwarzania zwykłego obciążenia po aktywowaniu trybu failover.

> [!IMPORTANT]
> Podstawowa baza danych w grupie trybu failover nie może być przeskalowana do wyższej warstwy, chyba że pomocnicza baza danych zostanie najpierw przeskalowana do tej wyższej warstwy. Jeśli spróbujesz skalować podstawową bazę danych przed skalowaniem pomocniczej bazy danych, może zostać wyświetlony następujący błąd:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Zapobieganie utracie danych o kluczowym znaczeniu

Ze względu na duże opóźnienie sieci rozległej, ciągła kopia używa mechanizmu replikacji asynchronicznej. Replikacja asynchroniczna powoduje nieuniknięcie utraty danych w przypadku wystąpienia błędu. Jednak niektóre aplikacje mogą nie wymagać utraty danych. Aby chronić te aktualizacje krytyczne, Deweloper aplikacji może wywoływać procedurę systemu [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) natychmiast po zatwierdzeniu transakcji. Wywołanie **sp_wait_for_database_copy_sync** blokuje wątek wywołujący do momentu przekazania ostatniej zatwierdzonej transakcji do pomocniczej bazy danych. Jednak nie czeka na odtworzenie i zatwierdzenie wysłanych transakcji na serwerze pomocniczym. **sp_wait_for_database_copy_sync** jest objęty zakresem do określonego linku ciągłego kopiowania. Każdy użytkownik z uprawnieniami do nawiązywania połączenia z podstawową bazą danych może wywoływać tę procedurę.

> [!NOTE]
> **sp_wait_for_database_copy_sync** zapobiega utracie danych po przejściu w tryb failover, ale nie gwarantuje pełnej synchronizacji dostępu do odczytu. Opóźnienie spowodowane przez **sp_wait_for_database_copy_sync** wywołanie procedury może być istotne i zależy od rozmiaru dziennika transakcji w czasie wywołania.

## <a name="monitoring-geo-replication-lag"></a>Monitorowanie opóźnienia replikacji geograficznej

Aby monitorować opóźnienie w odniesieniu do celu punktu odzyskiwania, użyj kolumny *replication_lag_sec* [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) w podstawowej bazie danych. Pokazuje zwłokę w sekundach między transakcjami zakontraktowanymi na serwerze podstawowym i trwałymi na serwerze pomocniczym. Na przykład Jeśli wartość opóźnienia wynosi 1 sekunda, oznacza to, że w tym momencie w przypadku wystąpienia podstawowego ma wpływ przestoju i zostanie zainicjowany tryb failover, 1 sekunda najnowszych przejść nie zostanie zapisana.

Aby zmierzyć zwłokę w odniesieniu do zmian w podstawowej bazie danych, która została zastosowana na serwerze pomocniczym, czyli dostępna do odczytu z pomocniczego programu, należy porównać *last_commit* czasie w pomocniczej bazie danych z tą samą wartością w podstawowej bazie danych.

> [!NOTE]
> Czasami *replication_lag_sec* w podstawowej bazie danych ma wartość null, co oznacza, że podstawowa obecnie nie wie, jak daleko jest pomocnicza.   Jest to zwykle wykonywane po ponownym uruchomieniu procesu i powinien być warunkiem przejściowym. Rozważ wysłanie alertu do aplikacji, jeśli *replication_lag_sec* zwraca wartość null przez dłuższy czas. Wskazuje to, że pomocnicza baza danych nie może komunikować się z serwerem podstawowym ze względu na trwały błąd łączności. Istnieją również warunki, które mogą spowodować, że różnica między *last_commit* czas na pomocniczym a podstawową bazę danych będzie duża. Na przykład Jeśli zatwierdzenie zostanie wykonane na serwerze podstawowym po długim okresie bez zmian, różnica przeskoczy do dużej wartości przed szybkim powracaniem do zera. Rozważ użycie warunku błędu, gdy różnica między tymi dwiema wartościami pozostanie duża przez długi czas.

## <a name="programmatically-managing-active-geo-replication"></a>Programowe zarządzanie aktywną replikacją geograficzną

Jak wspomniano wcześniej, aktywna replikacja geograficzna może być również zarządzana programowo przy użyciu Azure PowerShell i interfejsu API REST. W poniższych tabelach opisano zestaw dostępnych poleceń. Aktywna replikacja geograficzna obejmuje zestaw Azure Resource Manager interfejsów API do zarządzania, w tym [Azure SQL Database interfejsu API REST](/rest/api/sql/) i [poleceń cmdlet Azure PowerShell](/powershell/azure/). Te interfejsy API wymagają używania grup zasobów i obsługują kontrolę dostępu opartą na rolach (Azure RBAC). Aby uzyskać więcej informacji na temat implementowania ról dostępu, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: zarządzanie trybem failover dla jednej i puli baz danych

> [!IMPORTANT]
> Te polecenia języka Transact-SQL mają zastosowanie tylko do aktywnej replikacji geograficznej i nie mają zastosowania do grup trybu failover. W związku z tym nie dotyczą one również wystąpień wystąpienia zarządzanego SQL, ponieważ obsługują one tylko grupy trybu failover.

| Polecenie | Opis |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Użyj argumentu Dodaj dodatkową na serwerze, aby utworzyć pomocniczą bazę danych dla istniejącej bazy danych i rozpocząć replikację danych |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Użyj trybu FAILOVER lub FORCE_FAILOVER_ALLOW_DATA_LOSS, aby przełączyć pomocniczą bazę danych jako główną w celu zainicjowania trybu failover |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Użyj Usuń POMOCNICZy serwer na serwerze, aby zakończyć replikację danych między SQL Database a określoną pomocniczą bazą danych. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Zwraca informacje o wszystkich istniejących łączach replikacji dla każdej bazy danych na serwerze. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Pobiera czas ostatniej replikacji, ostatnie opóźnienie replikacji i inne informacje o łączu replikacji danej bazy danych. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Przedstawia stan wszystkich operacji bazy danych, w tym stan łączy replikacji. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |powoduje, że aplikacja czeka, aż wszystkie zatwierdzone transakcje zostaną zreplikowane i potwierdzone przez aktywną pomocniczą bazę danych. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: zarządzanie trybem failover z jedną i pulą baz danych

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

| Polecenie cmdlet | Opis |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Pobiera co najmniej jedną bazę danych. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Tworzy pomocniczą bazę danych dla istniejącej bazy danych i rozpoczyna replikację danych. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Przełącza pomocniczą bazę danych jako główną w celu zainicjowania trybu failover. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Przerywa replikację danych między bazą danych SQL Database i wybraną pomocniczą bazą danych. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Pobiera linki replikacji geograficznej między Azure SQL Database i grupą zasobów lub logicznym serwerem SQL. |
|  | |

> [!IMPORTANT]
> Przykładowe skrypty można znaleźć w temacie [Konfigurowanie i przełączanie w tryb failover pojedynczej bazy danych przy użyciu aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-database-powershell.md) oraz [Konfigurowanie i przełączanie w tryb failover bazy danych w puli przy użyciu aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>Interfejs API REST: zarządzanie trybem failover dla jednej i puli baz danych

| Interfejs API | Opis |
| --- | --- |
| [Utwórz lub zaktualizuj bazę danych (createmode = Restore)](/rest/api/sql/databases/createorupdate) |Tworzy, aktualizuje lub przywraca podstawową lub pomocniczą bazę danych. |
| [Pobieranie lub aktualizowanie stanu bazy danych](/rest/api/sql/databases/createorupdate) |Zwraca stan podczas operacji tworzenia. |
| [Ustaw pomocniczą bazę danych jako podstawową (planowana praca w trybie failover)](/rest/api/sql/replicationlinks/failover) |Ustawia pomocniczą bazę danych jako podstawową przez przechodzenie w tryb failover z bieżącej podstawowej bazy danych. **Ta opcja nie jest obsługiwana w przypadku wystąpienia zarządzanego SQL.**|
| [Ustaw pomocniczą bazę danych jako podstawową (nieplanowaną pracę w trybie failover)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Ustawia pomocniczą bazę danych jako podstawową przez przechodzenie w tryb failover z bieżącej podstawowej bazy danych. Ta operacja może spowodować utratę danych. **Ta opcja nie jest obsługiwana w przypadku wystąpienia zarządzanego SQL.**|
| [Pobierz łącze replikacji](/rest/api/sql/replicationlinks/get) |Pobiera określone łącze replikacji dla danej bazy danych w ramach powiązania z replikacją geograficzną. Pobiera informacje widoczne w widoku wykazu sys.geo_replication_links. **Ta opcja nie jest obsługiwana w przypadku wystąpienia zarządzanego SQL.**|
| [Linki replikacji — lista według bazy danych](/rest/api/sql/replicationlinks/listbydatabase) | Pobiera wszystkie linki replikacji dla danej bazy danych w ramach powiązania z replikacją geograficzną. Pobiera informacje widoczne w widoku wykazu sys.geo_replication_links. |
| [Usuń łącze replikacji](/rest/api/sql/replicationlinks/delete) | Usuwa łącze replikacji bazy danych. Nie można wykonać operacji w trybie failover. |
|  | |

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z przykładowymi skryptami, zobacz:
  - [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database obsługuje również grupy autotrybu failover. Aby uzyskać więcej informacji, zobacz Korzystanie z [grup Autotryb failover](auto-failover-group-overview.md).
- Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md).
- Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](recovery-using-backups.md).
- Aby dowiedzieć się więcej o wymaganiach dotyczących uwierzytelniania dla nowego serwera podstawowego i bazy danych, zobacz [SQL Database zabezpieczenia po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).
