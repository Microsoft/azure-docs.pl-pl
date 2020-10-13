---
title: Różnice w języku T-SQL między SQL Server & wystąpieniu zarządzanym usługi Azure SQL
description: W tym artykule omówiono różnice w języku Transact-SQL (T-SQL) między wystąpieniem zarządzanym usługi Azure SQL i SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 06/02/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 36377d34a03150fefb8332bcfbe7bb6633ccc606
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973312"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Różnice w języku T-SQL między SQL Server & wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ten artykuł zawiera podsumowanie i wyjaśnienie różnic między wystąpieniem zarządzanym usługi Azure SQL i SQL Server. 


Wystąpienie zarządzane SQL zapewnia wysoką zgodność z aparatem bazy danych SQL Server i większość funkcji jest obsługiwanych w wystąpieniu zarządzanym SQL.

![Łatwa migracja z SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Istnieją pewne ograniczenia PaaS wprowadzone w wystąpieniu zarządzanym SQL, a niektóre zmiany zachowania są porównywane z SQL Server. Różnice są podzielone na następujące kategorie: <a name="Differences"></a>

- [Dostępność](#availability) obejmuje różnice w przypadku [grup dostępności](#always-on-availability-groups) , które są zawsze włączone i [kopie zapasowe](#backup).
- [Zabezpieczenia](#security) obejmują różnice między [inspekcjami](#auditing), [certyfikatami](#certificates), [poświadczeniami](#credential), [dostawcami usług kryptograficznych](#cryptographic-providers), [logowania i użytkownikami](#logins-and-users)oraz [kluczem usługi i kluczem głównym usługi](#service-key-and-service-master-key).
- [Konfiguracja](#configuration) obejmuje różnice w [rozszerzeniu puli buforów](#buffer-pool-extension), [Sortowanie](#collation), [poziomy zgodności](#compatibility-levels), [dublowanie bazy danych](#database-mirroring), [Opcje bazy danych](#database-options), [Agent SQL Server](#sql-server-agent)i [Opcje tabeli](#tables).
- [Funkcje](#functionalities) obejmują [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transakcji rozproszonych](#distributed-transactions), [zdarzeń rozszerzonych](#extended-events), [bibliotek zewnętrznych](#external-libraries), obiektów [FILESTREAM i FileTable](#filestream-and-filetable), [Wyszukiwanie semantyki pełnotekstowe](#full-text-semantic-search), [połączone serwery](#linked-servers), [bazy](#polybase), [replikację](#replication), [przywracanie](#restore-statement), [Service Broker](#service-broker), [procedury składowane, funkcje i wyzwalacze](#stored-procedures-functions-and-triggers).
- [Ustawienia środowiska](#Environment) , takie jak sieci wirtualnych i konfiguracje podsieci.

Większość z tych funkcji jest ograniczeniami architektury i reprezentuje funkcje usługi.

Tymczasowe znane problemy, które zostały odnalezione w wystąpieniu zarządzanym SQL i zostaną rozwiązane w przyszłości, można znaleźć na [stronie informacje o wersji](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Dostępność

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Zawsze włączone grupy dostępności

[Wysoka dostępność](../database/high-availability-sla.md) jest wbudowana w wystąpienie zarządzane SQL i nie może być kontrolowana przez użytkowników. Następujące instrukcje nie są obsługiwane:

- [UTWÓRZ PUNKT KOŃCOWY... DLA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [UTWÓRZ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMIEŃ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [PORZUĆ GRUPĘ DOSTĘPNOŚCI](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzula [SET HADR Cluster](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Wystąpienie zarządzane SQL ma automatyczne kopie zapasowe, dlatego użytkownicy mogą tworzyć pełne `COPY_ONLY` kopie zapasowe bazy danych. Kopie zapasowe, dzienniki i migawki plików nie są obsługiwane.

- W przypadku wystąpienia zarządzanego SQL można utworzyć kopię zapasową bazy danych wystąpienia tylko na koncie usługi Azure Blob Storage:
  - `BACKUP TO URL`Obsługiwane są tylko.
  - `FILE`, `TAPE` i urządzenia zapasowe nie są obsługiwane.
- Większość ogólnych `WITH` opcji jest obsługiwana.
  - `COPY_ONLY` jest obowiązkowy.
  - `FILE_SNAPSHOT` nie jest obsługiwana.
  - Opcje taśmy: `REWIND` , `NOREWIND` , `UNLOAD` i `NOUNLOAD` nie są obsługiwane.
  - Opcje specyficzne dla dziennika: `NORECOVERY` , `STANDBY` i `NO_TRUNCATE` nie są obsługiwane.

Ograniczenia: 

- Korzystając z wystąpienia zarządzanego SQL, można utworzyć kopię zapasową bazy danych wystąpienia w ramach programu z maksymalnie 32 prążkami, co wystarcza dla baz danych o pojemności do 4 TB, jeśli jest używana kompresja kopii zapasowej.
- Nie można wykonać `BACKUP DATABASE ... WITH COPY_ONLY` na bazie danych, która jest zaszyfrowana za pomocą transparent Data Encryption zarządzanej przez usługę (TDE). TDE zarządzane przez usługę wymusza szyfrowanie kopii zapasowych przy użyciu wewnętrznego klucza TDE. Nie można wyeksportować klucza, dlatego nie można przywrócić kopii zapasowej. Użyj funkcji automatycznego tworzenia kopii zapasowych oraz przywracania do punktu w czasie lub zamiast tego użyj [zarządzanego przez klienta (BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) . Można również wyłączyć szyfrowanie bazy danych.
- Maksymalny rozmiar paska tworzenia kopii zapasowej przy użyciu `BACKUP` polecenia w wystąpieniu zarządzanym SQL to 195 GB, czyli maksymalny rozmiar obiektu BLOB. Zwiększ liczbę pasków w poleceniu kopii zapasowej, aby zmniejszyć rozmiar poszczególnych pasków i pozostać w tym limicie.

    > [!TIP]
    > Aby obejść to ograniczenie, podczas tworzenia kopii zapasowej bazy danych z poziomu SQL Server w środowisku lokalnym lub na maszynie wirtualnej można:
    >
    > - Utwórz kopię zapasową `DISK` zamiast tworzenia kopii zapasowej w programie `URL` .
    > - Przekaż pliki kopii zapasowej do magazynu obiektów BLOB.
    > - Przywróć do wystąpienia zarządzanego SQL.
    >
    > `Restore`Polecenie w wystąpieniu zarządzanym SQL obsługuje większe rozmiary obiektów BLOB w plikach kopii zapasowej, ponieważ do przechowywania przekazanych plików kopii zapasowych jest używany inny typ obiektu BLOB.

Aby uzyskać informacje o kopiach zapasowych przy użyciu języka T-SQL, zobacz [kopia zapasowa](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpieczenia

### <a name="auditing"></a>Inspekcja

Kluczowe różnice między inspekcjami w Microsoft Azure SQL i SQL Server są następujące:

- W przypadku wystąpienia zarządzanego SQL Inspekcja działa na poziomie serwera. `.xel`Pliki dziennika są przechowywane w usłudze Azure Blob Storage.
- W przypadku Azure SQL Database Inspekcja działa na poziomie bazy danych. `.xel`Pliki dziennika są przechowywane w usłudze Azure Blob Storage.
- W przypadku SQL Server lokalnych lub na maszynach wirtualnych Inspekcja działa na poziomie serwera. Zdarzenia są przechowywane w systemie plików lub w dziennikach zdarzeń systemu Windows.
 
Inspekcja systemu XEvent w wystąpieniu zarządzanym SQL obsługuje cele usługi Azure Blob Storage. Dzienniki plików i okien nie są obsługiwane.

Kluczowe różnice w `CREATE AUDIT` składni inspekcji usługi Azure Blob Storage są następujące:

- `TO URL`Dostępna jest nowa składnia, która umożliwia określenie adresu URL kontenera magazynu obiektów blob platformy Azure, w którym `.xel` umieszczane są pliki.
- Składnia `TO FILE` nie jest obsługiwana, ponieważ wystąpienie zarządzane SQL nie może uzyskać dostępu do udziałów plików systemu Windows.

Aby uzyskać więcej informacji, zobacz: 

- [UTWÓRZ INSPEKCJĘ SERWERA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Inspekcja](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certyfikaty

Wystąpienie zarządzane SQL nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc obowiązują następujące ograniczenia:

- Ten `CREATE FROM` / `BACKUP TO` plik nie jest obsługiwany w przypadku certyfikatów.
- `CREATE` / `BACKUP` Certyfikat z `FILE` / `ASSEMBLY` nie jest obsługiwany. Nie można używać plików kluczy prywatnych. 

Zobacz [Tworzenie certyfikatu](/sql/t-sql/statements/create-certificate-transact-sql) i tworzenie [kopii zapasowej certyfikatu](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Obejście**: zamiast tworzenia kopii zapasowej certyfikatu i przywracania kopii zapasowej [Pobierz zawartość binarną certyfikatu i klucz prywatny, Zapisz go jako plik. SQL i Utwórz na podstawie danych binarnych](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Poświadczenie

Obsługiwane są tylko Azure Key Vault i `SHARED ACCESS SIGNATURE` tożsamości. Użytkownicy systemu Windows nie są obsługiwani.

Zobacz [Tworzenie poświadczeń](/sql/t-sql/statements/create-credential-transact-sql) i [ALTER Credential](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Dostawcy usług kryptograficznych

Wystąpienie zarządzane SQL nie może uzyskać dostępu do plików, więc nie można utworzyć dostawców usług kryptograficznych:

- `CREATE CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [Tworzenie dostawcy usług kryptograficznych](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [ALTER Cryptographic Provider](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Nazwy logowania i użytkownicy

- Nazwy logowania SQL utworzone za pomocą `FROM CERTIFICATE` , `FROM ASYMMETRIC KEY` i `FROM SID` są obsługiwane. Zobacz [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql).
- Obsługiwane są Azure Active Directory (nazwy główne serwera usługi Azure AD) utworzone przy użyciu składni [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) lub [Create User from Login (logowanie za pomocą usługi Azure AD](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) ). Te nazwy logowania są tworzone na poziomie serwera.

    Wystąpienie zarządzane SQL obsługuje podmioty zabezpieczeń bazy danych usługi Azure AD przy użyciu składni `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Ta funkcja jest również znana jako użytkownicy zawartej bazy danych usługi Azure AD.

- Nazwy logowania systemu Windows utworzone za pomocą `CREATE LOGIN ... FROM WINDOWS` składni nie są obsługiwane. Użyj Azure Active Directory logowania i użytkowników.
- Użytkownik usługi Azure AD, który utworzył wystąpienie, ma [nieograniczone uprawnienia administratora](../database/logins-create-manage.md).
- Użytkownicy niebędący administratorami na poziomie bazy danych usługi Azure AD mogą być tworzone przy użyciu `CREATE USER ... FROM EXTERNAL PROVIDER` składni. Zobacz [Tworzenie użytkownika... OD dostawcy zewnętrznego](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Nazwy główne serwera usługi Azure AD obsługują funkcje SQL tylko w ramach jednego wystąpienia zarządzanego SQL. Funkcje, które wymagają interakcji między wystąpieniami, niezależnie od tego, czy znajdują się w tej samej dzierżawie usługi Azure AD, czy w różnych dzierżawach, nie są obsługiwane dla użytkowników usługi Azure AD. Przykłady takich funkcji to:

  - Replikacja transakcyjna bazy danych SQL.
  - Połącz serwer.

- Ustawienie identyfikatora logowania usługi Azure AD zamapowanego na grupę usługi Azure AD jako właściciel bazy danych nie jest obsługiwane.
- Personifikacja podmiotów zabezpieczeń na poziomie serwera usługi Azure AD przy użyciu innych podmiotów zabezpieczeń usługi Azure AD jest obsługiwana, takich jak klauzula [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) . Ograniczenia wykonywania jako są następujące:

  - Wartość Uruchom jako użytkownik nie jest obsługiwana dla użytkowników usługi Azure AD, gdy nazwa jest inna niż nazwa logowania. Przykładem jest to, że użytkownik jest tworzony przy użyciu składni CREATE USER [myAadUser] FROM LOGIN [ john@contoso.com ], a Personifikacja jest podejmowana przy użyciu polecenia exec as User = _myAadUser_. Podczas tworzenia **użytkownika** z poziomu podmiotu zabezpieczeń serwera usługi Azure AD (login) Określ user_name jako ten sam Login_name z **nazwy logowania**.
  - Tylko podmioty zabezpieczeń na poziomie SQL Server (logowania) będące częścią `sysadmin` roli mogą wykonywać następujące operacje, które są przeznaczone dla podmiotów zabezpieczeń usługi Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Eksportowanie/Importowanie bazy danych przy użyciu plików BACPAC jest obsługiwane dla użytkowników usługi Azure AD w wystąpieniu zarządzanym SQL przy użyciu programu [SSMS v 18.4 lub nowszego](/sql/ssms/download-sql-server-management-studio-ssms)albo [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - Następujące konfiguracje są obsługiwane za pomocą pliku BACPAC bazy danych: 
    - Eksportuj/importuj bazę danych między różnymi wystąpieniami zarządzania w ramach tej samej domeny usługi Azure AD.
    - Wyeksportuj bazę danych z wystąpienia zarządzanego SQL i zaimportuj ją do SQL Database w ramach tej samej domeny usługi Azure AD. 
    - Wyeksportuj bazę danych z SQL Database i zaimportuj do wystąpienia zarządzanego SQL w ramach tej samej domeny usługi Azure AD.
    - Wyeksportuj bazę danych z wystąpienia zarządzanego SQL i zaimportuj do SQL Server (wersja 2012 lub nowsza).
      - W tej konfiguracji wszyscy użytkownicy usługi Azure AD są utworzeni jako SQL Server podmioty zabezpieczeń bazy danych (Użytkownicy) bez logowania. Typy użytkowników są wyświetlane jako `SQL` i są widoczne jako `SQL_USER` w sys.database_principals). Ich uprawnienia i role pozostają w metadanych bazy danych SQL Server i mogą być używane do personifikacji. Nie można ich jednak używać do uzyskiwania dostępu do SQL Server i logowania się do nich przy użyciu swoich poświadczeń.

- Tylko główna nazwa logowania na poziomie serwera, która jest tworzona przez proces aprowizacji wystąpienia zarządzanego przez usługę SQL, członkowie ról serwera, takie jak `securityadmin` lub `sysadmin` , lub inne logowania z uprawnieniami do zmiany nazwy logowania na poziomie serwera mogą tworzyć nazwy główne serwera usługi Azure AD (nazwy logowania) w bazie danych Master dla wystąpienia zarządzanego SQL.
- Jeśli nazwa logowania jest podmiotem SQL, tylko nazwy logowania należące do `sysadmin` roli mogą używać polecenia CREATE do tworzenia logowań dla konta usługi Azure AD.
- Identyfikator logowania usługi Azure AD musi być członkiem usługi Azure AD w tym samym katalogu, który jest używany do wystąpienia zarządzanego usługi Azure SQL.
- Nazwy główne (Logins) serwera usługi Azure AD są widoczne w Eksplorator obiektów rozpoczynające się od SQL Server Management Studio 18,0 wersja zapoznawcza 5.
- Mogą nakładać się nazwy główne (logowania) serwera usługi Azure AD z kontem administratora usługi Azure AD. Nazwy główne (Logins) serwera usługi Azure AD mają pierwszeństwo przed administratorem usługi Azure AD po rozwiązaniu podmiotu zabezpieczeń i zastosowaniu uprawnień do wystąpienia zarządzanego SQL.
- Podczas uwierzytelniania następująca sekwencja jest stosowana w celu rozwiązania uwierzytelniania podmiotu zabezpieczeń:

    1. Jeśli konto usługi Azure AD istnieje jako bezpośrednio zamapowane do podmiotu zabezpieczeń serwera usługi Azure AD (logowanie), które jest obecne w sys.server_principals jako "E", Udziel dostępu i Zastosuj uprawnienia podmiotu zabezpieczeń serwera usługi Azure AD.
    2. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD mapowanej do podmiotu zabezpieczeń serwera usługi Azure AD (logowanie), która jest obecna w sys.server_principals jako typ "X", Udziel dostępu i Zastosuj uprawnienia do logowania do grupy usługi Azure AD.
    3. Jeśli konto usługi Azure AD jest specjalnym skonfigurowanym przez portal administratorem usługi Azure AD dla wystąpienia zarządzanego SQL, które nie istnieje w widokach systemu wystąpienia zarządzanego SQL, Zastosuj specjalne uprawnienia do programu Azure AD administrator dla wystąpienia zarządzanego SQL (starsza wersja).
    4. Jeśli konto usługi Azure AD istnieje jako bezpośrednio zamapowane do użytkownika usługi Azure AD w bazie danych, która jest obecna w sys.database_principals jako "E", Udziel dostępu i Zastosuj uprawnienia użytkownika usługi Azure AD Database.
    5. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest zamapowana do użytkownika usługi Azure AD w bazie danych, która jest obecna w sys.database_principals jako typ "X", Udziel dostępu i Zastosuj uprawnienia do logowania do grupy usługi Azure AD.
    6. W przypadku zamapowania identyfikatora logowania usługi Azure AD na konto użytkownika usługi Azure AD lub konto grupy usługi Azure AD, które jest rozpoznawane przez użytkownika, który uwierzytelnia się, zostaną zastosowane wszystkie uprawnienia z tej nazwy logowania usługi Azure AD.

### <a name="service-key-and-service-master-key"></a>Klucz usługi i klucz główny usługi

- [Tworzenie kopii zapasowej klucza głównego](/sql/t-sql/statements/backup-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywracanie klucza głównego](/sql/t-sql/statements/restore-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Tworzenie kopii zapasowej klucza głównego usługi](/sql/t-sql/statements/backup-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).
- [Przywracanie klucza głównego usługi](/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez usługę SQL Database).

## <a name="configuration"></a>Konfiguracja

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów

- [Rozszerzenie puli buforów](/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nie jest obsługiwana. Zobacz [ALTER Server Configuration](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Sortowanie

Domyślne sortowanie wystąpień jest `SQL_Latin1_General_CP1_CI_AS` i może być określone jako parametr tworzenia. Zobacz [sortowania](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Poziomy zgodności

- Obsługiwane poziomy zgodności to 100, 110, 120, 130, 140 i 150.
- Poziomy zgodności poniżej 100 nie są obsługiwane.
- Domyślny poziom zgodności dla nowych baz danych to 140. W przypadku przywróconych baz danych poziom zgodności pozostaje niezmieniony, jeśli jego wartość to 100 i nowsze.

Zobacz [ALTER DATABASE — poziom zgodności](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Funkcja dublowania baz danych nie jest obsługiwana.

- `ALTER DATABASE SET PARTNER``SET WITNESS`Opcje i nie są obsługiwane.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE partner i Set monitor](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) oraz [Create Endpoint... DLA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opcje bazy danych

- Wiele plików dziennika nie jest obsługiwanych.
- Obiekty w pamięci nie są obsługiwane w warstwie usług Ogólnego przeznaczenia. 
- Istnieje limit 280 plików na wystąpienie Ogólnego przeznaczenia, co oznacza maksymalnie 280 plików na bazę danych. Oba pliki danych i dziennika w warstwie Ogólnego przeznaczenia są wliczane do tego limitu. [Warstwa krytyczne dla działania firmy obsługuje 32 767 plików na bazę danych](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- Baza danych nie może zawierać grup plików zawierających dane FILESTREAM. Przywracanie kończy się niepowodzeniem, jeśli. bak zawiera `FILESTREAM` dane. 
- Każdy plik jest umieszczany w usłudze Azure Blob Storage. We/wy i przepływność na plik zależą od rozmiaru poszczególnych plików.

#### <a name="create-database-statement"></a>Instrukcja CREATE DATABASE

Następujące ograniczenia mają zastosowanie do `CREATE DATABASE` :

- Nie można definiować plików i grup plików. 
- `CONTAINMENT`Opcja nie jest obsługiwana. 
- `WITH` Opcje nie są obsługiwane. 
   > [!TIP]
   > Aby obejść ten element, użyj programu `ALTER DATABASE` After `CREATE DATABASE` do ustawienia opcji bazy danych w celu dodania plików lub ustawienia zawierania. 

- `FOR ATTACH`Opcja nie jest obsługiwana.
- `AS SNAPSHOT OF`Opcja nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [Tworzenie bazy danych](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrukcja ALTER DATABASE

Nie można ustawić lub zmienić niektórych właściwości pliku:

- Ścieżka pliku nie może być określona w `ALTER DATABASE ADD FILE (FILENAME='path')` instrukcji t-SQL. Usuń `FILENAME` ze skryptu, ponieważ wystąpienie zarządzane SQL automatycznie umieszcza pliki. 
- Nie można zmienić nazwy pliku przy użyciu `ALTER DATABASE` instrukcji.

Następujące opcje są domyślnie ustawione i nie można ich zmienić:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

Nie można modyfikować następujących opcji:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>Program SQL Server Agent

- Włączanie i wyłączanie agenta SQL Server nie jest obecnie obsługiwane w wystąpieniu zarządzanym SQL. Agent SQL zawsze działa.
- Ustawienia agenta SQL Server są tylko do odczytu. Procedura `sp_set_agent_properties` nie jest obsługiwana w wystąpieniu zarządzanym SQL. 
- Stanowiska
  - Obsługiwane są czynności zadania T-SQL.
  - Obsługiwane są następujące zadania replikacji:
    - Dziennik transakcji
    - Snapshot
    - Dystrybutor
  - Obsługiwane są etapy zadania SSIS.
  - Inne typy kroków zadań nie są obecnie obsługiwane:
    - Etap zadania replikacji scalającej nie jest obsługiwany. 
    - Czytnik kolejki nie jest obsługiwany. 
    - Powłoka poleceń nie jest jeszcze obsługiwana.
  - Wystąpienie zarządzane SQL nie może uzyskać dostępu do zasobów zewnętrznych, na przykład udziałów sieciowych za pośrednictwem Robocopy. 
  - SQL Server Analysis Services nie jest obsługiwana.
- Powiadomienia są częściowo obsługiwane.
- Powiadomienie e-mail jest obsługiwane, chociaż wymaga skonfigurowania profilu Poczta bazy danych. Agent SQL Server może używać tylko jednego profilu Poczta bazy danych i musi zostać wywołany `AzureManagedInstance_dbmail_profile` . 
  - Moduł stronicowania nie jest obsługiwany.
  - Wysyłanie wiadomości nie jest obsługiwane.
  - Alerty nie są jeszcze obsługiwane.
  - Serwery proxy nie są obsługiwane.
- Dziennik zdarzeń nie jest obsługiwany.

Następujące funkcje agenta SQL nie są obecnie obsługiwane:

- Proxy
- Planowanie zadań w bezczynnym procesorze CPU
- Włączanie lub wyłączanie agenta
- Alerty

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabele

Następujące typy tabel nie są obsługiwane:

- [STRUMIENI](/sql/relational-databases/blob/filestream-sql-server)
- [OBIEKTU](/sql/relational-databases/blob/filetables-sql-server)
- [Tabela zewnętrzna](/sql/t-sql/statements/create-external-table-transact-sql) (baza Base)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nieobsługiwane tylko w warstwie ogólnego przeznaczenia)

Aby uzyskać informacje o sposobach tworzenia i modyfikowania tabel, zobacz [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) i [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkcje

### <a name="bulk-insert--openrowset"></a>Wstawianie zbiorcze/OPENROWSET

Wystąpienie zarządzane SQL nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc pliki muszą zostać zaimportowane z usługi Azure Blob Storage:

- `DATASOURCE` jest wymagany w `BULK INSERT` poleceniu podczas importowania plików z usługi Azure Blob Storage. Zobacz [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` jest wymagany w `OPENROWSET` funkcji podczas odczytywania zawartości pliku z usługi Azure Blob Storage. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` może służyć do odczytywania danych z Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL lub wystąpienia SQL Server. Inne źródła, takie jak bazy danych Oracle lub pliki programu Excel, nie są obsługiwane.

### <a name="clr"></a>CLR

Wystąpienie zarządzane SQL nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, więc obowiązują następujące ograniczenia:

- `CREATE ASSEMBLY FROM BINARY`Obsługiwane są tylko. Zobacz [Tworzenie zestawu na podstawie danych binarnych](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nie jest obsługiwana. Zobacz [Tworzenie zestawu z pliku](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nie można odwoływać się do plików. Zobacz [ALTER Assembly](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Poczta bazy danych (db_mail)
 - `sp_send_dbmail` nie można wysłać załączników przy użyciu @file_attachments parametru. Nie ma dostępu do lokalnego systemu plików ani udziałów zewnętrznych ani platformy Azure Blob Storage.
 - Zobacz znane problemy związane z `@query` parametrem i uwierzytelnianiem.
 
### <a name="dbcc"></a>DBCC

Nieudokumentowane instrukcje DBCC, które są włączone w SQL Server nie są obsługiwane w wystąpieniu zarządzanym SQL.

- Obsługiwana jest tylko ograniczona liczba globalnych flag śledzenia. Poziom sesji `Trace flags` nie jest obsługiwany. Zobacz [flagi śledzenia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [Polecenia DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) i [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) współpracują z ograniczoną liczbą globalnych flag śledzenia.
- Nie można użyć [polecenia DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) z opcjami REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST i REPAIR_REBUILD, ponieważ w trybie nie można ustawić bazy danych `SINGLE_USER` — Zobacz temat [ALTER DATABASE — różnice](#alter-database-statement). Potencjalne uszkodzenie bazy danych jest obsługiwane przez zespół pomocy technicznej systemu Azure. Skontaktuj się z pomocą techniczną platformy Azure, jeśli wystąpiły jakieś informacje o uszkodzeniu bazy danych.

### <a name="distributed-transactions"></a>Transakcje rozproszone

Częściowa pomoc techniczna dla [transakcji rozproszonych](../database/elastic-transactions-overview.md) jest obecnie w publicznej wersji zapoznawczej. Obsługiwane scenariusze:
* Transakcje, w których uczestnicy są tylko wystąpieniami zarządzanymi usługi Azure SQL, które są częścią [grupy zaufania serwera](https://aka.ms/mitrusted-groups).
* Transakcje zainicjowane z platformy .NET (Klasa TransactionScope) i Transact-SQL.

Wystąpienie zarządzane Azure SQL obecnie nie obsługuje innych scenariuszy, które są regularnie obsługiwane przez usługę MSDTC lokalnie lub w usłudze Azure Virtual Machines.

### <a name="extended-events"></a>Zdarzenia rozszerzone

Niektóre elementy docelowe specyficzne dla systemu Windows dla zdarzeń rozszerzonych (XEvents) nie są obsługiwane:

- `etw_classic_sync`Element docelowy nie jest obsługiwany. Przechowywanie `.xel` plików w usłudze Azure Blob Storage. Zobacz [etw_classic_sync cel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- `event_file`Element docelowy nie jest obsługiwany. Przechowywanie `.xel` plików w usłudze Azure Blob Storage. Zobacz [event_file cel](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Biblioteki zewnętrzne

Biblioteki zewnętrzne języka R i Python są obsługiwane w ograniczonej publicznej wersji zapoznawczej. Zobacz [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL (wersja zapoznawcza)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>FILESTREAM i FileTable

- Dane FILESTREAM nie są obsługiwane.
- Baza danych nie może zawierać grup plików z `FILESTREAM` danymi.
- `FILETABLE` nie jest obsługiwana.
- Tabele nie mogą mieć `FILESTREAM` typów.
- Następujące funkcje nie są obsługiwane:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Aby uzyskać więcej informacji, zobacz [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) i [FileTable](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Wyszukiwanie semantyczne pełnotekstowe

[Wyszukiwanie semantyczne](/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone

Połączone serwery w wystąpieniu zarządzanym SQL obsługują ograniczoną liczbę elementów docelowych:

- Obsługiwane elementy docelowe to wystąpienia zarządzane SQL, SQL Database, Azure Synapse SQL i wystąpienia SQL Server. 
- Połączone serwery nie obsługują dystrybuowanych transakcji zapisywalnych (MS DTC).
- Elementy docelowe, które nie są obsługiwane to pliki, Analysis Services i inne RDBMS. Spróbuj użyć natywnego importu CSV z platformy Azure Blob Storage przy użyciu `BULK INSERT` lub `OPENROWSET` jako alternatywy dla importu pliku.

Operacje: 

- Transakcje zapisu między wystąpieniami nie są obsługiwane.
- `sp_dropserver` jest obsługiwany w przypadku upuszczania połączonego serwera. Zobacz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- `OPENROWSET`Funkcja może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENDATASOURCE`Funkcja może służyć do wykonywania zapytań tylko w wystąpieniach SQL Server. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Tylko `SQLNCLI` wartości, `SQLNCLI11` i `SQLOLEDB` są obsługiwane jako dostawcy. Może to być na przykład `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Połączonych serwerów nie można używać do odczytywania plików (Excel, CSV) z udziałów sieciowych. Spróbuj użyć [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) lub [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) , który odczytuje pliki CSV z usługi Azure Blob Storage. Śledź te żądania względem [elementu opinii o wystąpieniu zarządzanym SQL](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Tabele zewnętrzne odwołujące się do plików w systemie HDFS lub Azure Blob Storage nie są obsługiwane. Aby uzyskać informacje o bazie danych Base, zobacz artykuł [Base](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja

- Obsługiwane są migawki i dwukierunkowe typy replikacji. Replikacja scalająca, replikacja równorzędna i aktualizowalne subskrypcje nie są obsługiwane.
- [Replikacja transakcyjna](replication-transactional-overview.md) jest dostępna w publicznej wersji zapoznawczej w wystąpieniu zarządzanym SQL z pewnymi ograniczeniami:
    - Wszystkie typy uczestników replikacji (wydawcy, dystrybutora, subskrybenta ściągania i wypychania) mogą być umieszczane w wystąpieniu zarządzanym SQL, ale Wydawca i dystrybutor muszą być zarówno w chmurze, jak i lokalnie.
    - Wystąpienie zarządzane SQL może komunikować się z najnowszymi wersjami SQL Server. Aby uzyskać więcej informacji, zobacz [macierz obsługiwane wersje](replication-transactional-overview.md#supportability-matrix) .
    - Replikacja transakcyjna ma [dodatkowe wymagania dotyczące sieci](replication-transactional-overview.md#requirements).

Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:
- [Replikacja między programem SQL i subskrybentem SQL MI](replication-between-two-instances-configure-tutorial.md)
- [Replikacja między programem SQL MI Wydawca, dystrybutorem programu SQL MI i subskrybentem SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>Instrukcja RESTORE 

- Obsługiwana składnia:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nieobsługiwana Składnia:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Źródło: 
  - `FROM URL` (Magazyn obiektów blob platformy Azure) jest jedyną obsługiwaną opcją.
  - `FROM DISK`/`TAPE`/Backup urządzenie nie jest obsługiwane.
  - Zestawy kopii zapasowych nie są obsługiwane.
- `WITH` Opcje nie są obsługiwane. Próby przywracania, takie `WITH` jak `DIFFERENTIAL` ,, `STATS` `REPLACE` itp., zakończą się niepowodzeniem.
- `ASYNC RESTORE`: Przywracanie jest kontynuowane nawet w przypadku przerwania połączenia z klientem. Jeśli połączenie zostało porzucone, można sprawdzić `sys.dm_operation_status` stan operacji przywracania oraz utworzyć i usunąć bazę danych. Zobacz [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Następujące opcje bazy danych są ustawiane lub zastępowane i nie można ich zmienić później: 

- `NEW_BROKER` Jeśli Broker nie jest włączony w pliku. bak. 
- `ENABLE_BROKER` Jeśli Broker nie jest włączony w pliku. bak. 
- `AUTO_CLOSE=OFF` Jeśli baza danych w pliku. bak ma wartość `AUTO_CLOSE=ON` . 
- `RECOVERY FULL` Jeśli baza danych w pliku bak ma `SIMPLE` tryb odzyskiwania lub jest w nim `BULK_LOGGED` .
- Grupa plików zoptymalizowana pod kątem pamięci jest dodawana i nazywana XTP, jeśli nie została w pliku source. bak. 
- Wszystkie istniejące grupy plików zoptymalizowane pod kątem pamięci są zmieniane na XTP. 
- `SINGLE_USER` i `RESTRICTED_USER` Opcje są konwertowane na `MULTI_USER` .

Ograniczenia: 

- Kopie zapasowe uszkodzonych baz danych mogą zostać przywrócone w zależności od typu uszkodzenia, ale automatyczne kopie zapasowe nie będą pobierane do momentu, gdy uszkodzenie nie zostanie naprawione. Upewnij się, że uruchomiono `DBCC CHECKDB` w źródłowym wystąpieniu zarządzanym SQL, i Użyj kopii zapasowej, `WITH CHECKSUM` Aby uniknąć tego problemu.
- Nie można przywrócić `.BAK` pliku bazy danych zawierającej jakiekolwiek ograniczenie opisane w tym dokumencie (na przykład `FILESTREAM` lub `FILETABLE` obiekty) w wystąpieniu zarządzanym SQL.
- `.BAK` nie można przywrócić plików zawierających wiele zestawów kopii zapasowych. 
- `.BAK` nie można przywrócić plików zawierających wiele plików dziennika.
- Kopie zapasowe, które zawierają bazy danych o rozmiarze większym niż 8 TB, aktywne obiekty OLTP w pamięci lub liczba plików, które przekroczą 280 plików na wystąpienie, nie mogą zostać przywrócone na wystąpienie Ogólnego przeznaczenia. 
- Kopie zapasowe, które zawierają bazy danych o rozmiarze większym niż 4 TB lub obiekty OLTP w pamięci o łącznym poziomie większym niż rozmiar opisany w [limitach zasobów](resource-limits.md) , nie mogą zostać przywrócone w wystąpieniu krytyczne dla działania firmy.
Aby uzyskać informacje na temat przywracania instrukcji, zobacz [przywracanie instrukcji](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Te same ograniczenia dotyczą wbudowanej operacji przywracania do punktu w czasie. Przykładowo nie można przywrócić bazy danych Ogólnego przeznaczenia o pojemności większej niż 4 TB w wystąpieniu Krytyczne dla działania firmy. W wystąpieniu Ogólnego przeznaczenia nie można przywrócić bazy danych Krytyczne dla działania firmy z plikami OLTP w pamięci lub więcej niż 280 plików.

### <a name="service-broker"></a>Broker usług

Broker usług dla wielu wystąpień nie jest obsługiwany:

- `sys.routes`: Jako wymaganie wstępne należy wybrać adres z wykazu sys. routes. Adres musi być lokalny dla każdej trasy. Zobacz sekcję [sys. routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Nie można używać `CREATE ROUTE` z `ADDRESS` innymi niż `LOCAL` . Zobacz [Tworzenie trasy](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Nie można używać `ALTER ROUTE` z `ADDRESS` innymi niż `LOCAL` . Zobacz [ALTER Route](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedury składowane, funkcje i wyzwalacze

- `NATIVE_COMPILATION` nie jest obsługiwana w warstwie Ogólnego przeznaczenia.
- Następujące opcje [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nie są obsługiwane: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts` nie jest obsługiwana. Zobacz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nie jest obsługiwana. Zobacz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`nie są obsługiwane, w tym `sp_addextendedproc`   i `sp_dropextendedproc` . Zobacz [rozszerzone procedury składowane](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- `sp_attach_db`, `sp_attach_single_file_db` i `sp_detach_db` nie są obsługiwane. Zobacz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)i [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funkcje systemowe i zmienne

Następujące zmienne, funkcje i widoki zwracają różne wyniki:

- `SERVERPROPERTY('EngineEdition')` Zwraca wartość 8. Ta właściwość jednoznacznie identyfikuje wystąpienie zarządzane SQL. Zobacz [ServerProperty](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Zwraca wartość NULL, ponieważ koncepcja wystąpienia, która istnieje dla SQL Server nie ma zastosowania do wystąpienia zarządzanego SQL. Zobacz [ServerProperty ("InstanceName")](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Zwraca pełną nazwę DNS "Connected", na przykład my-managed-instance.wcus17662feb9ce98.database.windows.net. Zobacz [@ @SERVERNAME ](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Zwraca pełną nazwę DNS "Connected", `myinstance.domain.database.windows.net` na przykład dla właściwości "name" i "data_source". Zobacz sekcję [sys. SERWERY](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Zwraca wartość NULL, ponieważ koncepcja usługi, która istnieje dla SQL Server nie ma zastosowania do wystąpienia zarządzanego SQL. Zobacz [@ @SERVICENAME ](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` jest obsługiwana. Zwraca wartość NULL, jeśli logowanie za pomocą usługi Azure AD nie znajduje się w sys.syslogowaniach. Zobacz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nie jest obsługiwana. Zwracane są błędne dane, które są tymczasowym znanym problemem. Zobacz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Ograniczenia środowiska

### <a name="subnet"></a>Podsieć
-  W podsieci, w której wdrożono wystąpienie zarządzane SQL, nie można umieścić żadnych innych zasobów (np. maszyn wirtualnych). Wdróż te zasoby przy użyciu innej podsieci.
- Podsieć musi mieć wystarczającą liczbę dostępnych [adresów IP](connectivity-architecture-overview.md#network-requirements). Wartość minimalna to 16, podczas gdy zalecenie ma mieć co najmniej 32 adresów IP w podsieci.
- [Punktów końcowych usługi nie można kojarzyć z podsiecią wystąpienia zarządzanego SQL](connectivity-architecture-overview.md#network-requirements). Upewnij się, że opcja punkty końcowe usługi jest wyłączona podczas tworzenia sieci wirtualnej.
- Liczba rdzeni wirtualnych i typy wystąpień, które można wdrożyć w regionie, mają pewne [ograniczenia i](resource-limits.md#regional-resource-limitations)ograniczenia.
- Istnieją pewne [reguły zabezpieczeń, które należy zastosować w podsieci](connectivity-architecture-overview.md#network-requirements).

### <a name="vnet"></a>Sieć wirtualna
- Sieć wirtualną można wdrożyć przy użyciu modelu zasobów — model klasyczny dla sieci wirtualnej nie jest obsługiwany.
- Po utworzeniu wystąpienia zarządzanego SQL przeniesienie wystąpienia zarządzanego SQL lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.
- Niektóre usługi, takie jak środowiska App Service, Aplikacje logiki i wystąpienie zarządzane SQL (używane na potrzeby replikacji geograficznej, replikacji transakcyjnej lub połączonych serwerów) nie mogą uzyskać dostępu do wystąpienia zarządzanego SQL w różnych regionach, jeśli ich sieci wirtualnych są połączone przy użyciu [globalnej komunikacji równorzędnej](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Możesz połączyć się z tymi zasobami za pośrednictwem ExpressRoute lub sieci VNet-to-VNet za pośrednictwem bram sieci wirtualnej.

### <a name="failover-groups"></a>Grupy trybu failover
Systemowe bazy danych nie są replikowane do wystąpienia dodatkowego w grupie trybu failover. W związku z tym scenariusze, które są zależne od obiektów z systemowych baz danych, będą niedostępne w wystąpieniu pomocniczym, chyba że obiekty są tworzone ręcznie na serwerze pomocniczym.

### <a name="failover-groups"></a>Grupy trybu failover
Systemowe bazy danych nie są replikowane do wystąpienia dodatkowego w grupie trybu failover. W związku z tym scenariusze, które są zależne od obiektów z systemowych baz danych, będą niedostępne w wystąpieniu pomocniczym, chyba że obiekty są tworzone ręcznie na serwerze pomocniczym.

### <a name="tempdb"></a>TEMPDB

Maksymalny rozmiar pliku `tempdb` nie może być większy niż 24 GB na rdzeń w warstwie ogólnego przeznaczenia. Maksymalny `tempdb` rozmiar w warstwie krytyczne dla działania firmy jest ograniczony przez rozmiar magazynu wystąpienia zarządzanego SQL. `Tempdb` rozmiar pliku dziennika jest ograniczony do 120 GB na warstwie Ogólnego przeznaczenia. Niektóre zapytania mogą zwrócić błąd, jeśli potrzebują ponad 24 GB na rdzeń w `tempdb` lub, jeśli generują więcej niż 120 GB danych dziennika.

### <a name="msdb"></a>MSDB

Następujące schematy MSDB w wystąpieniu zarządzanym SQL muszą należeć do odpowiednich wstępnie zdefiniowanych ról:

- Role ogólne
  - TargetServersRole
- [Stałe role bazy danych](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Role DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Role usług Integration Services](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Zmiana wstępnie zdefiniowanych nazw ról, nazw schematów i właścicieli schematu przez klientów będzie miała wpływ na normalne działanie usługi. Wszystkie zmiany wprowadzone w tych punktach zostaną przywrócone do wstępnie zdefiniowanych wartości zaraz po wykryciu lub w następnej aktualizacji usługi w najnowszej wersji, aby zapewnić normalną operację usługi.

### <a name="error-logs"></a>Dzienniki błędów

Wystąpienie zarządzane SQL umieszcza pełne informacje w dziennikach błędów. Istnieje wiele wewnętrznych zdarzeń systemowych, które są rejestrowane w dzienniku błędów. Użyj niestandardowej procedury, aby odczytać dzienniki błędów, które filtrują pewne nieistotne wpisy. Aby uzyskać więcej informacji, zobacz [wystąpienie zarządzane SQL — sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) lub [rozszerzenie wystąpienia zarządzanego SQL (wersja zapoznawcza)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) dla Azure Data Studio.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o wystąpieniu zarządzanym SQL, zobacz [co to jest wystąpienie zarządzane SQL?](sql-managed-instance-paas-overview.md)
- Aby poznać funkcje i listę porównania, zobacz [porównanie funkcji wystąpienia zarządzanego Azure SQL](../database/features-comparison.md).
- Aby uzyskać informacje o aktualizacjach wersji i stanie znanych problemów, zobacz [Informacje o wersji wystąpienia zarządzanego SQL](../database/doc-changes-updates-release-notes.md)
- Aby zapoznać się z przewodnikiem Szybki Start, który pokazuje, jak utworzyć nowe wystąpienie zarządzane SQL, zobacz [Tworzenie wystąpienia zarządzanego SQL](instance-create-quickstart.md).
