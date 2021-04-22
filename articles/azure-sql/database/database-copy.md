---
title: Kopiowanie bazy danych
description: Utwórz spójną transakcyjnie kopię istniejącej bazy danych w programie Azure SQL Database na tym samym lub innym serwerze.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: b7084ef045d14b9715c41bb9ffa483d1f2f7bedf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865157"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopiowanie transakcyjnie spójnej kopii bazy danych w programie Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database udostępnia kilka metod tworzenia kopii istniejącej [](single-database-overview.md) bazy danych na tym samym lub innym serwerze. Bazę danych można skopiować przy użyciu języka Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub języka T-SQL.

## <a name="overview"></a>Omówienie

Kopia bazy danych jest spójną transakcyjnie migawką źródłowej bazy danych od punktu w czasie po zainicjowaniu żądania kopiowania. Możesz wybrać ten sam serwer lub inny serwer dla kopii. Można również zachować nadmiarowość kopii zapasowej, warstwę usługi i rozmiar obliczeniowy źródłowej bazy danych lub użyć innej nadmiarowości magazynu kopii zapasowych i/lub rozmiaru obliczeniowego w tej samej lub innej warstwie usługi. Po zakończeniu kopiowania staje się w pełni funkcjonalną, niezależną bazą danych. Identyfikatory logowania, użytkownicy i uprawnienia w skopiowanej bazie danych są zarządzane niezależnie od źródłowej bazy danych. Kopia jest tworzona przy użyciu technologii replikacji geograficznej. Po zakończeniu rozmieszczania repliki połączenie replikacji geograficznej zostaje przerwane. Wszystkie wymagania dotyczące korzystania z replikacji geograficznej dotyczą również operacji kopiowania bazy danych. Aby uzyskać szczegółowe informacje, zobacz [Omówienie aktywnej replikacji](active-geo-replication-overview.md) geograficznej.

> [!NOTE]
> Azure SQL Database Nadmiarowość magazynu kopii zapasowych z możliwością konfigurowania jest obecnie dostępna w publicznej wersji zapoznawczej w Brazylii Południowej i ogólnie dostępna tylko w regionie świadczenia usługi Azure w regionie Azja Południowo-Wschodnia. Jeśli w wersji zapoznawczej źródłową bazę danych utworzono z nadmiarowością magazynu kopii zapasowych lokalnie nadmiarowych lub strefowo nadmiarowych, kopiowanie bazy danych na serwer w innym regionie świadczenia usługi Azure nie jest obsługiwane. 

## <a name="logins-in-the-database-copy"></a>Identyfikatory logowania w kopii bazy danych

Podczas kopiowania bazy danych na ten sam serwer te same dane logowania mogą być używane w obu bazach danych. Podmiot zabezpieczeń, za pomocą których jest kopiowana baza danych, staje się właścicielem bazy danych w nowej bazie danych.

Podczas kopiowania bazy danych na inny serwer podmiot zabezpieczeń, który zainicjował operację kopiowania na serwerze docelowym, staje się właścicielem nowej bazy danych.

Niezależnie od serwera docelowego wszyscy użytkownicy bazy danych, ich uprawnienia i identyfikatory zabezpieczeń (SID) są kopiowane do kopii bazy danych. Użycie [użytkowników zawartej](logins-create-manage.md) bazy danych w celu uzyskania dostępu do danych gwarantuje, że skopiowana baza danych ma te same poświadczenia użytkownika, dzięki czemu po zakończeniu kopiowania można natychmiast uzyskać do niej dostęp przy użyciu tych samych poświadczeń.

Jeśli do uzyskiwania dostępu do danych i kopiowania bazy danych na inny serwer używasz danych logowania na poziomie serwera, dostęp z wykorzystaniem danych logowania może nie zadziałać. Dzieje się tak, ponieważ na serwerze docelowym nie istnieją dane logowania lub ponieważ odpowiednie hasła i identyfikatory zabezpieczeń są inne. Aby dowiedzieć się więcej o zarządzaniu nazwami logowania podczas kopiowania bazy danych na inny serwer, zobacz How to manage Azure SQL Database security after disaster recovery (Jak zarządzać zabezpieczeniami po [odzyskiwaniu po awarii).](active-geo-replication-security-configure.md) Po zakończeniu operacji kopiowania na inny serwer i przed ponowną mapą innych użytkowników tylko dane logowania skojarzone z właścicielem bazy danych lub administrator serwera mogą zalogować się do skopiowanej bazy danych. Aby rozwiązać problemy z logowaniem i ustanowić dostęp do danych po zakończeniu operacji kopiowania, zobacz [Resolve logins (Rozwiązywanie problemów z nazwami logowania).](#resolve-logins)

## <a name="copy-using-the-azure-portal"></a>Kopiowanie za pomocą witryny Azure Portal

Aby skopiować bazę danych przy użyciu Azure Portal, otwórz stronę bazy danych, a następnie kliknij pozycję **Kopiuj**.

   ![Kopia bazy danych](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiowanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Aby skopiować bazę danych, skorzystaj z poniższych przykładów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell użyj polecenia cmdlet [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> Moduł PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.  Argumenty poleceń w module Az i modułach AzureRm są w znacznym stopniu identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Wprowadzenie do nowego modułu Azure PowerShell Az.](/powershell/azure/new-azureps-module-az)

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania w toku, usuń docelową bazę danych przy użyciu polecenia cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Aby uzyskać kompletny przykładowy skrypt programu PowerShell, zobacz Copy a database to a new server (Kopiowanie [bazy danych na nowy serwer).](scripts/copy-database-to-new-server-powershell.md)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania w toku, usuń docelową bazę danych za pomocą [polecenia az sql db delete.](/cli/azure/sql/db#az_sql_db_delete)

* * *

## <a name="copy-using-transact-sql"></a>Kopiowanie przy użyciu języka Transact-SQL

Zaloguj się do bazy danych master przy użyciu identyfikatora logowania administratora serwera lub identyfikatora logowania, który utworzył bazę danych, którą chcesz skopiować. Aby kopiowanie bazy danych powiodło się, nazwy logowania, które nie są administratorem serwera, muszą być członkami `dbmanager` roli. Aby uzyskać więcej informacji na temat identyfikatorów logowania i nawiązywania połączenia z serwerem, zobacz [Zarządzanie nazwami logowania](logins-create-manage.md).

Rozpocznij kopiowanie źródłowej bazy danych za pomocą pliku [CREATE DATABASE... AS COPY OF,](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) instrukcja. Instrukcja języka T-SQL będzie nadal działać do momentu ukończenia operacji kopiowania bazy danych.

> [!NOTE]
> Zakończenie instrukcji języka T-SQL nie powoduje zakończenia operacji kopiowania bazy danych. Aby zakończyć operację, upuść docelową bazę danych.
>

> [!IMPORTANT]
> Wybieranie nadmiarowości magazynu kopii zapasowych w przypadku korzystania z polecenia CREATE DATABASE języka T-SQL... Polecenie AS COPY OF nie jest jeszcze obsługiwane. 

### <a name="copy-to-the-same-server"></a>Kopiowanie na ten sam serwer

Zaloguj się do bazy danych master przy użyciu identyfikatora logowania administratora serwera lub identyfikatora logowania, który utworzył bazę danych, którą chcesz skopiować. Aby kopiowanie bazy danych powiodło się, nazwy logowania, które nie są administratorem serwera, muszą być członkami `dbmanager` roli.

To polecenie kopiuje bazę danych Database1 do nowej bazy danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopiowanie do elastycznej puli

Zaloguj się do bazy danych master przy użyciu identyfikatora logowania administratora serwera lub identyfikatora logowania, który utworzył bazę danych, którą chcesz skopiować. Aby kopiowanie bazy danych powiodło się, nazwy logowania, które nie są administratorem serwera, muszą być członkami `dbmanager` roli.

To polecenie kopiuje bazę danych Database1 do nowej bazy danych o nazwie Database2 w elastycznej puli o nazwie pool1. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

Database1 może być pojedynczą bazą danych lub bazą danych w puli. Kopiowanie między różnymi pulami warstw jest obsługiwane, ale niektóre kopie między warstwami nie zostaną pomyślnie skopiowane. Na przykład możesz skopiować pojedynczą lub elastyczną standardową bazę danych do puli ogólnego przeznaczenia, ale nie możesz skopiować standardowej elastycznej bazy danych do puli Premium. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Kopiowanie na inny serwer

Zaloguj się do głównej bazy danych serwera docelowego, na którym ma zostać utworzona nowa baza danych. Użyj identyfikatora logowania, który ma taką samą nazwę i hasło jak właściciel bazy danych źródłowej bazy danych na serwerze źródłowym. Identyfikator logowania na serwerze docelowym musi być również członkiem roli lub być identyfikatorem logowania `dbmanager` administratora serwera.

To polecenie kopiuje bazę danych Database1 na serwerze server1 do nowej bazy danych o nazwie Database2 na serwerze server2. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Zapory obu serwerów muszą być skonfigurowane tak, aby zezwalały na połączenia przychodzące z adresu IP klienta wystawiającego bazę danych CREATE DATABASE w języku T-SQL... JAKO KOPIĘ POLECENIA.

### <a name="copy-to-a-different-subscription"></a>Kopiowanie do innej subskrypcji

Kroki opisane w sekcji Copy [a SQL Database to a different server](#copy-to-a-different-server) (Kopiowanie bazy danych na inny serwer) mogą pomóc w skopiowaniu bazy danych na serwer w innej subskrypcji przy użyciu języka T-SQL. Upewnij się, że używasz identyfikatora logowania, który ma taką samą nazwę i hasło jak właściciel bazy danych źródłowej bazy danych. Ponadto identyfikator logowania musi być członkiem roli lub administratorem serwera na serwerze źródłowym `dbmanager` i docelowym.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> Interfejs [Azure Portal](https://portal.azure.com), program PowerShell i interfejs wiersza polecenia platformy Azure nie obsługują kopiowania bazy danych do innej subskrypcji.

> [!TIP]
> Kopiowanie bazy danych przy użyciu języka T-SQL obsługuje kopiowanie bazy danych z subskrypcji w innej dzierżawie platformy Azure. Jest to obsługiwane tylko w przypadku logowania się do serwera docelowego przy użyciu uwierzytelniania SQL.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorowanie postępu operacji kopiowania

Monitoruj proces kopiowania, odpytując widoki [sys.databases,](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)i [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Gdy kopiowanie jest w toku, kolumna **state_desc** widoku sys.databases dla nowej bazy danych jest ustawiona na **kopiowanie**.

* Jeśli kopiowanie nie powiedzie się, **state_desc** widoku sys.databases dla nowej bazy danych zostanie ustawiona na **WARTOŚĆ SUSPECT**. Wykonaj instrukcje DROP w nowej bazie danych i spróbuj ponownie później.
* Jeśli kopiowanie powiedzie się, kolumna **state_desc** widoku sys.databases dla nowej bazy danych zostanie ustawiona na **wartość ONLINE.** Kopiowanie jest ukończone, a nowa baza danych jest zwykłą bazą danych, która może zostać zmieniona niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie w trakcie jego wykonywania, wykonaj instrukcje [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) w nowej bazie danych.

> [!IMPORTANT]
> Jeśli musisz utworzyć kopię o znacznie mniejszym celu usługi niż źródło, docelowa baza danych może nie mieć wystarczających zasobów do ukończenia procesu iniekcjowania i może to spowodować niepowodzenie operacji kopiowania. W tym scenariuszu użyj żądania przywracania geograficznego, aby utworzyć kopię na innym serwerze i/lub w innym regionie. Aby [uzyskać więcej informacji, Azure SQL Database temat Recover an Azure SQL Database using database backups](recovery-using-backups.md#geo-restore) (Odzyskiwanie danych przy użyciu kopii zapasowych bazy danych).

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Role RBAC platformy Azure i uprawnienia do zarządzania kopiowaniem bazy danych

Aby utworzyć kopię bazy danych, musisz mieć następujące role

* Właściciel subskrypcji lub
* SQL Server współautora lub
* Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Aby anulować kopię bazy danych, musisz mieć następujące role

* Właściciel subskrypcji lub
* SQL Server współautora lub
* Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Do zarządzania kopiowaniem bazy danych przy Azure Portal wymagane są również następujące uprawnienia:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Jeśli chcesz zobaczyć operacje w ramach wdrożeń w grupie zasobów w portalu, operacje wielu dostawców zasobów, w tym operacje SQL, będą potrzebne następujące dodatkowe uprawnienia:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Rozwiązywanie problemów z logowaniem

Gdy nowa baza danych będzie w trybie online na serwerze docelowym, użyj instrukcji [ALTER USER,](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) aby ponownie zamapować użytkowników z nowej bazy danych na identyfikatory logowania na serwerze docelowym. Aby rozwiązać problem użytkowników oddzielonych, zobacz [Rozwiązywanie problemów z użytkownikami oddzielonym.](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server) Zobacz też [Jak zarządzać zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii.](active-geo-replication-security-configure.md)

Wszyscy użytkownicy w nowej bazie danych zachowują uprawnienia, które mieli w źródłowej bazie danych. Użytkownik, który zainicjował kopię bazy danych, staje się właścicielem nowej bazy danych. Po zakończeniu kopiowania i przed ponownego zamapowania innych użytkowników tylko właściciel bazy danych może zalogować się do nowej bazy danych.

Aby dowiedzieć się więcej o zarządzaniu użytkownikami i nazwami logowania podczas kopiowania bazy danych na inny serwer, zobacz How to manage Azure SQL Database security after disaster recovery (Jak zarządzać zabezpieczeniami po [odzyskiwaniu po awarii).](active-geo-replication-security-configure.md)

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Następujące błędy można napotkać podczas kopiowania bazy danych w Azure SQL Database. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](database-copy.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient o adresie IP "%.&#x2a;ls" jest tymczasowo wyłączony. |
| 40637 |16 |Tworzenie kopii bazy danych jest obecnie wyłączone. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródło lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Dozwolona jest nie więcej niż 1 współbieżna kopia bazy danych z tego samego źródła. Upuść docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłową bazę danych stał się niedostępny. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stała się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o identyfikatorach logowania, zobacz [Zarządzanie nazwami logowania](logins-create-manage.md) i Jak zarządzać zabezpieczeniami [Azure SQL Database po odzyskiwaniu po awarii.](active-geo-replication-security-configure.md)
* Aby wyeksportować bazę danych, zobacz [Eksportowanie bazy danych do pliku BACPAC.](database-export.md)
