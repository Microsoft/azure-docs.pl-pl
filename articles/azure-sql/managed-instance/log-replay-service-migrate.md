---
title: Migrowanie baz danych do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
description: Dowiedz się, jak migrować bazy danych z SQL Server do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 74403b7ec1469ce7cdaadc9931eb5ac95f55f6f5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096840"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Migrowanie baz danych z SQL Server do wystąpienia zarządzanego SQL przy użyciu usługi powtarzania dzienników (wersja zapoznawcza)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak ręcznie skonfigurować migrację bazy danych z SQL Server 2008-2019 do wystąpienia zarządzanego SQL za pomocą usługi powtórzenia powtarzania (LRS) w publicznej wersji zapoznawczej. Jest to usługa w chmurze włączona dla wystąpienia zarządzanego na podstawie technologii dostarczania dzienników SQL Server. LRS należy używać w przypadkach, gdy istnieją złożone migracje niestandardowe i architektury hybrydowe, gdy jest wymagana większa kontrola, gdy występuje niewielka tolerancja dla przestojów lub gdy nie można użyć usługi Azure Data Migration Service (DMS).

Zarówno DMS, jak i LRS korzystają z tej samej podstawowej technologii migracji i tych samych interfejsów API. Dzięki wykorzystaniu LRS można dodatkowo włączyć złożone migracje niestandardowe i architekturę hybrydową między Premium. Wystąpienia zarządzane SQL Server i SQL.

## <a name="when-to-use-log-replay-service"></a>Kiedy używać usługi powtarzania dzienników

W przypadkach, gdy nie można użyć usługi [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) do migracji, usługa w chmurze LRS może być używana bezpośrednio z programem PowerShell, interfejsami wiersza polecenia lub API, aby ręcznie kompilować i organizować migracje baz danych do wystąpienia zarządzanego SQL. 

Warto rozważyć użycie usługi w chmurze LRS w niektórych następujących przypadkach:
- Dla projektu migracji bazy danych jest wymagana większa kontrola
- Istnieje mała tolerancja dla przestojów uruchomienie produkcyjne migracji
- Nie można zainstalować pliku wykonywalnego DMS w Twoim środowisku
- Plik wykonywalny DMS nie ma dostępu do plików do kopii zapasowych bazy danych
- Dostęp do systemu operacyjnego hosta nie jest dostępny lub nie ma uprawnień administratora
- Nie można otworzyć portów sieciowych z Twojego środowiska na platformie Azure
- Kopie zapasowe są przechowywane bezpośrednio w usłudze Azure Blob Storage przy użyciu opcji do adresu URL
- Istnieje potrzeba korzystania z różnicowych kopii zapasowych

> [!NOTE]
> Zalecany zautomatyzowany sposób migrowania baz danych z SQL Server do wystąpienia zarządzanego SQL korzysta z usługi Azure DMS. Ta usługa korzysta z tej samej usługi LRS w chmurze na zapleczu z funkcją wysyłania dzienników w trybie NORECOVERY. Należy rozważyć ręczne korzystanie z LRS, aby organizować migracje w przypadkach, gdy usługa Azure DMS nie w pełni obsługuje Twoje scenariusze.

## <a name="how-does-it-work"></a>Jak to działa

Kompilowanie niestandardowego rozwiązania przy użyciu LRS do migrowania baz danych do chmury wymaga wykonania kilku kroków aranżacji na diagramie i przedstawionych w poniższej tabeli.

Migracja obejmuje tworzenie pełnych kopii zapasowych bazy danych na SQL Server z włączoną SUMĄ KONTROLną i kopiowanie plików kopii zapasowej do usługi Azure Blob Storage. LRS jest używany do przywracania plików kopii zapasowej z platformy Azure Blob Storage do wystąpienia zarządzanego SQL. Blob Storage platformy Azure służy jako magazyn pośredniczący między wystąpieniem zarządzanym SQL Server i SQL.

Usługa LRS będzie monitorować nową różnicową platformę Blob Storage Azure, a kopie zapasowe dzienników dodane po pełnej kopii zapasowej zostaną przywrócone i program automatycznie przywróci nowe dodane pliki. Postęp przywracania plików kopii zapasowej w wystąpieniu zarządzanym SQL może być monitorowany przy użyciu usługi, a proces można także przerwać w razie potrzeby.

LRS nie wymaga określonej konwencji nazewnictwa plików kopii zapasowej, ponieważ skanuje wszystkie pliki umieszczane na platformie Azure Blob Storage i konstruuje łańcuch kopii zapasowych tylko do odczytu nagłówków pliku. Podczas procesu migracji bazy danych są w stanie "Przywracanie", ponieważ są przywracane w trybie [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) i nie można ich użyć do odczytu lub zapisu do momentu pełnego ukończenia procesu migracji. 

W przypadku migrowania kilku baz danych kopie zapasowe dla każdej bazy danych muszą być umieszczone w osobnym folderze na platformie Azure Blob Storage. LRS należy uruchomić oddzielnie dla każdej bazy danych i różnych ścieżek do oddzielenia folderów Blob Storage platformy Azure. 

LRS można uruchomić w programie Autouzupełnianie lub w trybie ciągłym. Po uruchomieniu w trybie Autouzupełnianie migracja zostanie zakończona automatycznie, gdy zostanie przywrócona poprzednia nazwa pliku kopii zapasowej. Po uruchomieniu w trybie ciągłym usługa będzie stale przywracać wszystkie dodane nowe pliki kopii zapasowej, a migracja zostanie ukończona tylko na uruchomienie produkcyjne ręcznym. Zaleca się, aby ręczne uruchomienie produkcyjne zostało wykonane dopiero po wykonaniu końcowej kopii zapasowej dziennika i pobraniu jej jako przywróconej w wystąpieniu zarządzanym SQL. Ostatni krok uruchomienie produkcyjne przejdzie do trybu online i będzie dostępny do odczytu i zapisu w wystąpieniu zarządzanym SQL.

Po zatrzymaniu usługi LRS automatycznie przy użyciu funkcji Autouzupełnianie lub ręcznie w uruchomienie produkcyjne, proces przywracania nie może zostać wznowiony dla bazy danych, która została przełączona w tryb online w wystąpieniu zarządzanym SQL. Aby przywrócić dodatkowe pliki kopii zapasowej po zakończeniu migracji za pomocą funkcji Autouzupełnianie lub ręcznie w uruchomienie produkcyjne, należy usunąć bazę danych, a cały łańcuch kopii zapasowych musi zostać przywrócony od zera przez ponowne uruchomienie LRS.

   :::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Powtórz kroki aranżacji usługi w dzienniku dla wystąpienia zarządzanego SQL" border="false":::
    
| Operacja | Szczegóły |
| :----------------------------- | :------------------------- |
| **1. Skopiuj kopie zapasowe bazy danych z SQL Server do platformy Azure Blob Storage**. | — Kopiowanie pełnych, różnicowych i dzienników kopii zapasowych z SQL Server do kontenera Blob Storage platformy Azure przy użyciu [AzCopy](/azure/storage/common/storage-use-azcopy-v10)lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/). <br />— Użyj dowolnych nazw plików, ponieważ LRS nie wymaga określonej konwencji nazewnictwa plików.<br />— W przypadku migrowania kilku baz danych dla każdej bazy danych wymagany jest osobny folder. |
| **2. Uruchom usługę LRS w chmurze**. | -Usługa można uruchomić za pomocą wybranych poleceń cmdlet: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Poleceń cmdlet az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)interfejsu wiersza polecenia. <br /> -Rozpocznij LRS oddzielnie dla każdej innej bazy danych wskazującej inny folder kopii zapasowej na platformie Azure Blob Storage. <br />— Po uruchomieniu usługa wykona kopie zapasowe z kontenera usługi Azure Blob Storage i zacznie je przywracać w wystąpieniu zarządzanym SQL.<br /> -W przypadku, gdy LRS został uruchomiony w trybie ciągłym, gdy wszystkie wstępnie przekazane kopie zapasowe zostaną przywrócone, usługa będzie oglądać wszystkie nowe pliki przekazane do folderu i będzie stale stosować dzienniki na podstawie łańcucha LSN, dopóki usługa nie zostanie zatrzymana. |
| **2,1. Monitoruj postęp operacji**. | -Postęp operacji przywracania można monitorować przy użyciu opcji lub poleceń cmdlet: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> [Poleceń cmdlet az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)interfejsu wiersza polecenia. |
| **2,2. Stop\abort operację w razie konieczności**. | -Jeśli proces migracji wymaga przerwania, operacja może zostać zatrzymana przy użyciu wybranych poleceń cmdlet: <br /> PowerShell [Stop-azsqlinstancedatabaselogreplay]/PowerShell/module/AZ.SQL/Stop-azsqlinstancedatabaselogreplay) <br /> Poleceń cmdlet [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) interfejsu wiersza polecenia. <br /><br />— Spowoduje to usunięcie przywróconej bazy danych w wystąpieniu zarządzanym SQL. <br />-Po zatrzymaniu nie można wznowić LRS dla bazy danych. Proces migracji należy uruchomić ponownie od podstaw. |
| **3. uruchomienie produkcyjne do chmury, gdy jest gotowa**. | -Zatrzymaj aplikację i obciążenie. Wypełnij ostatnią kopię zapasową dziennika i Przekaż do Blob Storage platformy Azure.<br /> -Ukończ uruchomienie produkcyjne przez zainicjowanie operacji LRS Complete przy użyciu wybranych poleceń cmdlet: <br />PowerShell [Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Poleceń cmdlet [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) interfejsu wiersza polecenia. <br /><br />— Spowoduje to zatrzymanie usługi LRS, a baza danych zostanie przełączona w tryb online w celu odczytu i zapisu w wystąpieniu zarządzanym SQL.<br /> -Przeciąganie parametrów połączenia aplikacji z SQL Server do wystąpienia zarządzanego SQL. |

## <a name="requirements-for-getting-started"></a>Wymagania dotyczące rozpoczynania pracy

### <a name="sql-server-side"></a>Strona SQL Server
- SQL Server 2008-2019
- Pełna kopia zapasowa baz danych (jeden lub wiele plików)
- Różnicowa kopia zapasowa (jeden lub wiele plików)
- Kopia zapasowa dziennika (niedzielona dla pliku dziennika transakcji)
- **Suma kontrolna musi być włączona** dla kopii zapasowych (obowiązkowe)

### <a name="azure-side"></a>Po stronie platformy Azure
- PowerShell AZ. SQL module w wersji 2.16.0 lub nowszej ([Instalowanie](https://www.powershellgallery.com/packages/Az.Sql/)lub korzystanie z platformy Azure [Cloud Shell](/azure/cloud-shell/))
- Interfejs wiersza polecenia w wersji 2.19.0 lub nowszej ([Instalacja](/cli/azure/install-azure-cli))
- Obsługa kontenera Blob Storage platformy Azure
- Token zabezpieczający SAS z uprawnieniami tylko do **odczytu** i **listy** wygenerowanymi dla kontenera magazynu obiektów BLOB

### <a name="migrating-multiple-databases"></a>Migrowanie wielu baz danych
- Pliki kopii zapasowych dla różnych baz danych muszą być umieszczone w oddzielnych folderach na platformie Azure Blob Storage.
- LRS należy uruchomić oddzielnie dla każdej bazy danych wskazującej odpowiedni folder na platformie Azure Blob Storage.
- LRS może obsłużyć do 100 procesów przywracania równoczesnego na pojedyncze wystąpienie zarządzane SQL.

### <a name="azure-rbac-permissions-required"></a>Wymagane są uprawnienia kontroli RBAC platformy Azure
Wykonanie LRS za pomocą podanych klientów wymaga jednej z następujących ról platformy Azure:
- Rola właściciela subskrypcji lub
- Rola [współautora wystąpienia zarządzanego](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) lub
- Rola niestandardowa z następującymi uprawnieniami:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Najlepsze rozwiązania

W przypadku najlepszych rozwiązań zaleca się używanie następujących metod:
- Uruchom [Data Migration Assistant](/sql/dma/dma-overview) , aby sprawdzić, czy bazy danych są gotowe do migracji do wystąpienia zarządzanego SQL. 
- Podziel pełne i różnicowe kopie zapasowe na wiele plików zamiast pojedynczego pliku.
- Włącz kompresję kopii zapasowej.
- Użyj Cloud Shell do wykonywania skryptów, ponieważ zawsze będzie ona aktualizowana do najnowszych wydanych poleceń cmdlet.
- Zaplanuj ukończenie migracji w ciągu 47 godzin od momentu uruchomienia usługi LRS. Jest to okres prolongaty uniemożliwiający LRS poprawek oprogramowania zarządzanych przez system po rozpoczęciu pracy.

> [!IMPORTANT]
> - Przywracana baza danych przy użyciu LRS nie może zostać użyta do momentu ukończenia procesu migracji.
> - Dostęp tylko do odczytu do baz danych podczas migracji nie jest obsługiwany przez LRS.
> - Po zakończeniu migracji proces migracji zostanie sfinalizowany, ponieważ LRS nie obsługuje wznawiania przywracania.

## <a name="steps-to-execute"></a>Kroki do wykonania

### <a name="make-backups-on-the-sql-server"></a>Tworzenie kopii zapasowych na SQL Server

Kopie zapasowe na SQL Server można wykonać przy użyciu jednej z następujących dwóch opcji:

- Utwórz kopię zapasową magazynu na dysku lokalnym, a następnie Przekaż pliki do usługi Azure Blob Storage, na wypadek, gdy środowisko ma ograniczone bezpośrednie tworzenie kopii zapasowej do usługi Azure Blob Storage.
- Utwórz kopię zapasową bezpośrednio w usłudze Azure Blob Storage przy użyciu opcji "do adresu URL" w języku T-SQL, w przypadku których środowisko i zabezpieczenia umożliwiają wykonanie tej czynności. 

Ustawianie baz danych, które chcesz migrować do trybu pełnego odzyskiwania, aby umożliwić tworzenie kopii zapasowych dzienników.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Aby ręcznie utworzyć pełną kopię zapasową bazy danych w postaci większej, różnicowej i dziennika w magazynie lokalnym, użyj dostarczonego przykładowego skryptu T-SQL. Upewnij się, że opcja sum KONTROLnych jest włączona, ponieważ jest to obowiązkowe wymaganie dla LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Tworzenie magazynu obiektów blob platformy Azure

Usługa Azure Blob Storage jest używana jako magazyn pośredniczący dla plików kopii zapasowych między SQL Server i wystąpieniem zarządzanym SQL. Aby utworzyć nowe konto magazynu i kontener obiektów BLOB w ramach konta magazynu, wykonaj następujące kroki:

1. [Tworzenie konta magazynu](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crete kontenera obiektów BLOB](../../storage/blobs/storage-quickstart-blobs-portal.md) w ramach konta magazynu

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopiuj kopie zapasowe z SQL Server na platformę Azure Blob Storage

Niektóre z poniższych metod można wykorzystać do przekazywania kopii zapasowych do magazynu obiektów BLOB w ramach migrowania baz danych do wystąpienia zarządzanego przy użyciu LRS:
- Używanie [AzCopy](/azure/storage/common/storage-use-azcopy-v10)lub [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer) do przekazywania kopii zapasowych do kontenera obiektów BLOB.
- Używanie Eksplorator usługi Storage w Azure Portal.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Tworzenie kopii zapasowych z SQL Server bezpośrednio na platformie Azure Blob Storage

W przypadku, gdy zasady firmowe i sieciowe pozwalają na to, alternatywnym sposobem jest tworzenie kopii zapasowych z SQL Server bezpośrednio do platformy Azure Blob Storage przy użyciu opcji SQL Server natywnej [kopii zapasowej na adres URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Jeśli możesz wykonać tę opcję, tworzenie kopii zapasowych w lokalnym magazynie i przekazywanie ich do usługi Azure Blob Storage nie jest konieczne.

Pierwszym krokiem jest, że ta operacja wymaga wygenerowania tokenu uwierzytelniania SAS dla Blob Storage platformy Azure, a token musi zostać zaimportowany do SQL Server. Drugim krokiem jest tworzenie kopii zapasowych z opcją "do adresu URL" w języku T-SQL. Upewnij się, że wszystkie kopie zapasowe są wykonywane z włączoną opcją CHEKSUM.

Aby uzyskać informacje na ten temat, należy podać przykładowy kod służący do tworzenia kopii zapasowych na platformie Azure Blob Storage. Ten przykład nie zawiera instrukcji dotyczących importowania tokenu sygnatury dostępu współdzielonego. Szczegółowe instrukcje, w tym sposób generowania i importowania tokenu sygnatury dostępu współdzielonego do SQL Server są podane w następującym samouczku: [Korzystanie z usługi Azure Blob Storage z SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Generuj uwierzytelnianie SAS Blob Storage Azure dla LRS

Usługa Azure Blob Storage jest używana jako magazyn pośredniczący dla plików kopii zapasowych między SQL Server i wystąpieniem zarządzanym SQL. Token uwierzytelniania SAS z uprawnieniami list i tylko do odczytu musi zostać wygenerowany do użycia przez usługę LRS. Umożliwi to usłudze LRS dostęp do usługi Azure Blob Storage i używanie plików kopii zapasowej do przywracania ich w wystąpieniu zarządzanym SQL. Wykonaj następujące kroki, aby wygenerować uwierzytelnianie SAS dla LRS użycia:

1. Dostęp do Eksplorator usługi Storage z Azure Portal

2. Rozwijanie kontenerów obiektów BLOB

3. Kliknij prawym przyciskiem myszy kontener obiektów blob i wybierz pozycję Pobierz sygnaturę dostępu współdzielonego.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Usługa powtarzania dzienników — uzyskiwanie sygnatury dostępu współdzielonego":::

4. Wybierz przedział czasu wygaśnięcia tokenu. Upewnij się, że token jest prawidłowy dla czasu trwania migracji.

5. Wybierz strefę czasową dla tokenu — UTC lub czasu lokalnego

   - Strefa czasowa tokenu i wystąpienie zarządzane SQL mogą niezgodność. Upewnij się, że token sygnatury dostępu współdzielonego ma odpowiedni czas na uwzględnienie stref czasowych. Jeśli to możliwe, ustaw strefę czasową na wcześniejszą i późniejszą godzinę planowanego okna migracji.

6. Wybierz tylko uprawnienia do odczytu i listy

   - Nie trzeba wybierać innych uprawnień lub w przeciwnym razie nie będzie można uruchomić LRS. To wymaganie dotyczące zabezpieczeń jest zaprojektowane.

7. Kliknij przycisk Utwórz

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Usługa powtarzania dzienników — generowanie tokenu uwierzytelniania SAS":::

   Uwierzytelnianie SAS zostanie wygenerowane z określonym wcześniej okresem ważności. Wymagana jest wersja identyfikatora URI wygenerowanego tokenu — jak pokazano na poniższym zrzucie ekranu.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Usługa powtarzania dzienników — kopiowanie sygnatury dostępu współdzielonego identyfikatora URI":::

### <a name="copy-parameters-from-sas-token-generated"></a>Wygenerowane parametry kopiowania z tokenu SAS

Aby można było prawidłowo używać tokenu SAS do rozpoczęcia LRS, musimy zrozumieć jego strukturę. Identyfikator URI wygenerowanego tokenu sygnatury dostępu współdzielonego składa się z dwóch części:
- StorageContainerUri i 
- StorageContainerSasToken, oddzielona od znaku zapytania (?), jak pokazano na poniższej ilustracji.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Przykład identyfikatora URI uwierzytelniania SAS wygenerowanego przez usługę powtórzenia dziennika" border="false":::

- Pierwsza część rozpoczynająca się od "https://" do momentu, gdy znak zapytania (?) jest używany dla parametru StorageContainerURI, który jest podawany jako dane wejściowe do LRS. Daje to LRS informacje o folderze, w którym są przechowywane pliki kopii zapasowej bazy danych.
- Druga część, rozpoczynająca się od znaku zapytania (?), w przykładzie "Sp =" i cały sposób aż do końca ciągu jest parametrem StorageContainerSasToken. Jest to rzeczywisty podpisany token uwierzytelniania, ważny przez określony czas. Ta część nie musi rozpoczynać się od "Sp =", jak pokazano, i że może się różnić w przypadku.

Kopiuj parametry w następujący sposób:

1. Skopiuj pierwszą część tokenu rozpoczynającą się od https://, aż do znaku zapytania (?) i użyjesz go jako parametru StorageContainerUri w programie PowerShell lub interfejsie wiersza polecenia do uruchamiania LRS, jak pokazano na poniższym zrzucie ekranu.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Kopiuj ponownie parametr StorageContainerUri usługi log Copy":::

2. Skopiuj drugą część tokenu rozpoczynającą się od znaku zapytania (?), aż do końca ciągu, i użyj go jako parametru StorageContainerSasToken w programie PowerShell lub interfejsie wiersza polecenia do uruchomienia LRS, jak pokazano na poniższym zrzucie ekranu.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Kopiuj ponownie parametr StorageContainerSasToken usługi log Copy":::

> [!IMPORTANT]
> - Uprawnienia do tokenu SAS dla usługi Azure Blob Storage muszą być tylko do odczytu i listy. Jeśli dla tokenu uwierzytelniania SYGNATURy dostępu współdzielonego są przyznane inne uprawnienia, uruchomienie usługi LRS zakończy się niepowodzeniem. Te wymagania dotyczące zabezpieczeń zostały zaprojektowane.
> - Token musi mieć odpowiedni czas ważności. Upewnij się, że strefy czasowe między tokenem a wystąpieniem zarządzanym są brane pod uwagę.
> - Upewnij się, że parametr StorageContainerUri dla programu PowerShell lub interfejsu wiersza polecenia jest kopiowany z identyfikatora URI wygenerowanego tokenu, rozpoczynając od https://do momentu znaku zapytania (?). Nie uwzględniaj znaku zapytania.
> Upewnij się, że parametr StorageContainerSasToken dla programu PowerShell interfejsu wiersza polecenia jest kopiowany z identyfikatora URI wygenerowanego tokenu, rozpoczynając od znaku zapytania (?), aż do końca ciągu. Nie uwzględniaj znaku zapytania.

### <a name="log-in-to-azure-and-select-subscription"></a>Zaloguj się do platformy Azure i wybierz pozycję subskrypcja

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

Uruchom LRS w trybie Autouzupełnianie — przykład interfejsu wiersza polecenia:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Uruchom LRS w trybie ciągłym

Uruchom LRS w trybie ciągłym — przykład programu PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Uruchom LRS w trybie ciągłym — przykład interfejsu wiersza polecenia:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>Uruchamianie skryptów LRS w trybie ciągłym

Klienci programu PowerShell i interfejsu wiersza polecenia do uruchamiania LRS w trybie ciągłym są synchronicznie. Oznacza to, że klienci oczekują, że odpowiedź interfejsu API będzie zgłaszać sukces lub niepowodzenie uruchomienia zadania. Podczas tego oczekiwania polecenie nie zwróci kontrolki z powrotem do wiersza polecenia. W przypadku wykonywania skryptów w środowisku migracji i wymaganiu uruchomienia LRS polecenie, aby natychmiast przekazać kontrolę z powrotem do dalszej części skryptu, można uruchomić program PowerShell jako zadanie w tle z przełącznikiem-AsJob. Na przykład:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Po uruchomieniu zadania w tle obiekt zadania wraca natychmiast, nawet jeśli ukończenie zadania trwa dłuższy czas. Po uruchomieniu zadania można kontynuować pracę w sesji bez przerw. Aby uzyskać szczegółowe informacje na temat uruchamiania programu PowerShell jako zadania w tle, zobacz dokumentację [zadania startowego programu PowerShell](/powershell/module/microsoft.powershell.core/start-job#description) .

Podobnie, aby uruchomić polecenie interfejsu wiersza polecenia w systemie Linux jako proces w tle, należy użyć znaku handlowego "i" (&) na końcu polecenia LRS Start.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Po rozpoczęciu LRS wszystkie poprawki oprogramowania zarządzane przez system zostaną zatrzymane przez następne 47 godzin. Po przekazaniu tego okna kolejna automatyczna poprawka programowa automatycznie zatrzyma trwającą LRS. W takim przypadku migracja nie może zostać wznowiona i musi zostać ponownie uruchomiona od podstaw. 

## <a name="monitor-the-migration-progress"></a>Monitorowanie postępu migracji

Aby monitorować postęp operacji migracji, użyj następującego polecenia programu PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby monitorować postęp operacji migracji, należy użyć poniższego polecenia CLI:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Zatrzymaj migrację

W przypadku konieczności zatrzymania migracji należy użyć następujących poleceń cmdlet. Zatrzymanie migracji spowoduje usunięcie bazy danych przywracania z wystąpienia zarządzanego SQL, z powodu której nie będzie możliwe wznowienie migracji.

Aby stop\abort proces migracji, użyj następującego polecenia programu PowerShell:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Aby stop\abort proces migracji, użyj następującego polecenia CLI:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Zakończ migrację (tryb ciągły)

W przypadku gdy LRS jest uruchamiany w trybie ciągłym, po upewnieniu się, że wszystkie kopie zapasowe zostały przywrócone, zainicjowanie uruchomienie produkcyjne spowoduje zakończenie migracji. Po zakończeniu uruchomienie produkcyjne baza danych zostanie zmigrowana i będzie gotowa do odczytu i zapisu.

Aby ukończyć proces migracji w trybie ciągłym LRS, użyj następującego polecenia programu PowerShell:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Aby dokończyć proces migracji w trybie ciągłym LRS, użyj następującego polecenia CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Ograniczenia funkcjonalne

Ograniczenia funkcjonalne usługi rejestrowania powtarzania (LRS) są następujące:
- Przywracana baza danych nie może być używana w przypadku dostępu tylko do odczytu podczas procesu migracji.
- Poprawki oprogramowania zarządzane przez system zostaną zablokowane przez 47 godzin od momentu uruchomienia LRS. Po wygaśnięciu tego przedziału czasu kolejna aktualizacja oprogramowania przestanie LRS. W takim przypadku LRS należy ponownie uruchomić od podstaw.
- LRS wymaga, aby baza danych w SQL Server była kopia zapasowa z włączoną opcją sumy KONTROLnej.
- Token sygnatury dostępu współdzielonego do użycia przez LRS musi zostać wygenerowany dla całego kontenera platformy Azure Blob Storage i musi mieć tylko uprawnienia do odczytu i listy.
- Pliki kopii zapasowych dla różnych baz danych muszą być umieszczone w oddzielnych folderach na platformie Azure Blob Storage.
- LRS musi być uruchamiana oddzielnie dla każdej bazy danych, wskazującej na oddzielne foldery z plikami kopii zapasowych na platformie Azure Blob Storage.
- LRS może obsłużyć do 100 procesów przywracania równoczesnego na pojedyncze wystąpienie zarządzane SQL.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po rozpoczęciu LRS Użyj poleceń cmdlet monitorowania (Get-azsqlinstancedatabaselogreplay lub az_sql_midb_log_replay_show), aby wyświetlić stan operacji. Jeśli po pewnym czasie LRS nie powiedzie się z błędem, sprawdź, czy nie występują niektóre z najczęstszych problemów:
- Czy istnieje już baza danych o tej samej nazwie w programie SQL i czy próbujesz przeprowadzić migrację z SQL Server? Rozwiąż ten konflikt, zmieniając nazwę jednej z baz danych.
- Czy utworzono kopię zapasową bazy danych na SQL Server przy użyciu opcji **sum kontrolnych** ?
- Czy uprawnienia dostępu do tokenu SAS są **odczytywane** i **postawiane** tylko dla usługi LRS?
- Czy token sygnatury dostępu współdzielonego dla LRS kopiowanych rozpoczyna się po znaku zapytania "?" z zawartością rozpoczynającą się podobnie jak w przypadku tego "OHR = 2020-02-10..."? 
- Czy czas **ważności tokenu** sygnatury dostępu współdzielonego dotyczy czasu rozpoczęcia i zakończenia migracji? Mogą być niezgodne ze względu na różne **strefy czasowe** używane dla wystąpienia zarządzanego SQL i token sygnatury dostępu współdzielonego. Spróbuj ponownie wygenerować token sygnatury dostępu współdzielonego, rozszerzając ważność przedziału czasu przed i po bieżącej dacie.
- Czy nazwa bazy danych, nazwa grupy zasobów i nazwy wystąpienia zarządzanego są poprawnie napisane?
- Jeśli LRS zostało uruchomione w trybie Autouzupełnianie, była prawidłową nazwą pliku dla ostatniego określonego pliku kopii zapasowej?

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [migrowania SQL Server do wystąpienia zarządzanego SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Dowiedz się więcej o [różnicach między SQL Server i wystąpieniem zarządzanym usługi Azure SQL](transact-sql-tsql-differences-sql-server.md).
- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących kosztów i rozmiarów obciążeń migrowanych do platformy Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
