---
title: Migrowanie baz danych do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
description: Dowiedz się, jak migrować bazy danych z SQL Server do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/17/2021
ms.openlocfilehash: 07da1d5dbfd6384751e01f5becccd7b7b4c97e99
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095221"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrowanie baz danych z SQL Server do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak ręcznie skonfigurować migrację bazy danych z SQL Server 2008-2019 do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników (LRS). Jest to usługa w chmurze włączona dla wystąpienia zarządzanego na podstawie technologii dostarczania dzienników SQL Server. LRS należy używać w przypadkach, gdy nie można użyć usługi Azure Data Migration Service (DMS), gdy jest wymagana większa kontrola lub gdy istnieje mała tolerancja dla przestojów.

## <a name="when-to-use-log-replay-service"></a>Kiedy używać usługi powtarzania dzienników

W przypadkach, gdy nie można użyć usługi [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) do migracji, usługa w chmurze LRS może być używana bezpośrednio z programem PowerShell, interfejsami wiersza polecenia lub API, aby ręcznie kompilować i organizować migracje baz danych do wystąpienia zarządzanego SQL. 

Warto rozważyć użycie usługi w chmurze LRS w niektórych następujących przypadkach:
- Dla projektu migracji bazy danych jest wymagana większa kontrola
- Istnieje mała tolerancja dla przestojów uruchomienie produkcyjne migracji
- Nie można zainstalować pliku wykonywalnego DMS w Twoim środowisku
- Plik wykonywalny DMS nie ma dostępu do plików do kopii zapasowych bazy danych
- Dostęp do systemu operacyjnego hosta nie jest dostępny lub nie ma uprawnień administratora

> [!NOTE]
> Zalecany zautomatyzowany sposób migrowania baz danych z SQL Server do wystąpienia zarządzanego SQL korzysta z usługi Azure DMS. Ta usługa korzysta z tej samej usługi LRS w chmurze na zapleczu z funkcją wysyłania dzienników w trybie NORECOVERY. Należy rozważyć ręczne korzystanie z LRS, aby organizować migracje w przypadkach, gdy usługa Azure DMS nie w pełni obsługuje Twoje scenariusze.

## <a name="how-does-it-work"></a>Jak to działa

Kompilowanie niestandardowego rozwiązania przy użyciu LRS do migrowania bazy danych do chmury wymaga wykonania kilku kroków aranżacji na diagramie i przedstawionych w poniższej tabeli.

Migracja obejmuje tworzenie pełnych kopii zapasowych bazy danych na SQL Server i kopiowanie plików kopii zapasowej do usługi Azure Blob Storage. LRS jest używany do przywracania plików kopii zapasowej z platformy Azure Blob Storage do wystąpienia zarządzanego SQL. Blob Storage platformy Azure służy jako magazyn pośredniczący między wystąpieniem zarządzanym SQL Server i SQL.

Usługa LRS będzie monitorować nową różnicową platformę Blob Storage Azure, a kopie zapasowe dzienników dodane po pełnej kopii zapasowej zostaną przywrócone i program automatycznie przywróci nowe dodane pliki. Postęp przywracania plików kopii zapasowej w wystąpieniu zarządzanym SQL może być monitorowany przy użyciu usługi, a proces można także przerwać w razie potrzeby. Bazy danych przywracane podczas procesu migracji będą znajdować się w trybie przywracania i nie mogą być używane do odczytu ani zapisu do momentu ukończenia procesu.

LRS można uruchomić w programie Autouzupełnianie lub w trybie ciągłym. Po uruchomieniu w trybie Autouzupełnianie migracja zostanie zakończona automatycznie po przywróceniu ostatnio określonego pliku kopii zapasowej. Po uruchomieniu w trybie ciągłym usługa będzie stale przywracać wszystkie dodane nowe pliki kopii zapasowej, a migracja zostanie ukończona tylko na uruchomienie produkcyjne ręcznym. Ostatni krok uruchomienie produkcyjne umożliwia udostępnienie baz danych do odczytu i zapisu w wystąpieniu zarządzanym SQL. 

  ![Powtórz kroki aranżacji usługi w dzienniku dla wystąpienia zarządzanego SQL](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operacja | Szczegóły |
| :----------------------------- | :------------------------- |
| **1. Skopiuj kopie zapasowe bazy danych z SQL Server do platformy Azure Blob Storage**. | — Kopiowanie pełnych, różnicowych i dzienników kopii zapasowych z SQL Server do kontenera Blob Storage platformy Azure przy użyciu [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br />— W przypadku migrowania kilku baz danych dla każdej bazy danych wymagany jest osobny folder. |
| **2. Uruchom usługę LRS w chmurze**. | -Usługa można uruchomić za pomocą wybranych poleceń cmdlet: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Poleceń cmdlet az_sql_midb_log_replay_start](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)interfejsu wiersza polecenia. <br /><br />— Po uruchomieniu usługa wykona kopie zapasowe z kontenera usługi Azure Blob Storage i zacznie je przywracać w wystąpieniu sqlmanage. <br /> — Po przywróceniu wszystkich wstępnie przekazanych kopii zapasowych usługa będzie śledzić wszystkie nowe pliki przekazane do folderu i w sposób ciągły stosować dzienniki na podstawie łańcucha LSN, dopóki usługa nie zostanie zatrzymana. |
| **2,1. Monitoruj postęp operacji**. | -Postęp operacji przywracania można monitorować przy użyciu opcji lub poleceń cmdlet: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Poleceń cmdlet az_sql_midb_log_replay_show](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)interfejsu wiersza polecenia. |
| **2,2. Stop\abort operację w razie konieczności**. | -Jeśli proces migracji wymaga przerwania, operacja może zostać zatrzymana przy użyciu wybranych poleceń cmdlet: <br /> PowerShell [stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Poleceń cmdlet [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) interfejsu wiersza polecenia. <br /><br />— Spowoduje to przywrócenie przywróconej bazy danych w wystąpieniu zarządzanym SQL. <br />-Po zatrzymaniu nie można kontynuować LRS dla bazy danych. Proces migracji należy uruchomić ponownie od podstaw. |
| **3. uruchomienie produkcyjne do chmury, gdy jest gotowa**. | -Po przywróceniu wszystkich kopii zapasowych do wystąpienia SQL mnaged, Ukończ uruchomienie produkcyjne przez zainicjowanie operacji zakończono LRS z wyborem wywołania interfejsu API lub poleceniami cmdlet: <br />PowerShell [Complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Poleceń cmdlet [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) interfejsu wiersza polecenia. <br /><br />— Spowoduje to zatrzymanie usługi LRS i przywrócenie bazy danych w wystąpieniu zarządzanym. <br />-Przeciąganie parametrów połączenia aplikacji z SQL Server do wystąpienia zarządzanego SQL. <br />— Baza danych uzupełniania operacji jest dostępna dla operacji języka R/w chmurze. |

## <a name="requirements-for-getting-started"></a>Wymagania dotyczące rozpoczynania pracy

### <a name="sql-server-side"></a>Strona SQL Server
- SQL Server 2008-2019
- Pełna kopia zapasowa baz danych (jeden lub wiele plików)
- Różnicowa kopia zapasowa (jeden lub wiele plików)
- Kopia zapasowa dziennika (niedzielona dla pliku dziennika transakcji)
- **Suma kontrolna musi być włączona** dla kopii zapasowych (obowiązkowe)

### <a name="azure-side"></a>Po stronie platformy Azure
- PowerShell AZ. SQL module w wersji 2.16.0 lub nowszej ([Instalowanie](https://www.powershellgallery.com/packages/Az.Sql/)lub korzystanie z platformy Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
- Interfejs wiersza polecenia w wersji 2.19.0 lub nowszej ([Instalacja](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Obsługa kontenera Blob Storage platformy Azure
- Token zabezpieczający SAS z uprawnieniami tylko do **odczytu** i **listy** wygenerowanymi dla kontenera magazynu obiektów BLOB

## <a name="best-practices"></a>Najlepsze rozwiązania

W przypadku najlepszych rozwiązań zaleca się używanie następujących metod:
- Uruchom [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) , aby sprawdzić, czy bazy danych nie będą miały żadnych problemów migrowanych do wystąpienia zarządzanego SQL. 
- Podziel pełne i różnicowe kopie zapasowe na wiele plików zamiast pojedynczego pliku.
- Włącz kompresję kopii zapasowej.
- Użyj Cloud Shell do wykonywania skryptów, ponieważ zawsze będzie ona aktualizowana do najnowszych wydanych poleceń cmdlet.
- Zaplanuj ukończenie migracji w ciągu 47 godzin od momentu uruchomienia usługi LRS.

> [!IMPORTANT]
> - Przywracana baza danych przy użyciu LRS nie może zostać użyta do momentu ukończenia procesu migracji. Wynika to z faktu, że podstawowa technologia to wysyłanie dziennika w trybie NORECOVERY.
> - Tryb WSTRZYMAnia dla wysyłania dziennika nie jest obsługiwany przez LRS z powodu różnic wersji między wystąpieniem zarządzanym SQL a najnowszą wersją SQL Server w wersji na rynku.

## <a name="steps-to-execute"></a>Kroki do wykonania

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopiuj kopie zapasowe z SQL Server na platformę Azure Blob Storage

Poniższe dwa podejścia mogą być wykorzystane do kopiowania kopii zapasowych do magazynu obiektów BLOB w celu migrowania baz danych do wystąpienia zarządzanego przy użyciu LRS:
- Korzystanie z funkcji SQL Server natywnej [kopii zapasowej na potrzeby adresu URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Kopiowanie kopii zapasowych do kontenera obiektów BLOB za pomocą [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Tworzenie tokenu uwierzytelniania usługi Azure BLOB i sygnatury dostępu współdzielonego

Usługa Azure Blob Storage jest używana jako magazyn pośredniczący dla plików kopii zapasowych między SQL Server i wystąpieniem zarządzanym SQL. Wykonaj następujące kroki, aby utworzyć kontener usługi Azure Blob Storage:

1. [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Crete kontenera obiektów BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) w ramach konta magazynu

Po utworzeniu kontenera obiektów BLOB Generuj token uwierzytelniania SAS z uprawnieniami do odczytu i listy, wykonując następujące czynności:

1. Dostęp do konta magazynu przy użyciu Azure Portal
2. Przejdź do Eksplorator usługi Storage
3. Rozwijanie kontenerów obiektów BLOB
4. Kliknij prawym przyciskiem myszy kontener obiektów BLOB
5. Wybierz pozycję Pobierz sygnaturę dostępu współdzielonego
6. Wybierz przedział czasu wygaśnięcia tokenu. Upewnij się, że token jest prawidłowy dla czasu trwania migracji.
7. Upewnij się, że wybrano uprawnienia tylko do odczytu i listy
8. Kliknij przycisk Utwórz
9. Skopiuj token zaczynający się od "OHR =" w identyfikatorze URI do użycia w kodzie

> [!IMPORTANT]
> Uprawnienia do tokenu SAS dla usługi Azure Blob Storage muszą być tylko do odczytu i listy. W przypadku innych uprawnień udzielonych dla tokenu uwierzytelniania SAS uruchamianie usługi LRS zakończy się niepowodzeniem. Te wymagania dotyczące zabezpieczeń zostały zaprojektowane.

## <a name="log-in-to-azure-and-select-subscription"></a>Zaloguj się do platformy Azure i wybierz pozycję subskrypcja

Aby zalogować się do platformy Azure, użyj następującego polecenia cmdlet programu PowerShell:

```powershell
Login-AzAccount
```

Wybierz odpowiednią subskrypcję, w której znajduje się wystąpienie zarządzane SQL, przy użyciu następującego polecenia cmdlet programu PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Rozpoczynanie migracji

Migracja jest uruchamiana przez uruchomienie usługi LRS. Usługa może być uruchamiana w ramach autouzupełniania lub trybu ciągłego. Po uruchomieniu w trybie Autouzupełnianie migracja zostanie zakończona automatycznie po przywróceniu ostatnio określonego pliku kopii zapasowej. Ta opcja wymaga uruchomienia polecenia, aby określić nazwę pliku z ostatnim plikiem kopii zapasowej. Gdy LRS jest uruchamiany w trybie ciągłym, usługa będzie stale przywracać wszystkie dodane nowe pliki kopii zapasowej, a migracja zostanie ukończona tylko w ręcznym uruchomienie produkcyjne. 

### <a name="start-lrs-in-autocomplete-mode"></a>Uruchom LRS w trybie Autouzupełnianie

Aby uruchomić usługę LRS w trybie Autouzupełnianie, użyj następujących poleceń programu PowerShell lub interfejsu wiersza polecenia. Określ nazwę ostatniego pliku kopii zapasowej z parametrem-LastBackupName. Po przywróceniu ostatniej określonej nazwy pliku kopii zapasowej usługa automatycznie inicjuje uruchomienie produkcyjne.

Uruchom LRS w trybie Autouzupełnianie — przykład programu PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Uruchom LRS w trybie Autouzupełnianie — przykład interfejsu wiersza polecenia:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Uruchom LRS w trybie ciągłym

Uruchom LRS w trybie ciągłym — przykład programu PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Uruchom LRS w trybie ciągłym — przykład interfejsu wiersza polecenia:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Po rozpoczęciu LRS wszystkie poprawki oprogramowania zarządzane przez system zostaną zatrzymane przez następne 47 godzin. Po przekazaniu tego okna kolejna automatyczna poprawka programowa automatycznie zatrzyma trwającą LRS. W takim przypadku migracja nie może zostać wznowiona i musi zostać ponownie uruchomiona od podstaw. 

## <a name="monitor-the-migration-progress"></a>Monitorowanie postępu migracji

Aby monitorować postęp operacji migracji, użyj następującego polecenia programu PowerShell:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby monitorować postęp operacji migracji, należy użyć poniższego polecenia CLI:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zatrzymaj migrację

W przypadku konieczności zatrzymania migracji należy użyć następujących poleceń cmdlet. Zatrzymanie migracji spowoduje usunięcie bazy danych przywracania z wystąpienia zarządzanego SQL, z powodu której nie będzie możliwe wznowienie migracji.

Aby stop\abort proces migracji, użyj następującego polecenia programu PowerShell:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby stop\abort proces migracji, użyj następującego polecenia CLI:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Zakończ migrację (tryb ciągły)

W przypadku gdy LRS jest uruchamiany w trybie ciągłym, po upewnieniu się, że wszystkie kopie zapasowe zostały przywrócone, zainicjowanie uruchomienie produkcyjne spowoduje zakończenie migracji. Po zakończeniu uruchomienie produkcyjne baza danych zostanie zmigrowana i będzie gotowa do odczytu i zapisu.

Aby ukończyć proces migracji w trybie ciągłym LRS, użyj następującego polecenia programu PowerShell:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Aby dokończyć proces migracji w trybie ciągłym LRS, użyj następującego polecenia CLI:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [migrowania SQL Server do wystąpienia zarządzanego SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Dowiedz się więcej o [różnicach między SQL Server i wystąpieniem zarządzanym usługi Azure SQL](transact-sql-tsql-differences-sql-server.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących kosztów i rozmiarów obciążeń migrowanych do platformy Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
