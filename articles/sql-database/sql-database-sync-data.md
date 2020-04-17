---
title: Synchronizacja danych
description: W tym ekscesie wprowadzono usługę Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 08/20/2019
ms.openlocfilehash: 8708c458e1064e4b9ea7dc67f1a4d4fbce1547b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481964"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Synchronizowanie danych w wielu chmurach i lokalnych bazach danych za pomocą synchronizacji danych SQL

SQL Data Sync to usługa oparta na bazie danych SQL Azure, która umożliwia synchronizację wybranych danych dwukierunkowo w wielu bazach danych SQL i wystąpieniach programu SQL Server.

> [!IMPORTANT]
> Usługa Azure SQL Data Sync nie obsługuje obecnie wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="when-to-use-data-sync"></a>Kiedy używać synchronizacji danych

Synchronizacja danych jest przydatna w przypadkach, gdy dane muszą być aktualizowane w kilku bazach danych SQL platformy Azure lub bazach danych programu SQL Server. Oto główne przypadki użycia synchronizacji danych:

- **Hybrydowa synchronizacja danych:** Dzięki synchronizacji danych można synchronizować dane między lokalnymi bazami danych i bazami danych SQL platformy Azure, aby włączyć aplikacje hybrydowe. Ta funkcja może spodobać się klientom, którzy rozważają przejście do chmury i chcieliby umieścić niektóre z ich aplikacji na platformie Azure.
- **Aplikacje rozproszone:** W wielu przypadkach jest korzystne, aby oddzielić różne obciążenia w różnych bazach danych. Na przykład jeśli masz dużą produkcyjną bazę danych, ale musisz również uruchomić obciążenie raportowania lub analizy tych danych, warto mieć drugą bazę danych dla tego dodatkowego obciążenia. Takie podejście minimalizuje wpływ na wydajność obciążenia produkcyjnego. Synchronizacja danych służy do przechowywania tych dwóch baz danych zsynchronizowane.
- **Aplikacje rozproszone globalnie:** Wiele firm obejmuje kilka regionów, a nawet kilka krajów/regionów. Aby zminimalizować opóźnienia w sieci, najlepiej mieć dane w regionie blisko Ciebie. Synchronizacja danych umożliwia synchronizację baz danych w regionach na całym świecie.

Synchronizacja danych nie jest preferowanym rozwiązaniem dla następujących scenariuszy:

| Scenariusz | Niektóre zalecane rozwiązania |
|----------|----------------------------|
| Odzyskiwanie po awarii | [Geob nadmiarowe kopie zapasowe platformy Azure](sql-database-automated-backups.md) |
| Skala odczytu | [Używanie replik tylko do odczytu w celu obciążenia obciążeń zapytań tylko do odczytu (wersja zapoznawcza)](sql-database-read-scale-out.md) |
| ETL (OLTP do OLAP) | [Usługa Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lub [USŁUGI integracji programu SQL Server](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| Migracja z lokalnego programu SQL Server do bazy danych SQL usługi Azure | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>Omówienie synchronizacji danych SQL

Synchronizacja danych opiera się na koncepcji grupy synchronizacji. Grupa synchronizacji to grupa baz danych, które chcesz zsynchronizować.

Synchronizacja danych używa topologii koncentratora i szprychy do synchronizowania danych. Jedną z baz danych w grupie synchronizacji można zdefiniować jako bazę danych centrum. Pozostałe bazy danych są bazami danych członków. Synchronizacja występuje tylko między Centrum i poszczególnych członków.

- **Baza danych centrum** musi być bazą danych SQL platformy Azure.
- Bazy **danych elementów członkowskich** mogą być bazami danych SQL, lokalnymi bazami danych programu SQL Server lub wystąpieniami programu SQL Server na maszynach wirtualnych platformy Azure.
- **Baza danych synchronizacji** zawiera metadane i dziennik synchronizacji danych. Baza danych synchronizacji musi być bazą danych SQL platformy Azure znajdującą się w tym samym regionie co baza danych centrum. Baza danych synchronizacji jest utworzona przez klienta i należąca do klienta.

> [!NOTE]
> Jeśli używasz lokalnej bazy danych jako bazy danych elementów członkowskich, musisz [zainstalować i skonfigurować lokalnego agenta synchronizacji](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Synchronizowanie danych między bazami danych](media/sql-database-sync-data/sync-data-overview.png)

Grupa synchronizacji ma następujące właściwości:

- **Schemat synchronizacji** opisuje, które dane są synchronizowane.
- **Kierunek synchronizacji** może być dwukierunkowy lub może płynąć tylko w jednym kierunku. Oznacza to, że kierunek synchronizacji może być *centrum do elementu członkowskiego*lub element *członkowski do centrum*lub oba.
- **Interwał synchronizacji** opisuje, jak często występuje synchronizacja.
- **Zasady rozwiązywania konfliktów** to zasada na poziomie grupy, która może być *wygrana centrum* lub *wygrane członka*.

## <a name="how-does-data-sync-work"></a>Jak działa synchronizacja danych

- **Śledzenie zmian danych:** Synchronizacja danych śledzi zmiany za pomocą wyzwalaczy wstawiania, aktualizowania i usuwania. Zmiany są rejestrowane w tabeli bocznej w bazie danych użytkowników. Należy zauważyć, że bulk insert domyślnie nie uruchamia wyzwalaczy. Jeśli nie określono FIRE_TRIGGERS, nie są wykonywane żadne wyzwalacze wstawiania. Dodaj opcję FIRE_TRIGGERS, aby synchronizacja danych mogła śledzić te wstawienia. 
- **Synchronizowanie danych:** Synchronizacja danych została zaprojektowana w modelu koncentratora i szprychy. Centrum synchronizuje się z każdym członkiem indywidualnie. Zmiany z Centrum są pobierane do członka, a następnie zmiany z członka są przekazywane do Centrum.
- **Rozwiązywanie konfliktów:** Synchronizacja danych udostępnia dwie opcje rozwiązywania konfliktów, *wygranych centrum* lub *wygranych członków.*
  - Jeśli wybierzesz *Hub wins*, zmiany w koncentratorze zawsze zastępują zmiany w człowiaństce.
  - Jeśli wybierzesz *element członkowski wygrywa,* zmiany w element członkowski zastąpić zmiany w centrum. Jeśli istnieje więcej niż jeden element członkowski, wartość końcowa zależy od tego, który element członkowski synchronizuje się jako pierwszy.

## <a name="compare-data-sync-with-transactional-replication"></a>Porównaj synchronizację danych z replikacją transakcyjną

| | Synchronizacja danych | Replikacja transakcyjna |
|---|---|---|
| Zalety | - Aktywne-aktywne wsparcie<br/>- Dwukierunkowe między lokalnymi i usługi Azure SQL Database | - Mniejsze opóźnienie<br/>- Spójność transakcyjna<br/>- Ponowne wykorzystanie istniejącej topologii po migracji |
| Wady | - 5 min lub więcej opóźnień<br/>- Brak spójności transakcyjnej<br/>- Większy wpływ na wydajność | - Nie można opublikować z pojedynczej bazy danych usługi Azure SQL Database lub puli bazy danych<br/>- Wysokie koszty konserwacji |

## <a name="get-started-with-sql-data-sync"></a>Wprowadzenie do synchronizacji danych SQL

### <a name="set-up-data-sync-in-the-azure-portal"></a>Konfigurowanie synchronizacji danych w witrynie Azure portal

- [Konfigurowanie usługi Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)

### <a name="set-up-data-sync-with-powershell"></a>Konfigurowanie synchronizacji danych za pomocą programu PowerShell

- [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](scripts/sql-database-sync-data-between-sql-databases.md)
- [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Zapoznaj się z najlepszymi rozwiązaniami w zakresie synchronizacji danych

- [Best practices for Azure SQL Data Sync (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>Czy coś poszło nie tak

- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>Spójność i wydajność

### <a name="eventual-consistency"></a>Spójność ostateczna

Ponieważ synchronizacja danych jest oparta na wyzwalaczach, spójność transakcyjna nie jest gwarantowana. Firma Microsoft gwarantuje, że wszystkie zmiany zostaną wprowadzone po pewnym czasie i że synchronizacja danych nie powoduje utraty danych.

### <a name="performance-impact"></a>Wpływ na wydajność

Synchronizacja danych używa wyzwalaczy wstawiania, aktualizowania i usuwania w celu śledzenia zmian. Tworzy tabele boczne w bazie danych użytkownika do śledzenia zmian. Te działania śledzenia zmian mają wpływ na obciążenie bazy danych. W razie potrzeby oceń warstwę usług i uaktualnij.

Inicjowanie obsługi administracyjnej i anulowanie obsługi administracyjnej podczas tworzenia, aktualizowania i usuwania grupy synchronizacji może również mieć wpływ na wydajność bazy danych.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a>Wymagania i ograniczenia

### <a name="general-requirements"></a>Wymagania ogólne

- Każda tabela musi mieć klucz podstawowy. Nie zmieniaj wartości klucza podstawowego w żadnym wierszu. Jeśli trzeba zmienić wartość klucza podstawowego, usuń wiersz i ponownie utworzyć go z nową wartością klucza podstawowego.

> [!IMPORTANT]
> Zmiana wartości istniejącego klucza podstawowego spowoduje następujące wadliwe zachowanie:
> - Dane między koncentratorem a członkiem mogą zostać utracone, nawet jeśli synchronizacja nie zgłasza żadnego problemu.
> - Synchronizacja może zakończyć się niepowodzeniem, ponieważ tabela śledzenia ma nieistniejący wiersz ze źródła ze względu na zmianę klucza podstawowego.

- Izolacji migawki musi być włączona. Aby uzyskać więcej informacji, zobacz [Izolacja migawki w programie SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Ogólne ograniczenia

- Tabela nie może mieć kolumny tożsamości, która nie jest kluczem podstawowym.
- Klucz podstawowy nie może mieć następujących typów danych: sql_variant, binarny, varbinary, image, xml.
- Należy zachować ostrożność podczas korzystania z następujących typów danych jako klucz podstawowy, ponieważ obsługiwana dokładność jest tylko do drugiego: czas, datetime, datetime2, datetimeoffset.
- Nazwy obiektów (baz danych, tabel i kolumn) nie mogą zawierać znaków do wydrukowania kropki (.), lewego nawiasu kwadratowego ([) lub prawego nawiasu kwadratowego (]).
- Uwierzytelnianie usługi Azure Active Directory nie jest obsługiwane.
- Tabele o tej samej nazwie, ale inny schemat (na przykład dbo.customers i sales.customers) nie są obsługiwane.
- Kolumny z typami danych zdefiniowanymi przez użytkownika nie są obsługiwane
- Przenoszenie serwerów między różnymi subskrypcjami nie jest obsługiwane. 

#### <a name="unsupported-data-types"></a>Nieobsługiwały typy danych

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (obsługiwane przez XML)
- Kursor, RowVersion, Sygnatura czasowa, Hierarchaid

#### <a name="unsupported-column-types"></a>Nieobsługiwały typy kolumn

Synchronizacja danych nie może synchronizować kolumn tylko do odczytu ani generowanych przez system. Przykład:

- Kolumny obliczane.
- Kolumny generowane przez system dla tabel czasowych.

#### <a name="limitations-on-service-and-database-dimensions"></a>Ograniczenia dotyczące usług i wymiarów bazy danych

| **Wymiary**                                                  | **Limit**              | **Obejście**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maksymalna liczba grup synchronizacji, do których może należeć dowolna baza danych.       | 5                      |                             |
| Maksymalna liczba punktów końcowych w jednej grupie synchronizacji              | 30                     |                             |
| Maksymalna liczba lokalnych punktów końcowych w jednej grupie synchronizacji. | 5                      | Tworzenie wielu grup synchronizacji |
| Nazwy baz danych, tabel, schematów i kolumn                       | 50 znaków na nazwę |                             |
| Tabele w grupie synchronizacji                                          | 500                    | Tworzenie wielu grup synchronizacji |
| Kolumny w tabeli w grupie synchronizacji                              | 1000                   |                             |
| Rozmiar wiersza danych w tabeli                                        | 24 Mb                  |                             |
| Minimalny interwał synchronizacji                                           | 5 min.              |                             |

> [!NOTE]
> Może istnieć maksymalnie 30 punktów końcowych w jednej grupie synchronizacji, jeśli istnieje tylko jedna grupa synchronizacji. Jeśli istnieje więcej niż jedna grupa synchronizacji, całkowita liczba punktów końcowych we wszystkich grupach synchronizacji nie może przekraczać 30. Jeśli baza danych należy do wielu grup synchronizacji, jest liczona jako wiele punktów końcowych, a nie jeden.

## <a name="faq-about-sql-data-sync"></a>Często zadawane pytania dotyczące synchronizacji danych SQL

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Ile kosztuje usługa SQL Data Sync

Nie ma żadnych opłat za samą usługę SQL Data Sync. Jednak nadal zbierać opłaty za transfer danych dla przenoszenia danych i z wystąpienia bazy danych SQL. Aby uzyskać więcej informacji, zobacz [Cennik bazy danych SQL](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>Regiony obsługują synchronizację danych

Synchronizacja danych SQL jest dostępna we wszystkich regionach.

### <a name="is-a-sql-database-account-required"></a>Czy wymagane jest konto bazy danych SQL

Tak. Aby hostować bazę danych centrum, musi istnieć konto bazy danych SQL.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>Za pomocą synchronizacji danych można synchronizować tylko między lokalnymi bazami danych programu SQL Server

Nie bezpośrednio. Można synchronizować między lokalnymi bazami danych programu SQL Server pośrednio, jednak tworząc bazę danych centrum na platformie Azure, a następnie dodając lokalne bazy danych do grupy synchronizacji.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>Czy mogę używać synchronizacji danych do synchronizacji między bazami danych SQL, które należą do różnych subskrypcji

Tak. Można synchronizować między bazami danych SQL, które należą do grup zasobów należących do różnych subskrypcji.

- Jeśli subskrypcje należą do tej samej dzierżawy i masz uprawnienia do wszystkich subskrypcji, można skonfigurować grupę synchronizacji w witrynie Azure portal.
- W przeciwnym razie należy użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Czy mogę używać synchronizacji danych do synchronizacji między bazami danych SQL należącymi do różnych chmur (takich jak Azure Public Cloud i Azure China 21Vianet)

Tak. Można synchronizować między bazami danych SQL, które należą do różnych chmur, trzeba użyć programu PowerShell, aby dodać elementy członkowskie synchronizacji, które należą do różnych subskrypcji.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Czy mogę użyć synchronizacji danych do wysiewu danych z produkcyjnej bazy danych do pustej bazy danych, a następnie zsynchronizować je

Tak. Utwórz schemat ręcznie w nowej bazie danych, skryptując go z oryginału. Po utworzeniu schematu dodaj tabele do grupy synchronizacji, aby skopiować dane i zachować synchronizację.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Czy używam synchronizacji danych SQL do utworzenia kopii zapasowej i przywrócenia baz danych

Nie zaleca się używania synchronizacji danych SQL do tworzenia kopii zapasowej danych. Nie można wywróć kopii zapasowej i przywrócić do określonego punktu w czasie, ponieważ synchronizacja synchronizacji danych SQL nie są wersjona. Ponadto SQL Data Sync nie wykonuje kopii zapasowej innych obiektów SQL, takich jak procedury przechowywane, i nie wykonuje odpowiednik operacji przywracania szybko.

Aby uzyskać jedną zalecaną technikę tworzenia kopii zapasowej, zobacz [Kopiowanie bazy danych SQL platformy Azure](sql-database-copy.md).

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Czy synchronizacja danych może synchronizować zaszyfrowane tabele i kolumny

- Jeśli baza danych używa zawsze zaszyfrowane, można synchronizować tylko tabele i kolumny, które *nie* są szyfrowane. Zaszyfrowane kolumny nie można zsynchronizować, ponieważ synchronizacja danych nie może odszyfrować danych.
- Jeśli kolumna używa szyfrowania na poziomie kolumny (CLE), można zsynchronizować kolumnę, o ile rozmiar wiersza jest mniejszy niż maksymalny rozmiar 24 Mb. Synchronizacja danych traktuje kolumnę zaszyfrowaną przez klucz (CLE) jako normalne dane binarne. Aby odszyfrować dane na innych członków synchronizacji, musisz mieć ten sam certyfikat.

### <a name="is-collation-supported-in-sql-data-sync"></a>Czy sortowanie jest obsługiwane w synchronizacji danych SQL

Tak. Sql Data Sync obsługuje sortowanie w następujących scenariuszach:

- Jeśli wybrane tabele schematów synchronizacji nie są jeszcze w bazach danych centrum lub członków, podczas wdrażania grupy synchronizacji usługa automatycznie tworzy odpowiednie tabele i kolumny z ustawieniami sortowania wybranymi w pustych docelowych bazach danych.
- Jeśli tabele, które mają być synchronizowane już istnieją w bazach danych centrum i członków, SQL Data Sync wymaga, aby kolumny klucza podstawowego miały takie samo sortowanie między koncentratorem i bazami danych członkowskich, aby pomyślnie wdrożyć grupę synchronizacji. Nie ma żadnych ograniczeń sortowania dla kolumn innych niż kolumny klucza podstawowego.

### <a name="is-federation-supported-in-sql-data-sync"></a>Czy federacja jest obsługiwana w synchronizacji danych SQL

Federacjowa główna baza danych może być używana w usłudze synchronizacji danych SQL bez żadnych ograniczeń. Nie można dodać punktu końcowego federacyjnej bazy danych do bieżącej wersji synchronizacji danych SQL.

## <a name="next-steps"></a>Następne kroki

### <a name="update-the-schema-of-a-synced-database"></a>Aktualizowanie schematu zsynchronizowanej bazy danych

Czy trzeba zaktualizować schemat bazy danych w grupie synchronizacji? Zmiany schematu nie są automatycznie replikowane. W przypadku niektórych rozwiązań zobacz następujące artykuły:

- [Automatyzacja replikacji zmian schematu w usłudze Azure SQL Data Sync](sql-database-update-sync-schema.md)
- [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>Monitorowanie i rozwiązywanie problemów

Czy synchronizacja danych SQL działa zgodnie z oczekiwaniami? Aby monitorować aktywność i rozwiązywać problemy, zobacz następujące artykuły:

- [Monitorowanie synchronizacji danych SQL usługi Azure za pomocą dzienników usługi Azure Monitor](sql-database-sync-monitor-oms.md)
- [Troubleshoot issues with Azure SQL Data Sync (Rozwiązywanie problemów z usługą Azure SQL Data Sync)](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Dowiedz się więcej o bazie danych SQL usługi Azure

Aby uzyskać więcej informacji na temat bazy danych SQL, zobacz następujące artykuły:

- [Omówienie usługi SQL Database](sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
