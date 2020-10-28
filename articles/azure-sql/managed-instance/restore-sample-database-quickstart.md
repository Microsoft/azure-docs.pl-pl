---
title: 'Szybki Start: Przywracanie kopii zapasowej (SSMS)'
titleSuffix: Azure SQL Managed Instance
description: W tym przewodniku szybki start dowiesz się, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego usługi Azure SQL przy użyciu SQL Server Management Studio (SSMS).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 413786cf8946c1ffbb76bd0e18eae7c7ba16a9c1
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790750"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>Szybki Start: Przywracanie bazy danych do wystąpienia zarządzanego usługi Azure SQL za pomocą programu SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym przewodniku szybki start użyjesz SQL Server Management Studio (SSMS) do przywracania bazy danych (plik o szerokim świecie) z usługi Azure Blob Storage do [wystąpienia zarządzanego Azure SQL](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Aby uzyskać więcej informacji na temat migracji przy użyciu Azure Database Migration Service, zobacz [migracja wystąpienia zarządzanego SQL przy użyciu Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
> Aby uzyskać więcej informacji na temat różnych metod migracji, zobacz [SQL Server migracji do wystąpienia zarządzanego Azure SQL](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa zasobów z poziomu [tworzenia wystąpienia zarządzanego](instance-create-quickstart.md) — Szybki Start.
- Wymaga zainstalowanej najnowszej wersji programu [SSMS](/sql/ssms/sql-server-management-studio-ssms) .
- Do nawiązania połączenia z wystąpieniem zarządzanym SQL wymaga użycia programu SSMS. Zobacz te przewodniki Szybki start dotyczące nawiązywania połączenia:
  - [Włączenie publicznego punktu końcowego](public-endpoint-configure.md) w wystąpieniu zarządzanym SQL — jest to zalecane podejście dla tego samouczka.
  - [Nawiąż połączenie z wystąpieniem zarządzanym SQL z maszyny wirtualnej platformy Azure](connect-vm-instance-configure.md).
  - [Skonfiguruj połączenie punkt-lokacja z wystąpieniem zarządzanym SQL z lokalnego](point-to-site-p2s-configure.md).

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania bazy danych programu SQL Server przy użyciu usługi Azure Blob Storage i [klucza sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md), zobacz artykuł [SQL Server Backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-from-a-backup-file"></a>Przywracanie z pliku kopii zapasowej

W SQL Server Management Studio wykonaj następujące kroki, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego SQL. Plik kopii zapasowej bazy danych znajduje na wstępnie skonfigurowanym koncie usługi Azure Blob Storage.

1. Otwórz narzędzie SSMS i Połącz się z wystąpieniem zarządzanym.
2. W **Eksplorator obiektów** kliknij prawym przyciskiem myszy wystąpienie zarządzane i wybierz polecenie **nowe zapytanie** , aby otworzyć nowe okno zapytania.
3. Uruchom następujący skrypt SQL, który używa wstępnie skonfigurowanego konta magazynu i klucza sygnatury dostępu współdzielonego, aby [utworzyć poświadczenie](/sql/t-sql/statements/create-credential-transact-sql) w wystąpieniu zarządzanym.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![tworzenie poświadczenia](./media/restore-sample-database-quickstart/credential.png)

4. Aby sprawdzić swoje poświadczenie, uruchom następujący skrypt, który używa adresu URL [kontenera](https://azure.microsoft.com/services/container-instances/), aby uzyskać listę plików kopii zapasowej.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![lista plików](./media/restore-sample-database-quickstart/file-list.png)

5. Uruchom następujący skrypt, aby przywrócić bazę danych Wide World Importers.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Zrzut ekranu przedstawia skrypt uruchomiony w Eksplorator obiektów z komunikatem o powodzeniu.](./media/restore-sample-database-quickstart/restore.png)

6. Uruchom następujący skrypt, aby śledzić stan przywracania.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Po zakończeniu przywracania Wyświetl bazę danych w Eksplorator obiektów. Można sprawdzić, czy przywracanie bazy danych zostało ukończone przy użyciu widoku [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .

> [!NOTE]
> Operacja przywracania bazy danych jest asynchroniczna i ponowienie. Może wystąpić błąd w SQL Server Management Studio, jeśli przerwania połączenia lub limit czasu wygaśnie. Azure SQL Database będzie ponawiać próbę przywrócenia bazy danych w tle, a postęp przywracania można śledzić przy użyciu widoków [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .
> W niektórych fazach procesu przywracania w widokach systemu zostanie wyświetlony unikatowy identyfikator zamiast rzeczywistej nazwy bazy danych. Więcej informacji na temat `RESTORE` różnic dotyczących zachowań instrukcji [znajdziesz tutaj](./transact-sql-tsql-differences-sql-server.md#restore-statement).

## <a name="next-steps"></a>Następne kroki

- Jeśli w kroku 5 przywracanie bazy danych zostało zakończone z IDENTYFIKATORem komunikatu 22003, Utwórz nowy plik kopii zapasowej zawierający sumy kontrolne kopii zapasowej i ponownie wykonaj operację przywracania. Zobacz [Włączanie lub wyłączanie sum kontrolnych kopii zapasowych podczas wykonywania kopii zapasowej lub przywracania](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
- Aby uzyskać informacje na temat rozwiązywania problemów z kopią zapasową w adresie URL, zobacz [SQL Server Tworzenie i rozwiązywanie problemów z usługą Backup](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)
- Aby zapoznać się z omówieniem opcji połączenia aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym SQL](connect-application-instance.md).
- Aby wykonywać zapytania przy użyciu ulubionych narzędzi lub języków, zobacz [Przewodniki Szybki Start: Azure SQL Database Connect and Query](../database/connect-query-content-reference-guide.md).