---
title: Konfigurowanie replikacji między wystąpieniami zarządzanymi
titleSuffix: Azure SQL Managed Instance
description: Ten samouczek uczy się skonfigurować replikację transakcyjną między wydawcą/dystrybutorem wystąpienia zarządzanego usługi Azure SQL i subskrybentem wystąpienia zarządzanego SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690689"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Samouczek: Konfigurowanie replikacji między dwoma wystąpieniami zarządzanymi

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Replikacja transakcyjna umożliwia replikowanie danych z jednej bazy danych do innej hostowanej na SQL Server lub [wystąpieniu zarządzanym usługi Azure SQL](sql-managed-instance-paas-overview.md). Wystąpienie zarządzane SQL może być wydawcą, dystrybutorem lub subskrybentem w topologii replikacji. Zobacz [konfiguracje replikacji transakcyjnej](replication-transactional-overview.md#common-configurations) dla dostępnych konfiguracji. 

Replikacja transakcyjna jest obecnie dostępna w publicznej wersji zapoznawczej dla wystąpienia zarządzanego SQL. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Skonfiguruj wystąpienie zarządzane jako wydawcę i dystrybutora replikacji.
> - Skonfiguruj wystąpienie zarządzane jako subskrybent replikacji.

![Replikacja między dwoma wystąpieniami zarządzanymi](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Ten samouczek jest przeznaczony dla doświadczonych odbiorców i zakłada, że użytkownik zna wdrażanie i nawiązywanie połączenia z zarówno wystąpieniami zarządzanymi, jak i SQL Server maszynami wirtualnymi na platformie Azure. 


> [!NOTE]
> - W tym artykule opisano sposób korzystania z [replikacji transakcyjnej](/sql/relational-databases/replication/transactional/transactional-replication) w wystąpieniu zarządzanym usługi Azure SQL. Nie jest on związany z [grupami trybu failover](../database/auto-failover-group-overview.md), funkcja wystąpienia zarządzanego Azure SQL, która umożliwia tworzenie kompletnych możliwych do odczytu replik poszczególnych wystąpień. Podczas konfigurowania [replikacji transakcyjnej przy użyciu grup trybu failover](replication-transactional-overview.md#with-failover-groups)należy uwzględnić dodatkowe zagadnienia.



## <a name="requirements"></a>Wymagania

Skonfigurowanie wystąpienia zarządzanego SQL do działania jako Wydawca i/lub dystrybutor wymaga:

- Że wystąpienie zarządzane wydawcy znajduje się w tej samej sieci wirtualnej co dystrybutor i subskrybenta, lub skonfigurowano [bramy sieci VPN](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) między sieciami wirtualnymi wszystkich trzech jednostek. 
- Połączenie korzysta z uwierzytelniania SQL między uczestnikami replikacji.
- Udział konta usługi Azure Storage dla katalogu roboczego replikacji.
- Port 445 (ruch wychodzący TCP) jest otwarty w regułach zabezpieczeń sieciowej grupy zabezpieczeń dla wystąpień zarządzanych, aby uzyskać dostęp do udziału plików platformy Azure.  Jeśli wystąpi błąd `failed to connect to azure storage <storage account name> with os error 53` , konieczne będzie dodanie reguły wychodzącej do sieciowej grupy zabezpieczeń odpowiedniej podsieci wystąpienia zarządzanego SQL.

## <a name="1---create-a-resource-group"></a>1 — Tworzenie grupy zasobów

Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć grupę zasobów o nazwie `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2 — Tworzenie wystąpień zarządzanych

Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć dwa [wystąpienia zarządzane SQL](instance-create-quickstart.md) w tej samej sieci wirtualnej i podsieci. Na przykład Nazwij dwa wystąpienia zarządzane:

- `sql-mi-pub` (wraz z niektórymi znakami dla losowości)
- `sql-mi-sub` (wraz z niektórymi znakami dla losowości)

Należy również [skonfigurować maszynę wirtualną platformy Azure w celu nawiązania połączenia](connect-vm-instance-configure.md) z wystąpieniami zarządzanymi. 

## <a name="3---create-an-azure-storage-account"></a>3 — Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](../../storage/common/storage-account-create.md#create-a-storage-account) dla katalogu roboczego, a następnie Utwórz [udział plików](../../storage/files/storage-how-to-create-file-share.md) na koncie magazynu. 

Skopiuj ścieżkę udziału plików w formacie: `\\storage-account-name.file.core.windows.net\file-share-name`

Przykład: `\\replstorage.file.core.windows.net\replshare`

Skopiuj klucze dostępu do magazynu w formacie: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Przykład: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../../storage/common/storage-account-keys-manage.md). 

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

Na wystąpieniu zarządzanym programu SQL wydawcy `sql-mi-pub` Zmień wartość w polu wykonanie zapytania na tryb [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) i uruchom następujący kod w celu zarejestrowania nowego dystrybutora z wydawcą.

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
   > Upewnij się, że dla parametru file_storage są używane tylko ukośniki odwrotne ( `\` ). Użycie ukośnika ( `/` ) może spowodować błąd podczas nawiązywania połączenia z udziałem plików.

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


-- Configure your log reader agent
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

W wystąpieniu zarządzanym usługi Azure SQL są obecnie występujące problemy z zapleczem dotyczące łączności z agentami replikacji. Podczas rozwiązywania tego problemu obejście polega na zwiększeniu wartości limitu czasu logowania dla agentów replikacji.

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

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

Możesz wyczyścić zasoby platformy Azure, [usuwając zasoby wystąpienia zarządzanego SQL z grupy zasobów](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) , a następnie usuwając grupę zasobów `SQLMI-Repl` . 

## <a name="next-steps"></a>Następne kroki

Możesz również uzyskać więcej informacji na temat [replikacji transakcyjnej przy użyciu wystąpienia zarządzanego Azure SQL](replication-transactional-overview.md) lub dowiedzieć się, jak skonfigurować replikację między [wydawcą/dystrybutorem wystąpienia zarządzanego SQL a serwerem SQL na subskrybencie maszyny wirtualnej platformy Azure](replication-two-instances-and-sql-server-configure-tutorial.md).