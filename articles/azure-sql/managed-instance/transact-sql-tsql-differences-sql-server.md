---
title: Różnice między językami T-SQL SQL Server & Azure SQL Managed Instance
description: W tym artykule omówiono różnice między językami Transact-SQL (T-SQL) Azure SQL Managed Instance i SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: f744b718919a6da75b2064efdc163ef4618b5a7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815904"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Różnice między językami T-SQL SQL Server & Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule podsumowano i wyjaśniono różnice składni i zachowania między Azure SQL Managed Instance i SQL Server. 


SQL Managed Instance zapewnia wysoką zgodność z aparatem SQL Server bazy danych, a większość funkcji jest obsługiwanych w SQL Managed Instance.

![Łatwa migracja z SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Istnieją pewne ograniczenia paaS, które zostały wprowadzone w SQL Managed Instance i pewne zmiany zachowania w porównaniu do SQL Server. Różnice dzielą się na następujące kategorie: <a name="Differences"></a>

- [Dostępność](#availability) obejmuje różnice w zawsze [dostępnych grupach dostępności](#always-on-availability-groups) i [kopiach zapasowych.](#backup)
- [Zabezpieczenia](#security) obejmują różnice w [inspekcji,](#auditing) [](#credential) [certyfikatach,](#certificates)poświadczeniach, dostawcach usług [kryptograficznych,](#cryptographic-providers)identyfikatorach logowania i użytkownikach [oraz](#logins-and-users)kluczu usługi i [kluczu głównym usługi.](#service-key-and-service-master-key)
- [Konfiguracja](#configuration) obejmuje różnice w [rozszerzeniu](#buffer-pool-extension)puli buforów, [](#collation)sortowanie, [](#compatibility-levels)poziomy [zgodności,](#database-mirroring)dublowanie bazy [danych,](#database-options)opcje bazy danych, SQL Server [Agent](#sql-server-agent)i opcje [tabeli.](#tables)
- [](#functionalities) Funkcje obejmują [BULK INSERT/OPENROWSET,](#bulk-insert--openrowset) [CLR,](#clr) [DBCC,](#dbcc)transakcje [rozproszone,](#distributed-transactions)zdarzenia [rozszerzone,](#extended-events)biblioteki [zewnętrzne,](#external-libraries)filestream i [FileTable,](#filestream-and-filetable)pełnotekstowe wyszukiwanie semantyczne, [](#full-text-semantic-search)serwery [połączone,](#linked-servers) [PolyBase,](#polybase) [replikację,](#replication) [PRZYWRACANIE](#restore-statement), [Service Broker](#service-broker), procedury składowane, funkcje [i wyzwalacze](#stored-procedures-functions-and-triggers).
- [Ustawienia środowiska,](#Environment) takie jak sieci wirtualne i konfiguracje podsieci.

Większość z tych funkcji to ograniczenia architektoniczne i reprezentują funkcje usługi.

Tymczasowe znane problemy wykryte w programie SQL Managed Instance, które zostaną rozwiązane w przyszłości, opisano na [stronie informacji o wersji.](../database/doc-changes-updates-release-notes.md)

## <a name="availability"></a>Dostępność

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Zawsze włączone grupy dostępności

[Wysoka dostępność](../database/high-availability-sla.md) jest wbudowana w SQL Managed Instance i nie może być kontrolowana przez użytkowników. Następujące instrukcje nie są obsługiwane:

- [UTWÓRZ PUNKT KOŃCOWY... NA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [TWORZENIE GRUPY DOSTĘPNOŚCI](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ZMIENIANIE GRUPY DOSTĘPNOŚCI](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- Klauzula [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

SQL Managed Instance ma automatyczne kopie zapasowe, dzięki czemu użytkownicy mogą tworzyć pełne kopie `COPY_ONLY` zapasowe bazy danych. Różnicowe kopie zapasowe, dziennika i migawki plików nie są obsługiwane.

- W SQL Managed Instance kopii zapasowej bazy danych wystąpienia można utworzyć tylko na koncie usługi Azure Blob Storage:
  - Obsługiwane `BACKUP TO URL` są tylko te urządzenia.
  - `FILE`Urządzenia, i nie są obsługiwane na `TAPE` urządzeniach kopii zapasowych.
- Większość opcji ogólnych `WITH` jest obsługiwanych.
  - `COPY_ONLY` jest obowiązkowy.
  - `FILE_SNAPSHOT` nie jest obsługiwana.
  - Opcje taśmy: `REWIND` , , i nie są `NOREWIND` `UNLOAD` `NOUNLOAD` obsługiwane.
  - Opcje specyficzne dla dziennika: `NORECOVERY` , i nie są `STANDBY` `NO_TRUNCATE` obsługiwane.

Ograniczenia: 

- Dzięki SQL Managed Instance można utworzyć kopię zapasową bazy danych wystąpienia w kopii zapasowej z maksymalnie 32 pasmami, co jest wystarczające dla baz danych o pojemności do 4 TB, jeśli jest używana kompresja kopii zapasowej.
- Nie można wykonać operacji na bazie danych, która jest zaszyfrowana za pomocą usługi `BACKUP DATABASE ... WITH COPY_ONLY` zarządzanej Transparent Data Encryption (TDE). Szyfrowanie TDE zarządzane przez usługę wymusza szyfrowanie kopii zapasowych za pomocą wewnętrznego klucza TDE. Nie można wyeksportować klucza, dlatego nie można przywrócić kopii zapasowej. Użyj automatycznych kopii zapasowych i przywracania do punktu w czasie lub zamiast tego użyj funkcji TDE zarządzanej przez klienta [(BYOK).](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) Możesz również wyłączyć szyfrowanie w bazie danych.
- Natywnych kopii zapasowych w wystąpieniu zarządzanym nie można przywrócić do SQL Server. Wynika to z tego, że wystąpienie zarządzane ma wyższą wersję wewnętrznej bazy danych w porównaniu z dowolną wersją SQL Server.
- Maksymalny rozmiar paska kopii zapasowej przy użyciu polecenia w `BACKUP` SQL Managed Instance to 195 GB, czyli maksymalny rozmiar obiektu blob. Zwiększ liczbę pasm w poleceniu kopii zapasowej, aby zmniejszyć rozmiar pojedynczego paska rozłożonego i utrzymać ten limit.

    > [!TIP]
    > Aby ominąc to ograniczenie, w przypadku kopii zapasowej bazy danych SQL Server w środowisku lokalnym lub na maszynie wirtualnej można:
    >
    > - Zamiast wrócić do kopii `DISK` zapasowej w . `URL`
    > - Przekaż pliki kopii zapasowej do usługi Blob Storage.
    > - Przywróć do SQL Managed Instance.
    >
    > Polecenie w SQL Managed Instance obsługuje większe rozmiary obiektów blob w plikach kopii zapasowej, ponieważ do przechowywania przekazanych plików kopii zapasowej jest używany `Restore` inny typ obiektu blob.

Aby uzyskać informacje o kopiach zapasowych przy użyciu języka T-SQL, zobacz [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Zabezpieczenia

### <a name="auditing"></a>Inspekcja

Najważniejsze różnice między inspekcją w języku MICROSOFT AZURE SQL i w SQL Server są:

- W SQL Managed Instance inspekcja działa na poziomie serwera. Pliki `.xel` dziennika są przechowywane w usłudze Azure Blob Storage.
- W Azure SQL Database inspekcja działa na poziomie bazy danych. Pliki `.xel` dziennika są przechowywane w usłudze Azure Blob Storage.
- W SQL Server, lokalnie lub na maszynach wirtualnych inspekcja działa na poziomie serwera. Zdarzenia są przechowywane w systemie plików lub dziennikach zdarzeń systemu Windows.
 
Inspekcja XEvent w SQL Managed Instance obsługuje obiekty docelowe usługi Azure Blob Storage. Dzienniki plików i systemu Windows nie są obsługiwane.

Najważniejsze różnice w składni `CREATE AUDIT` inspekcji w usłudze Azure Blob Storage to:

- Podano nową składnię, której można użyć do określenia adresu URL kontenera usługi `TO URL` Azure Blob Storage, w którym `.xel` znajdują się pliki.
- Składnia `TO FILE` nie jest obsługiwana, ponieważ SQL Managed Instance nie może uzyskać dostępu do udziałów plików systemu Windows.

Aby uzyskać więcej informacji, zobacz: 

- [TWORZENIE INSPEKCJI SERWERA](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Inspekcja](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certyfikaty

SQL Managed Instance dostępu do udziałów plików i folderów systemu Windows, dlatego obowiązują następujące ograniczenia:

- Plik `CREATE FROM` / `BACKUP TO` nie jest obsługiwany w przypadku certyfikatów.
- Certyfikat `CREATE` / `BACKUP` z `FILE` / `ASSEMBLY` usługi nie jest obsługiwany. Nie można używać plików kluczy prywatnych. 

Zobacz [CREATE CERTIFICATE and](/sql/t-sql/statements/create-certificate-transact-sql) BACKUP CERTIFICATE (TWORZENIE CERTYFIKATU I KOPII [ZAPASOWEJ CERTYFIKATU).](/sql/t-sql/statements/backup-certificate-transact-sql) 
 
**Obejście:** Zamiast tworzyć kopię zapasową certyfikatu i przywracać kopię zapasową, pobierz zawartość binarną certyfikatu i klucz prywatny, przechowaj go jako plik SQL i utwórz na bazie [pliku binarnego](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Poświadczenie

Obsługiwane Azure Key Vault tożsamości `SHARED ACCESS SIGNATURE` i tożsamości. Użytkownicy systemu Windows nie są obsługiwani.

Zobacz [CREATE CREDENTIAL (TWORZENIE POŚWIADCZEŃ)](/sql/t-sql/statements/create-credential-transact-sql) [i ALTER CREDENTIAL (ZMIENIANIE POŚWIADCZEŃ).](/sql/t-sql/statements/alter-credential-transact-sql)

### <a name="cryptographic-providers"></a>Dostawcy usług kryptograficznych

SQL Managed Instance nie można uzyskać dostępu do plików, dlatego nie można utworzyć dostawców usług kryptograficznych:

- `CREATE CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- `ALTER CRYPTOGRAPHIC PROVIDER` nie jest obsługiwana. Zobacz [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Nazwy logowania i użytkownicy

- Obsługiwane są identyfikatory logowania SQL `FROM CERTIFICATE` utworzone przy użyciu poleceń , i `FROM ASYMMETRIC KEY` `FROM SID` . Zobacz [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Azure Active Directory (identyfikatory logowania) serwera (Azure AD) utworzone przy użyciu składni [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) lub CREATE USER FROM [LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) są obsługiwane. Te identyfikatory logowania są tworzone na poziomie serwera.

    SQL Managed Instance obsługuje podmioty zabezpieczeń bazy danych usługi Azure AD o składni `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Ta funkcja jest również znana jako użytkownicy zawartej bazy danych usługi Azure AD.

- Identyfikatory logowania systemu Windows utworzone `CREATE LOGIN ... FROM WINDOWS` przy użyciu składni nie są obsługiwane. Użyj Azure Active Directory logowania i użytkowników.
- Użytkownik usługi Azure AD, który utworzył wystąpienie, ma [nieograniczone uprawnienia administratora.](../database/logins-create-manage.md)
- Użytkowników na poziomie bazy danych usługi Azure AD bez uprawnień administratora można tworzyć przy użyciu `CREATE USER ... FROM EXTERNAL PROVIDER` składni . Zobacz [CREATE USER... OD DOSTAWCY ZEWNĘTRZNEGO](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- Jednostki usługi (identyfikatory logowania) serwera Azure AD obsługują funkcje SQL tylko SQL Managed Instance jednym miejscu. Funkcje, które wymagają interakcji między wystąpieniami, niezależnie od tego, czy znajdują się w tej samej dzierżawie usługi Azure AD, czy w różnych dzierżawach, nie są obsługiwane dla użytkowników usługi Azure AD. Przykłady takich funkcji to:

  - Replikacja transakcyjna SQL.
  - Serwer linków.

- Ustawienie nazwy logowania usługi Azure AD zamapowanych na grupę usługi Azure AD jako właściciela bazy danych nie jest obsługiwane.
- Obsługiwana jest personifikacja podmiotów zabezpieczeń na poziomie serwera usługi Azure AD przy użyciu innych podmiotów zabezpieczeń usługi Azure AD, takich jak [klauzula EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Ograniczenia instrukcji EXECUTE AS są:

  - Polecenie EXECUTE AS USER nie jest obsługiwane w przypadku użytkowników usługi Azure AD, gdy nazwa różni się od nazwy logowania. Przykładem może być utworzenie użytkownika za pomocą składni CREATE USER [myAadUser] FROM LOGIN [ ] i próba personifikacji za pomocą polecenia john@contoso.com EXEC AS USER = _myAadUser._ Podczas tworzenia użytkownika **z jednostki** usługi (identyfikatora logowania) serwera Azure AD określ identyfikator user_name taki sam jak login_name identyfikatora **LOGOWANIA.**
  - Tylko podmioty zabezpieczeń SQL Server poziomie podstawowym (identyfikatory logowania), które są częścią roli, mogą wykonywać następujące operacje, które są `sysadmin` kierowane do podmiotów zabezpieczeń usługi Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Aby personifikować użytkownika za pomocą instrukcji EXECUTE AS, użytkownik musi być mapowany bezpośrednio na nazwę główną (identyfikator logowania) serwera Azure AD. Użytkownicy, którzy są członkami grup usługi Azure AD zamapowanych na podmioty zabezpieczeń serwera Azure AD, nie mogą być skutecznie personifikatorami za pomocą instrukcji EXECUTE AS, mimo że wywołujący ma uprawnienia personifikacji dla określonej nazwy użytkownika.

- Eksportowanie/importowanie bazy danych przy użyciu plików bacpac jest obsługiwane dla użytkowników usługi Azure AD w programie SQL Managed Instance przy użyciu [programu SSMS w wersji 18.4](/sql/ssms/download-sql-server-management-studio-ssms)lub nowszej albo [SQLPackage.exe. ](/sql/tools/sqlpackage-download)
  - Następujące konfiguracje są obsługiwane przy użyciu pliku bacpac bazy danych: 
    - Eksportowanie/importowanie bazy danych między różnymi wystąpieniami zarządzania w tej samej domenie usługi Azure AD.
    - Wyeksportuj bazę danych z SQL Managed Instance i zaimportuj SQL Database w tej samej domenie usługi Azure AD. 
    - Wyeksportuj bazę danych SQL Database i zaimportuj SQL Managed Instance w tej samej domenie usługi Azure AD.
    - Wyeksportuj bazę danych z SQL Managed Instance i zaimportuj do SQL Server (wersja 2012 lub nowsza).
      - W tej konfiguracji wszyscy użytkownicy usługi Azure AD są tworzona jako SQL Server bazy danych (użytkownicy) bez identyfikatorów logowania. Typ użytkowników jest wymieniony jako `SQL` i jest widoczny na `SQL_USER` sys.database_principals). Ich uprawnienia i role pozostają w SQL Server metadanych bazy danych i mogą służyć do personifikacji. Nie można ich jednak używać do uzyskiwania dostępu do aplikacji i logowania się do SQL Server przy użyciu swoich poświadczeń.

- Tylko identyfikator logowania podmiotu zabezpieczeń na poziomie serwera, który jest tworzony przez proces inicjowania obsługi administracyjnej usługi SQL Managed Instance, członków ról serwera, takich jak lub , lub inne identyfikatory logowania z uprawnieniem ALTER ANY LOGIN na poziomie serwera, mogą tworzyć jednostki usługi (identyfikatory logowania) serwera Azure AD w bazie danych master na `securityadmin` `sysadmin` SQL Managed Instance.
- Jeśli identyfikator logowania jest podmiotem zabezpieczeń SQL, tylko identyfikatory logowania, które są częścią roli, mogą używać polecenia create do tworzenia identyfikatorów logowania `sysadmin` dla konta usługi Azure AD.
- Identyfikator logowania usługi Azure AD musi być członkiem usługi Azure AD w tym samym katalogu, który jest używany na Azure SQL Managed Instance.
- Jednostki usługi (identyfikatory logowania) serwera Azure AD są widoczne w Eksplorator obiektów począwszy od wersji SQL Server Management Studio 18.0 (wersja zapoznawcza 5).
- Nakładające się jednostki usługi (identyfikatory logowania) serwera Azure AD przy użyciu konta administratora usługi Azure AD są dozwolone. Jednostki usługi (identyfikatory logowania) serwera Azure AD mają pierwszeństwo przed administratorem usługi Azure AD w przypadku rozpoznania podmiotu zabezpieczeń i zastosowania uprawnień do SQL Managed Instance.
- Podczas uwierzytelniania stosowana jest następująca sekwencja w celu rozpoznania podmiotu zabezpieczeń uwierzytelniania:

    1. Jeśli konto usługi Azure AD istnieje bezpośrednio zamapowane na jednostkę usługi (identyfikator logowania) serwera Azure AD, która jest obecna w usłudze sys.server_principals jako typ "E", uślij dostęp i zastosuj uprawnienia jednostki usługi (identyfikatora logowania) serwera Azure AD.
    2. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest mapowana na nazwę główną (identyfikator logowania) serwera Azure AD, która jest obecna w usłudze sys.server_principals jako typ "X", przyznaj dostęp i zastosuj uprawnienia logowania do grupy usługi Azure AD.
    3. Jeśli konto usługi Azure AD jest specjalnym kontem administratora usługi Azure AD skonfigurowanym w portalu dla usługi SQL Managed Instance, które nie istnieje w widokach systemu SQL Managed Instance, zastosuj specjalne stałe uprawnienia administratora usługi Azure AD dla usługi SQL Managed Instance (tryb starszej wersji).
    4. Jeśli konto usługi Azure AD istnieje jako bezpośrednio mapowane na użytkownika usługi Azure AD w bazie danych, która jest obecna w usłudze sys.database_principals jako typ "E", uślij dostęp i zastosuj uprawnienia użytkownika bazy danych usługi Azure AD.
    5. Jeśli konto usługi Azure AD jest członkiem grupy usługi Azure AD, która jest mapowana na użytkownika usługi Azure AD w bazie danych, która jest obecna w usłudze sys.database_principals jako typ "X", przyznaj dostęp i zastosuj uprawnienia logowania do grupy usługi Azure AD.
    6. Jeśli istnieje identyfikator logowania usługi Azure AD zamapowany na konto użytkownika usługi Azure AD lub konto grupy usługi Azure AD, co jest rozpoznawane jako uwierzytelnianie użytkownika, zostaną zastosowane wszystkie uprawnienia z tego identyfikatora logowania usługi Azure AD.

### <a name="service-key-and-service-master-key"></a>Klucz usługi i klucz główny usługi

- [Tworzenie kopii zapasowej klucza](/sql/t-sql/statements/backup-master-key-transact-sql) głównego nie jest obsługiwane (zarządzane przez SQL Database service).
- [Przywracanie klucza głównego](/sql/t-sql/statements/restore-master-key-transact-sql) nie jest obsługiwane (zarządzane przez SQL Database service).
- [Tworzenie kopii zapasowej klucza głównego](/sql/t-sql/statements/backup-service-master-key-transact-sql) usługi nie jest obsługiwane (zarządzane przez SQL Database service).
- [Przywracanie klucza głównego usługi](/sql/t-sql/statements/restore-service-master-key-transact-sql) nie jest obsługiwane (zarządzane przez SQL Database service).

## <a name="configuration"></a>Konfigurowanie

### <a name="buffer-pool-extension"></a>Rozszerzenie puli buforów

- [Rozszerzenie puli buforów](/sql/database-engine/configure-windows/buffer-pool-extension) nie jest obsługiwane.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION` nie jest obsługiwana. Zobacz [ALTER SERVER CONFIGURATION .](/sql/t-sql/statements/alter-server-configuration-transact-sql)

### <a name="collation"></a>Sortowanie

Sortowanie wystąpienia domyślnego to `SQL_Latin1_General_CP1_CI_AS` i można je określić jako parametr tworzenia. Zobacz [Collations (Sortowanie).](/sql/t-sql/statements/collations)

### <a name="compatibility-levels"></a>Poziomy zgodności

- Obsługiwane poziomy zgodności to 100, 110, 120, 130, 140 i 150.
- Poziomy zgodności poniżej 100 nie są obsługiwane.
- Domyślny poziom zgodności dla nowych baz danych to 140. W przypadku przywróconych baz danych poziom zgodności pozostaje niezmieniony, jeśli był to 100 lub wyższy.

Zobacz ALTER DATABASE Compatibility Level ( [Alter DATABASE Compatibility Level](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)).

### <a name="database-mirroring"></a>Dublowanie bazy danych

Dublowanie bazy danych nie jest obsługiwane.

- `ALTER DATABASE SET PARTNER` Opcje `SET WITNESS` i nie są obsługiwane.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING` nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE SET PARTNER i SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) oraz CREATE ENDPOINT [... NA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opcje bazy danych

- Wiele plików dziennika nie jest obsługiwanych.
- Obiekty w pamięci nie są obsługiwane w Ogólnego przeznaczenia usługi. 
- Istnieje limit 280 plików na wystąpienie Ogólnego przeznaczenia, co oznacza, że maksymalnie 280 plików na bazę danych. Zarówno pliki danych, jak i dziennika w Ogólnego przeznaczenia są wliczane do tego limitu. [Warstwa Krytyczne dla działania firmy obsługuje 32 767 plików na bazę danych.](./resource-limits.md#service-tier-characteristics)
- Baza danych nie może zawierać grup plików, które zawierają dane strumienia plików. Przywracanie kończy się niepowodzeniem, jeśli plik bak `FILESTREAM` zawiera dane. 
- Każdy plik jest umieszczany w usłudze Azure Blob Storage. Przepływność i we/wy na plik zależą od rozmiaru poszczególnych plików.

#### <a name="create-database-statement"></a>INSTRUKCJA CREATE DATABASE

W przypadku systemu obowiązują następujące `CREATE DATABASE` ograniczenia:

- Nie można zdefiniować plików i grup plików. 
- Ta `CONTAINMENT` opcja nie jest obsługiwana. 
- `WITH` opcje nie są obsługiwane. 
   > [!TIP]
   > Aby obejść ten problem, użyj funkcji po , aby ustawić opcje bazy danych w celu dodania `ALTER DATABASE` `CREATE DATABASE` plików lub ustawienia zawierania. 

- Ta `FOR ATTACH` opcja nie jest obsługiwana.
- Ta `AS SNAPSHOT OF` opcja nie jest obsługiwana.

Aby uzyskać więcej informacji, zobacz [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>INSTRUKCJA ALTER DATABASE

Niektórych właściwości pliku nie można ustawić ani zmienić:

- Nie można określić ścieżki pliku w instrukcji `ALTER DATABASE ADD FILE (FILENAME='path')` języka T-SQL. Usuń `FILENAME` plik ze skryptu, SQL Managed Instance automatycznie umieszcza pliki. 
- Nazwy pliku nie można zmienić przy użyciu `ALTER DATABASE` instrukcji .

Następujące opcje są ustawiane domyślnie i nie można ich zmienić:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

Nie można zmodyfikować następujących opcji:

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

Niektóre `ALTER DATABASE` instrukcje (na przykład [ZAWIERANIE ZESTAWU](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) mogą przejściowo nie powieść się, na przykład podczas automatycznego tworzenia kopii zapasowej bazy danych lub bezpośrednio po utworzeniu bazy danych. W tym przypadku `ALTER DATABASE` należy ponownie ponoć instrukcje . Aby uzyskać więcej informacji na temat powiązanych komunikatów o błędach, [zobacz sekcję Uwagi](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2).

Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Włączanie i wyłączanie agenta SQL Server nie jest obecnie obsługiwane w SQL Managed Instance. Agent SQL zawsze działa.
- Wyzwalacz harmonogramu zadań oparty na bezczynnych procesorach CPU nie jest obsługiwany.
- SQL Server ustawienia agenta są tylko do odczytu. Procedura nie `sp_set_agent_properties` jest obsługiwana w SQL Managed Instance. 
- Stanowiska
  - Obsługiwane są kroki zadania T-SQL.
  - Obsługiwane są następujące zadania replikacji:
    - Czytnik dzienników transakcji
    - Snapshot
    - Dystrybutor
  - Obsługiwane są kroki zadania SSIS.
  - Inne typy kroków zadania nie są obecnie obsługiwane:
    - Krok zadania replikacji scalania nie jest obsługiwany. 
    - Czytnik kolejek nie jest obsługiwany. 
    - Powłoka poleceń nie jest jeszcze obsługiwana.
  - SQL Managed Instance nie może uzyskać dostępu do zasobów zewnętrznych, na przykład udziałów sieciowych za pośrednictwem programu Robocopy. 
  - SQL Server Analysis Services nie jest obsługiwana.
- Powiadomienia są obsługiwane częściowo.
- Powiadomienia e-mail są obsługiwane, chociaż wymaga skonfigurowania profilu Poczta bazy danych e-mail. SQL Server agent może używać tylko jednego Poczta bazy danych profilu i musi mieć nazwę `AzureManagedInstance_dbmail_profile` . 
  - Pager nie jest obsługiwany.
  - Program NetSend nie jest obsługiwany.
  - Alerty nie są jeszcze obsługiwane.
  - Proxy nie są obsługiwane.
- Zdarzenie EventLog nie jest obsługiwane.
- Użytkownik musi być bezpośrednio mapowany na jednostkę usługi (identyfikator logowania) serwera Azure AD w celu tworzenia, modyfikowania lub wykonywania zadań agenta SQL. Użytkownicy, którzy nie są bezpośrednio mapowane, na przykład użytkownicy należący do grupy usługi Azure AD, która ma uprawnienia do tworzenia, modyfikowania lub wykonywania zadań agenta SQL, nie będą mogli skutecznie wykonywać tych akcji. Jest to spowodowane personifikacji wystąpienia zarządzanego i [ograniczeniami polecenia EXECUTE AS.](#logins-and-users)
- Funkcja administracji wieloma serwerami dla zadań główny/docelowy (MSX/TSX) nie jest obsługiwana.

Aby uzyskać informacje na temat agenta programu SQL Server, zobacz [SQL Server Agent (Agent programu SQL Server)](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>tabelami

Następujące typy tabel nie są obsługiwane:

- [Filestream](/sql/relational-databases/blob/filestream-sql-server)
- [Filetable](/sql/relational-databases/blob/filetables-sql-server)
- [TABELA ZEWNĘTRZNA](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (nie jest obsługiwane tylko w Ogólnego przeznaczenia warstwie)

Aby uzyskać informacje na temat tworzenia i modyfikowania tabel, [zobacz CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) i [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funkcje

### <a name="bulk-insert--openrowset"></a>Wstawianie zbiorcze / OPENROWSET

SQL Managed Instance nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, dlatego pliki muszą zostać zaimportowane z usługi Azure Blob Storage:

- `DATASOURCE` jest wymagany w `BULK INSERT` poleceniu podczas importowania plików z usługi Azure Blob Storage. Zobacz [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` jest wymagany w funkcji podczas odczytywania zawartości pliku `OPENROWSET` z usługi Azure Blob Storage. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` Może służyć do odczytywania danych z Azure SQL Database, Azure SQL Managed Instance lub SQL Server wystąpień. Inne źródła, takie jak bazy danych Oracle lub pliki programu Excel, nie są obsługiwane.

### <a name="clr"></a>CLR

Usługa SQL Managed Instance nie może uzyskać dostępu do udziałów plików i folderów systemu Windows, dlatego obowiązują następujące ograniczenia:

- Obsługiwane `CREATE ASSEMBLY FROM BINARY` są tylko te urządzenia. Zobacz [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- `CREATE ASSEMBLY FROM FILE` nie jest obsługiwana. Zobacz [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` nie może odwoływać się do plików. Zobacz [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Poczta bazy danych (db_mail)
 - `sp_send_dbmail` nie może wysyłać załączników przy użyciu @file_attachments parametru . Lokalny system plików i udziały zewnętrzne Azure Blob Storage nie są dostępne w tej procedurze.
 - Zobacz znane problemy związane z `@query` parametrami i uwierzytelnianiem.
 
### <a name="dbcc"></a>Dbcc

Nieudokumentowane instrukcje DBCC włączone w SQL Server nie są obsługiwane w SQL Managed Instance.

- Obsługiwana jest tylko ograniczona liczba flag śledzenia globalnego. Poziom sesji `Trace flags` nie jest obsługiwany. Zobacz [Flagi śledzenia](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [Narzędzia DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) i [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) działają z ograniczoną liczbą globalnych flag śledzenia.
- Nie można użyć polecenia [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST i REPAIR_REBUILD, ponieważ nie można ustawić bazy danych w trybie — zobacz ALTER DATABASE differences (Różnice w alter `SINGLE_USER` [DATABASE).](#alter-database-statement) Potencjalne uszkodzenie bazy danych jest obsługiwane przez zespół pomocy technicznej platformy Azure. W przypadku jakichkolwiek oznak uszkodzenia bazy danych skontaktuj się z pomocą techniczną platformy Azure.

### <a name="distributed-transactions"></a>Transakcje rozproszone

Częściowa obsługa [transakcji rozproszonych jest](../database/elastic-transactions-overview.md) obecnie dostępna w publicznej wersji zapoznawczej. Transakcje rozproszone są obsługiwane w następujących warunkach (wszystkie muszą zostać spełnione):
* Wszyscy uczestnicy transakcji Azure SQL wystąpienia zarządzane, które są częścią [grupy zaufania serwera](./server-trust-group-overview.md).
* Transakcje są inicjowane z .NET (klasa TransactionScope) lub Transact-SQL.

Azure SQL Managed Instance obecnie nie obsługuje innych scenariuszy, które są regularnie obsługiwane przez usługę MSDTC lokalnie lub w usłudze Azure Virtual Machines.

### <a name="extended-events"></a>Zdarzenia rozszerzone

Niektóre elementy docelowe właściwe dla systemu Windows dotyczące zdarzeń rozszerzonych (XEvents) nie są obsługiwane:

- Element `etw_classic_sync` docelowy nie jest obsługiwany. Przechowywanie `.xel` plików w usłudze Azure Blob Storage. Zobacz [element docelowy etw_classic_sync](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Element `event_file` docelowy nie jest obsługiwany. Przechowywanie `.xel` plików w usłudze Azure Blob Storage. Zobacz [element docelowy event_file](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Biblioteki zewnętrzne

Biblioteki zewnętrzne języka R i Python w bazie danych są obsługiwane w ograniczonej publicznej wersji zapoznawczej. Zobacz [Machine Learning Services w Azure SQL Managed Instance (wersja zapoznawcza).](machine-learning-services-overview.md)

### <a name="filestream-and-filetable"></a>Filestream i FileTable

- Dane strumienia plików nie są obsługiwane.
- Baza danych nie może zawierać grup plików z `FILESTREAM` danymi.
- `FILETABLE` nie jest obsługiwana.
- Tabele nie mogą mieć `FILESTREAM` typów.
- Następujące funkcje nie są obsługiwane:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Aby uzyskać więcej informacji, [zobacz FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) i [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Wyszukiwanie semantyczne pełno tekstowe

[Wyszukiwanie semantyczne](/sql/relational-databases/search/semantic-search-sql-server) nie jest obsługiwane.

### <a name="linked-servers"></a>Serwery połączone

Serwery połączone w SQL Managed Instance obsługują ograniczoną liczbę obiektów docelowych:

- Obsługiwane cele to SQL Managed Instance, SQL Database, Azure Synapse [i](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) dedykowane pule SQL oraz SQL Server wystąpień. 
- Rozproszone transakcje zapisywalne są możliwe tylko między wystąpieniami zarządzanymi. Aby uzyskać więcej informacji, zobacz [Transakcje rozproszone](../database/elastic-transactions-overview.md). Jednak MS DTC nie są obsługiwane.
- Obiekty docelowe, które nie są obsługiwane, to pliki, Analysis Services i inne RDBMS. Spróbuj użyć natywnego importu plików CSV z Azure Blob Storage lub jako alternatywy dla importowania plików lub załaduj pliki przy użyciu bez serwera puli SQL w Azure Synapse Analytics `BULK INSERT` `OPENROWSET` . [](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)

Operacje: 

- [Transakcje zapisu między](../database/elastic-transactions-overview.md) wystąpieniami są obsługiwane tylko w przypadku wystąpień zarządzanych.
- `sp_dropserver` Program jest obsługiwany w przypadku porzucania połączonego serwera. Zobacz [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- Funkcja może służyć do wykonywania zapytań tylko w `OPENROWSET` SQL Server wystąpieniach. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Zobacz [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- Funkcja może służyć do wykonywania zapytań tylko w `OPENDATASOURCE` SQL Server wystąpieniach. Mogą być zarządzane, lokalne lub na maszynach wirtualnych. Tylko `SQLNCLI` wartości `SQLNCLI11` , i są obsługiwane `SQLOLEDB` jako dostawca. Może to być na przykład `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Zobacz [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Serwery połączone nie mogą być używane do odczytywania plików (Excel, CSV) z udziałów sieciowych. Spróbuj użyć [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OPENROWSET,](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) który odczytuje pliki CSV z usługi Azure Blob Storage, lub serwera połączonego, który odwołuje się do bez serwera puli SQL w [programie Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Śledzenie tych żądań w [SQL Managed Instance opinii](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Jedynym dostępnym typem źródła zewnętrznego jest RDBMS (w publicznej wersji zapoznawczej) do Azure SQL, Azure SQL zarządzanego i Azure Synapse puli. Możesz użyć tabeli [zewnętrznej,](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) która odwołuje się do bez serwera puli SQL w usłudze Synapse Analytics jako obejścia dla tabel zewnętrznych programu Polybase, które bezpośrednio odczytuje dane z usługi Azure Storage. W Azure SQL zarządzanego można używać serwerów połączonych do bez serwera puli SQL w usłudze [Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) lub SQL Server do odczytu danych usługi Azure Storage.
Aby uzyskać informacje na temat programu PolyBase, zobacz [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replikacja

- Obsługiwane są typy replikacji migawki i dwukierunkowej. Replikacja scalania, replikacja równorzędna i subskrypcje, które można ujednolić, nie są obsługiwane.
- [Replikacja transakcyjna jest](replication-transactional-overview.md) dostępna w publicznej wersji zapoznawczej SQL Managed Instance z pewnymi ograniczeniami:
    - Wszystkie typy uczestników replikacji (wydawca, dystrybutor, subskrybent ściągania i subskrybent wypychania) mogą być umieszczane w usłudze SQL Managed Instance, ale wydawca i dystrybutor muszą znajdować się zarówno w chmurze, jak i w środowisku lokalnym.
    - SQL Managed Instance może komunikować się z najnowszymi wersjami SQL Server. Zobacz macierz [obsługiwanych wersji,](replication-transactional-overview.md#supportability-matrix) aby uzyskać więcej informacji.
    - Replikacja transakcyjna ma pewne [dodatkowe wymagania dotyczące sieci.](replication-transactional-overview.md#requirements)

Aby uzyskać więcej informacji na temat konfigurowania replikacji transakcyjnej, zobacz następujące samouczki:
- [Replikacja między wydawcą programu SQL MI i subskrybentem programu SQL MI](replication-between-two-instances-configure-tutorial.md)
- [Replikacja między wydawcą programu SQL MI, dystrybutorem programu SQL MI i SQL Server subskrybentem](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE, instrukcja 

- Obsługiwana składnia:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Nieobsługiwana składnia:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Źródło: 
  - `FROM URL` (Azure Blob Storage) jest jedyną obsługiwaną opcją.
  - `FROM DISK`/`TAPE`Urządzenie /backup nie jest obsługiwane.
  - Zestawy kopii zapasowych nie są obsługiwane.
- `WITH` opcje nie są obsługiwane. Próby przywrócenia, takie `WITH` jak `DIFFERENTIAL` , , `STATS` `REPLACE` itp., nie powiodą się.
- `ASYNC RESTORE`: przywracanie będzie kontynuowane nawet wtedy, gdy połączenie klienta zostanie przerywane. Jeśli połączenie zostanie przerwane, możesz sprawdzić w widoku stan operacji przywracania oraz bazę danych `sys.dm_operation_status` CREATE i DROP. Zobacz [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

Następujące opcje bazy danych są ustawione lub zastąpione i nie można ich później zmienić: 

- `NEW_BROKER` jeśli broker nie jest włączony w pliku bak. 
- `ENABLE_BROKER` jeśli broker nie jest włączony w pliku bak. 
- `AUTO_CLOSE=OFF` Jeśli baza danych w pliku bak `AUTO_CLOSE=ON` ma . 
- `RECOVERY FULL` jeśli baza danych w pliku bak ma `SIMPLE` tryb odzyskiwania `BULK_LOGGED` lub .
- Zostanie dodana zoptymalizowana pod kątem pamięci grupa plików o nazwie XTP, jeśli nie znajduje się ona w źródłowym pliku bak. 
- Nazwa istniejącej grupy plików zoptymalizowanej pod kątem pamięci jest zmieniana na XTP. 
- `SINGLE_USER` Opcje `RESTRICTED_USER` i są konwertowane na `MULTI_USER` .

Ograniczenia: 

- Kopie zapasowe uszkodzonych baz danych mogą zostać przywrócone w zależności od typu uszkodzenia, ale automatyczne kopie zapasowe nie zostaną wykonane, dopóki uszkodzenie nie zostanie naprawione. Upewnij się, że uruchamiasz plik na SQL Managed Instance i użyj kopii zapasowej, `DBCC CHECKDB` `WITH CHECKSUM` aby zapobiec temu problemowi.
- Nie można przywrócić przywracania pliku bazy danych zawierającego ograniczenia opisane w tym dokumencie (na przykład obiektów lub `.BAK` `FILESTREAM` ) `FILETABLE` SQL Managed Instance.
- `.BAK` Nie można przywrócić plików zawierających wiele zestawów kopii zapasowych. 
- `.BAK` Nie można przywrócić plików zawierających wiele plików dziennika.
- Nie można przywrócić kopii zapasowych zawierających bazy danych większe niż 8 TB, aktywne obiekty OLTP w pamięci lub liczbę plików, które przekraczałyby 280 plików na wystąpienie, Ogólnego przeznaczenia wystąpienia. 
- Nie można przywrócić kopii zapasowych zawierających bazy danych większe niż 4 TB lub [](resource-limits.md) obiekty OLTP w pamięci o łącznym rozmiarze większym niż rozmiar opisany w te Krytyczne dla działania firmy zasobów.
Aby uzyskać informacje o instrukcjach przywracania, [zobacz instrukcje RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > Te same ograniczenia dotyczą wbudowanej operacji przywracania do punktu w czasie. Na przykład nie można Ogólnego przeznaczenia bazy danych o pojemności większej niż 4 TB w Krytyczne dla działania firmy wystąpienia. Krytyczne dla działania firmy danych z plikami OLTP w pamięci lub więcej niż 280 plików nie można przywrócić w Ogólnego przeznaczenia wystąpienia.

### <a name="service-broker"></a>Service Broker

Wymiana komunikatów brokera usług między wystąpieniami jest obsługiwana tylko między Azure SQL Managed Instances:

- `CREATE ROUTE`: nie można używać z `CREATE ROUTE` inną niż lub nazwą DNS innej `ADDRESS` `LOCAL` SQL Managed Instance.
- `ALTER ROUTE`: nie można używać z `ALTER ROUTE` inną niż lub nazwą DNS innej `ADDRESS` `LOCAL` SQL Managed Instance.

Zabezpieczenia transportu są obsługiwane, zabezpieczenia okien dialogowych nie są:
- `CREATE REMOTE SERVICE BINDING`nie są obsługiwane.

Usługa Service Broker jest domyślnie włączona i nie można jej wyłączyć. Następujące opcje ALTER DATABSE nie są obsługiwane:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Procedury składowane, funkcje i wyzwalacze

- `NATIVE_COMPILATION` Nie jest obsługiwana w Ogólnego przeznaczenia warstwie.
- Następujące [opcje sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) nie są obsługiwane: 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- `sp_execute_external_scripts` nie jest obsługiwana. Zobacz [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- `xp_cmdshell` nie jest obsługiwana. Zobacz [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` nie są obsługiwane, a obejmuje to `sp_addextendedproc` elementy i `sp_dropextendedproc` . Ta funkcja nie będzie obsługiwana, ponieważ jest w ścieżce cofania SQL Server. Aby uzyskać więcej informacji, zobacz Extended Stored Procedures ( [Rozszerzone procedury składowane).](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming)
- `sp_attach_db`, `sp_attach_single_file_db` i nie są `sp_detach_db` obsługiwane. Zobacz [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql)i [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funkcje systemowe i zmienne

Następujące zmienne, funkcje i widoki zwracają różne wyniki:

- `SERVERPROPERTY('EngineEdition')` Zwraca wartość 8. Ta właściwość jednoznacznie identyfikuje SQL Managed Instance. Zobacz [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` zwraca wartość NULL, ponieważ pojęcie wystąpienia, które istnieje dla SQL Server, nie ma zastosowania do SQL Managed Instance. Zobacz [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` Zwraca pełną nazwę "podłączaną" DNS, na przykład my-managed-instance.wcus17662feb9ce98.database.windows.net. Zobacz [ @SERVERNAME @](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` Zwraca pełną nazwę "podłączaną" DNS, na przykład dla właściwości `myinstance.domain.database.windows.net` "name" i "data_source". Zobacz [SYS. SERWERY](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` zwraca wartość NULL, ponieważ pojęcie usługi, które istnieje dla SQL Server, nie ma zastosowania do SQL Managed Instance. Zobacz [ @SERVICENAME @](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` jest obsługiwana. Zwraca wartość NULL, jeśli identyfikator logowania usługi Azure AD nie znajduje się w sys.syslogowania. Zobacz [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- `SUSER_SID` nie jest obsługiwana. Zwracane są nieprawidłowe dane, co jest tymczasowym znanym problemem. Zobacz [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Ograniczenia środowiska

### <a name="subnet"></a>Podsieć
-  Nie można umieścić żadnych innych zasobów (na przykład maszyn wirtualnych) w podsieci, w której wdrożono SQL Managed Instance. Wd wdrażaj te zasoby przy użyciu innej podsieci.
- Podsieć musi mieć wystarczającą liczbę [dostępnych adresów IP.](connectivity-architecture-overview.md#network-requirements) Minimalna wartość to co najmniej 32 adresy IP w podsieci.
- Liczba rdzeni wirtualnych i typy wystąpień, które można wdrożyć w regionie, mają [pewne ograniczenia i ograniczenia.](resource-limits.md#regional-resource-limitations)
- W podsieci [należy zastosować konfigurację](connectivity-architecture-overview.md#network-requirements) sieci.

### <a name="vnet"></a>Sieć wirtualna
- Sieć wirtualną można wdrożyć przy użyciu modelu zasobów — klasyczny model sieci wirtualnej nie jest obsługiwany.
- Po utworzeniu SQL Managed Instance nie jest obsługiwane przenoszenie sieci SQL Managed Instance wirtualnej do innej grupy zasobów lub subskrypcji.
- W przypadku wystąpień zarządzanych SQL hostowanych w klastrach wirtualnych utworzonych przed [](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 22.09.2020 r. globalna komunikacja równorzędna nie jest obsługiwana. Możesz połączyć się z tymi zasobami za pośrednictwem usługi ExpressRoute lub połączenia sieć wirtualna-sieć wirtualna za pośrednictwem bram sieci wirtualnej.

### <a name="failover-groups"></a>Grupy trybu failover
Systemowe bazy danych nie są replikowane do wystąpienia pomocniczego w grupie trybu failover. W związku z tym scenariusze zależne od obiektów z systemowych baz danych będą niemożliwe w wystąpieniu pomocniczym, chyba że obiekty zostaną utworzone ręcznie w pomocniczym wystąpieniu.

### <a name="tempdb"></a>Tempdb
- Maksymalny rozmiar pliku nie może być większy niż `tempdb` 24 GB na rdzeń w Ogólnego przeznaczenia warstwie. Maksymalny rozmiar `tempdb` w warstwie Krytyczne dla działania firmy jest ograniczony przez rozmiar SQL Managed Instance magazynu. `Tempdb` rozmiar pliku dziennika jest ograniczony do 120 GB Ogólnego przeznaczenia warstwie. Niektóre zapytania mogą zwracać błąd, jeśli potrzebują więcej niż 24 GB na rdzeń w programie lub jeśli dają `tempdb` więcej niż 120 GB danych dziennika.
- `Tempdb` Jest zawsze podzielony na 12 plików danych: 1 podstawowy, nazywany również plikiem głównym, plikiem danych i 11 plikami danych innych niż podstawowe. Nie można zmienić struktury plików i nie można dodać nowych plików do pliku `tempdb` . 
- [Metadane zoptymalizowane pod `tempdb` kątem pamięci](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata), nowa funkcja bazy SQL Server 2019 w pamięci, nie jest obsługiwana.
- Obiektów utworzonych w bazie danych modelu nie można automatycznie utworzyć w programie po ponownym uruchomieniu ani w trybie failover, ponieważ nie można pobrać początkowej listy obiektów `tempdb` `tempdb` z bazy danych modelu. Obiekty należy tworzyć ręcznie po każdym ponownym uruchomieniu lub pracy `tempdb` w trybu failover.

### <a name="msdb"></a>Msdb

Następujące schematy MSDB w SQL Managed Instance muszą być własnością odpowiednich wstępnie zdefiniowanych ról:

- Role ogólne
  - TargetServersRole
- [Stałe role bazy danych](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail roles (Role poczty bazy danych):](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile)
  - Databasemailuserrole
- [Role usług integracji:](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true)
  - Db_ssisadmin
  - Db_ssisltduser
  - Db_ssisoperator
  
> [!IMPORTANT]
> Zmiana wstępnie zdefiniowanych nazw ról, nazw schematów i właścicieli schematu przez klientów będzie mieć wpływ na normalne działanie usługi. Wszelkie wprowadzone zmiany zostaną przywrócone do wstępnie zdefiniowanych wartości natychmiast po wykryciu lub przy następnej aktualizacji usługi, aby zapewnić normalne działanie usługi.

### <a name="error-logs"></a>Dzienniki błędów

SQL Managed Instance umieszcza pełne informacje w dziennikach błędów. Istnieje wiele zdarzeń systemu wewnętrznego, które są rejestrowane w dzienniku błędów. Użyj procedury niestandardowej, aby odczytać dzienniki błędów, które odfiltrowuje niektóre nieistotne wpisy. Aby uzyskać więcej informacji, [zobacz SQL Managed Instance — sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) lub SQL Managed Instance [(wersja zapoznawcza)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) dla Azure Data Studio.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na SQL Managed Instance, zobacz [Co to jest SQL Managed Instance?](sql-managed-instance-paas-overview.md)
- Aby uzyskać listę funkcji i porównań, zobacz [Azure SQL Managed Instance porównanie funkcji.](../database/features-comparison.md)
- Aby uzyskać informacje o aktualizacjach wersji i stanie znanych problemów, [SQL Managed Instance informacje o wersji](../database/doc-changes-updates-release-notes.md)
- Aby uzyskać przewodnik Szybki start, który pokazuje, jak utworzyć nową SQL Managed Instance, zobacz [Tworzenie SQL Managed Instance](instance-create-quickstart.md).
