---
title: Kopiowanie bazy danych
description: Utwórz spójnie transakcyjną kopię istniejącej bazy danych w Azure SQL Database na tym samym serwerze lub na innym serwerze.
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
ms.openlocfilehash: 1a86522975ffb7b5b2bd514402dd97a76aa2506e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014608"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopiuj spójnie transakcyjną kopię bazy danych w Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database oferuje kilka metod tworzenia kopii istniejącej [bazy danych](single-database-overview.md) na tym samym lub innym serwerze. Bazę danych można skopiować za pomocą Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub języka T-SQL.

## <a name="overview"></a>Omówienie

Kopia bazy danych jest spójną transakcyjnie migawką źródłowej bazy danych jako punkt w czasie po zainicjowaniu żądania kopiowania. Możesz wybrać ten sam serwer lub inny serwer dla kopii. Można również wybrać opcję zachowania nadmiarowości kopii zapasowej, warstwy usług i rozmiaru obliczeń źródłowej bazy danych albo użyć innej nadmiarowości magazynu kopii zapasowych i/lub rozmiaru obliczeń w ramach tej samej lub innej warstwy usług. Po zakończeniu kopiowania zostanie ona w pełni funkcjonalna, niezależna baza danych. Nazwy logowania, użytkownicy i uprawnienia w skopiowanej bazie danych są zarządzane niezależnie od źródłowej bazy danych. Kopia jest tworzona przy użyciu technologii replikacji geograficznej. Po zakończeniu rozmieszczania repliki połączenie replikacji geograficznej zostaje przerwane. Wszystkie wymagania dotyczące korzystania z replikacji geograficznej dotyczą również operacji kopiowania bazy danych. Szczegółowe informacje znajdują się w temacie [Omówienie aktywnej replikacji geograficznej](active-geo-replication-overview.md) .

> [!NOTE]
> Azure SQL Database konfigurowalnej nadmiarowości magazynu kopii zapasowych jest obecnie dostępna w publicznej wersji zapoznawczej w Brazylii Południowej, a ogólnie dostępna w regionie tylko Azja Wschodnia. W wersji zapoznawczej, jeśli źródłowa baza danych jest tworzona z użyciem lokalnie nadmiarowej lub nadmiarowej nadmiarowości magazynu kopii zapasowych, kopia bazy danych na serwerze w innym regionie platformy Azure nie jest obsługiwana. 

## <a name="logins-in-the-database-copy"></a>Nazwy logowania w kopii bazy danych

Podczas kopiowania bazy danych na ten sam serwer można używać tych samych identyfikatorów logowania w obu bazach danych. Podmiot zabezpieczeń, który jest używany do kopiowania bazy danych, zostaje właścicielem bazy danych w nowej bazie danych.

Podczas kopiowania bazy danych na inny serwer podmiot zabezpieczeń, który zainicjował operację kopiowania na serwerze docelowym, zostaje właścicielem nowej bazy danych.

Bez względu na to, że serwer docelowy, wszyscy użytkownicy bazy danych, ich uprawnienia i identyfikatory zabezpieczeń (SID) są kopiowane do kopii bazy danych. Korzystanie z [użytkowników zawartej bazy danych](logins-create-manage.md) na potrzeby dostępu do danych gwarantuje, że skopiowana baza danych ma te same poświadczenia użytkownika, więc po zakończeniu kopiowania można natychmiast uzyskać do niego dostęp przy użyciu tych samych poświadczeń.

Jeśli do uzyskiwania dostępu do danych i kopiowania bazy danych na inny serwer używasz danych logowania na poziomie serwera, dostęp z wykorzystaniem danych logowania może nie zadziałać. Dzieje się tak, ponieważ na serwerze docelowym nie istnieją dane logowania lub ponieważ odpowiednie hasła i identyfikatory zabezpieczeń są inne. Aby dowiedzieć się więcej o zarządzaniu nazwami logowania podczas kopiowania bazy danych na inny serwer, zapoznaj się z tematem [jak zarządzać zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii](active-geo-replication-security-configure.md). Po pomyślnym przeprowadzeniu operacji kopiowania na inny serwer, a przed ponownym zamapowaniem innych użytkowników, tylko nazwa logowania skojarzona z właścicielem bazy danych lub administrator serwera może zalogować się do skopiowanej bazy danych. Aby rozwiązać nazwy logowania i ustanowić dostęp do danych po zakończeniu operacji kopiowania, zobacz temat [Rozwiązywanie logowań](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Kopiowanie za pomocą witryny Azure Portal

Aby skopiować bazę danych przy użyciu Azure Portal, Otwórz stronę dla bazy danych, a następnie kliknij przycisk **Kopiuj**.

   ![Kopia bazy danych](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiowanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure

Aby skopiować bazę danych, należy użyć następujących przykładów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell należy użyć polecenia cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania nadal w toku, Porzuć docelową bazę danych za pomocą polecenia cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Aby zapoznać się z kompletnym przykładowym skryptem programu PowerShell, zobacz [Kopiowanie bazy danych na nowy serwer](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Kopia bazy danych jest operacją asynchroniczną, ale docelowa baza danych jest tworzona natychmiast po zaakceptowaniu żądania. Jeśli chcesz anulować operację kopiowania nadal w toku, Porzuć docelową bazę danych za pomocą polecenia [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Kopiowanie przy użyciu języka Transact-SQL

Zaloguj się do bazy danych Master przy użyciu identyfikatora logowania administratora serwera lub nazwy logowania, która utworzyła bazę danych, którą chcesz skopiować. Aby Kopiowanie bazy danych zakończyło się pomyślnie, logowania, które nie są administratora serwera, muszą należeć do `dbmanager` roli. Aby uzyskać więcej informacji na temat nazw logowania i łączenia się z serwerem, zobacz Zarządzanie nazwami [logowania](logins-create-manage.md).

Rozpocznij kopiowanie źródłowej bazy danych za pomocą programu [CREATE DATABASE... JAKO kopia](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) instrukcji. Instrukcja T-SQL kontynuuje działanie do momentu ukończenia operacji kopiowania bazy danych.

> [!NOTE]
> Zakończenie instrukcji języka T-SQL nie kończy operacji kopiowania bazy danych. Aby zakończyć operację, Porzuć docelową bazę danych.
>

> [!IMPORTANT]
> Wybieranie nadmiarowości magazynu kopii zapasowych podczas korzystania z usługi T-SQL CREATE DATABASE... Ponieważ kopia polecenia nie jest jeszcze obsługiwana. 

### <a name="copy-to-the-same-server"></a>Kopiuj na ten sam serwer

Zaloguj się do bazy danych Master przy użyciu identyfikatora logowania administratora serwera lub nazwy logowania, która utworzyła bazę danych, którą chcesz skopiować. Aby Kopiowanie bazy danych zakończyło się pomyślnie, nazwy logowania, które nie są administratora serwera, muszą należeć do `dbmanager` roli.

To polecenie kopiuje database1 do nowej bazy danych o nazwie Database2 na tym samym serwerze. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopiuj do puli elastycznej

Zaloguj się do bazy danych Master przy użyciu identyfikatora logowania administratora serwera lub nazwy logowania, która utworzyła bazę danych, którą chcesz skopiować. Aby Kopiowanie bazy danych zakończyło się pomyślnie, nazwy logowania, które nie są administratora serwera, muszą należeć do `dbmanager` roli.

To polecenie kopiuje database1 do nowej bazy danych o nazwie Database2 w puli elastycznej o nazwie pool1. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

Database1 może być jedną lub bazą danych w puli. Obsługa kopiowania między różnymi pulami warstw jest obsługiwana, ale niektóre kopie między warstwami nie powiodą się. Na przykład można skopiować pojedynczą lub elastyczną bazę danych w warstwie ogólnego przeznaczenia, ale nie można skopiować standardowej elastycznej bazy danych do puli w warstwie Premium. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Kopiuj na inny serwer

Zaloguj się do bazy danych Master serwera docelowego, na którym ma zostać utworzona nowa baza danych. Użyj nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych na serwerze źródłowym. Identyfikator logowania na serwerze docelowym musi być również członkiem `dbmanager` roli lub być identyfikatorem logowania administratora serwera.

To polecenie kopiuje database1 na serwer1 do nowej bazy danych o nazwie Database2 na serwerze serwer2. W zależności od rozmiaru bazy danych operacja kopiowania może zająć trochę czasu.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Zapory obu serwerów muszą być skonfigurowane tak, aby zezwalały na połączenia przychodzące z adresu IP klienta wystawiającego bazę danych T-SQL... JAKO kopia polecenia.

### <a name="copy-to-a-different-subscription"></a>Kopiuj do innej subskrypcji

Aby skopiować bazę danych na serwer w innej subskrypcji przy użyciu języka T-SQL, można wykonać kroki opisane w sekcji [Kopiuj a SQL Database do innego serwera](#copy-to-a-different-server) . Upewnij się, że używasz nazwy logowania, która ma taką samą nazwę i hasło, jak właścicielem bazy danych źródłowej bazy danych. Ponadto identyfikator logowania musi być członkiem `dbmanager` roli lub administratora serwera zarówno na serwerze źródłowym, jak i docelowym.

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
> [Azure Portal](https://portal.azure.com), PowerShell i interfejs wiersza polecenia platformy Azure nie obsługują kopiowania bazy danych do innej subskrypcji.

> [!TIP]
> Kopiowanie bazy danych przy użyciu języka T-SQL obsługuje kopiowanie bazy danych z subskrypcji w innej dzierżawie platformy Azure. Jest to obsługiwane tylko w przypadku korzystania z logowania za pomocą uwierzytelniania SQL na serwerze docelowym.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitoruj postęp operacji kopiowania

Monitoruj proces kopiowania, wykonując zapytania dotyczące widoków [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)i [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . Podczas kopiowania jest w toku, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona do **kopiowania**.

* Jeśli kopiowanie nie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona na **podejrzane**. Wykonaj instrukcję DROP w nowej bazie danych i spróbuj ponownie później.
* Jeśli kopiowanie powiedzie się, kolumna **state_desc** widoku sys. databases dla nowej bazy danych jest ustawiona na **online**. Kopiowanie zostało ukończone, a nowa baza danych jest zwykłą bazą danych, która może zostać zmieniona niezależnie od źródłowej bazy danych.

> [!NOTE]
> Jeśli zdecydujesz się anulować kopiowanie w trakcie wykonywania, wykonaj instrukcję [Drop Database](/sql/t-sql/statements/drop-database-transact-sql) w nowej bazie danych.

> [!IMPORTANT]
> Jeśli trzeba utworzyć kopię z znacznie mniejszym celem usługi niż źródło, docelowa baza danych może nie mieć wystarczających zasobów do ukończenia procesu tworzenia i może spowodować niepowodzenie operacji kopiowania. W tym scenariuszu Użyj żądania przywracania geograficznego, aby utworzyć kopię na innym serwerze i/lub innym regionie. Aby uzyskać więcej informacji [, zobacz odzyskiwanie Azure SQL Database przy użyciu kopii zapasowych bazy danych](recovery-using-backups.md#geo-restore) .

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Role i uprawnienia usługi Azure RBAC do zarządzania kopią bazy danych

Aby utworzyć kopię bazy danych, musisz mieć następujące role:

* Właściciel subskrypcji lub
* SQL Server rolę współautor lub
* Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft. SQL/serwery/bazy danych/Odczyt Microsoft. SQL/serwery/bazy danych/zapis

Aby anulować kopię bazy danych, musisz mieć następujące role:

* Właściciel subskrypcji lub
* SQL Server rolę współautor lub
* Rola niestandardowa w źródłowej i docelowej bazie danych z następującymi uprawnieniami:

   Microsoft. SQL/serwery/bazy danych/Odczyt Microsoft. SQL/serwery/bazy danych/zapis

Aby zarządzać kopią bazy danych przy użyciu Azure Portal, wymagane są również następujące uprawnienia:

   Microsoft. resources/subscriptions/zasobów/Odczytaj Microsoft. resources/subscriptions/Resources/Write Microsoft. resources/Deployments/Odczytaj Microsoft. resources/Deployments/Write Microsoft. resources/Deployments

Jeśli chcesz zobaczyć operacje w obszarze wdrożenia w grupie zasobów portalu, operacje między wieloma dostawcami zasobów, w tym operacje SQL, będą potrzebne następujące dodatkowe role platformy Azure:

   Microsoft. resources/subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. resources/subscriptions/ResourceGroups/Deployments/operationstatuses/Read

## <a name="resolve-logins"></a>Rozwiązywanie logowań

Gdy nowa baza danych jest w trybie online na serwerze docelowym, użyj instrukcji [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) , aby ponownie zamapować użytkowników z nowej bazy danych na nazwy logowania na serwerze docelowym. Aby rozwiązać użytkowników oddzielonych, zobacz [Rozwiązywanie problemów z użytkownikami](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)oddzielonymi. Zobacz również [jak zarządzać zabezpieczeniami Azure SQL Database po odzyskaniu po awarii](active-geo-replication-security-configure.md).

Wszyscy użytkownicy w nowej bazie danych zachowują uprawnienia, które miały w źródłowej bazie danych. Użytkownik, który zainicjował kopię bazy danych, zostaje właścicielem bazy danych nowej bazy danych. Po pomyślnym zakończeniu kopiowania i wcześniejszym zamapowaniu użytkowników tylko właściciel bazy danych może zalogować się do nowej bazy danych.

Aby dowiedzieć się więcej o zarządzaniu użytkownikami i logowaniami podczas kopiowania bazy danych na inny serwer, zobacz [jak zarządzać zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Błędy kopiowania bazy danych

Podczas kopiowania bazy danych w Azure SQL Database można napotkać następujące błędy. Więcej informacji znajdziesz w artykule [Kopiowanie bazy danych usługi Azure SQL Database](database-copy.md).

| Kod błędu | Ważność | Opis |
| ---:| ---:|:--- |
| 40635 |16 |Klient z adresem IP "%. &#x2a;ls" jest tymczasowo wyłączony. |
| 40637 |16 |Tworzenie kopii bazy danych jest obecnie wyłączone. |
| 40561 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa lub docelowa baza danych nie istnieje. |
| 40562 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych została porzucona. |
| 40563 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych została porzucona. |
| 40564 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40565 |16 |Kopiowanie bazy danych nie powiodło się. Dozwolona jest nie więcej niż 1 współbieżna kopia bazy danych z tego samego źródła. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40566 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40567 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40568 |16 |Kopiowanie bazy danych nie powiodło się. Źródłowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40569 |16 |Kopiowanie bazy danych nie powiodło się. Docelowa baza danych stanie się niedostępna. Porzuć docelową bazę danych i spróbuj ponownie. |
| 40570 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |
| 40571 |16 |Kopiowanie bazy danych nie powiodło się z powodu błędu wewnętrznego. Porzuć docelową bazę danych i spróbuj ponownie później. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat nazw logowania, zobacz Zarządzanie nazwami [logowania](logins-create-manage.md) i [Zarządzanie zabezpieczeniami Azure SQL Database po odzyskiwaniu po awarii](active-geo-replication-security-configure.md).
* Aby wyeksportować bazę danych, zobacz [Eksportowanie bazy danych do BACPAC](database-export.md).
