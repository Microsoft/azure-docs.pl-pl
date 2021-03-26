---
title: Migrowanie baz danych do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
description: Dowiedz się, jak migrować bazy danych z SQL Server do wystąpienia zarządzanego SQL za pomocą usługi powtarzania dzienników
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 1b2a3f018b16258622b817648cb00e230313bf49
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564521"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrowanie baz danych z SQL Server do wystąpienia zarządzanego SQL za pomocą usługi powtarzania dzienników (wersja zapoznawcza)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak ręcznie skonfigurować migrację bazy danych z SQL Server 2008-2019 do wystąpienia zarządzanego Azure SQL, używając usługi log Powtórzeniaing (LRS), która jest obecnie dostępna w publicznej wersji zapoznawczej. LRS to usługa w chmurze, która jest włączona dla wystąpienia zarządzanego SQL i oparta na SQL Server technologii dostarczania dzienników. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) i LRS używają tej samej podstawowej technologii migracji i tych samych interfejsów API. Zwalniając LRS, możemy dodatkowo włączyć złożone migracje niestandardowe i architekturę hybrydową między lokalnymi SQL Server i wystąpieniem zarządzanym SQL.

## <a name="when-to-use-log-replay-service"></a>Kiedy używać usługi powtarzania dzienników

Gdy nie można użyć Azure Database Migration Service do migracji, można użyć programu LRS bezpośrednio z programem PowerShell, poleceniami cmdlet interfejsu CLI platformy Azure lub interfejsów API do ręcznego kompilowania i organizowania migracji bazy danych do wystąpienia zarządzanego SQL. 

Można rozważyć użycie LRS w następujących przypadkach:
- Potrzebna jest większa kontrola nad projektem migracji bazy danych.
- Niewielka tolerancja dla przestojów uruchomienie produkcyjne migracji.
- Database Migration Service pliku wykonywalnego nie można zainstalować w Twoim środowisku.
- Database Migration Service pliku wykonywalnego nie ma dostępu do plików kopii zapasowych bazy danych.
- Dostęp do systemu operacyjnego hosta nie jest dostępny lub nie ma uprawnień administratora.
- Nie można otworzyć portów sieciowych z Twojego środowiska na platformie Azure.
- Kopie zapasowe są przechowywane bezpośrednio w usłudze Azure Blob Storage przy użyciu `TO URL` opcji.
- Należy użyć różnicowych kopii zapasowych.

> [!NOTE]
> Zalecamy automatyzację migracji baz danych z SQL Server do wystąpienia zarządzanego SQL przy użyciu Database Migration Service. Ta usługa używa tej samej usługi w chmurze LRS na zapleczu, z `NORECOVERY` trybem wysyłania dziennika. Rozważ ręczne Użycie LRS do organizowania migracji, gdy Database Migration Service nie w pełni obsługuje Twoje scenariusze.

## <a name="how-it-works"></a>Jak to działa

Tworzenie niestandardowego rozwiązania przy użyciu LRS do migrowania baz danych do chmury wymaga wykonania kilku kroków związanych z aranżacją, jak pokazano na diagramie i tabeli w dalszej części tej sekcji.

Migracja obejmuje tworzenie pełnych kopii zapasowych bazy danych na SQL Server z `CHECKSUM` włączonym i kopiowaniem plików kopii zapasowej do usługi Azure Blob Storage. LRS jest używany do przywracania plików kopii zapasowych z Blob Storage do wystąpienia zarządzanego SQL. Blob Storage jest magazynem pośrednim między SQL Server i wystąpieniem zarządzanym SQL.

LRS monitoruje Blob Storage dla każdej nowej różnicowej lub kopii zapasowej dziennika dodanej po przywróceniu pełnej kopii zapasowej. LRS następnie automatycznie przywraca te nowe pliki. Usługi można użyć do monitorowania postępu przywracania plików kopii zapasowej w wystąpieniu zarządzanym SQL. w razie potrzeby można zatrzymać proces.

LRS nie wymaga określonej konwencji nazewnictwa dla plików kopii zapasowej. Skanuje wszystkie pliki umieszczone w Blob Storage i konstruuje łańcuch kopii zapasowych, odczytując tylko nagłówki plików. Podczas procesu migracji bazy danych są w stanie "Przywracanie". Bazy danych są przywracane w trybie [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) , dlatego nie można ich użyć do odczytu lub zapisu do momentu ukończenia procesu migracji. 

W przypadku migrowania kilku baz danych należy:
 
- Umieść kopie zapasowe dla każdej bazy danych w osobnym folderze na Blob Storage.
- Zacznij LRS oddzielnie dla każdej bazy danych.
- Określ różne ścieżki, aby oddzielić foldery Blob Storage. 

LRS można uruchomić w trybie *Autouzupełnianie* lub *ciągłym* . Po uruchomieniu w trybie Autouzupełnianie migracja zostanie zakończona automatycznie po przywróceniu ostatniego z określonych plików kopii zapasowej. Po rozpoczęciu LRS w trybie ciągłym usługa będzie stale przywracać wszystkie dodane nowe pliki kopii zapasowej, a migracja zostanie zakończona tylko w przypadku ręcznego uruchomienie produkcyjne. 

Zalecamy ręczne wycinanie po zakończeniu ostatecznej kopii zapasowej dziennika i jest ono wyświetlane jako przywrócone w wystąpieniu zarządzanym SQL. Ostatni krok uruchomienie produkcyjne przejdzie do trybu online i będzie dostępny do odczytu i zapisu w wystąpieniu zarządzanym SQL.

Po zatrzymaniu usługi LRS automatycznie za pomocą funkcji Autouzupełnianie lub ręcznie za pomocą uruchomienie produkcyjne, nie można wznowić procesu przywracania bazy danych, która została przełączona w tryb online w wystąpieniu zarządzanym SQL. Aby przywrócić dodatkowe pliki kopii zapasowej po zakończeniu migracji za pomocą funkcji Autouzupełnianie lub uruchomienie produkcyjne, należy usunąć bazę danych. Należy również przywrócić cały łańcuch kopii zapasowych od podstaw, ponownie uruchamiając LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram, który objaśnia kroki aranżacji usługi log Powtórz dla wystąpienia zarządzanego SQL." border="false":::
    
| Operacja | Szczegóły |
| :----------------------------- | :------------------------- |
| **1. Skopiuj kopie zapasowe bazy danych z SQL Server do BLOB Storage**. | Kopiowanie pełnych, różnicowych i dzienników kopii zapasowych z SQL Server do kontenera Blob Storage przy użyciu [AzCopy](../../storage/common/storage-use-azcopy-v10.md) lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Użyj dowolnych nazw plików. LRS nie wymaga określonej konwencji nazewnictwa plików.<br /><br />W przypadku migrowania kilku baz danych konieczne jest oddzielny folder dla każdej bazy danych. |
| **2. Uruchom LRS w chmurze**. | Usługę można uruchomić ponownie przy użyciu wybranych poleceń cmdlet: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) lub interfejsu wiersza polecenia platformy Azure ([az_sql_midb_log_replay_start poleceń cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Uruchom LRS oddzielnie dla każdej bazy danych, która wskazuje folder kopii zapasowej na Blob Storage. <br /><br /> Po uruchomieniu usługi zostaną wykonane kopie zapasowe z kontenera Blob Storage i rozpocznie się ich przywracanie w wystąpieniu zarządzanym SQL.<br /><br /> Jeśli uruchomiono LRS w trybie ciągłym, po przywróceniu wszystkich wstępnie przekazanych kopii zapasowych usługa będzie oglądać wszystkie nowe pliki przekazane do folderu. Usługa będzie stale stosować dzienniki na podstawie łańcucha numeru sekwencyjnego dziennika (LSN), dopóki nie zostanie zatrzymana. |
| **2,1. Monitoruj postęp operacji**. | Możesz monitorować postęp operacji przywracania za pomocą wybranych poleceń cmdlet: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) lub interfejsu wiersza polecenia platformy Azure ([az_sql_midb_log_replay_show poleceń cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. Zatrzymaj operację w razie konieczności**. | Jeśli chcesz zatrzymać proces migracji, masz możliwość wyboru poleceń cmdlet: PowerShell ([stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) lub interfejsu wiersza polecenia platformy Azure ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Zatrzymanie operacji spowoduje usunięcie bazy danych, która jest przywracana w wystąpieniu zarządzanym SQL. Po zatrzymaniu operacji nie można wznowić LRS dla bazy danych. Należy ponownie uruchomić proces migracji od podstaw. |
| **3. Wytnij do chmury, gdy wszystko będzie gotowe**. | Zatrzymaj aplikację i obciążenie. Wypełnij ostatnią kopię zapasową dziennika i przekaż ją do usługi Azure Blob Storage.<br /><br /> Ukończ uruchomienie produkcyjne przez zainicjowanie `complete` operacji LRS z użyciem wybranych poleceń cmdlet: PowerShell ([Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) lub interfejsu wiersza polecenia platformy Azure [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Ta operacja przestanie być LRS i spowoduje, że baza danych zostanie przełączona w tryb online w celu odczytu i zapisu w wystąpieniu zarządzanym SQL.<br /><br /> Odwskaż parametry połączenia aplikacji z SQL Server do wystąpienia zarządzanego SQL. |

## <a name="requirements-for-getting-started"></a>Wymagania dotyczące rozpoczynania pracy

### <a name="sql-server-side"></a>Strona SQL Server
- SQL Server 2008-2019
- Pełna kopia zapasowa baz danych (jeden lub wiele plików)
- Różnicowa kopia zapasowa (jeden lub wiele plików)
- Kopia zapasowa dziennika (niedzielona dla pliku dziennika transakcji)
- `CHECKSUM` włączone dla kopii zapasowych (obowiązkowe)

### <a name="azure-side"></a>Po stronie platformy Azure
- Program PowerShell AZ. SQL module w wersji 2.16.0 lub nowszej ([zainstalowaną](https://www.powershellgallery.com/packages/Az.Sql/) lub dostępną za poorednictwem [Azure Cloud Shell](/azure/cloud-shell/))
- Interfejs wiersza polecenia platformy Azure w wersji 2.19.0 lub nowszej ([zainstalowany](/cli/azure/install-azure-cli))
- Obsługa kontenera Blob Storage platformy Azure
- Token zabezpieczający sygnatury dostępu współdzielonego (SAS) z uprawnieniami do odczytu i listy wygenerowanymi dla kontenera Blob Storage

### <a name="migration-of-multiple-databases"></a>Migracja wielu baz danych
Należy umieścić pliki kopii zapasowych dla różnych baz danych w oddzielnych folderach na Blob Storage.

Zacznij LRS oddzielnie dla każdej bazy danych, wskazując odpowiedni folder na Blob Storage. LRS może obsługiwać maksymalnie 100 procesów przywracania na jednym zarządzanym wystąpieniu.

### <a name="azure-rbac-permissions"></a>Uprawnienia kontroli RBAC platformy Azure
Uruchomienie LRS za pomocą podanych klientów wymaga jednej z następujących ról platformy Azure:
- Rola właściciela subskrypcji
- Rola [współautora wystąpienia zarządzanego](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Rola niestandardowa z następującymi uprawnieniami: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Najlepsze rozwiązania

Zalecamy następujące najlepsze rozwiązania:
- Uruchom [Data Migration Assistant](/sql/dma/dma-overview) , aby sprawdzić, czy bazy danych są gotowe do migracji do wystąpienia zarządzanego SQL. 
- Podziel pełne i różnicowe kopie zapasowe na wiele plików, zamiast korzystać z pojedynczego pliku.
- Włącz kompresję kopii zapasowej.
- Użyj Cloud Shell do uruchamiania skryptów, ponieważ zawsze będzie ona aktualizowana do najnowszych wydanych poleceń cmdlet.
- Zaplanuj ukończenie migracji w ciągu 47 godzin od rozpoczęcia LRS. Jest to okres prolongaty, który uniemożliwia instalację poprawek oprogramowania zarządzanych przez system.

> [!IMPORTANT]
> - Nie można użyć bazy danych, która jest przywracana za pomocą LRS, dopóki nie zakończy się proces migracji. 
> - LRS nie obsługuje dostępu tylko do odczytu do baz danych podczas migracji.
> - Po zakończeniu migracji proces migracji zostanie sfinalizowany, ponieważ LRS nie obsługuje wznawiania procesu przywracania.

## <a name="steps-to-execute"></a>Kroki do wykonania

### <a name="make-backups-of-sql-server"></a>Tworzenie kopii zapasowych SQL Server

Kopie zapasowe SQL Server można tworzyć przy użyciu jednej z następujących opcji:

- Utwórz kopię zapasową magazynu na dysku lokalnym, a następnie Przekaż pliki do usługi Azure Blob Storage, jeśli środowisko ogranicza bezpośrednie kopie zapasowe do Blob Storage.
- Wykonaj kopię zapasową bezpośrednio, aby Blob Storage z `TO URL` opcją w języku T-SQL, jeśli pozwala na to środowisko i procedury zabezpieczeń. 

Ustawianie baz danych, które mają zostać poddane migracji do trybu pełnego odzyskiwania, aby umożliwić tworzenie kopii zapasowych dzienników.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Aby ręcznie wprowadzić pełne, różnicowe i Rejestruj kopie zapasowe bazy danych w magazynie lokalnym, użyj następujących skryptów T-SQL. Upewnij się, że `CHECKSUM` opcja jest włączona, ponieważ jest wymagana do LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Usługa Azure Blob Storage jest używana jako magazyn pośredniczący dla plików kopii zapasowych między SQL Server i wystąpieniem zarządzanym SQL. Aby utworzyć nowe konto magazynu i kontener obiektów BLOB w ramach konta magazynu, wykonaj następujące kroki:

1. [Utwórz konto magazynu](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete kontenera obiektów BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md) w ramach konta magazynu.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopiuj kopie zapasowe z SQL Server do Blob Storage

W przypadku migrowania baz danych do wystąpienia zarządzanego przy użyciu programu LRS można użyć następujących metod, aby przekazać kopie zapasowe do Blob Storage:
- Używanie SQL Server natywnej [kopii zapasowej do obsługi adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Przekazywanie kopii zapasowych do kontenera obiektów BLOB za pomocą [AzCopy](../../storage/common/storage-use-azcopy-v10.md) lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer)
- Używanie Eksplorator usługi Storage w Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Utwórz kopie zapasowe SQL Server bezpośrednio do Blob Storage
Jeśli zasady firmowe i sieciowe zezwalają na to, alternatywą jest tworzenie kopii zapasowych z SQL Server bezpośrednio do Blob Storage przy użyciu opcji SQL Server natywnej [kopii zapasowej na adres URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Jeśli możesz wykonać tę opcję, nie musisz tworzyć kopii zapasowych w magazynie lokalnym i przekazywać ich do Blob Storage.

W pierwszym kroku ta operacja wymaga wygenerowania tokenu uwierzytelniania sygnatury dostępu współdzielonego dla Blob Storage, a następnie zaimportowania tokenu do SQL Server. Drugim krokiem jest wykonanie kopii zapasowej przy użyciu `TO URL` opcji w języku T-SQL. Upewnij się, że wszystkie kopie zapasowe są wykonywane z `CHEKSUM` włączoną opcją.

W przypadku odwołania Poniższy przykładowy kod tworzy kopie zapasowe Blob Storage. Ten przykład nie zawiera instrukcji dotyczących importowania tokenu sygnatury dostępu współdzielonego. Szczegółowe instrukcje, w tym sposób generowania i importowania tokenu SAS do SQL Server, można znaleźć w samouczku [Korzystanie z usługi Azure Blob Storage z SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Wygeneruj token uwierzytelniania SAS Blob Storage dla LRS

Usługa Azure Blob Storage jest używana jako magazyn pośredniczący dla plików kopii zapasowych między SQL Server i wystąpieniem zarządzanym SQL. Musisz wygenerować token uwierzytelniania sygnatury dostępu współdzielonego z uprawnieniami do wyświetlania i odczytywania dla LRS. Token umożliwi LRS dostęp do Blob Storage i używanie plików kopii zapasowej do przywracania ich w wystąpieniu zarządzanym SQL. 

Wykonaj następujące kroki, aby wygenerować token:

1. Otwórz Eksplorator usługi Storage z Azure Portal.
2. Rozwiń węzeł **kontenery obiektów BLOB**.
3. Kliknij prawym przyciskiem myszy kontener obiektów blob i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Zrzut ekranu pokazujący wybory do generowania tokenu uwierzytelniania S A S.":::

4. Wybierz przedział czasu dla wygaśnięcia tokenu. Upewnij się, że token jest prawidłowy dla czasu trwania migracji.
5. Wybierz strefę czasową dla tokenu: UTC lub czas lokalny.
    
   > [!IMPORTANT]
   > Strefa czasowa tokenu i wystąpienia zarządzanego mogą niezgodność. Upewnij się, że token sygnatury dostępu współdzielonego ma odpowiedni czas ważności, biorąc pod uwagę strefy czasowe. Jeśli to możliwe, ustaw strefę czasową na wcześniejszą i późniejszą godzinę planowanego okna migracji.
6. Wybierz tylko uprawnienia do **odczytu** i **listy** .

   > [!IMPORTANT]
   > Nie zaznaczaj żadnych innych uprawnień. Jeśli to zrobisz, LRS nie zostanie uruchomiona. To wymaganie dotyczące zabezpieczeń jest zaprojektowane.
7. Wybierz przycisk **Utwórz**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Zrzut ekranu pokazujący opcje wygaśnięcia tokenu S, strefy czasowej i uprawnień, a także przycisk Utwórz.":::

Uwierzytelnianie sygnatury dostępu współdzielonego jest generowane z określonym okresem ważności. Wymagana jest wersja identyfikatora URI tokenu, jak pokazano na poniższym zrzucie ekranu.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Zrzut ekranu przedstawiający przykład wersji U I tokenu S A s.":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopiowanie parametrów z tokenu SAS

Przed użyciem tokenu sygnatury dostępu współdzielonego do rozpoczęcia LRS należy zrozumieć jego strukturę. Identyfikator URI wygenerowanego tokenu SYGNATURy dostępu współdzielonego składa się z dwóch części oddzielonych znakiem zapytania ( `?` ), jak pokazano w tym przykładzie:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Przykład U R I dla wygenerowanego tokenu S A S dla usługi powtórzenia dziennika." border="false":::

Pierwsza część, rozpoczynająca się `https://` do znaku zapytania ( `?` ), jest używana dla `StorageContainerURI` parametru, który jest podawany jako wejściowy do LRS. Zapewnia LRS informacji o folderze, w którym są przechowywane pliki kopii zapasowej bazy danych.

Druga część, rozpoczynająca się od znaku zapytania ( `?` ) i przechodzenie do końca ciągu, jest `StorageContainerSasToken` parametrem. Jest to rzeczywisty podpisany token uwierzytelniania, który jest ważny przez określony czas. Ta część nie musi rozpoczynać się od `sp=` , jak pokazano w przykładzie. Twoje przypadki mogą się różnić.

Skopiuj parametry w następujący sposób:

1. Skopiuj pierwszą część tokenu, rozpoczynając od `https://` całej metody aż do znaku zapytania ( `?` ). Użyj go jako `StorageContainerUri` parametru w programie PowerShell lub interfejsu wiersza polecenia platformy Azure do uruchomienia LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Zrzut ekranu pokazujący kopiowanie pierwszej części tokenu.":::

2. Skopiuj drugą część tokenu, rozpoczynając od znaku zapytania ( `?` ) aż do końca ciągu. Użyj go jako `StorageContainerSasToken` parametru w programie PowerShell lub interfejsu wiersza polecenia platformy Azure do uruchomienia LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Zrzut ekranu pokazujący kopiowanie drugiej części tokenu.":::
   
> [!NOTE]
> Nie dołączaj znaku zapytania podczas kopiowania którejkolwiek części tokenu.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Zaloguj się do platformy Azure i wybierz subskrypcję

Aby zalogować się do platformy Azure, użyj następującego polecenia cmdlet programu PowerShell:

```powershell
Login-AzAccount
```

Wybierz odpowiednią subskrypcję, w której znajduje się wystąpienie zarządzane, przy użyciu następującego polecenia cmdlet programu PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Rozpoczynanie migracji

Rozpoczęto migrację, uruchamiając LRS. Usługę można uruchomić w trybie Autouzupełnianie lub ciągłym. 

W przypadku korzystania z trybu Autouzupełnianie migracja zostanie zakończona automatycznie po przywróceniu ostatniego z określonych plików kopii zapasowej. Ta opcja wymaga uruchomienia polecenia, aby określić nazwę pliku z ostatnim plikiem kopii zapasowej. 

W przypadku korzystania z trybu ciągłego usługa ciągle przywraca wszystkie dodane nowe pliki kopii zapasowej. Migracja zostanie zakończona tylko ręcznie uruchomienie produkcyjne. 

### <a name="start-lrs-in-autocomplete-mode"></a>Uruchom LRS w trybie Autouzupełnianie

Aby rozpocząć LRS w trybie Autouzupełnianie, użyj następujących poleceń programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Określ nazwę ostatniego pliku kopii zapasowej za pomocą `-LastBackupName` parametru. Po przywróceniu ostatniego z określonych plików kopii zapasowej usługa automatycznie inicjuje uruchomienie produkcyjne.

Oto przykład uruchamiania LRS w trybie Autouzupełnianie przy użyciu programu PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Oto przykład uruchamiania LRS w trybie Autouzupełnianie przy użyciu interfejsu wiersza polecenia platformy Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Uruchom LRS w trybie ciągłym

Oto przykład uruchamiania LRS w trybie ciągłym przy użyciu programu PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Oto przykład uruchamiania LRS w trybie ciągłym przy użyciu interfejsu wiersza polecenia platformy Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Klienci programu PowerShell i interfejsu wiersza polecenia do uruchamiania LRS w trybie ciągłym są synchronicznie. Oznacza to, że klienci oczekują, że odpowiedź interfejsu API będzie zgłaszać sukces lub niepowodzenie uruchomienia zadania. 

W trakcie tego oczekiwania polecenie nie zwróci kontroli do wiersza polecenia. Jeśli wykonujesz obsługę skryptów w środowisku migracji, a potrzebujesz polecenia startowego LRS, aby natychmiast wprowadzić dalszą kontrolę z resztą skryptu, możesz uruchomić program PowerShell jako zadanie w tle z `-AsJob` przełącznikiem. Na przykład:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Po uruchomieniu zadania w tle obiekt zadania wraca natychmiast, nawet jeśli ukończenie zadania trwa dłuższy czas. Po uruchomieniu zadania można kontynuować pracę w sesji bez przerw. Aby uzyskać szczegółowe informacje na temat uruchamiania programu PowerShell jako zadania w tle, zobacz dokumentację [zadania startowego programu PowerShell](/powershell/module/microsoft.powershell.core/start-job#description) .

Podobnie, aby uruchomić polecenie interfejsu wiersza polecenia platformy Azure w systemie Linux jako proces w tle, użyj znaku handlowego "i" na `&` końcu polecenia LRS Start:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Po rozpoczęciu LRS wszystkie poprawki oprogramowania zarządzane przez system są zatrzymywane przez 47 godzin. Po tym oknie kolejna automatyczna poprawka programowa zostanie automatycznie zatrzymana LRS. W takim przypadku nie można wznowić migracji i należy uruchomić ją od podstaw. 

## <a name="monitor-the-migration-progress"></a>Monitorowanie postępu migracji

Aby monitorować postęp migracji za pomocą programu PowerShell, użyj następującego polecenia:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby monitorować postęp migracji za pomocą interfejsu wiersza polecenia platformy Azure, użyj następującego polecenia:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zatrzymaj migrację

Jeśli zachodzi potrzeba zatrzymania migracji, należy użyć następujących poleceń cmdlet. Zatrzymanie migracji spowoduje usunięcie bazy danych przywracania z wystąpienia zarządzanego SQL, dlatego wznowienie migracji nie będzie możliwe.

Aby zatrzymać proces migracji za pomocą programu PowerShell, użyj następującego polecenia:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby zatrzymać proces migracji za pomocą interfejsu wiersza polecenia platformy Azure, użyj następującego polecenia:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Zakończ migrację (tryb ciągły)

Po rozpoczęciu LRS w trybie ciągłym po upewnieniu się, że wszystkie kopie zapasowe zostały przywrócone, zainicjowanie uruchomienie produkcyjne spowoduje zakończenie migracji. Po uruchomienie produkcyjne baza danych zostanie zmigrowana i będzie gotowa do odczytu i zapisu.

Aby ukończyć proces migracji w trybie ciągłym LRS za pomocą programu PowerShell, użyj następującego polecenia:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Aby ukończyć proces migracji w trybie ciągłym LRS za pomocą interfejsu wiersza polecenia platformy Azure, użyj następującego polecenia:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

Ograniczenia funkcjonalne LRS są następujące:
- Przywracanej bazy danych nie można używać na potrzeby dostępu tylko do odczytu podczas procesu migracji.
- Poprawki oprogramowania zarządzane przez system są blokowane przez 47 godzin po rozpoczęciu LRS. Po wygaśnięciu tego przedziału czasu kolejne aktualizacje oprogramowania przestaną LRS. Następnie należy ponownie uruchomić LRS od podstaw.
- LRS wymaga, aby kopie zapasowe baz danych w SQL Server z `CHECKSUM` włączoną opcją.
- Token sygnatury dostępu współdzielonego, który będzie używany przez LRS, musi być wygenerowany dla całego kontenera usługi Azure Blob Storage i musi mieć tylko uprawnienia do odczytu i wyświetlania listy.
- Pliki kopii zapasowych dla różnych baz danych muszą być umieszczone w oddzielnych folderach w Blob Storage.
- LRS należy uruchomić oddzielnie dla każdej bazy danych, która wskazuje osobne foldery z plikami kopii zapasowych na Blob Storage.
- LRS może obsługiwać maksymalnie 100 procesów przywracania na jednym zarządzanym wystąpieniu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po rozpoczęciu LRS Użyj polecenia cmdlet monitorowania ( `get-azsqlinstancedatabaselogreplay` lub `az_sql_midb_log_replay_show` ), aby wyświetlić stan operacji. Jeśli LRS nie powiedzie się po pewnym czasie i zostanie wyświetlony komunikat o błędzie, sprawdź najczęstsze problemy:

- Czy istniejąca baza danych w wystąpieniu zarządzanym SQL ma taką samą nazwę jak ta, którą próbujesz zmigrować z SQL Server? Rozwiąż ten konflikt, zmieniając nazwę jednej z baz danych.
- Czy utworzono kopię zapasową bazy danych SQL Server za pomocą `CHECKSUM` opcji?
- Czy uprawnienia do tokenu SAS są tylko do odczytu i listy dla LRS?
- Czy został skopiowany token sygnatury dostępu współdzielonego dla LRS po znaku zapytania ( `?` ), którego zawartość zaczyna wyglądać następująco: `sv=2020-02-10...` ? 
- Czy czas ważności tokenu sygnatury dostępu współdzielonego dotyczy czasu rozpoczęcia i zakończenia migracji? Mogą występować niezgodności ze względu na różne strefy czasowe używane dla wystąpienia zarządzanego SQL i token sygnatury dostępu współdzielonego. Spróbuj ponownie wygenerować token sygnatury dostępu współdzielonego i przedłuż ważność ważności przedziału czasu przed i po bieżącym dniu.
- Czy nazwa bazy danych, nazwa grupy zasobów i nazwa wystąpienia zarządzanego są poprawnie napisane?
- Jeśli uruchomiono LRS w trybie Autouzupełnianie, była prawidłową nazwą pliku dla ostatniego określonego pliku kopii zapasowej?

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [migrowania SQL Server do wystąpienia zarządzanego SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Dowiedz się więcej o [różnicach między SQL Server i wystąpieniem zarządzanym programu SQL Server](transact-sql-tsql-differences-sql-server.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących kosztów i rozmiarów obciążeń migrowanych do platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).