---
title: Rozwiązywanie typowych problemów z połączeniem z usługą Azure SQL Database
description: Zawiera instrukcje dotyczące rozwiązywania problemów z połączeniami Azure SQL Database i rozwiązywania problemów związanych z innymi Azure SQL Database lub wystąpieniami zarządzanymi przez usługę Azure SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2020
ms.openlocfilehash: aa4bcee7a2eaf5e6ec11b9066ed6eca6b33bdba1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284129"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Rozwiązywanie problemów z łącznością i innych błędów przy użyciu Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Komunikaty o błędach są wyświetlane, gdy połączenie z Azure SQL Database lub wystąpienie zarządzane Azure SQL nie powiedzie się. Te problemy z połączeniem mogą być spowodowane ponowną konfiguracją, ustawieniami zapory, przekroczeniem limitu czasu połączenia, niepoprawnymi informacjami logowania lub niepowodzeniem zastosowania najlepszych praktyk i projektowania podczas procesu [projektowania aplikacji](develop-overview.md) . Ponadto jeśli Osiągnięto maksymalny Azure SQL Database limit zasobów wystąpień zarządzanych przez program SQL Server, nie będzie można nawiązać połączenia.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Komunikaty o błędach przejściowych (40197, 40613 i inne)

Infrastruktura platformy Azure ma możliwość dynamicznego ponownego konfigurowania serwerów w przypadku wystąpienia dużych obciążeń w usłudze SQL Database.  Takie zachowanie dynamiczne może spowodować utratę połączenia z bazą danych lub wystąpieniem programu klienckiego. Ten rodzaj warunku błędu nazywa się *błędem przejściowym*. Zdarzenia ponownej konfiguracji bazy danych występują ze względu na planowane zdarzenie (na przykład uaktualnienie oprogramowania) lub niezaplanowane zdarzenie (na przykład awaria procesu lub Równoważenie obciążenia). Większość zdarzeń ponownej konfiguracji jest zwykle krótkoterminowa i powinna być wykonana w mniej niż 60 sekund. Jednak te zdarzenia mogą czasami trwać dłużej, na przykład wtedy, gdy duża transakcja powoduje długotrwałe odzyskiwanie. W poniższej tabeli wymieniono różne błędy przejściowe, które aplikacje mogą odbierać podczas nawiązywania połączenia z usługą SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Lista kodów błędów przejściowych

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 4060 |16 |Nie można otworzyć bazy danych "%. &#x2a;ls" żądanej przez nazwę logowania. Logowanie nie powiodło się. Aby uzyskać więcej informacji, zobacz [błędy 4000 do 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Usługa napotkała błąd podczas przetwarzania żądania. Spróbuj ponownie. Kod błędu% d.<br/><br/>Ten błąd występuje, gdy usługa nie działa z powodu uaktualnień oprogramowania lub sprzętu, awarii sprzętu lub innych problemów z trybem failover. Kod błędu (% d) osadzony w komunikacie o błędzie 40197 zawiera dodatkowe informacje o rodzaju awarii lub pracy awaryjnej, które wystąpiły. Niektóre przykłady kodów błędów są osadzone w komunikacie o błędzie 40197 to 40020, 40143, 40166 i 40540.<br/><br/>Ponowne łączenie automatycznie nawiązuje połączenie z dobrą kopią bazy danych. Aplikacja musi obsłużyć błąd 40197, zarejestrować osadzony kod błędu (% d) w komunikacie w celu rozwiązywania problemów, a następnie spróbować ponownie nawiązać połączenie z SQL Database, dopóki zasoby nie będą dostępne, a połączenie zostanie nawiązane. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Usługa jest obecnie zajęta. Ponów żądanie po 10 sekundach. Identyfikator zdarzenia:% ls. Kod:% d. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md).|
| 40613 |17 |Baza danych "%. &#x2a;ls" na serwerze "%. &#x2a;ls" jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną i podaj identyfikator śledzenia sesji "%. &#x2a;ls".<br/><br/> Ten błąd może wystąpić, jeśli istnieje już istniejące dedykowane połączenie administratora (DAC) dla bazy danych. Aby uzyskać więcej informacji, zobacz [Błędy przejściowe](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Nie można przetworzyć żądania. Za mało zasobów, aby przetworzyć żądanie.<br/><br/>Usługa jest obecnie zajęta. Spróbuj ponownie wykonać żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md). |
| 49919 |16 |Nie można przetworzyć żądania Create lub Update. Zbyt wiele operacji tworzenia lub aktualizacji w toku dla subskrypcji "% LD".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań utworzenia lub aktualizacji dla Twojej subskrypcji lub serwera. Żądania są obecnie blokowane na potrzeby optymalizacji zasobów. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) kwerendy dla oczekujących operacji. Zaczekaj na ukończenie oczekujących żądań utworzenia lub aktualizacji lub Usuń jedno z oczekujących żądań, a następnie ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md). |
| 49920 |16 |Nie można przetworzyć żądania. Zbyt wiele operacji w toku dla subskrypcji "% LD".<br/><br/>Usługa jest zajęta przetwarzaniem wielu żądań dla tej subskrypcji. Żądania są obecnie blokowane na potrzeby optymalizacji zasobów. [Sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) kwerendy dla stanu operacji. Zaczekaj na zakończenie oczekujących żądań lub Usuń jedno z oczekujących żądań, a następnie ponów żądanie później. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md). |
| 4221 |16 |Logowanie do odczytu i pomocnicze nie powiodło się z powodu długiego oczekiwania na "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Replika jest niedostępna do logowania, ponieważ brakuje wersji wiersza dla transakcji, które były odtwarzane podczas odtwarzania repliki. Problem można rozwiązać przez wycofanie lub zatwierdzenie aktywnych transakcji w replice podstawowej. Wystąpienia tego warunku można zminimalizować, unikając długich transakcji zapisu na serwerze podstawowym. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki rozwiązywania przejściowych problemów z łącznością

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://azure.microsoft.com/status) pod kątem znanych przestojów, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje łączące się z usługą w chmurze, takie jak Azure SQL Database, powinny oczekiwać okresowych zdarzeń ponownej konfiguracji i wdrożyć logikę ponawiania w celu obsługi tych błędów zamiast obsłużyć te błędy jako błędy aplikacji dla użytkowników.
3. Jako że baza danych zbliża się do limitów zasobów, może wydawać się przejściowy problem z łącznością. Zobacz [limity zasobów](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Jeśli problemy z łącznością będą kontynuowane lub jeśli czas, przez który aplikacja napotyka błąd, przekracza 60 sekund lub Jeśli zobaczysz wiele wystąpień błędu w danym dniu, Utwórz żądanie pomocy technicznej platformy Azure, wybierając pozycję **Uzyskaj pomoc** techniczną w witrynie [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementowanie logiki ponawiania

Zdecydowanie zaleca się, aby program kliencki miał logikę ponowień, aby można było ponownie nawiązać połączenie po podaniu błędu przejściowego.  Zalecamy opóźnienie przez 5 sekund przed pierwszym ponowieniem próby. Ponawianie próby po opóźnieniu krótszym niż 5 sekund ryzyka przeciążania usługi w chmurze. Dla każdej kolejnej ponownej próby opóźnienie powinno się zwiększyć wykładniczo, maksymalnie 60 sekund.

Przykłady kodu logiki ponowień można znaleźć w temacie:

- [Nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą środowiska ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [nawiązywanie połączeń odpornych na błędy z bazą danych SQL za pomocą języka PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Dodatkowe informacje na temat obsługi błędów przejściowych w aplikacji można znaleźć w [temacie Rozwiązywanie problemów z błędami połączeń przejściowych do SQL Database](troubleshoot-common-connectivity-issues.md)

Omówienie *okresu blokowania* dla klientów korzystających z usługi ADO.NET jest dostępny w [puli połączeń (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Wystąpił błąd związany z siecią lub wystąpieniem podczas ustanawiania połączenia z serwerem

Problem występuje, jeśli aplikacja nie może nawiązać połączenia z serwerem.

Aby rozwiązać ten problem, spróbuj wykonać czynności opisane w sekcji [kroki rozwiązywania typowych problemów z połączeniami](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Serwer/wystąpienie nie zostało odnalezione lub nie było dostępne (błędy 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Błąd 26: Wystąpił błąd podczas lokalizowania określonego serwera

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Błąd 40: nie można otworzyć połączenia z serwerem

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Błąd 10053: Wystąpił błąd poziomu transportu podczas otrzymywania wyników z serwera

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Te problemy występują, jeśli aplikacja nie może nawiązać połączenia z serwerem.

Aby rozwiązać te problemy, spróbuj wykonać czynności opisane w sekcji [kroki rozwiązywania typowych problemów z połączeniami](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Nie można nawiązać połączenia z serwerem z powodu problemów z zaporą

### <a name="error-40615-cannot-connect-to--servername-"></a>Błąd 40615: nie można nawiązać połączenia z serwerem < ServerName >

Aby rozwiązać ten problem, [Skonfiguruj ustawienia zapory na SQL Database przy użyciu Azure Portal](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Błąd 5: nie można nawiązać połączenia z serwerem < ServerName >

Aby rozwiązać ten problem, upewnij się, że port 1433 jest otwarty dla połączeń wychodzących wszystkich zapór między klientem a Internetem.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Nie można zalogować się na serwerze (błędy 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Nie można zalogować użytkownika "< nazwy użytkownika >"

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Aby rozwiązać ten problem, skontaktuj się z administratorem usługi w celu podania prawidłowej nazwy użytkownika i hasła.

Zazwyczaj administrator usługi może wykonać następujące kroki, aby dodać poświadczenia logowania:

1. Zaloguj się do serwera przy użyciu programu SQL Server Management Studio (SSMS).
2. Uruchom następujące zapytanie SQL, aby sprawdzić, czy nazwa logowania jest wyłączona:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Jeśli odpowiadająca nazwa jest wyłączona, włącz ją przy użyciu następującej instrukcji:

   ```sql
   Alter login <User name> enable
   ```

4. Jeśli nazwa użytkownika logowania SQL nie istnieje, utwórz ją, wykonując następujące czynności:

   1. W programie SSMS kliknij dwukrotnie pozycję **zabezpieczenia** , aby ją rozwinąć.
   2. Kliknij prawym przyciskiem myszy pozycję **logowania**, a następnie wybierz pozycję **Nowa nazwa logowania**.
   3. W wygenerowanym skrypcie z symbolami zastępczymi Edytuj i uruchom następujące zapytanie SQL:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Kliknij dwukrotnie pozycję **baza danych**.
6. Wybierz bazę danych, do której chcesz przyznać uprawnienia użytkownika.
7. Kliknij dwukrotnie pozycję **zabezpieczenia**.
8. Kliknij prawym przyciskiem myszy pozycję **Użytkownicy**, a następnie wybierz polecenie **nowy użytkownik**.
9. W wygenerowanym skrypcie z symbolami zastępczymi Edytuj i uruchom następujące zapytanie SQL:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Można również użyć `sp_addrolemember` do mapowania określonych użytkowników do określonych ról bazy danych.

Aby uzyskać więcej informacji, zobacz [Zarządzanie bazami danych i nazwami logowania w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Błędy upłynął limit czasu połączenia

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): upłynął limit czasu połączenia

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. Data. SqlClient. SqlException (0x80131904): upłynął limit czasu

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. Data. Entity. Core. EntityException: źródłowy dostawca nie powiódł się podczas otwierania

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Nie można nawiązać połączenia z nazwą serwera < >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Te wyjątki mogą wystąpić z powodu problemów z połączeniem lub zapytaniem. Aby potwierdzić, że ten błąd jest spowodowany problemami z łącznością, zobacz [potwierdzenie, czy błąd jest spowodowany problemem z łącznością](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Przekroczono limit czasu połączenia, ponieważ aplikacja nie może nawiązać połączenia z serwerem. Aby rozwiązać ten problem, spróbuj wykonać czynności opisane w sekcji [kroki rozwiązywania typowych problemów z połączeniami](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Błędy ładu zasobów

### <a name="error-10928-resource-id-d"></a>Błąd 10928: Identyfikator zasobu:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Aby obejść ten problem, wypróbuj jedną z następujących metod:

- Sprawdź, czy są wykonywane długotrwałe zapytania.

  > [!NOTE]
  > Jest to minimalistyczny podejście, które może nie rozwiązać problemu.

1. Uruchom następujące zapytanie SQL, aby sprawdzić widok [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) , aby wyświetlić wszystkie żądania blokowania:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Określ **bufor wejściowy** dla bloku głównego.
3. Dostrajanie zapytania bloku nagłówkowego.

   Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

Jeśli baza danych konsekwentnie osiągnie swój limit, niezależnie od blokowania i długotrwałych zapytań, rozważ uaktualnienie [do wersji o](https://azure.microsoft.com/pricing/details/sql-database/)większej liczbie zasobów.

Aby uzyskać więcej informacji na temat dynamicznych widoków zarządzania, zobacz [dynamiczne zarządzanie dynamicznymi widokami](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Aby uzyskać więcej informacji na temat limitów baz danych, zobacz  [SQL Database limitów zasobów dla serwerów](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Błąd 10929: Identyfikator zasobu: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Błąd 40501: usługa jest obecnie zajęta

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Jest to błąd ograniczania aparatu, wskazujący, że limity zasobów są przekraczane.

Aby uzyskać więcej informacji na temat limitów zasobów, zobacz [logiczne limity zasobów programu SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Błąd 40544: osiągnięto limit przydziału rozmiaru bazy danych

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Ten błąd występuje, gdy osiągnięto limit przydziału rozmiaru bazy danych.

Poniższe kroki mogą pomóc w obejść problemu lub zapewnieniu dodatkowych opcji:

1. Sprawdź bieżący rozmiar bazy danych przy użyciu pulpitu nawigacyjnego w Azure Portal.

   > [!NOTE]
   > Aby określić, które tabele zużywają najwięcej miejsca, i dlatego potencjalni kandydaci do oczyszczenia, uruchom następujące zapytanie SQL:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Jeśli bieżący rozmiar nie przekracza maksymalnego rozmiaru obsługiwanego przez daną wersję, można użyć polecenia ALTER DATABASE, aby zwiększyć ustawienie rozmiaru.
3. Jeśli baza danych jest już poza maksymalnym obsługiwanym rozmiarem wersji, spróbuj wykonać co najmniej jedną z następujących czynności:

   - Wykonaj normalne działania czyszczenia bazy danych. Na przykład Wyczyść niepożądane dane przy użyciu funkcji Truncate/DELETE lub Przenieś dane przy użyciu programu SQL Server Integration Services (SSIS) lub narzędzia do kopiowania masowego (bcp).
   - Utwórz partycję lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami.
   - Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) i [skalowanie zasobów puli elastycznej](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Błąd 40549: sesja została zakończona, ponieważ masz długotrwałą transakcję

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Jeśli ten błąd wystąpi wielokrotnie, spróbuj rozwiązać problem, wykonując następujące czynności:

1. Sprawdź widok sys.dm_exec_requests, aby wyświetlić otwarte sesje o dużej wartości dla kolumny total_elapsed_time. Wykonaj to sprawdzenie, uruchamiając następujący skrypt SQL:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Określ bufor wejściowy dla długotrwałego zapytania.
3. Dostosuj zapytanie.

Należy również rozważyć przetwarzanie wsadowe zapytań. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Błąd 40551: sesja została przerwana z powodu nadmiernego użycia bazy danych TEMPDB

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Aby obejść ten problem, wykonaj następujące kroki:

1. Zmień zapytania, aby zmniejszyć użycie tymczasowego obszaru tabeli.
2. Usuń obiekty tymczasowe, gdy nie są już potrzebne.
3. Obcinanie tabel lub usuwanie nieużywanych tabel.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Błąd 40552: sesja została przerwana z powodu nadmiernego użycia miejsca w dzienniku transakcji

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Aby rozwiązać ten problem, wypróbuj poniższe metody:

- Problem może wystąpić z powodu operacji INSERT, Update lub DELETE.
Spróbuj zmniejszyć liczbę wierszy, które są bezpośrednio obsługiwane przez zaimplementowanie operacji wsadowych lub dzielenia na wiele mniejszych transakcji.
- Problem może wystąpić z powodu operacji odbudowywania indeksu. Aby obejść ten problem, upewnij się, że liczba wierszy, których dotyczy ta tabela * (średni rozmiar pola, które jest aktualizowane w bajtach + 80) < 2 gigabajty (GB).

  > [!NOTE]
  > W przypadku ponownego kompilowania indeksu średni rozmiar aktualizowanego pola powinien być zastępowany przez średni rozmiar indeksu.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Błąd 40553: sesja została przerwana z powodu nadmiernego użycia pamięci

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Aby obejść ten problem, spróbuj zoptymalizować zapytanie.

Aby uzyskać szczegółową procedurę rozwiązywania problemów, zobacz [czy moje zapytanie działa prawidłowo w chmurze?](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabela dodatkowych komunikatów o błędach nadzoru zasobów

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 10928 |20 |Identyfikator zasobu:% d. Limit% s dla bazy danych to% d i został osiągnięty. Aby uzyskać więcej informacji, zobacz [SQL Database limitów zasobów dla jednej i puli baz danych](resource-limits-logical-server.md).<br/><br/>Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2.<br/><br/>Aby uzyskać więcej informacji na temat tego błędu i sposobu jego rozwiązania, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md). |
| 10929 |20 |Identyfikator zasobu:% d. Minimalna gwarancja% s wynosi% d, maksymalny limit wynosi% d, a bieżące użycie bazy danych to% d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż% d dla tej bazy danych. Identyfikator zasobu wskazuje zasób, który osiągnął limit. W przypadku wątków roboczych identyfikator zasobu = 1. W przypadku sesji identyfikator zasobu = 2. Aby uzyskać więcej informacji, zobacz: <br/>&bull;&nbsp; [Limity zasobów logicznego serwera SQL](resource-limits-logical-server.md)<br/>&bull;Limity oparte na jednostkach &nbsp; [DTU dla pojedynczych baz danych](service-tiers-dtu.md)<br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md). <br/>W przeciwnym razie spróbuj ponownie później. |
| 40544 |20 |Osiągnięto limit przydziału rozmiaru bazy danych. Utwórz partycję lub Usuń dane, Porzuć indeksy lub zapoznaj się z dokumentacją, aby zapoznać się z możliwymi rozwiązaniami. Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).|
| 40549 |16 |Sesja została przerwana, ponieważ masz długotrwałą transakcję. Spróbuj skrócić swoją transakcję. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](../performance-improve-use-batching.md).|
| 40550 |16 |Sesja została przerwana, ponieważ uzyskała zbyt wiele blokad. Spróbuj odczytać lub zmodyfikować mniejszą liczbę wierszy w jednej transakcji. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](../performance-improve-use-batching.md).|
| 40551 |16 |Sesja została przerwana z powodu nadmiernego `TEMPDB` użycia. Spróbuj zmodyfikować zapytanie, aby zmniejszyć użycie obszaru tabeli tymczasowej.<br/><br/>W przypadku korzystania z obiektów tymczasowych należy zaoszczędzić miejsce w `TEMPDB` bazie danych, upuszczając obiekty tymczasowe, gdy nie są już potrzebne przez sesję. Aby uzyskać więcej informacji na temat użycia tempdb w SQL Database, zobacz [baza danych tempdb w SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sesja została przerwana z powodu nadmiernego wykorzystania miejsca w dzienniku transakcji. Spróbuj zmodyfikować mniejszą liczbę wierszy w jednej transakcji. Aby uzyskać informacje na temat tworzenia pakietów wsadowych, zobacz [jak używać usługi Batch w celu ulepszania SQL Database aplikacji](../performance-improve-use-batching.md).<br/><br/>Jeśli wykonujesz operacje wstawiania zbiorczego za pomocą `bcp.exe` Narzędzia lub `System.Data.SqlClient.SqlBulkCopy` klasy, spróbuj użyć `-b batchsize` opcji lub, `BatchSize` Aby ograniczyć liczbę wierszy skopiowanych do serwera w każdej transakcji. W przypadku odbudowywania indeksu przy użyciu `ALTER INDEX` instrukcji spróbuj użyć `REBUILD WITH ONLINE = ON` opcji. Aby uzyskać informacje o rozmiarach dzienników transakcji dla modelu zakupu rdzeń wirtualny, zobacz: <br/>&bull;&nbsp; [limity rdzeń wirtualny dla pojedynczych baz danych](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Limity zasobów wystąpienia zarządzanego usługi Azure SQL](../managed-instance/resource-limits.md).|
| 40553 |16 |Sesja została przerwana z powodu nadmiernego użycia pamięci. Spróbuj zmodyfikować zapytanie, aby przetworzyć mniej wierszy.<br/><br/>Zmniejszenie liczby `ORDER BY` i `GROUP BY` operacji w kodzie Transact-SQL zmniejsza wymagania dotyczące pamięci zapytania. Aby uzyskać skalowanie bazy danych, zobacz [skalowanie zasobów pojedynczych baz danych](single-database-scale.md) i [skalowanie zasobów puli elastycznej](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Błędy puli elastycznej

Następujące błędy są związane z tworzeniem i używaniem pul elastycznych:

| Kod błędu | Ważność | Opis | Akcja naprawcza |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Osiągnięto limit magazynowania elastycznej puli. Użycie magazynu dla puli elastycznej nie może przekroczyć (% d) MB. Podjęto próbę zapisu danych w bazie danych, gdy osiągnięto limit magazynowania puli elastycznej. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md). <br/> |Rozważ zwiększenie DTU i/lub dodanie magazynu do puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit magazynowania, zmniejszyć ilość miejsca używanego przez pojedyncze bazy danych w puli elastycznej lub usunąć bazy danych z puli elastycznej. Aby uzyskać skalowanie puli elastycznej, zobacz [skalowanie zasobów w puli elastycznej](elastic-pool-scale.md).|
| 10929 | 16 |Minimalna gwarancja% s wynosi% d, maksymalny limit wynosi% d, a bieżące użycie bazy danych to% d. Jednak serwer jest obecnie zbyt zajęty, aby obsługiwać żądania większe niż% d dla tej bazy danych. Aby uzyskać informacje na temat limitów zasobów, zobacz: <br/>&bull;&nbsp; [Limity dla pul elastycznych](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [limity dla pul elastycznych rdzeń wirtualny](resource-limits-vcore-elastic-pools.md). <br/> W przeciwnym razie spróbuj ponownie później. Wartość DTU/rdzeń wirtualny min na bazę danych; Maksymalna liczba jednostek DTU/rdzeń wirtualny na bazę danych. Całkowita liczba współbieżnych procesów roboczych (żądań) dla wszystkich baz danych w puli elastycznej podjęła próbę przekroczenia limitu puli. |Rozważ zwiększenie DTU lub rdzeni wirtualnych puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit procesów roboczych lub usunąć bazy danych z puli elastycznej. |
| 40844 | 16 |Baza danych "% ls" na serwerze "% ls" jest bazą danych wersji "% ls" w puli elastycznej i nie może mieć relacji ciągłego kopiowania.  |Nie dotyczy |
| 40857 | 16 |Nie znaleziono puli elastycznej dla serwera: "% ls", Nazwa puli elastycznej: "% ls". Określona Pula elastyczna nie istnieje na określonym serwerze. | Podaj prawidłową nazwę puli elastycznej. |
| 40858 | 16 |Pula elastyczna "% ls" już istnieje na serwerze: "% ls". Określona Pula elastyczna już istnieje na określonym serwerze. | Podaj nazwę nowej puli elastycznej. |
| 40859 | 16 |Pula elastyczna nie obsługuje warstwy usług "% ls". Określona warstwa usług nie jest obsługiwana w przypadku aprowizacji elastycznej puli. |Wprowadź poprawną wersję lub pozostaw pustą warstwę usługi, aby użyć domyślnej warstwy usług. |
| 40860 | 16 |Kombinacja elastycznej puli "% ls" i celu usługi "% ls" jest nieprawidłowa. Pulę elastyczną i warstwę usług można określić razem tylko wtedy, gdy typ zasobu jest określony jako "ElasticPool". |Określ poprawną kombinację elastycznej puli i warstwy usług. |
| 40861 | 16 |Wersja bazy danych "%. *element ls ' nie może być inny niż warstwa usług puli elastycznej, która jest "%.* ls '. Wersja bazy danych różni się od warstwy usług puli elastycznej. |Nie określaj wersji bazy danych, która różni się od warstwy usług puli elastycznej.  Należy pamiętać, że nie trzeba określać wersji bazy danych. |
| 40862 | 16 |Jeśli określono cel usługi puli elastycznej, należy określić nazwę puli elastycznej. Cel usługi elastycznej puli nie identyfikuje jednoznacznie puli elastycznej. |Określ nazwę puli elastycznej, jeśli jest używany cel usługi puli elastycznej. |
| 40864 | 16 |DTU dla puli elastycznej musi wynosić co najmniej (% d) DTU dla warstwy usług "%. * ls". Podjęto próbę ustawienia DTU dla elastycznej puli poniżej limitu minimalnego. |Spróbuj ustawić co najmniej minimalny limit DTU dla puli elastycznej. |
| 40865 | 16 |DTU dla puli elastycznej nie może przekroczyć (% d) DTU dla warstwy usług "%. * ls". Podjęto próbę ustawienia DTU dla elastycznej puli powyżej maksymalnego limitu. |Ponowienie próby ustawienia DTU dla puli elastycznej nie przekracza maksymalnego limitu. |
| 40867 | 16 |Maksymalna liczba jednostek DTU na bazę danych musi wynosić co najmniej (% d) w przypadku warstwy usług "%. * ls". Podjęto próbę ustawienia maksymalnej liczby jednostek DTU na bazę danych poniżej obsługiwanego limitu. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40868 | 16 |Maksymalna liczba jednostek DTU na bazę danych nie może przekroczyć (% d) dla warstwy usług "%. * ls". Podjęto próbę ustawienia maksymalnej liczby jednostek DTU na bazę danych poza obsługiwanym limitem. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40870 | 16 |Minimalna wartość DTU na bazę danych nie może przekroczyć (% d) dla warstwy usług "%. * ls". Podjęto próbę ustawienia minimalnej liczby jednostek DTU na bazę danych poza obsługiwanym limitem. | Rozważ użycie warstwy usług puli elastycznej, która obsługuje odpowiednie ustawienie. |
| 40873 | 16 |Liczba baz danych (% d) i minimalna wartość DTU na bazę danych (% d) nie może przekraczać DTU puli elastycznej (% d). Podjęto próbę określenia minimalnej liczby jednostek DTU dla baz danych w puli elastycznej, które przekraczają DTU puli elastycznej. | Rozważ zwiększenie DTU puli elastycznej lub zmniejszenie minimalnej liczby jednostek DTU na bazę danych, lub Zmniejsz liczbę baz danych w puli elastycznej. |
| 40877 | 16 |Nie można usunąć puli elastycznej, jeśli nie zawiera ona żadnych baz danych. Pula elastyczna zawiera co najmniej jedną bazę danych i dlatego nie można jej usunąć. |Usuń bazy danych z elastycznej puli, aby je usunąć. |
| 40881 | 16 |Osiągnięto limit liczby baz danych dla puli elastycznej "%. * ls".  Limit liczby baz danych dla puli elastycznej nie może przekroczyć (% d) dla puli elastycznej z (% d) DTU. Podjęto próbę utworzenia lub dodania bazy danych do puli elastycznej, gdy osiągnięto limit liczby baz danych w puli elastycznej. | Rozważ zwiększenie DTU puli elastycznej, jeśli jest to możliwe, aby zwiększyć jej limit bazy danych, lub usuń bazy danych z puli elastycznej. |
| 40889 | 16 |Nie można zmniejszyć DTU lub limitu magazynu dla puli elastycznej "%. * ls", ponieważ nie zapewnia ona wystarczającej ilości miejsca w magazynie dla swoich baz danych. Podjęto próbę zmniejszenia limitu magazynowania elastycznej puli poniżej użycia magazynu. | Rozważ zmniejszenie użycia magazynu dla poszczególnych baz danych w elastycznej puli lub usuń bazy danych z puli, aby zmniejszyć limit DTU lub magazynu. |
| 40891 | 16 |Minimalna wartość DTU na bazę danych (% d) nie może przekroczyć maksymalnej wartości DTU na bazę danych (% d). Podjęto próbę ustawienia minimalnej wartości DTU na bazę danych wyższą niż maksymalna wartość DTU na bazę danych. |Upewnij się, że minimalna wartość DTU na bazy danych nie przekracza maksymalnej wartości DTU na bazę danych. |
| TBD | 16 |Rozmiar magazynu dla pojedynczej bazy danych w puli elastycznej nie może przekroczyć maksymalnego rozmiaru dozwolonego przez pulę elastyczną warstwy usług "%. * ls". Maksymalny rozmiar bazy danych przekracza maksymalny rozmiar dozwolony przez warstwę usługi elastycznej puli. |Ustaw maksymalny rozmiar bazy danych w granicach maksymalnego rozmiaru dozwolonego przez warstwę usług Elastic Pool. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Nie można otworzyć bazy danych "Master" żądanej przez nazwę logowania. Logowanie nie powiodło się

Ten problem występuje, ponieważ konto nie ma uprawnień dostępu do bazy danych Master. Domyślnie program SQL Server Management Studio (SSMS) próbuje nawiązać połączenie z bazą danych Master.

Aby rozwiązać ten problem, wykonaj poniższe czynności:

1. Na ekranie logowania programu SSMS wybierz pozycję **Opcje**, a następnie wybierz pozycję **Właściwości połączenia**.
2. W polu **Połącz z bazą danych** Wprowadź domyślną nazwę bazy danych użytkownika jako domyślną bazę danych logowania, a następnie wybierz pozycję **Połącz**.

   ![Connection properties (Właściwości połączenia)](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Sprawdź, czy błąd jest spowodowany problemem z łącznością

Aby sprawdzić, czy błąd jest spowodowany problemem z łącznością, przejrzyj ślad stosu dla ramek, które pokazują wywołania, aby otworzyć połączenie podobne do następujących (należy zwrócić uwagę na odwołanie do klasy **SqlConnection** ):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

Gdy wyjątek jest wyzwalany przez problemy z kwerendą, zobaczysz stos wywołań podobny do poniższego (należy zwrócić uwagę na odwołanie do klasy **SqlCommand** ). W tej sytuacji [Dostosuj swoje zapytania](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Dodatkowe wskazówki dotyczące wydajności dostrajania można znaleźć w następujących zasobach:

- [Jak zarządzać indeksami i statystykami SQL platformy Azure](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Ręczne dostrajanie wydajności zapytań w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [Monitorowanie Azure SQL Database wydajności przy użyciu dynamicznych widoków zarządzania](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [Działanie magazynu zapytań w Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Steps to fix common connection issues (Kroki umożliwiające rozwiązywanie typowych problemów z połączeniem)

1. Upewnij się, że protokół TCP/IP jest włączony jako protokół klienta na serwerze aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie protokołów klienta](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Na serwerach aplikacji, na których nie zainstalowano narzędzi SQL, upewnij się, że protokół TCP/IP jest włączony, uruchamiając polecenie **cliconfg.exe** (SQL Server narzędzia sieciowe klienta).
2. Sprawdź parametry połączenia aplikacji, aby upewnić się, że jest prawidłowo skonfigurowana. Na przykład upewnij się, że parametry połączenia określają prawidłowy port (1433) i w pełni kwalifikowaną nazwę serwera.
Zobacz [Uzyskiwanie informacji o połączeniu](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Spróbuj zwiększyć wartość limitu czasu połączenia. Zalecamy użycie limitu czasu połączenia wynoszącego co najmniej 30 sekund.
4. Przetestuj łączność między serwerem aplikacji a Azure SQL Database za pomocą programu [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), pliku UDL, polecenia ping lub Telnet. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z łącznością](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) i [Diagnostyka w przypadku problemów z łącznością](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Krok rozwiązywania problemów umożliwia również przetestowanie łączności na innym komputerze klienckim.

5. Najlepszym rozwiązaniem jest upewnienie się, że logika ponowienia jest na miejscu. Aby uzyskać więcej informacji o logice ponowień, zobacz [Rozwiązywanie błędów przejściowych i błędy połączeń w SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Jeśli te kroki nie rozwiążą problemu, spróbuj zebrać więcej danych, a następnie skontaktuj się z pomocą techniczną. Jeśli aplikacja jest usługą w chmurze, Włącz rejestrowanie. Ten krok zwraca sygnaturę czasową UTC błędu. Ponadto SQL Database zwraca identyfikator śledzenia. [Usługi pomocy technicznej firmy Microsoft](https://azure.microsoft.com/support/options/) mogą korzystać z tych informacji.

Aby uzyskać więcej informacji na temat włączania rejestrowania, zobacz [Włączanie rejestrowania diagnostycznego dla aplikacji w Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Następne kroki

- [Architektura łączności Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
- [Azure SQL Database i kontrola dostępu do sieci w usłudze Azure Synapse Analytics](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
