---
title: Co to jest SQL Data Sync dla platformy Azure?
description: W tym omówieniu wprowadzono SQL Data Sync platformy Azure, która umożliwia synchronizowanie danych w wielu bazach danych w chmurze i lokalnych.
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
ms.openlocfilehash: c77001707eda7c208ad19a014a1f0cff2b85b25d
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736480"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Co to jest SQL Data Sync dla platformy Azure?

SQL Data Sync to usługa oparta na Azure SQL Database, która pozwala synchronizować dane, które są wybierane dwukierunkowo dla wielu baz danych, zarówno lokalnie, jak i w chmurze. 

> [!IMPORTANT]
> W tej chwili usługa Azure SQL Data Sync nie obsługuje wystąpienia zarządzanego usługi Azure SQL.


## <a name="overview"></a>Omówienie 

Synchronizacja danych opiera się na koncepcji grupy synchronizacji. Grupa synchronizacji to grupa baz danych, które mają zostać zsynchronizowane.

Synchronizacja danych używa topologii gwiazdy do synchronizowania danych. Jedną z baz danych w grupie synchronizacji należy zdefiniować jako bazę danych centrum. Pozostałe bazy danych są członkami baz danych. Synchronizacja odbywa się tylko między węzłem i indywidualnymi elementami członkowskimi.

- **Baza danych centrum** musi być Azure SQL Database.
- **Bazy danych elementów członkowskich** mogą być albo bazami danych w Azure SQL Database, albo w wystąpieniach SQL Server.
- **Baza danych metadanych synchronizacji** zawiera metadane i Dziennik synchronizacji danych. Baza danych metadanych synchronizacji musi być Azure SQL Database umieszczona w tym samym regionie, w którym znajduje się baza danych centrów. Baza danych metadanych synchronizacji jest tworzona przez klienta i jest własnością klienta. Możesz mieć tylko jedną bazę danych metadanych synchronizacji na region i subskrypcję. Nie można usunąć bazy danych metadanych synchronizacji ani zmienić jej nazwy, gdy istnieją grupy synchronizacji lub agenci synchronizacji. Firma Microsoft zaleca, aby utworzyć nową, pustą bazę danych, która będzie używana jako baza danych metadanych synchronizacji. Synchronizacja danych tworzy tabele w tej bazie danych i uruchamia częste obciążenie.

> [!NOTE]
> W przypadku korzystania z lokalnej bazy danych jako bazy danych będącej członkiem należy [zainstalować i skonfigurować lokalnego agenta synchronizacji](sql-data-sync-sql-server-configure.md#add-on-prem).

![Synchronizowanie danych między bazami danych](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Grupa synchronizacji ma następujące właściwości:

- **Schemat synchronizacji** opisuje, które dane są synchronizowane.
- **Kierunek synchronizacji** może być dwukierunkowy lub może przepływać tylko w jednym kierunku. Oznacza to, że kierunek synchronizacji może być *centrum do elementu członkowskiego* lub *składowej lub z* obu tych elementów.
- **Interwał synchronizacji** opisuje, jak często odbywa się synchronizacja.
- **Zasady rozwiązywania konfliktów** są zasadami na poziomie grupy, które mogą być *centrami WINS* lub *członkowskimi*.

## <a name="when-to-use"></a>Kiedy stosować

Synchronizacja danych jest przydatna w przypadkach, gdy dane muszą być przechowywane w wielu bazach danych w Azure SQL Database lub SQL Server. Poniżej przedstawiono główne przypadki użycia synchronizacji danych:

- **Synchronizacja danych hybrydowych:** Dzięki funkcji synchronizacji danych można synchronizować dane między bazami danych w SQL Server i Azure SQL Database, aby umożliwić aplikacjom hybrydowym. Ta możliwość może odwoływać się do klientów, którzy rozważają przechodzenie do chmury i chcą umieścić niektóre aplikacje na platformie Azure.
- **Aplikacje rozproszone:** W wielu przypadkach korzystne jest oddzielenie różnych obciążeń między różnymi bazami danych. Na przykład jeśli masz dużą produkcyjną bazę danych, ale musisz także uruchomić obciążenie związane z raportowaniem lub analizą danych, warto mieć drugą bazę danych dla tego dodatkowego obciążenia. Takie podejście minimalizuje wpływ na wydajność produkcyjną. Synchronizacja danych służy do synchronizowania tych dwóch baz danych.
- **Aplikacje rozproszone globalnie:** Wiele firm obejmuje kilka regionów, a nawet kilka krajów/regionów. Aby zminimalizować opóźnienie sieci, najlepiej, aby dane w regionie były blisko siebie. Dzięki synchronizacji danych można łatwo synchronizować bazy danych w regionach na całym świecie.

Synchronizacja danych nie jest preferowanym rozwiązaniem dla następujących scenariuszy:

| Scenariusz | Niektóre zalecane rozwiązania |
|----------|----------------------------|
| Odzyskiwanie po awarii | [Geograficznie nadmiarowe platformy Azure](automated-backups-overview.md) |
| Skala odczytu | [Używanie replik tylko do odczytu do równoważenia obciążenia obciążeń zapytań tylko do odczytu (wersja zapoznawcza)](read-scale-out.md) |
| ETL (OLTP do OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lub [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migracja z SQL Server do Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>Jak to działa

- **Śledzenie zmian danych:** Synchronizacja danych śledzi zmiany przy użyciu wyzwalaczy INSERT, Update i DELETE. Zmiany są rejestrowane w tabeli bocznej w bazie danych użytkownika. Należy zauważyć, że BULK INSERT domyślnie nie uruchamia wyzwalaczy. Jeśli FIRE_TRIGGERS nie jest określony, żadne wyzwalacze wstawiania nie są wykonywane. Dodaj opcję FIRE_TRIGGERS, aby synchronizacja danych mogła śledzić te operacje wstawiania. 
- **Synchronizowanie danych:** Synchronizacja danych została zaprojektowana w modelu gwiazdy. Koncentrator synchronizuje się z każdym członkiem indywidualnie. Zmiany z centrum są pobierane do elementu członkowskiego, a następnie zmiany z elementu członkowskiego są przekazywane do centrum.
- **Rozwiązywanie konfliktów:** Synchronizacja danych oferuje dwie opcje rozwiązywania konfliktów, *centrum WINS* lub *członka usługi WINS*.
  - W przypadku wybrania *usługi Hub WINS* zmiany w centrum zawsze zastępują zmiany w elemencie członkowskim.
  - W przypadku wybrania *elementu członkowskiego usługi WINS* zmiany wprowadzone w elemencie członkowskim zastępują zmiany w centrum. Jeśli istnieje więcej niż jeden element członkowski, końcowa wartość zależy od tego, który element członkowski synchronizuje najpierw.

## <a name="compare-with-transactional-replication"></a>Porównanie z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| **Zalety** | -Aktywne-aktywne wsparcie<br/>-Dwukierunkowe między środowiskiem lokalnym i Azure SQL Database | -Małe opóźnienia<br/>-Spójności transakcyjnej<br/>-Ponowne użycie istniejącej topologii po migracji <br/>— Obsługa wystąpienia zarządzanego usługi Azure SQL |
| **Wady** | -Brak spójności transakcyjnej<br/>— Wyższy wpływ na wydajność | -Nie można opublikować z Azure SQL Database <br/>— Wysoki koszt konserwacji |

## <a name="get-started"></a>Wprowadzenie 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurowanie synchronizacji danych w Azure Portal

- [Konfigurowanie usługi Azure SQL Data Sync](sql-data-sync-sql-server-configure.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurowanie synchronizacji danych przy użyciu programu PowerShell

- [Synchronizowanie wielu baz danych w Azure SQL Database przy użyciu programu PowerShell](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Używanie programu PowerShell do synchronizacji między bazą danych w Azure SQL Database i bazami danych w wystąpieniu SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi synchronizacji danych

- [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Wystąpił problem

- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Spójność i wydajność

### <a name="eventual-consistency"></a>Spójność ostateczna

Ponieważ synchronizacja danych jest oparta na wyzwalaczu, nie ma gwarancji spójności transakcyjnej. Firma Microsoft gwarantuje, że wszystkie zmiany są wprowadzane ostatecznie i synchronizacja danych nie powoduje utraty danych.

### <a name="performance-impact"></a>Wpływ na wydajność

Synchronizacja danych używa wyzwalaczy INSERT, Update i DELETE do śledzenia zmian. Tworzy tabele boczne w bazie danych użytkownika na potrzeby śledzenia zmian. Te działania śledzenia zmian mają wpływ na obciążenie bazy danych. Oceń swoją warstwę usług i Uaktualnij w razie konieczności.

Inicjowanie obsługi administracyjnej i cofanie aprowizacji podczas tworzenia grupy synchronizacji, aktualizacji i usuwania może również mieć wpływ na wydajność bazy danych.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Wymagania i ograniczenia

### <a name="general-requirements"></a>Wymagania ogólne

- Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w żadnym wierszu. Jeśli musisz zmienić wartość klucza podstawowego, Usuń wiersz i utwórz go ponownie z nową wartością klucza podstawowego.

> [!IMPORTANT]
> Zmiana wartości istniejącego klucza podstawowego spowoduje wystąpienie następujących nieprawidłowych zachowań:
> - Dane między centrami i elementami członkowskimi mogą zostać utracone nawet wtedy, gdy synchronizacja nie zgłasza żadnego problemu.
> - Synchronizacja może zakończyć się niepowodzeniem, ponieważ tabela śledzenia ma nieistniejący wiersz ze źródła ze względu na zmianę klucza podstawowego.

- Izolacja migawki musi być włączona zarówno dla elementów członkowskich synchronizacji, jak i dla centrum. Aby uzyskać więcej informacji, zobacz [Izolacja migawki w programie SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Ogólne ograniczenia

- Tabela nie może mieć kolumny tożsamości, która nie jest kluczem podstawowym.
- Tabela musi mieć indeks klastrowany, aby można było używać synchronizacji danych.
- Klucz podstawowy nie może mieć następujących typów danych: sql_variant, Binary, varbinary, Image, XML.
- Należy zachować ostrożność w przypadku używania następujących typów danych jako klucza podstawowego, ponieważ obsługiwana precyzja jest tylko sekundą: Time, DateTime, datetime2, DateTimeOffset.
- Nazwy obiektów (baz danych, tabel i kolumn) nie mogą zawierać kropki do drukowalnego znaku (.), lewego nawiasu kwadratowego ([) lub prawego nawiasu kwadratowego (]).
- Nazwa tabeli nie może zawierać znaków drukowalnych:! "# $% ' () * +-spacja
- Uwierzytelnianie Azure Active Directory nie jest obsługiwane.
- Jeśli istnieją tabele o tej samej nazwie, ale różnych schematach (na przykład dbo. Customers i Sales. Customers), można dodać do synchronizacji tylko jedną z tabel.
- Kolumny z User-Defined typami danych nie są obsługiwane
- Przeniesienie serwerów między różnymi subskrypcjami nie jest obsługiwane. 

#### <a name="unsupported-data-types"></a>Nieobsługiwane typy danych

- Strumieni
- SQL/CLR UDT
- XmlSchemaCollection (obsługiwana w języku XML)
- Kursor, RowVersion, sygnatura czasowa, hierarchyid

#### <a name="unsupported-column-types"></a>Nieobsługiwane typy kolumn

Synchronizacja danych nie może synchronizować kolumn tylko do odczytu lub generowanych przez system. Na przykład:

- Kolumny obliczane.
- Kolumny danych czasowych generowane przez system.

#### <a name="limitations-on-service-and-database-dimensions"></a>Ograniczenia dotyczące wymiarów usługi i bazy danych

| **Wymiary**                                                  | **Limit**              | **Obejście**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji, do których może należeć baza danych.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w pojedynczej grupie synchronizacji              | 30                     |                             |
| Maksymalna liczba lokalnych punktów końcowych w pojedynczej grupie synchronizacji. | 5                      | Tworzenie wielu grup synchronizacji |
| Nazwy bazy danych, tabeli, schematu i kolumny                       | 50 znaków na nazwę |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Tworzenie wielu grup synchronizacji |
| Kolumny w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 MB                  |                             |

> [!NOTE]
> W jednej grupie synchronizacji może istnieć maksymalnie 30 punktów końcowych, jeśli istnieje tylko jedna grupa synchronizacji. Jeśli istnieje więcej niż jedna grupa synchronizacji, Łączna liczba punktów końcowych we wszystkich grupach synchronizacji nie może przekroczyć 30. Jeśli baza danych należy do wielu grup synchronizacji, jest traktowana jako wiele punktów końcowych, a nie jeden.

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Po ustanowieniu grupy synchronizacji usługa synchronizacji danych musi nawiązać połączenie z bazą danych centrum. Podczas ustanawiania grupy synchronizacji w ustawieniach programu Azure SQL Server musi znajdować się następująca konfiguracja `Firewalls and virtual networks` :

 * *Odmowa dostępu do sieci publicznej* musi być ustawiona na *off*.
 * *Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera* musi być ustawiona wartość *Yes (tak*) lub należy utworzyć reguły adresów IP dla [adresów IP używanych przez usługę synchronizacji danych](network-access-controls-overview.md#data-sync).

Po utworzeniu i udostępnieniu grupy synchronizacji można wyłączyć te ustawienia. Agent synchronizacji będzie łączył się bezpośrednio z centralną bazą danych i można użyć [reguł IP zapory](firewall-configure.md) serwera lub [prywatnych punktów końcowych](private-endpoint-overview.md) , aby umożliwić agentowi dostęp do serwera centrum.

> [!NOTE]
> W przypadku zmiany ustawień schematu grupy synchronizacji należy zezwolić usłudze synchronizacji danych na dostęp do serwera ponownie, aby można było ponownie zainicjować obsługę administracyjną bazy danych.

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Jak dużo koszt usługi SQL Data Sync

Nie jest naliczana opłata za usługę SQL Data Sync. Jednak nadal są pobierane opłaty za transfer danych w przypadku przenoszenia i wychodzącego wystąpienia SQL Database. Aby uzyskać więcej informacji, zobacz [Cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Jakie regiony obsługują synchronizację danych

SQL Data Sync jest dostępna we wszystkich regionach.

### <a name="is-a-sql-database-account-required"></a>Czy wymagane jest konto SQL Database

Tak. Aby hostować bazę danych centrów, musisz mieć konto SQL Database.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Czy synchronizacja danych może być używana do synchronizacji tylko między bazami danych SQL Server

Nie bezpośrednio. Istnieje jednak możliwość synchronizacji między bazami danych SQL Server, ale przez utworzenie centralnej bazy danych na platformie Azure, a następnie dodanie lokalnych baz danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Czy można używać synchronizacji danych do synchronizacji między bazami danych w SQL Database, które należą do różnych subskrypcji

Tak. Można synchronizować między bazami danych należącymi do grup zasobów należących do różnych subskrypcji.

- Jeśli subskrypcje należą do tej samej dzierżawy i masz uprawnienia do wszystkich subskrypcji, możesz skonfigurować grupę synchronizacji w Azure Portal.
- W przeciwnym razie musisz użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji należące do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Czy można używać synchronizacji danych do synchronizacji między bazami danych w SQL Database, które należą do różnych chmur (takich jak chmura publiczna platformy Azure i usługa Azure Chiny 21Vianet)

Tak. Można synchronizować między bazami danych, które należą do różnych chmur. Musisz użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji należące do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Czy można używać synchronizacji danych do wypełniania danych z mojej produkcyjnej bazy danych do pustej bazy danych, a następnie synchronizować ją

Tak. Utwórz schemat ręcznie w nowej bazie danych, wykonując skrypt z oryginału. Po utworzeniu schematu należy dodać tabele do grupy synchronizacji w celu skopiowania danych i zsynchronizowania ich z synchronizacją.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Czy należy używać SQL Data Sync do tworzenia kopii zapasowych i przywracania baz danych

Nie zaleca się używania SQL Data Sync, aby utworzyć kopię zapasową danych. Nie można utworzyć kopii zapasowej i przywrócić do określonego punktu w czasie, ponieważ synchronizacje SQL Data Sync nie są obsługiwane. Ponadto SQL Data Sync nie tworzy kopii zapasowej innych obiektów SQL, takich jak procedury składowane, i nie wykonuje operacji przywracania szybko.

Aby zapoznać się z jedną zalecaną techniką tworzenia kopii zapasowych, zobacz [Kopiowanie bazy danych w Azure SQL Database](database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Czy zaszyfrowane tabele i kolumny synchronizacji synchronizacji danych

- Jeśli baza danych używa Always Encrypted, można synchronizować tylko tabele i kolumny, które *nie* są zaszyfrowane. Nie można zsynchronizować zaszyfrowanych kolumn, ponieważ synchronizacja danych nie może odszyfrować danych.
- Jeśli kolumna używa szyfrowania Column-Level (CLE), możesz zsynchronizować kolumnę, o ile rozmiar wiersza jest mniejszy niż rozmiar maksymalny wynoszący 24 MB. Synchronizacja danych traktuje kolumnę zaszyfrowaną przez klucz (CLE) jako normalne dane binarne. Aby odszyfrować dane w innych elementach członkowskich synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Sortowanie jest obsługiwane w SQL Data Sync

Tak. SQL Data Sync obsługuje sortowanie w następujących scenariuszach:

- Jeśli wybrane tabele schematu synchronizacji nie znajdują się już w centrum lub bazach danych, to podczas wdrażania grupy synchronizacji usługa automatycznie tworzy odpowiednie tabele i kolumny z ustawieniami sortowania wybranymi w pustej docelowej bazie danych.
- Jeśli tabele, które mają być synchronizowane, znajdują się już zarówno w centrum, jak i w bazach danych Członkowskich, SQL Data Sync wymaga, aby kolumny klucza podstawowego miały takie samo sortowanie między bazami danych centrum i elementami członkowskimi, aby pomyślnie wdrożyć grupę synchronizacji. Brak ograniczeń sortowania dla kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Czy Federacja jest obsługiwana w SQL Data Sync

Główna baza danych Federacji może być używana w usłudze SQL Data Sync bez ograniczeń. Nie można dodać punktu końcowego bazy danych federacyjnych do bieżącej wersji SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Czy można używać funkcji synchronizacji danych do synchronizowania danych wyeksportowanych z systemu Dynamics 365 przy użyciu narzędzia do przenoszenia własnych baz danych (BYOD)?

Funkcja Dynamics 365 umożliwia administratorom eksportowanie jednostek danych z aplikacji do własnych Microsoft Azure bazy danych SQL. Synchronizacja danych może służyć do synchronizowania tych danych z innymi bazami danych, jeśli dane są eksportowane przy użyciu **wypychania przyrostowego** (pełna wypychanie nie jest obsługiwana), a **wyzwalacze włączania w docelowej bazie danych** są ustawione na **wartość tak**.

## <a name="next-steps"></a>Następne kroki

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizowanie schematu zsynchronizowanej bazy danych

Czy musisz zaktualizować schemat bazy danych w grupie synchronizacji? Zmiany schematu nie są automatycznie replikowane. W przypadku niektórych rozwiązań zapoznaj się z następującymi artykułami:

- [Automatyzowanie replikacji zmian schematu przy użyciu SQL Data Sync na platformie Azure](./sql-data-sync-update-sync-schema.md)
- [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Monitorowanie i rozwiązywanie problemów

Czy SQL Data Sync działa zgodnie z oczekiwaniami? Aby monitorować aktywność i rozwiązywać problemy, zobacz następujące artykuły:

- [Monitorowanie SQL Data Sync przy użyciu dzienników Azure Monitor](./monitor-tune-overview.md)
- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Dowiedz się więcej o Azure SQL Database

Aby uzyskać więcej informacji na temat Azure SQL Database, zobacz następujące artykuły:

- [Omówienie usługi SQL Database](sql-database-paas-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
