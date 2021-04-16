---
title: Co to jest SQL Data Sync platformy Azure?
description: W tym przeglądzie wprowadzono SQL Data Sync platformy Azure, co umożliwia synchronizowanie danych między wieloma bazami danych w chmurze i lokalnymi.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531933"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Co to jest SQL Data Sync platformy Azure?

SQL Data Sync to usługa zbudowana w oparciu o Azure SQL Database, która umożliwia synchronizowanie wybranych danych dwukierunkowo między wieloma bazami danych, zarówno lokalnymi, jak i w chmurze. 

> [!IMPORTANT]
> Azure SQL Data Sync nie obsługuje Azure SQL Managed Instance w tej chwili.


## <a name="overview"></a>Omówienie 

Data Sync jest oparta na koncepcji grupy synchronizacji. Grupa synchronizacji to grupa baz danych, które chcesz zsynchronizować.

Data Sync używa topologii gwiazdy do synchronizowania danych. Jedną z baz danych w grupie synchronizacji definiuje się jako bazę danych centrum. Pozostałe bazy danych są bazami danych członkowskimi. Synchronizacja odbywa się tylko między centrum i poszczególnymi członkami.

- Baza **danych centrum** musi być Azure SQL Database.
- Członkowskie **bazy danych** mogą być bazami danych w Azure SQL Database lub w wystąpieniach SQL Server.
- Baza **danych metadanych synchronizacji** zawiera metadane i dziennik dla Data Sync. Baza danych metadanych synchronizacji musi być bazą Azure SQL Database w tym samym regionie co baza danych centrum. Baza danych metadanych synchronizacji jest tworzona przez klienta i należąca do klienta. Możesz mieć tylko jedną bazę danych metadanych synchronizacji na region i subskrypcję. Nie można usunąć ani zmienić nazwy bazy danych metadanych synchronizacji, gdy istnieją grupy synchronizacji lub agenci synchronizacji. Firma Microsoft zaleca utworzenie nowej, pustej bazy danych do użycia jako baza danych metadanych synchronizacji. Data Sync tworzy tabele w tej bazie danych i uruchamia częste obciążenie.

> [!NOTE]
> Jeśli używasz lokalnej bazy danych jako bazy danych członkowskiej, musisz zainstalować i skonfigurować [lokalnego](sql-data-sync-sql-server-configure.md#add-on-prem)agenta synchronizacji .

![Synchronizowanie danych między bazami danych](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Grupa synchronizacji ma następujące właściwości:

- Schemat **synchronizacji opisuje,** które dane są synchronizowane.
- Kierunek **synchronizacji może** być dwukierunkowy lub może przepływać tylko w jednym kierunku. Oznacza to, że kierunek synchronizacji może być *piastą do członka,* *członkiem do centrum* lub obu.
- Interwał **synchronizacji opisuje,** jak często odbywa się synchronizacja.
- Zasady **rozwiązywania konfliktów** to zasady na poziomie grupy, które mogą być *hub wins* lub członek *wins*.

## <a name="when-to-use"></a>Kiedy stosować

Data Sync jest przydatna w przypadkach, gdy dane muszą być aktualizowane w kilku bazach danych w Azure SQL Database lub SQL Server. Poniżej podano główne przypadki użycia Data Sync:

- **Hybrydowa synchronizacja danych:** Dzięki Data Sync można zachować synchronizację danych między bazami danych w usługach SQL Server i Azure SQL Database w celu włączenia aplikacji hybrydowych. Ta funkcja może być atrakcyjna dla klientów, którzy rozważają przejście do chmury i chcą umieścić część swoich aplikacji na platformie Azure.
- **Aplikacje rozproszone:** W wielu przypadkach korzystne jest oddzielienie różnych obciążeń między różnymi bazami danych. Jeśli na przykład masz dużą produkcyjną bazę danych, ale musisz również uruchomić obciążenie raportowania lub analizy dla tych danych, warto mieć drugą bazę danych dla tego dodatkowego obciążenia. Takie podejście minimalizuje wpływ na wydajność obciążenia produkcyjnego. Możesz użyć Data Sync, aby zachować synchronizację tych dwóch baz danych.
- **Globalnie dystrybuowane aplikacje:** Wiele firm obejmuje kilka regionów, a nawet kilka krajów/regionów. Aby zminimalizować opóźnienie sieci, najlepiej mieć dane w regionie w pobliżu. Dzięki Data Sync można łatwo synchronizować bazy danych w regionach na całym świecie.

Data Sync nie jest preferowanym rozwiązaniem w następujących scenariuszach:

| Scenariusz | Niektóre zalecane rozwiązania |
|----------|----------------------------|
| Odzyskiwanie po awarii | [Geograficznie nadmiarowe kopie zapasowe platformy Azure](automated-backups-overview.md) |
| Skalowanie odczytu | [Używanie replik tylko do odczytu do równoważenia obciążenia obciążeń zapytań tylko do odczytu (wersja zapoznawcza)](read-scale-out.md) |
| ETL (OLTP do OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lub [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migracja z SQL Server do Azure SQL Database. Można jednak SQL Data Sync po zakończeniu migracji, aby upewnić się, że źródło i element docelowy są zsynchronizowane.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Jak to działa

- **Śledzenie zmian danych:** Data Sync śledzi zmiany przy użyciu wyzwalaczy wstawiania, aktualizowania i usuwania. Zmiany są rejestrowane w tabeli bocznej w bazie danych użytkownika. Pamiętaj, BULK INSERT wyzwalacze nie są wyzwalane domyślnie. Jeśli FIRE_TRIGGERS nie zostanie określony, nie będą wykonywane żadne wyzwalacze wstawiania. Dodaj FIRE_TRIGGERS, aby Data Sync można było śledzić te wstawienia. 
- **Synchronizowanie danych:** Data Sync jest projektowany w modelu piasty i szprych. Centrum jest synchronizowane z poszczególnymi członkami indywidualnie. Zmiany z centrum są pobierane do członka, a następnie zmiany z tego członka są przekazywane do centrum.
- **Rozwiązywanie konfliktów:** Data Sync zapewnia dwie opcje rozwiązywania konfliktów: *hub wins lub* member *wins*.
  - Jeśli wybierzesz *centrum wins*, zmiany w centrum zawsze zastępują zmiany w członkowskim.
  - W przypadku wybrania *opcji Członek wygrywa*, zmiany w zastąpieniu członka zmienią się w centrum. Jeśli istnieje więcej niż jeden członek, końcowa wartość zależy od tego, który członek zostanie najpierw zsynchronizowany.

## <a name="compare-with-transactional-replication"></a>Porównanie z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| **Zalety** | - Pomoc techniczna typu aktywne-aktywne<br/>— Dwukierunkowe między środowiskiem lokalnym i Azure SQL Database | — Mniejsze opóźnienie<br/>- Spójność transakcyjna<br/>- Ponowne użycie istniejącej topologii po migracji <br/>-Azure SQL Managed Instance pomocy technicznej |
| **Wady** | - Brak spójności transakcyjnej<br/>— Większy wpływ na wydajność | — Nie można opublikować z Azure SQL Database <br/>— Wysoki koszt konserwacji |

## <a name="private-link-for-data-sync-preview"></a>Link prywatny dla Data Sync (wersja zapoznawcza)
Nowa funkcja łącza prywatnego (wersja zapoznawcza) umożliwia wybranie zarządzanego przez usługę prywatnego punktu końcowego w celu ustanowienia bezpiecznego połączenia między usługą synchronizacji a bazami danych elementu członkowskiego/centrum podczas procesu synchronizacji danych. Prywatny punkt końcowy zarządzany przez usługę to prywatny adres IP w określonej sieci wirtualnej i podsieci. W Data Sync prywatnego punktu końcowego zarządzanego przez usługę jest tworzony przez firmę Microsoft i jest używany wyłącznie przez usługę Data Sync dla danej operacji synchronizacji. Przed skonfigurowaniem linku prywatnego zapoznaj się z [ogólnymi wymaganiami](sql-data-sync-data-sql-server-sql-database.md#general-requirements) funkcji. 

![Link prywatny dla Data Sync](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> Prywatny punkt końcowy zarządzany przez usługę  należy ręcznie zatwierdzić na stronie Połączenia prywatnego punktu końcowego w Azure Portal podczas wdrażania grupy synchronizacji lub przy użyciu programu PowerShell.

## <a name="get-started"></a>Rozpoczęcie pracy 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurowanie Data Sync w Azure Portal

- [Konfigurowanie usługi Azure SQL Data Sync](sql-data-sync-sql-server-configure.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurowanie Data Sync za pomocą programu PowerShell

- [Synchronizowanie wielu baz danych w programie Azure SQL Database za pomocą programu PowerShell](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Używanie programu PowerShell do synchronizacji między bazą danych w Azure SQL Database a bazami danych w wystąpieniu SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Konfigurowanie aplikacji Data Sync interfejsem API REST
- [Używanie interfejsu API REST do synchronizacji wielu baz danych w Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Zapoznaj się z najlepszymi rozwiązaniami w zakresie Data Sync

- [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Czy coś poszło nie tak

- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Spójność i wydajność

### <a name="eventual-consistency"></a>Spójność ostateczna

Ponieważ Data Sync jest oparta na wyzwalaczu, spójność transakcyjna nie jest gwarantowana. Firma Microsoft gwarantuje, że wszystkie zmiany zostaną wprowadzone w końcu i że Data Sync nie spowoduje utraty danych.

### <a name="performance-impact"></a>Wpływ na wydajność

Data Sync używa wyzwalaczy wstawiania, aktualizowania i usuwania do śledzenia zmian. Tworzy tabele boczne w bazie danych użytkownika na celu śledzenie zmian. Te działania śledzenia zmian mają wpływ na obciążenie bazy danych. Oceń warstwę usługi i uaktualnij w razie potrzeby.

Aprowizowanie i coprowizowanie podczas tworzenia, aktualizowania i usuwania grupy synchronizacji może również mieć wpływ na wydajność bazy danych.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Wymagania i ograniczenia

### <a name="general-requirements"></a>Wymagania ogólne

- Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w żadnym wierszu. Jeśli musisz zmienić wartość klucza podstawowego, usuń wiersz i utwórz go ponownie z nową wartością klucza podstawowego.

> [!IMPORTANT]
> Zmiana wartości istniejącego klucza podstawowego spowoduje następujące błędne zachowanie:
> - Dane między koncentratorem i członkiem mogą zostać utracone, mimo że synchronizacja nie zgłasza żadnego problemu.
> - Synchronizacja może zakończyć się niepowodzeniem, ponieważ tabela śledzenia zawiera nieistnieższy wiersz ze źródła z powodu zmiany klucza podstawowego.

- Izolacja migawki musi być włączona zarówno dla elementów członkowskich synchronizacji, jak i centrum. Aby uzyskać więcej informacji, zobacz [Izolacja migawki w programie SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Aby można było korzystać z linku prywatnego z usługą Data Sync, bazy danych dla członków i centrów muszą być hostowane na platformie Azure (w tym samym lub różnych regionach) w tym samym typie chmury (np. zarówno w chmurze publicznej, jak i w chmurze dla instytucji rządowych). Ponadto, aby można było korzystać z łącza prywatnego, dostawcy zasobów Microsoft.Network muszą być zarejestrowani dla subskrypcji hostowanych serwerów centrum i serwerów członkowskich. Ponadto należy ręcznie zatwierdzić link prywatny dla usługi Data Sync podczas konfiguracji synchronizacji, w sekcji "Połączenia z prywatnym punktem końcowym" w Azure Portal lub za pomocą programu PowerShell. Aby uzyskać więcej informacji na temat zatwierdzania linku prywatnego, zobacz [Konfigurowanie SQL Data Sync](./sql-data-sync-sql-server-configure.md). Po zatwierdzeniu zarządzanego przez usługę prywatnego punktu końcowego cała komunikacja między usługą synchronizacji a bazami danych członka/centrum odbywa się za pośrednictwem linku prywatnego. Aby włączyć tę funkcję, można zaktualizować istniejące grupy synchronizacji.

### <a name="general-limitations"></a>Ogólne ograniczenia

- Tabela nie może mieć kolumny tożsamości, która nie jest kluczem podstawowym.
- Aby można było korzystać z synchronizacji danych, tabela musi mieć indeks klastrowany.
- Klucz podstawowy nie może mieć następujących typów danych: sql_variant, binarny, varbinary, image, xml.
- Należy zachować ostrożność w przypadku używania następujących typów danych jako klucza podstawowego, ponieważ obsługiwana precyzja wynosi tylko sekundę: godzina, data/godzina, data/godzina2 i datagodzinaoffset.
- Nazwy obiektów (baz danych, tabel i kolumn) nie mogą zawierać drukowalnych znaków okresu (.), lewego nawiasu kwadratowego ([) ani prawego nawiasu kwadratowego (]).
- Nazwa tabeli nie może zawierać drukowalnych znaków: ! " # $ % ' ( ) * + - space
- Azure Active Directory nie jest obsługiwane.
- Jeśli istnieją tabele o tej samej nazwie, ale różne schematy (na przykład dbo.customers i sales.customers), tylko jedną z tabel można dodać do synchronizacji.
- Kolumny User-Defined typami danych nie są obsługiwane
- Przenoszenie serwerów między różnymi subskrypcjami nie jest obsługiwane. 
- Jeśli dwa klucze podstawowe różnią się tylko w przypadku (np. Foo i foo), Data Sync nie będzie obsługiwać tego scenariusza.

#### <a name="unsupported-data-types"></a>Nieobsługiwane typy danych

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (obsługiwany jest język XML)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Nieobsługiwane typy kolumn

Data Sync nie można synchronizować kolumn tylko do odczytu ani kolumn generowanych przez system. Na przykład:

- Kolumny obliczane.
- Kolumny generowane przez system dla tabel czasowych.

#### <a name="limitations-on-service-and-database-dimensions"></a>Ograniczenia dotyczące wymiarów usługi i bazy danych

| **Wymiary**                                                  | **Limit**              | **Obejście**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji, do których może należeć dowolna baza danych.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w pojedynczej grupie synchronizacji              | 30                     |                             |
| Maksymalna liczba lokalnych punktów końcowych w pojedynczej grupie synchronizacji. | 5                      | Tworzenie wielu grup synchronizacji |
| Nazwy baz danych, tabel, schematów i kolumn                       | 50 znaków na nazwę |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Tworzenie wielu grup synchronizacji |
| Kolumny w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 MB                  |                             |

> [!NOTE]
> W jednej grupie synchronizacji może być maksymalnie 30 punktów końcowych, jeśli istnieje tylko jedna grupa synchronizacji. Jeśli istnieje więcej niż jedna grupa synchronizacji, łączna liczba punktów końcowych we wszystkich grupach synchronizacji nie może przekraczać 30. Jeśli baza danych należy do wielu grup synchronizacji, jest ona liczona jako wiele punktów końcowych, a nie jeden.

### <a name="network-requirements"></a>Wymagania dotyczące sieci

> [!NOTE]
> Jeśli używasz łącza prywatnego, te wymagania dotyczące sieci nie mają zastosowania. 

Po nawiązania grupy synchronizacji usługa Data Sync nawiązać połączenie z bazą danych centrum. W czasie ustanawiania grupy synchronizacji serwer synchronizacji Azure SQL musi mieć następującą konfigurację w swoich `Firewalls and virtual networks` ustawieniach:

 * *Ustawienie Odmów dostępu do sieci* publicznej musi mieć wartość *Wyłączone.*
 * *Zezwalanie usługom i zasobom* platformy Azure na dostęp do tego serwera musi mieć wartość *Tak* lub należy utworzyć reguły adresów IP dla adresów IP używanych przez [Data Sync usługi](network-access-controls-overview.md#data-sync).

Po utworzeniu i aprowizowania grupy synchronizacji można wyłączyć te ustawienia. Agent synchronizacji połączy się bezpośrednio z bazą danych centrum i można [](private-endpoint-overview.md) użyć reguł adresów [IP](firewall-configure.md) zapory serwera lub prywatnych punktów końcowych, aby umożliwić agentowi dostęp do serwera koncentratora.

> [!NOTE]
> Jeśli zmienisz ustawienia schematu grupy synchronizacji, musisz zezwolić usłudze Data Sync na ponowne uzyskiwanie dostępu do serwera, aby można było ponownie aprowizować bazę danych centrum.

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Ile kosztuje SQL Data Sync usługi

Za samą usługę SQL Data Sync nie są naliczane opłaty. Nadal jednak są zbierane opłaty za transfer danych w przypadku przenoszenia danych do i z SQL Database wystąpienia. Aby uzyskać więcej informacji, [zobacz SQL Database cennika](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Które regiony obsługują Data Sync

SQL Data Sync jest dostępna we wszystkich regionach.

### <a name="is-a-sql-database-account-required"></a>Czy wymagane SQL Database konto użytkownika

Tak. Musisz mieć konto SQL Database, aby hostować bazę danych centrum.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Czy mogę używać Data Sync tylko między SQL Server bazami danych

Nie bezpośrednio. Można jednak synchronizować SQL Server bazami danych pośrednio, tworząc bazę danych centrum na platformie Azure, a następnie dodając lokalne bazy danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Czy mogę używać Data Sync do synchronizowania baz danych w SQL Database należących do różnych subskrypcji

Tak. Można synchronizować bazy danych należące do grup zasobów należących do różnych subskrypcji.

- Jeśli subskrypcje należą do tej samej dzierżawy i masz uprawnienia do wszystkich subskrypcji, możesz skonfigurować grupę synchronizacji w Azure Portal.
- W przeciwnym razie należy użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Czy mogę używać Data Sync do synchronizowania baz danych w usługach SQL Database należących do różnych chmur (takich jak chmura publiczna platformy Azure i Azure (Chiny) — 21Vianet)

Tak. Można synchronizować bazy danych należące do różnych chmur. Musisz użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Czy mogę użyć Data Sync do iniekcji danych z produkcyjnej bazy danych do pustej bazy danych, a następnie zsynchronizować je

Tak. Utwórz schemat ręcznie w nowej bazie danych, tworząc skrypty z oryginalnej bazy danych. Po utworzeniu schematu dodaj tabele do grupy synchronizacji, aby skopiować dane i zachować ich synchronizację.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Czy należy używać SQL Data Sync do kopii zapasowej i przywracania baz danych

Nie zaleca się używania SQL Data Sync tworzenia kopii zapasowej danych. Nie można utworzyć kopii zapasowej i przywrócić jej do określonego punktu w czasie, ponieważ SQL Data Sync nie mają wersji. Ponadto SQL Data Sync kopii zapasowej innych obiektów SQL, takich jak procedury składowane, i nie robi odpowiednika operacji przywracania szybko.

Aby uzyskać jedną zalecaną technikę tworzenia kopii zapasowych, [zobacz Kopiowanie bazy danych w Azure SQL Database](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Można Data Sync synchronizować zaszyfrowane tabele i kolumny

- Jeśli baza danych używa Always Encrypted, można synchronizować tylko tabele i kolumny, które nie *są* szyfrowane. Nie można zsynchronizować zaszyfrowanych kolumn, ponieważ Data Sync nie może odszyfrować danych.
- Jeśli kolumna używa Column-Level Encryption (CLE), możesz zsynchronizować kolumnę, o ile rozmiar wiersza jest mniejszy niż maksymalny rozmiar 24 Mb. Data Sync traktuje kolumnę zaszyfrowaną za pomocą klucza (CLE) jako normalne dane binarne. Aby odszyfrować dane na innych elementach członkowskich synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Czy sortowanie jest obsługiwane w SQL Data Sync

Tak. SQL Data Sync obsługuje sortowanie w następujących scenariuszach:

- Jeśli wybrane tabele schematu synchronizacji nie są jeszcze w bazie danych centrum lub członkowskich, podczas wdrażania grupy synchronizacji usługa automatycznie tworzy odpowiednie tabele i kolumny z ustawieniami sortowanie wybranymi w pustych docelowych bazach danych.
- Jeśli tabele do zsynchronizowania już istnieją zarówno w bazie danych centrum, jak i w członkowskich bazach danych, program SQL Data Sync wymaga, aby kolumny klucza podstawowego były tak samo sortowane między bazami danych centrum i elementami członkowskimi, aby pomyślnie wdrożyć grupę synchronizacji. Nie ma żadnych ograniczeń dotyczących sortowania kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Czy federacja jest obsługiwana w SQL Data Sync

Federacyjna główna baza danych może być używana w usłudze SQL Data Sync bez żadnych ograniczeń. Nie można dodać punktu końcowego Federowana baza danych do bieżącej wersji SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Czy mogę użyć Data Sync do synchronizowania danych wyeksportowanych z usługi Dynamics 365 przy użyciu funkcji "Przynieź własną bazę danych" (BYOD,Bring Your Own Database)?

Funkcja "przyniesienie własnej bazy danych" usługi Dynamics 365 umożliwia administratorom eksportowanie jednostek danych z aplikacji do Microsoft Azure SQL Database. Data Sync można zsynchronizować te dane z innymi bazami danych, jeśli dane są eksportowane  przy użyciu wypychania przyrostowego **(pełne** wypychanie nie jest obsługiwane), a wyzwalacze w docelowej bazie danych mają wartość **tak.**

## <a name="next-steps"></a>Następne kroki

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizowanie schematu zsynchronizowanej bazy danych

Czy musisz zaktualizować schemat bazy danych w grupie synchronizacji? Zmiany schematu nie są automatycznie replikowane. Niektóre rozwiązania można znaleźć w następujących artykułach:

- [Automatyzowanie replikacji zmian schematu za pomocą SQL Data Sync na platformie Azure](./sql-data-sync-update-sync-schema.md)
- [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorowanie i rozwiązywanie problemów

Czy SQL Data Sync się zgodnie z oczekiwaniami? Aby monitorować aktywność i rozwiązywać problemy, zobacz następujące artykuły:

- [Monitorowanie SQL Data Sync za pomocą Azure Monitor dzienników](./monitor-tune-overview.md)
- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Dowiedz się więcej o Azure SQL Database

Aby uzyskać więcej informacji na Azure SQL Database, zobacz następujące artykuły:

- [Omówienie usługi SQL Database](sql-database-paas-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
