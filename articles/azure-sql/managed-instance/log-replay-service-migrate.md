---
title: Migrowanie baz danych do SQL Managed Instance przy użyciu usługi Log Replay
description: Dowiedz się, jak migrować bazy danych z SQL Server do SQL Managed Instance przy użyciu usługi ponownego odtwarzania dziennika
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484034"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrowanie baz danych z SQL Server do SQL Managed Instance przy użyciu usługi Ponowne odtwarzanie dziennika (wersja zapoznawcza)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak ręcznie skonfigurować migrację bazy danych z programu SQL Server 2008–2019 do programu Azure SQL Managed Instance przy użyciu usługi Log Replay Service (LRS), która jest obecnie dostępna w publicznej wersji zapoznawczej. LRS to usługa w chmurze, która jest włączona dla SQL Managed Instance i jest oparta SQL Server technologii wysyłki dzienników. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) i LRS używają tej samej podstawowej technologii migracji i tych samych interfejsów API. Zwalniając LRS, umożliwiamy jeszcze bardziej złożone migracje niestandardowe i architekturę hybrydową między środowiskiem lokalnym SQL Server a SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Kiedy używać usługi ponownego odtwarzania dzienników

Jeśli nie możesz użyć usługi Azure Database Migration Service do migracji, możesz użyć magazynu LRS bezpośrednio z programem PowerShell, poleceniami cmdlet interfejsu wiersza polecenia platformy Azure lub interfejsami API w celu ręcznego kompilowania i organizowania migracji baz danych do SQL Managed Instance. 

Można rozważyć użycie LRS w następujących przypadkach:
- Potrzebujesz większej kontroli nad projektem migracji bazy danych.
- Nie ma niewielkiej tolerancji na przestój podczas migracji migracji.
- Nie Database Migration Service pliku wykonywalnego w twoim środowisku.
- Plik Database Migration Service wykonywalny nie ma dostępu do plików kopii zapasowych bazy danych.
- Dostęp do systemu operacyjnego hosta nie jest dostępny lub nie ma żadnych uprawnień administratora.
- Nie można otwierać portów sieciowych ze środowiska na platformę Azure.
- Problemy z ograniczaniem przepustowości sieci lub blokowaniem serwera proxy w środowisku.
- Kopie zapasowe są przechowywane bezpośrednio w Azure Blob Storage za pośrednictwem `TO URL` opcji .
- Należy użyć różnicowych kopii zapasowych.

> [!NOTE]
> Zalecamy zautomatyzowanie migracji baz danych z SQL Server do SQL Managed Instance przy użyciu Database Migration Service. Ta usługa korzysta z tej samej usługi LRS w chmurze na za back end, z wysyłaniem dziennika w `NORECOVERY` trybie. Rozważ ręczne użycie LRS do organizowania migracji, Database Migration Service scenariusze nie są w pełni obsługiwane.

## <a name="how-it-works"></a>Jak to działa

Tworzenie niestandardowego rozwiązania przy użyciu LRS do migrowania baz danych do chmury wymaga kilku kroków aranżacji, jak pokazano na diagramie i w tabeli w dalszej części tej sekcji.

Migracja obejmuje tworzenie pełnych kopii zapasowych bazy danych na SQL Server z włączoną obsługą oraz kopiowanie plików kopii zapasowej `CHECKSUM` do Azure Blob Storage. LRS służy do przywracania plików kopii zapasowej z Blob Storage do SQL Managed Instance. Blob Storage jest magazynem pośredniczącym między SQL Server i SQL Managed Instance.

LRS monitoruje Blob Storage dla wszystkich nowych różnicowych kopii zapasowych lub dziennika dodanych po przywróceniu pełnej kopii zapasowej. Następnie LRS automatycznie przywraca te nowe pliki. Za pomocą usługi można monitorować postęp przywracania plików kopii zapasowej na SQL Managed Instance i w razie potrzeby zatrzymać proces.

LRS nie wymaga określonej konwencji nazewnictwa dla plików kopii zapasowych. Skanuje wszystkie pliki umieszczone na Blob Storage i konstruuje łańcuch kopii zapasowych, odczytując tylko nagłówki plików. Bazy danych są w stanie "przywracania" podczas procesu migracji. Bazy danych są przywracane w trybie [NORECOVERY,](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) więc nie można ich używać do odczytu ani zapisu do czasu ukończenia procesu migracji. 

W przypadku migrowania kilku baz danych należy:
 
- Kopie zapasowe poszczególnych baz danych należy umieszczać w oddzielnym folderze Blob Storage.
- Uruchom LRS oddzielnie dla każdej bazy danych.
- Określ różne ścieżki, aby oddzielić Blob Storage folderów. 

Usługę LRS można uruchomić w *trybie autouzupełniania* *lub ciągłym.* Po uruchomieniu w trybie autouzupełniania migracja zakończy się automatycznie po przywróceniu ostatniego z określonych plików kopii zapasowej. Po uruchomieniu trybu ciągłego LRS usługa będzie stale przywracać wszystkie nowe dodane pliki kopii zapasowej, a migracja zakończy się tylko w przypadku ręcznego uruchomienia. 

Zalecamy ręczne przecięcie po zakończeniu tworzenia końcowej kopii zapasowej dziennika i jest ona wyświetlana jako przywrócona na SQL Managed Instance. Ostatni krok w trybie cutover sprawi, że baza danych stanie się w trybie online i będzie dostępna do odczytu i zapisu na SQL Managed Instance.

Po zatrzymaniu LRS , automatycznie za pośrednictwem autouzupełniania lub ręcznie w ramach procesów wycinania, nie można wznowić procesu przywracania dla bazy danych, która została w trybie online w SQL Managed Instance. Aby przywrócić dodatkowe pliki kopii zapasowej po zakończeniu migracji za pomocą autouzupełniania lub migracji ponownej, należy usunąć bazę danych. Należy również przywrócić cały łańcuch kopii zapasowych od podstaw przez ponowne uruchomienie magazynu LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram przedstawiający kroki aranżacji usługi Log Replay service SQL Managed Instance." border="false":::
    
| Operacja | Szczegóły |
| :----------------------------- | :------------------------- |
| **1. Kopiowanie kopii zapasowych bazy danych z SQL Server do Blob Storage**. | Kopiowanie pełnych, różnicowych i dziennika kopii zapasowych z SQL Server do Blob Storage za pomocą narzędzia [Azcopy](../../storage/common/storage-use-azcopy-v10.md) [lub Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Użyj dowolnych nazw plików. LRS nie wymaga określonej konwencji nazewnictwa plików.<br /><br />W przypadku migrowania kilku baz danych potrzebny jest oddzielny folder dla każdej bazy danych. |
| **2. Uruchom LRS w chmurze.** | Możesz ponownie uruchomić usługę przy użyciu różnych poleceń cmdlet:[PowerShell (start-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)lub interfejs wiersza polecenia platformy Azure (az_sql_midb_log_replay_start[polecenia cmdlet).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start) <br /><br /> Uruchom LRS oddzielnie dla każdej bazy danych, która wskazuje folder kopii zapasowej na Blob Storage. <br /><br /> Po uruchomieniu usługi będzie ona tworzyć kopie zapasowe z kontenera Blob Storage i przywracać je na SQL Managed Instance.<br /><br /> Jeśli usługa LRS jest uruchomiona w trybie ciągłym, po przywróceniu wszystkich początkowo przekazanych kopii zapasowych usługa będzie obserwować wszystkie nowe pliki przekazane do folderu. Usługa będzie stale stosować dzienniki na podstawie łańcucha numeru sekwencji dzienników (LSN), dopóki nie zostanie zatrzymana. |
| **2.1. Monitoruj postęp operacji**. | Postęp operacji przywracania można monitorować przy użyciu różnych poleceń cmdlet: PowerShell[(get-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)lub interfejs wiersza polecenia platformy Azure[(az_sql_midb_log_replay_show polecenia cmdlet](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. W razie potrzeby zatrzymaj operację**. | Jeśli musisz zatrzymać proces migracji, masz do wyboru polecenia cmdlet: PowerShell[(stop-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)lub interfejs wiersza polecenia platformy Azure[(az_sql_midb_log_replay_stop).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) <br /><br /> Zatrzymanie operacji spowoduje usunięcie przywracanej bazy danych na SQL Managed Instance. Po zatrzymaniu operacji nie można wznowić działania LRS dla bazy danych. Należy ponownie uruchomić proces migracji od podstaw. |
| **3. Przetnij do chmury, gdy wszystko będzie gotowe.** | Zatrzymaj aplikację i obciążenie. Wykonywanie ostatniej kopii zapasowej dziennika i przekazywanie jej do Azure Blob Storage.<br /><br /> Wykonaj czek, inicjując operację LRS z wyborem `complete` poleceń cmdlet: PowerShell[(complete-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)lub interfejs wiersza polecenia platformy Azure az_sql_midb_log_replay_complete . [](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) Ta operacja spowoduje zatrzymanie LRS i spowodować, że baza danych będzie w trybie online do odczytu i zapisu na SQL Managed Instance.<br /><br /> Ponownie punktować parametrów połączenia aplikacji z SQL Server do SQL Managed Instance. Ten krok należy orkiestrować samodzielnie, za pomocą ręcznej zmiany parametrów połączenia w aplikacji lub automatycznie (np. jeśli aplikacja może na przykład odczytać ciąg połączenia z właściwości lub bazy danych). |

## <a name="requirements-for-getting-started"></a>Wymagania dotyczące rozpoczynania pracy

### <a name="sql-server-side"></a>SQL Server strony
- SQL Server 2008–2019
- Pełna kopia zapasowa baz danych (jeden lub wiele plików)
- Różnicowa kopia zapasowa (jeden lub wiele plików)
- Kopia zapasowa dziennika (nie podzielone dla pliku dziennika transakcji)
- `CHECKSUM` włączone dla kopii zapasowych (obowiązkowe)

### <a name="azure-side"></a>Po stronie platformy Azure
- Moduł Az.SQL programu PowerShell w wersji 2.16.0 lub[nowszej](https://www.powershellgallery.com/packages/Az.Sql/) (zainstalowany lub dostępny za [pośrednictwem Azure Cloud Shell](/azure/cloud-shell/))
- Interfejs wiersza polecenia platformy Azure w wersji 2.19.0 lub nowszej[(zainstalowany](/cli/azure/install-azure-cli))
- Azure Blob Storage aprowizowany kontener
- Token zabezpieczający sygnatury dostępu współdzielonego z uprawnieniami odczytu i listy wygenerowanym dla Blob Storage danych

### <a name="migration-of-multiple-databases"></a>Migracja wielu baz danych
Pliki kopii zapasowej dla różnych baz danych należy umieścić w oddzielnych folderach na Blob Storage.

Uruchom LRS oddzielnie dla każdej bazy danych, wskazując odpowiedni folder na Blob Storage. LRS może obsługiwać do 100 równoczesnych procesów przywracania na jedno wystąpienie zarządzane.

### <a name="azure-rbac-permissions"></a>Uprawnienia RBAC platformy Azure
Uruchamianie magazynu LRS za pośrednictwem podanych klientów wymaga jednej z następujących ról platformy Azure:
- Rola właściciela subskrypcji
- [Rola Współautor wystąpienia zarządzanego](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Rola niestandardowa z następującymi uprawnieniami: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Najlepsze rozwiązania

Zalecamy stosowanie następujących najlepszych rozwiązań:
- Uruchom [Data Migration Assistant,](/sql/dma/dma-overview) aby sprawdzić, czy bazy danych są gotowe do migracji do SQL Managed Instance. 
- Pełne i różnicowe kopie zapasowe można podzielić na wiele plików, zamiast korzystać z jednego pliku.
- Włącz kompresję kopii zapasowej.
- Użyj Cloud Shell, aby uruchamiać skrypty, ponieważ zawsze będą one aktualizowane do najnowszych wydanych cmdlet.
- Zaplanuj ukończenie migracji w ciągu 47 godzin po uruchomieniu LRS. Jest to okres prolongaty uniemożliwiający instalację poprawek oprogramowania zarządzanych przez system.

> [!IMPORTANT]
> - Nie można używać bazy danych przywracanej za pośrednictwem usługi LRS do momentu zakończenia procesu migracji. 
> - LRS nie obsługuje dostępu tylko do odczytu do baz danych podczas migracji.
> - Po zakończeniu migracji proces migracji jest finalizowany, ponieważ usługi LRS nie obsługują wznawiania procesu przywracania.

## <a name="steps-to-execute"></a>Kroki do wykonania

### <a name="make-backups-of-sql-server"></a>Tworzenie kopii zapasowych SQL Server

Możesz tworzyć kopie zapasowe SQL Server przy użyciu jednej z następujących opcji:

- Tworzenie kopii zapasowej w magazynie na dysku lokalnym, a następnie przekazywanie plików do Azure Blob Storage, jeśli środowisko ogranicza bezpośrednie kopie zapasowe do Blob Storage.
- Jeśli zezwalają na to procedury środowiska i zabezpieczeń, można wrócić do kopii zapasowej bezpośrednio Blob Storage z opcją w języku `TO URL` T-SQL. 

Ustaw bazy danych, które mają być migrowane do trybu pełnego odzyskiwania, aby umożliwić tworzenie kopii zapasowych dzienników.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Aby ręcznie utworzyć pełne, różnicowe i dziennika kopii zapasowych bazy danych w magazynie lokalnym, użyj następujących przykładowych skryptów języka T-SQL. Upewnij się, `CHECKSUM` że opcja jest włączona, ponieważ jest wymagana w przypadku LRS.

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

Azure Blob Storage jest używany jako pośredni magazyn dla plików kopii zapasowych między SQL Server a SQL Managed Instance. Aby utworzyć nowe konto magazynu i kontener obiektów blob wewnątrz konta magazynu, wykonaj następujące kroki:

1. [Utwórz konto magazynu.](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [W obrębie konta magazynu można](../../storage/blobs/storage-quickstart-blobs-portal.md) ująć kontener obiektów blob.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopiowanie kopii zapasowych z SQL Server do Blob Storage

W przypadku migrowania baz danych do wystąpienia zarządzanego przy użyciu usługi LRS można użyć następujących metod przekazywania kopii zapasowych do Blob Storage:
- Korzystanie SQL Server funkcji [TWORZENIA KOPII ZAPASOWEJ POD ADRESEM URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Przekazywanie kopii zapasowych [Eksplorator usługi Azure Storage](https://azure.microsoft.com/en-us/features/storage-explorer) do kontenera obiektów blob przy użyciu narzędzia [Azcopy](../../storage/common/storage-use-azcopy-v10.md) lub narzędzia Eksplorator usługi Azure Storage
- Używanie Eksplorator usługi Storage w Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Tworzenie kopii zapasowych z SQL Server bezpośrednio do Blob Storage
Jeśli zezwalają na to zasady firmowe i sieciowe, alternatywą jest tworzenie kopii zapasowych z usługi SQL Server bezpośrednio do usługi Blob Storage przy użyciu natywnej SQL Server KOPII ZAPASOWEJ DO [ADRESU URL.](/sql/relational-databases/backup-restore/sql-server-backup-to-url) Jeśli możesz użyć tej opcji, nie musisz tworzyć kopii zapasowych w magazynie lokalnym i przekazywać ich do Blob Storage.

W pierwszym kroku ta operacja wymaga wygenerowania tokenu uwierzytelniania sygnatury dostępu współdzielonego dla Blob Storage a następnie zaimportowania tokenu do SQL Server. Drugim krokiem jest tworzenie kopii zapasowych z `TO URL` opcją w języku T-SQL. Upewnij się, że wszystkie kopie zapasowe są wykonane z `CHEKSUM` włączoną opcją .

Dla porównania poniższy przykładowy kod tworzy kopie zapasowe Blob Storage. Ten przykład nie zawiera instrukcji dotyczących importowania tokenu SAS. Szczegółowe instrukcje, w tym sposób generowania i importowania tokenu SAS do usługi SQL Server, można znaleźć w samouczku Używanie Azure Blob Storage [z](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)SQL Server . 

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

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Generowanie tokenu uwierzytelniania Blob Storage SAS dla LRS

Azure Blob Storage jest używany jako pośredni magazyn plików kopii zapasowych między SQL Server a SQL Managed Instance. Musisz wygenerować token uwierzytelniania sygnatury dostępu współdzielonego z uprawnieniami tylko do listy i odczytu dla LRS. Token umożliwi LRS dostęp do Blob Storage i użyje plików kopii zapasowej, aby przywrócić je na SQL Managed Instance. 

Wykonaj następujące kroki, aby wygenerować token:

1. Otwórz Eksplorator usługi Storage z Azure Portal.
2. Rozwiń **kontenery obiektów blob.**
3. Kliknij prawym przyciskiem myszy kontener obiektów blob i wybierz polecenie **Pobierz sygnaturę dostępu współdzielnego.**

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Zrzut ekranu przedstawiający opcje generowania tokenu uwierzytelniania S A S.":::

4. Wybierz ramy czasowe wygaśnięcia tokenu. Upewnij się, że token jest ważny w czasie trwania migracji.
5. Wybierz strefę czasową tokenu: UTC lub czas lokalny.
    
   > [!IMPORTANT]
   > Strefa czasowa tokenu i wystąpienie zarządzane mogą być niezgodne. Upewnij się, że token sygnatury dostępu współdzielonego ma odpowiedni czas ważności, biorąc pod uwagę strefy czasowe. Jeśli to możliwe, ustaw strefę czasową na wcześniejszą i późniejszą część okna planowanej migracji.
6. Wybierz **tylko uprawnienia** Odczyt **i** Lista.

   > [!IMPORTANT]
   > Nie wybieraj żadnych innych uprawnień. Jeśli to zrobisz, nie będzie można uruchomić LRS. To wymaganie dotyczące zabezpieczeń jest projektowe.
7. Wybierz przycisk **Utwórz**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Zrzut ekranu przedstawiający opcje wygaśnięcia tokenu S A, strefy czasowej i uprawnień wraz z przyciskiem Utwórz.":::

Uwierzytelnianie sygnatury dostępu współdzielonego jest generowane z określonym czasem ważności. Potrzebna jest wersja URI tokenu, jak pokazano na poniższym zrzucie ekranu.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Zrzut ekranu przedstawiający przykład wersji us I tokenu S A S.":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopiowanie parametrów z tokenu sygnatury dostępu współdzielonego

Przed użyciem tokenu SAS do uruchomienia LRS należy zrozumieć jego strukturę. URI wygenerowanego tokenu SYGNATURY dostępu współdzielonego składa się z dwóch części oddzielonych znakiem zapytania ( `?` ), jak pokazano w tym przykładzie:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Przykład użytkownika dla wygenerowanego tokenu S A S dla usługi ponownego odtwarzania dziennika." border="false":::

Pierwsza część, począwszy od znaku zapytania (), jest używana dla parametru, który jest podawane jako dane `https://` `?` wejściowe do `StorageContainerURI` LRS. Udostępnia ona informacje LRS dotyczące folderu, w którym są przechowywane pliki kopii zapasowej bazy danych.

Druga część, rozpoczynająca się po znaku zapytania () i przechodząca aż do końca `?` ciągu, jest `StorageContainerSasToken` parametrem . Jest to rzeczywisty podpisany token uwierzytelniania, który jest ważny przez określony czas. Ta część nie musi zaczynać się od , `sp=` jak pokazano w przykładzie. Twój przypadek może się różnić.

Skopiuj parametry w następujący sposób:

1. Skopiuj pierwszą część tokenu, zaczynając od znaku zapytania `https://` ( `?` ). Użyj go jako `StorageContainerUri` parametru w programie PowerShell lub interfejsie wiersza polecenia platformy Azure do uruchamiania magazynu LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Zrzut ekranu przedstawiający kopiowanie pierwszej części tokenu.":::

2. Skopiuj drugą część tokenu, rozpoczynając od znaku zapytania ( ) aż do `?` końca ciągu. Użyj go jako `StorageContainerSasToken` parametru w programie PowerShell lub interfejsie wiersza polecenia platformy Azure do uruchamiania magazynu LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Zrzut ekranu przedstawiający kopiowanie drugiej części tokenu.":::
   
> [!NOTE]
> Nie dołączaj znaku zapytania podczas kopiowania jednej z części tokenu.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Logowanie do platformy Azure i wybieranie subskrypcji

Aby zalogować się do platformy Azure, użyj następującego polecenia cmdlet programu PowerShell:

```powershell
Login-AzAccount
```

Wybierz odpowiednią subskrypcję, w której znajduje się wystąpienie zarządzane, przy użyciu następującego polecenia cmdlet programu PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Rozpoczynanie migracji

Migrację rozpoczyna się od uruchomienia LRS. Usługę można uruchomić w trybie autouzupełniania lub ciągłym. 

W przypadku korzystania z trybu autouzupełniania migracja zakończy się automatycznie po przywróceniu ostatniego z określonych plików kopii zapasowej. Ta opcja wymaga start polecenie, aby określić nazwę pliku ostatniej kopii zapasowej pliku. 

W przypadku korzystania z trybu ciągłego usługa będzie stale przywracać wszystkie nowe pliki kopii zapasowej, które zostały dodane. Migracja zakończy się tylko w przypadku ręcznego migracji. 

### <a name="start-lrs-in-autocomplete-mode"></a>Uruchamianie LRS w trybie autouzupełniania

Aby uruchomić magazyn LRS w trybie autouzupełniania, użyj następujących poleceń programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Określ nazwę pliku ostatniej kopii zapasowej przy użyciu `-LastBackupName` parametru . Po przywróceniu ostatniego z określonych plików kopii zapasowej usługa automatycznie zainicjuje cutover.

Oto przykład uruchamiania LRS w trybie autouzupełniania przy użyciu programu PowerShell:

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

Oto przykład uruchamiania magazynu LRS w trybie autouzupełniania przy użyciu interfejsu wiersza polecenia platformy Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Uruchamianie LRS w trybie ciągłym

Oto przykład uruchamiania LRS w trybie ciągłym przy użyciu programu PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Oto przykład uruchamiania magazynu LRS w trybie ciągłym przy użyciu interfejsu wiersza polecenia platformy Azure:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Klienci programu PowerShell i interfejsu wiersza polecenia do uruchamiania LRS w trybie ciągłym są synchronini. Oznacza to, że klienci będą czekać na odpowiedź interfejsu API, aby zgłosić powodzenie lub niepowodzenie uruchomienia zadania. 

Podczas tego oczekiwania polecenie nie zwróci kontrolki do wiersza polecenia. Jeśli używasz skryptu środowiska migracji i potrzebujesz polecenia uruchamiania LRS, aby natychmiast przekazać kontrolę w celu kontynuowania pracy z resztą skryptu, możesz uruchomić program PowerShell jako zadanie w tle z `-AsJob` przełącznikiem . Na przykład:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Po uruchomieniu zadania w tle obiekt zadania jest zwracany natychmiast, nawet jeśli ukończenie zadania zajmuje więcej czasu. Możesz kontynuować pracę w sesji bez przerwy podczas działania zadania. Aby uzyskać szczegółowe informacje na temat uruchamiania programu PowerShell jako zadania w tle, zobacz [dokumentację start-job programu PowerShell.](/powershell/module/microsoft.powershell.core/start-job#description)

Podobnie, aby uruchomić polecenie interfejsu wiersza polecenia platformy Azure w systemie Linux jako proces w tle, użyj polecenia ampersand ( ) na końcu polecenia `&` uruchamiania magazynu LRS:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Po uruchomieniu LRS wszystkie poprawki oprogramowania zarządzanego przez system są zatrzymywane na 47 godzin. Po tym oknie następna automatyczna poprawka oprogramowania automatycznie zatrzyma LRS. W takim przypadku nie można wznowić migracji i trzeba ją ponownie uruchomić od podstaw. 

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

## <a name="stop-the-migration"></a>Zatrzymywanie migracji

Jeśli musisz zatrzymać migrację, użyj następujących polecenia cmdlet. Zatrzymanie migracji spowoduje usunięcie przywracanej bazy danych na SQL Managed Instance, więc wznowienie migracji nie będzie możliwe.

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

## <a name="complete-the-migration-continuous-mode"></a>Ukończenie migracji (tryb ciągły)

Jeśli proces LRS został uruchomiony w trybie ciągłym, po upewnieniu się, że wszystkie kopie zapasowe zostały przywrócone, zainicjowanie migracji zostanie zakończone. Po migracji awaryjnej baza danych zostanie zmigrowana i będzie gotowa do odczytu i zapisu.

Aby ukończyć proces migracji w trybie ciągłym LRS za pośrednictwem programu PowerShell, użyj następującego polecenia:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Aby ukończyć proces migracji w trybie ciągłym LRS za pośrednictwem interfejsu wiersza polecenia platformy Azure, użyj następującego polecenia:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

Ograniczenia funkcjonalne LRS są:
- Przywracana baza danych nie może być używana do uzyskiwania dostępu tylko do odczytu podczas procesu migracji.
- Poprawki oprogramowania zarządzanego przez system są blokowane na 47 godzin po uruchomieniu LRS. Po upływie tego okna czasowego następna aktualizacja oprogramowania zatrzyma LRS. Następnie należy ponownie uruchomić usługę LRS od podstaw.
- Funkcja LRS wymaga, SQL Server kopii zapasowej baz danych na SQL Server z `CHECKSUM` włączoną opcją .
- Token SYGNATURY dostępu współdzielonego, który będzie używać usługi LRS, musi zostać wygenerowany dla całego kontenera Azure Blob Storage i musi mieć tylko uprawnienia do odczytu i listy.
- Pliki kopii zapasowej dla różnych baz danych muszą być umieszczone w oddzielnych folderach na Blob Storage.
- Usługi LRS muszą być uruchomione oddzielnie dla każdej bazy danych, która wskazuje na oddzielone foldery z plikami kopii zapasowej Blob Storage.
- LRS może obsługiwać do 100 równoczesnych procesów przywracania na jedno wystąpienie zarządzane.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu LRS, użyj monitorowania polecenia cmdlet ( lub ), aby wyświetlić `get-azsqlinstancedatabaselogreplay` `az_sql_midb_log_replay_show` stan operacji. Jeśli po pewnym czasie nie można uruchomić LRS i wystąpi błąd, sprawdź, czy wystąpiły najczęstsze problemy:

- Czy istniejąca baza danych w SQL Managed Instance ma taką samą nazwę jak baza danych, z SQL Server? Rozwiąż ten konflikt, zmiana nazwy jednej z baz danych.
- Czy kopia zapasowa bazy danych SQL Server została wykonana za pośrednictwem `CHECKSUM` opcjiÂ?
- Czy uprawnienia do tokenÂ sygnatury dostępu współdzielonego są tylko do odczytu i listy dla LRS?
- Czy skopiowaliśmy token SYGNATURY dostępu współdzielonego dla LRS po znaku zapytania ( ), z `?` zawartością rozpoczynającą się w ten sposób: `sv=2020-02-10...` ? Â 
- Czy czas ważności tokenu sygnatury dostępu współdzielonego ma zastosowanie w oknie czasowym rozpoczęcia i zakończenia migracji? Mogą wystąpić niezgodności ze względu na różne strefy czasowe używane dla SQL Managed Instance tokenu SAS. Spróbuj ponownie wygenerować token sygnatury dostępu współdzielonego i rozszerzyć ważność tokenu w oknie czasowym przed bieżącą datą i po jej zakończeniu.
- Czy nazwa bazy danych, nazwa grupy zasobów i nazwa wystąpienia zarządzanego są poprawnie napisane?
- Jeśli uruchomiono LRS w trybie autouzupełniania, czy określono prawidłową nazwę pliku dla ostatniego pliku kopii zapasowej?

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [migrowaniu SQL Server do wystąpienia zarządzanego SQL.](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)
- Dowiedz się więcej o [różnicach między SQL Server i SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących kosztów i rozmiarów obciążeń migrowanych na platformę Azure.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
