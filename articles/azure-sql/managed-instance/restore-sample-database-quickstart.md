---
title: 'Szybki Start: Przywracanie kopii zapasowej (SSMS)'
titleSuffix: Azure SQL SQL Managed Instance
description: W tym przewodniku szybki start dowiesz się, jak przywrócić kopię zapasową bazy danych do wystąpienia zarządzanego usługi Azure SQL SQL przy użyciu narzędzia do zarządzania SQL Server (SSMS).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 74975c050ef77c02e8912ce6cdd9404ac5bba019
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054731"
---
# <a name="quickstart-restore-a-database-to-an-azure-sql-managed-instance-with-ssms"></a>Szybki Start: Przywracanie bazy danych do wystąpienia zarządzanego usługi Azure SQL przy użyciu programu SSMS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym przewodniku szybki start użyjesz SQL Server Management Studio (SSMS), aby przywrócić bazę danych (w standardowym pliku kopii zapasowej Wide World Importers) z usługi Azure Blob Storage do [wystąpienia zarządzanego Azure SQL](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Aby uzyskać więcej informacji na temat migracji przy użyciu Azure Database Migration Service (DMS), zobacz [migracja wystąpienia zarządzanego SQL za pomocą usługi DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
> Aby uzyskać więcej informacji na temat różnych metod migracji, zobacz [SQL Server migracji do wystąpienia zarządzanego Azure SQL](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start:

- Używa zasobów z usługi [Tworzenie wystąpienia zarządzanego SQL](instance-create-quickstart.md) — Szybki Start.
- Wymaga zainstalowanej najnowszej wersji programu [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) .
- Do nawiązania połączenia z wystąpieniem zarządzanym SQL wymaga użycia programu SSMS. Zobacz te przewodniki Szybki start dotyczące nawiązywania połączenia:
  - [Włącz publiczny punkt końcowy](public-endpoint-configure.md) w wystąpieniu zarządzanym SQL — jest to zalecane podejście do tego samouczka.
  - [Nawiązywanie połączenia z wystąpieniem zarządzanym SQL z maszyny wirtualnej platformy Azure](connect-vm-instance-configure.md)
  - [Skonfiguruj połączenie punkt-lokacja z wystąpieniem zarządzanym SQL z lokalnego](point-to-site-p2s-configure.md).

> [!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania bazy danych programu SQL Server przy użyciu usługi Azure Blob Storage i [klucza sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), zobacz artykuł [SQL Server Backup to URL (Tworzenie kopii zapasowej programu SQL Server pod określonym adresem URL)](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-from-a-backup-file"></a>Przywracanie z pliku kopii zapasowej

W SQL Server Management Studio (SSMS) wykonaj następujące kroki, aby przywrócić bazę danych Wide World Importers do wystąpienia zarządzanego SQL. Plik kopii zapasowej bazy danych znajduje na wstępnie skonfigurowanym koncie usługi Azure Blob Storage.

1. Otwórz program SSMS i Połącz się z wystąpieniem zarządzanym SQL.
2. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy wystąpienie zarządzane SQL i wybierz polecenie **nowe zapytanie** , aby otworzyć nowe okno zapytania.
3. Uruchom następujący skrypt SQL, który używa wstępnie skonfigurowanego konta magazynu i klucza sygnatury dostępu współdzielonego, aby [utworzyć poświadczenia](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) w wystąpieniu zarządzanym SQL.

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

    ![Przywracanie](./media/restore-sample-database-quickstart/restore.png)

6. Uruchom następujący skrypt, aby śledzić stan przywracania.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Po zakończeniu przywracania Wyświetl bazę danych w Eksplorator obiektów. Można sprawdzić, czy przywracanie bazy danych zostało ukończone przy użyciu widoku [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .

> [!NOTE]
> Operacja przywracania bazy danych jest asynchroniczna i ponowienie. Może pojawić SQL Server Management Studio się komunikat o błędzie, jeśli wystąpiły przerwy w połączeniach lub wygaśnie limit czasu. Azure SQL Database będzie próbować przywrócić bazę danych w tle, a postęp przywracania można śledzić przy użyciu widoków [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) i [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .
> W niektórych fazach procesu przywracania w widokach systemu zostanie wyświetlony unikatowy identyfikator zamiast rzeczywistej nazwy bazy danych. Więcej informacji na temat `RESTORE` różnic dotyczących zachowań instrukcji [znajdziesz tutaj](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Następne kroki

- W celu rozwiązywania problemów dotyczących tworzenia kopii zapasowej do adresu URL, zobacz artykuł [SQL Server Backup to URL Best Practices and Troubleshooting](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting) (Kopia zapasowa programu SQL Server do adresu URL — najlepsze rozwiązania i rozwiązywanie problemów).
- Aby zapoznać się z omówieniem opcji połączenia aplikacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym SQL](connect-application-instance.md).
- Aby wykonywać zapytania przy użyciu ulubionych narzędzi lub języków, zobacz [Przewodniki Szybki Start: Azure SQL Database Connect and Query](../database/connect-query-content-reference-guide.md).
