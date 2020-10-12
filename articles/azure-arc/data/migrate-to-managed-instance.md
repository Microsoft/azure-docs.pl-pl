---
title: Migrowanie bazy danych z SQL Server do wystąpienia zarządzanego SQL z obsługą usługi Azure Arc
description: Migrowanie bazy danych z SQL Server do wystąpienia zarządzanego SQL z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939631"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migrowanie: SQL Server do wystąpienia zarządzanego SQL z obsługą usługi Azure Arc

W tym scenariuszu przedstawiono procedurę migrowania bazy danych z wystąpienia SQL Server do wystąpienia zarządzanego usługi Azure SQL w usłudze Azure Arc za pośrednictwem dwóch różnych metod tworzenia kopii zapasowych i przywracania.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Korzystanie z usługi Azure Blob Storage 

Użyj usługi Azure Blob Storage, aby przeprowadzić migrację do wystąpienia zarządzanego usługi SQL z funkcją Azure Arc.

Ta metoda używa platformy Azure Blob Storage jako tymczasowej lokalizacji przechowywania, do której można utworzyć kopię zapasową, a następnie przywrócić z programu.

### <a name="prerequisites"></a>Wymagania wstępne

- [Zainstaluj Azure Data Studio](install-client-tools.md)
- [Zainstaluj Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/)
- Subskrypcja platformy Azure

### <a name="step-1-provision-azure-blob-storage"></a>Krok 1. Inicjowanie obsługi usługi Azure Blob Storage

1. Wykonaj kroki opisane w temacie [Tworzenie konta usługi Azure Blob Storage](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Eksplorator usługi Azure Storage uruchamiania
1. [Zaloguj się do platformy Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) , aby uzyskać dostęp do magazynu obiektów BLOB utworzonego w poprzednim kroku
1. Kliknij prawym przyciskiem myszy konto magazynu obiektów blob i wybierz pozycję **Utwórz kontener obiektów BLOB** , aby utworzyć nowy kontener, w którym będzie przechowywany plik kopii zapasowej.

### <a name="step-2-get-storage-blob-credentials"></a>Krok 2. Uzyskiwanie poświadczeń obiektu blob magazynu

1. W Eksplorator usługi Azure Storage kliknij prawym przyciskiem myszy kontener obiektów blob, który został właśnie utworzony, a następnie wybierz pozycję **Pobierz sygnaturę dostępu współdzielonego** .

1. Wybierz **Odczyt**, **zapis** i **listę**

1. Wybierz pozycję **Utwórz**

   Zanotuj identyfikator URI i ciąg zapytania z tego ekranu. Będą one wymagane w dalszych krokach. Kliknij przycisk **Kopiuj** , aby zapisać w Notatniku/programie OneNote itd.

1. Zamknij okno **sygnatura dostępu współdzielonego** .

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Krok 3. Tworzenie kopii zapasowej pliku bazy danych na platformie Azure Blob Storage

W tym kroku połączymy się z SQL Serverą źródłową i utworzysz plik kopii zapasowej bazy danych, którą chcemy zmigrować do wystąpienia zarządzanego SQL — Azure Arc.

1. Azure Data Studio uruchamiania
1. Nawiązywanie połączenia z wystąpieniem SQL Server, które zawiera bazę danych, którą chcesz zmigrować do wystąpienia zarządzanego SQL — łuk platformy Azure
1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz pozycję **nowe zapytanie** .
1. Przygotuj zapytanie w następującym formacie, zastępując symbole zastępcze wskazywane przez `<...>` użycie informacji z sygnatury dostępu współdzielonego w poprzednich krokach.  Po zastąpieniu wartości należy uruchomić zapytanie.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. Podobnie Przygotuj polecenie **tworzenia kopii zapasowej bazy danych** w następujący sposób, aby utworzyć plik kopii zapasowej w kontenerze obiektów BLOB.  Po zastąpieniu wartości należy uruchomić zapytanie.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Otwórz Eksplorator usługi Azure Storage i sprawdź, czy plik kopii zapasowej utworzony w poprzednim kroku jest widoczny w kontenerze obiektów BLOB

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Krok 4. Przywracanie bazy danych z usługi Azure Blob Storage do wystąpienia zarządzanego SQL — łuk platformy Azure

1. W Azure Data Studio Zaloguj się i Połącz z wystąpieniem zarządzanym SQL — Azure Arc.
1. Rozwiń **systemowe bazy danych**, kliknij prawym przyciskiem myszy bazę danych **Master** i wybierz polecenie **nowe zapytanie**.
1. W oknie Edytor zapytań Przygotuj i Uruchom to samo zapytanie z poprzedniego kroku, aby utworzyć poświadczenia.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Przygotuj i uruchom poniższe polecenie, aby upewnić się, że plik kopii zapasowej jest czytelny i niezmieniony.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Przygotuj i uruchom polecenie **Restore Database** w następujący sposób, aby przywrócić plik kopii zapasowej do bazy danych w wystąpieniu zarządzanym SQL — Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Dowiedz się więcej o usłudze Backup to URL tutaj:

[Utwórz kopię zapasową dokumentów URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Tworzenie kopii zapasowej do adresu URL przy użyciu SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Metoda 2. skopiowanie pliku kopii zapasowej do wystąpienia zarządzanego Azure SQL — usługa Azure Arc pod użyciem polecenia kubectl

Ta metoda pokazuje, jak utworzyć plik kopii zapasowej tworzony za pomocą dowolnej metody, a następnie skopiować go do magazynu lokalnego w wystąpieniu zarządzanym usługi Azure SQL pod, tak aby można było przywrócić je w taki sam sposób, jak w przypadku typowego systemu plików w systemie Windows lub Linux. W tym scenariuszu będziesz używać polecenia, `kubectl cp` Aby skopiować plik z jednego miejsca do systemu plików pod.

### <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj i skonfiguruj polecenia kubectl, aby wskazywały na klaster Kubernetes, w którym wdrożono usługi Azure Arc Data Services
- Należy zainstalować narzędzie, takie jak Azure Data Studio lub SQL Server Management Server i połączone z SQL Serverem, w którym chcesz utworzyć plik kopii zapasowej, lub mieć istniejący plik BAK już utworzony w lokalnym systemie plików.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Krok 1. Tworzenie kopii zapasowej bazy danych, jeśli nie zostało to jeszcze zrobione

Utwórz kopię zapasową bazy danych SQL Server na ścieżce do pliku lokalnego, tak jak w przypadku typowych SQL Server kopii zapasowej na dysku:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Krok 2. skopiowanie pliku kopii zapasowej do systemu plików pod

Znajdź nazwę, pod którą wdrożono wystąpienie programu SQL Server. Zwykle powinien wyglądać tak, jak `pod/<sqlinstancename>-0`

Pobierz listę wszystkich zasobników, uruchamiając:

 ```console
kubectl get pods -n <namespace of data controller>
```

Przykład:

Skopiuj plik kopii zapasowej z magazynu lokalnego do bazy danych SQL w klastrze.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Krok 3. Przywracanie bazy danych

Przygotuj i uruchom polecenie RESTORE, aby przywrócić plik kopii zapasowej do wystąpienia zarządzanego usługi Azure SQL — Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Przykład:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o funkcjach i możliwościach wystąpienia zarządzanego przez usługę Azure Arc](managed-instance-features.md)

[Zacznij od utworzenia kontrolera danych](create-data-controller.md)

[Utworzono już kontroler danych? Utwórz wystąpienie zarządzane SQL z funkcją Azure Arc](create-sql-managed-instance.md)