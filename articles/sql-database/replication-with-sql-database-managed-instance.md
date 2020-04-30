---
title: Konfigurowanie replikacji w bazie danych wystąpienia zarządzanego
description: Dowiedz się, jak skonfigurować replikację transakcyjną Azure SQL Database między wydawcą/dystrybutorem wystąpienia zarządzanego i subskrybentem wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 9ac30b6d502bb0fbdb454d7a3c36cde23a57fb6b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231632"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurowanie replikacji w Azure SQL Database bazie danych wystąpienia zarządzanego

Replikacja transakcyjna umożliwia replikowanie danych do bazy danych wystąpienia zarządzanego Azure SQL Database z bazy danych SQL Server lub innej bazy danych wystąpienia.

> [!NOTE]
> W tym artykule opisano sposób korzystania z [replikacji transakcyjnej](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) w wystąpieniu zarządzanym usługi Azure SQL. Nie jest ona powiązana z aktywną replikacją geograficzną lub [grupami trybu failover](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group), funkcja wystąpienia zarządzanego usługi Azure SQL, która umożliwia tworzenie kompletnych możliwych do odczytu replik poszczególnych wystąpień.

W tym artykule pokazano, jak skonfigurować replikację między wydawcą/dystrybutorem wystąpienia zarządzanego i subskrybentem wystąpienia zarządzanego. 

![Replikacja między dwoma wystąpieniami zarządzanymi](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Replikację transakcyjną można także użyć do wypychania zmian wprowadzonych w bazie danych wystąpienia w Azure SQL Database wystąpieniu zarządzanym, aby:

- Baza danych SQL Server.
- Pojedyncza baza danych w Azure SQL Database.
- Baza danych w puli w puli elastycznej Azure SQL Database.
 
Replikacja transakcyjna jest w publicznej wersji zapoznawczej na [Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance.md). Wystąpienie zarządzane może hostować bazy danych wydawcy, dystrybutora i subskrybentów. Zobacz [konfiguracje replikacji transakcyjnej](sql-database-managed-instance-transactional-replication.md#common-configurations) dla dostępnych konfiguracji.

  > [!NOTE]
  > - Ten artykuł ma na celu umożliwienie użytkownikowi konfigurowania replikacji z wystąpieniem zarządzanym usługi Azure Database od końca do końca, rozpoczynając od tworzenia grupy zasobów. Jeśli masz już wdrożone wystąpienia zarządzane, przejdź do [kroku 4](#4---create-a-publisher-database) , aby utworzyć bazę danych wydawcy, lub [krok 6](#6---configure-distribution) , jeśli masz już wydawcę i bazę danych subskrybentów i są gotowe do rozpoczęcia konfigurowania replikacji.  
  > - W tym artykule konfiguruje wydawcę i dystrybutora na tym samym zarządzanym wystąpieniu. Aby umieścić dystrybutora w oddzielnym wystąpieniu, zapoznaj się z samouczkiem [Konfigurowanie replikacji między programem i dystrybutorem mi](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Wymagania

Skonfigurowanie wystąpienia zarządzanego do działania w ramach wydawcy i/lub dystrybutora wymaga:

- Że wystąpienie zarządzane wydawcy znajduje się w tej samej sieci wirtualnej co dystrybutora i subskrybenta, lub ustanowiono [komunikację równorzędną](../virtual-network/tutorial-connect-virtual-networks-powershell.md) sieci wirtualnych w ramach wszystkich trzech jednostek. 
- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji.
- Udział konta usługi Azure Storage dla katalogu roboczego replikacji.
- Port 445 (ruch wychodzący TCP) jest otwarty w regułach zabezpieczeń sieciowej grupy zabezpieczeń dla wystąpień zarządzanych, aby uzyskać dostęp do udziału plików platformy Azure.  Jeśli wystąpi błąd "nie można nawiązać połączenia z nazwą konta \<magazynu usługi azure Storage> z błędem systemu operacyjnego 53", musisz dodać regułę ruchu wychodzącego do sieciowej grupy zabezpieczeń odpowiedniej podsieci wystąpienia zarządzanego SQL.


 > [!NOTE]
 > Pojedynczymi bazami danych i bazami danych w puli w Azure SQL Database mogą być tylko subskrybenci. 


## <a name="features"></a>Funkcje

Uguje

- Mieszanie transakcji transakcyjnych i migawek dla SQL Server lokalnych i zarządzanych wystąpień w Azure SQL Database.
- Subskrybenci mogą znajdować się w lokalnych bazach danych SQL Server, pojedynczych bazach danych/wystąpieniach zarządzanych w Azure SQL Database lub w puli baz danych w Azure SQL Database pul elastycznych.
- Replikacja jednokierunkowa lub dwukierunkowa.

Następujące funkcje nie są obsługiwane w wystąpieniu zarządzanym w Azure SQL Database:

- [Aktualizowalne subskrypcje](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktywna replikacja geograficzna](sql-database-active-geo-replication.md) z replikacją transakcyjną. Zamiast aktywnej replikacji geograficznej, należy użyć grup z obsługą [trybu failover](sql-database-auto-failover-group.md), ale należy pamiętać, że publikacja musi zostać [ręcznie usunięta](sql-database-managed-instance-transact-sql-information.md#replication) z głównego wystąpienia zarządzanego i ponownie utworzona na pomocniczym wystąpieniu zarządzanym po przejściu do trybu failover.  
 
## <a name="1---create-a-resource-group"></a>1 — Tworzenie grupy zasobów

Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć grupę zasobów o nazwie `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 — Tworzenie wystąpień zarządzanych

Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć dwa [wystąpienia zarządzane](sql-database-managed-instance-create-tutorial-portal.md) w tej samej sieci wirtualnej i podsieci. Na przykład Nazwij dwa wystąpienia zarządzane:

- `sql-mi-pub`(wraz z niektórymi znakami dla losowości)
- `sql-mi-sub`(wraz z niektórymi znakami dla losowości)

Należy również [skonfigurować maszynę wirtualną platformy Azure do łączenia](sql-database-managed-instance-configure-vm.md) się z wystąpieniami zarządzanymi Azure SQL Database. 

## <a name="3---create-azure-storage-account"></a>3 — Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego, a następnie Utwórz [udział plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu. 

Skopiuj ścieżkę udziału plików w formacie:`\\storage-account-name.file.core.windows.net\file-share-name`

Przykład: `\\replstorage.file.core.windows.net\replshare`

Skopiuj klucze dostępu do magazynu w formacie:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Przykład: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 — Tworzenie bazy danych wydawcy

Połącz się z `sql-mi-pub` wystąpieniem zarządzanym przy użyciu SQL Server Management Studio i uruchom następujący kod języka Transact-SQL (T-SQL), aby utworzyć bazę danych wydawcy:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 — Tworzenie bazy danych subskrybenta

Połącz się z `sql-mi-sub` wystąpieniem zarządzanym przy użyciu SQL Server Management Studio i uruchom następujący kod T-SQL, aby utworzyć pustą bazę danych subskrybenta:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 — Konfigurowanie dystrybucji

Połącz się z `sql-mi-pub` wystąpieniem zarządzanym przy użyciu SQL Server Management Studio i uruchom następujący kod T-SQL w celu skonfigurowania bazy danych dystrybucji. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 — Konfigurowanie wydawcy do korzystania z dystrybutora 

W wystąpieniu `sql-mi-pub`zarządzanym wydawcy Zmień wykonanie zapytania na tryb [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) i uruchom następujący kod w celu zarejestrowania nowego dystrybutora z wydawcą. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Upewnij się, że dla parametru file_storage są używane`\`tylko ukośniki odwrotne (). Użycie ukośnika (`/`) może spowodować błąd podczas nawiązywania połączenia z udziałem plików. 

Ten skrypt konfiguruje lokalnego wydawcę na zarządzanym wystąpieniu, dodaje połączony serwer i tworzy zestaw zadań dla agenta SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 — Tworzenie publikacji i subskrybentów

Korzystając z trybu [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , uruchom następujący skrypt T-SQL, aby włączyć replikację bazy danych i skonfigurować replikację między wydawcą, dystrybutorem i subskrybentem. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 — modyfikowanie parametrów agenta

W Azure SQL Database wystąpieniu zarządzanym obecnie występują pewne problemy z zapleczem dotyczące łączności z agentami replikacji. Podczas rozwiązywania tego problemu należy zastosować obejście, aby zwiększyć wartość limitu czasu logowania dla agentów replikacji. 

Uruchom następujące polecenie T-SQL na wydawcy, aby zwiększyć limit czasu logowania: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Uruchom ponownie następujące polecenie T-SQL, aby ustawić limit czasu logowania z powrotem do wartości domyślnej. należy to zrobić:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Uruchom ponownie wszystkich trzech agentów, aby zastosować te zmiany. 

## <a name="10---test-replication"></a>10-testowa replikacja

Po skonfigurowaniu replikacji można testować ją, wstawiając nowe elementy na wydawcy i obserwując zmiany propagowane do subskrybenta. 

Uruchom Poniższy fragment kodu T-SQL, aby wyświetlić wiersze na subskrybencie:

```sql
select * from dbo.ReplTest
```

Uruchom Poniższy fragment kodu T-SQL, aby wstawić dodatkowe wiersze na wydawcy, a następnie ponownie sprawdzić wiersze na subskrybencie. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby usunąć publikację, uruchom następujące polecenie T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Aby usunąć opcję replikacji z bazy danych, uruchom następujące polecenie T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Aby wyłączyć publikowanie i dystrybucję, uruchom następujące polecenie T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Możesz wyczyścić zasoby platformy Azure, [usuwając zasoby wystąpienia zarządzanego z grupy zasobów](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) , a następnie usuwając grupę `SQLMI-Repl`zasobów. 

   
## <a name="see-also"></a>Zobacz też

- [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md)
- [Samouczek: Konfigurowanie replikacji transakcyjnej między wydawcą MI a subskrybentem SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
