---
title: Kontrolowanie dostępu do konta magazynu dla puli SQL bezserwerowej
description: Opisuje sposób, w jaki Pula SQL bezserwerowa uzyskuje dostęp do usługi Azure Storage oraz jak można kontrolować dostęp do magazynu dla puli SQL bezserwerowej w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 631aaf3c6a99e093f6ed59089f7ce99803f3f054
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446622"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Kontrolowanie dostępu do konta magazynu dla puli SQL bezserwerowej w usłudze Azure Synapse Analytics

Zapytanie puli SQL bezserwerowe odczytuje pliki bezpośrednio z usługi Azure Storage. Uprawnienia dostępu do plików w usłudze Azure Storage są kontrolowane na dwóch poziomach:
- **Poziom magazynu** — użytkownik powinien mieć uprawnienia dostępu do podstawowych plików magazynu. Administrator magazynu powinien zezwalać podmiotowi zabezpieczeń usługi Azure AD na odczyt/zapis plików lub generowanie klucza SAS, który będzie używany do uzyskiwania dostępu do magazynu.
- **Poziom usługi SQL** — użytkownik powinien mieć `SELECT` uprawnienia do odczytu danych z [tabeli zewnętrznej](develop-tables-external-tables.md) lub `ADMINISTER BULK ADMIN` uprawnienia do wykonywania `OPENROWSET` , a także uprawnienia do używania poświadczeń, które będą używane w celu uzyskania dostępu do magazynu.

W tym artykule opisano typy poświadczeń, których można użyć oraz sposób przeszukiwania poświadczeń dla użytkowników usług SQL i Azure AD.

## <a name="supported-storage-authorization-types"></a>Obsługiwane typy autoryzacji magazynu

Użytkownik zalogowany do bezserwerowej puli SQL musi mieć autoryzację, aby uzyskać dostęp do plików w usłudze Azure Storage i wysyłać do nich zapytania, jeśli pliki nie są publicznie dostępne. Możesz użyć trzech typów autoryzacji, aby uzyskać dostęp do [tożsamości użytkownika](?tabs=user-identity)niepublicznego, [sygnatury dostępu współdzielonego](?tabs=shared-access-signature)i [tożsamości zarządzanej](?tabs=managed-identity).

> [!NOTE]
> **Usługa Azure AD Pass-through** to domyślne zachowanie podczas tworzenia obszaru roboczego.

### <a name="user-identity"></a>[Tożsamość użytkownika](#tab/user-identity)

**Tożsamość użytkownika**, znana również jako "przekazywanie usługi Azure AD", jest typem autoryzacji, w którym tożsamość użytkownika usługi Azure AD, która została zarejestrowana w puli SQL bezserwerowej, jest używana do autoryzowania dostępu do danych. Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień użytkownikowi usługi Azure AD. Zgodnie z poniższą tabelą, nie jest obsługiwana w przypadku typu użytkownika SQL.

> [!IMPORTANT]
> Aby móc uzyskiwać dostęp do danych, musisz mieć rolę właściciel danych obiektów BLOB/współautor/czytelnik.
> Nawet jeśli jesteś właścicielem konta magazynu, nadal musisz dodać siebie do jednej z ról danych obiektów blob magazynu.
>
> Aby dowiedzieć się więcej na temat kontroli dostępu w Azure Data Lake Store Gen2, zapoznaj się z informacjami na temat [kontroli dostępu w Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) artykule.
>

### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

**Sygnatura dostępu współdzielonego (SAS)** zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu współdzielonego klient może udzielić klientom dostępu do zasobów na koncie magazynu bez udostępniania kluczy konta. Sygnatura dostępu współdzielonego zapewnia szczegółową kontrolę nad dostępem udzielonym klientom, którzy mają SYGNATURę czasową, w tym interwałem ważności, udzielonymi uprawnieniami, akceptowalnym zakresem adresów IP i dozwolonym protokołem (https/http).

Token SYGNATURy dostępu współdzielonego można uzyskać, przechodząc do **konta magazynu Azure Portal-> Storage-> sygnatura dostęp współdzielony-> skonfigurować uprawnienia-> generować sygnatury SAS i parametry połączenia.**

> [!IMPORTANT]
> Po wygenerowaniu tokenu SAS zawiera znak zapytania ("?") na początku tokenu. Aby użyć tokenu w puli SQL bezserwerowej, należy usunąć znak zapytania ("?") podczas tworzenia poświadczenia. Przykład:
>
> Token sygnatury dostępu współdzielonego:? SV = 2018 r-03-28&SS = bfqt&narzędzia SRT = SCO&Sp = rwdlacup&SE = 2019-04-18T20:42:12Z&St = 2019-04-18T12:42:12Z&spr = https&SIG = lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78% 3D

Aby włączyć dostęp przy użyciu tokenu SAS, należy utworzyć poświadczenia w zakresie bazy danych lub serwera 

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

**Tożsamość zarządzana** jest również znana jako plik msi. Jest to funkcja Azure Active Directory (Azure AD), która zapewnia usługi platformy Azure dla puli SQL bezserwerowej. Wdraża także automatycznie zarządzaną tożsamość w usłudze Azure AD. Ta tożsamość może służyć do autoryzowania żądania dostępu do danych w usłudze Azure Storage.

Przed uzyskaniem dostępu do danych administrator usługi Azure Storage musi udzielić uprawnień do zarządzanej tożsamości na potrzeby uzyskiwania dostępu do danych. Przyznawanie uprawnień do tożsamości zarządzanej odbywa się tak samo jak udzielanie uprawnień innym użytkownikom usługi Azure AD.

### <a name="anonymous-access"></a>[Dostęp anonimowy](#tab/public-access)

Możesz uzyskać dostęp do publicznie dostępnych plików umieszczonych na kontach usługi Azure Storage, które [zezwalają na dostęp anonimowy](/azure/storage/blobs/storage-manage-access-to-resources).

---

### <a name="supported-authorization-types-for-databases-users"></a>Obsługiwane typy autoryzacji dla użytkowników baz danych

W poniższej tabeli znajdują się dostępne typy autoryzacji:

| Typ autoryzacji                    | *Użytkownik SQL*    | *Użytkownik usługi Azure AD*     |
| ------------------------------------- | ------------- | -----------    |
| [Tożsamość użytkownika](?tabs=user-identity#supported-storage-authorization-types)       | Nieobsługiwane | Obsługiwane      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Obsługiwane     | Obsługiwane      |
| [Tożsamość zarządzana](?tabs=managed-identity#supported-storage-authorization-types) | Nieobsługiwane | Obsługiwane      |

### <a name="supported-storages-and-authorization-types"></a>Obsługiwane magazyny i typy autoryzacji

Można użyć następujących kombinacji typów autoryzacji i usługi Azure Storage:

| Typ autoryzacji  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | Obsługiwane\*      | Nieobsługiwane   | Obsługiwane\*     |
| [Tożsamość zarządzana](?tabs=managed-identity#supported-storage-authorization-types) | Obsługiwane      | Obsługiwane        | Obsługiwane     |
| [Tożsamość użytkownika](?tabs=user-identity#supported-storage-authorization-types)    | Obsługiwane\*      | Obsługiwane\*        | Obsługiwane\*     |

\* Token sygnatury dostępu współdzielonego i tożsamość usługi Azure AD mogą być używane do uzyskiwania dostępu do magazynu, który nie jest chroniony za pomocą zapory.

> [!IMPORTANT]
> Podczas uzyskiwania dostępu do magazynu chronionego za pomocą zapory można używać tylko tożsamości zarządzanej. Musisz [zezwolić na zaufane usługi firmy Microsoft... ustawienie](../../storage/common/storage-network-security.md#trusted-microsoft-services) i jawne [przypisanie roli platformy Azure](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights) do [zarządzanej tożsamości przypisanej do systemu](../../active-directory/managed-identities-azure-resources/overview.md) dla tego wystąpienia zasobu. W takim przypadku zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do zarządzanej tożsamości.
>

## <a name="credentials"></a>Poświadczenia

Aby wysłać zapytanie do pliku znajdującego się w usłudze Azure Storage, punkt końcowy puli SQL bezserwerowej musi mieć poświadczenia zawierające informacje o uwierzytelnianiu. Używane są dwa typy poświadczeń:
- POŚWIADCZENIA na poziomie serwera są używane dla zapytań ad hoc wykonywanych za pomocą `OPENROWSET` funkcji. Nazwa poświadczenia musi być zgodna z adresem URL magazynu.
- Poświadczenie o zakresie bazy danych jest używane w przypadku tabel zewnętrznych. Tabela zewnętrzna zawiera odwołania `DATA SOURCE` do poświadczeń, które powinny być używane do uzyskiwania dostępu do magazynu.

Aby zezwolić użytkownikowi na tworzenie lub porzucanie poświadczeń, administrator może udzielić/odmówić zmiany uprawnień poświadczeń użytkownikowi:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Użytkownicy baz danych, którzy uzyskują dostęp do magazynu zewnętrznego, muszą mieć uprawnienia do korzystania z poświadczeń.

### <a name="grant-permissions-to-use-credential"></a>Przyznawanie uprawnień do korzystania z poświadczeń

Aby można było korzystać z poświadczeń, użytkownik musi mieć `REFERENCES` uprawnienia do określonego poświadczenia. Aby udzielić `REFERENCES` uprawnienia storage_credential dla specific_user, wykonaj następujące polecenie:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Aby zapewnić bezproblemowe środowisko przekazywania usługi Azure AD, wszyscy użytkownicy będą domyślnie mieli prawo do korzystania z `UserIdentity` poświadczeń.

## <a name="server-scoped-credential"></a>Poświadczenie o zakresie serwera

Poświadczenia z zakresem serwera są używane, gdy logowanie SQL wywołuje `OPENROWSET` funkcję bez `DATA_SOURCE` odczytywania plików na niektórych kontach magazynu. Nazwa poświadczenia z zakresem serwera **musi** być zgodna z adresem URL usługi Azure Storage. Poświadczenie jest dodawane przez uruchomienie [Create Credential](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Musisz podać argument nazwy poświadczenia. Musi być zgodna z każdą częścią ścieżki lub pełną ścieżką do danych w magazynie (patrz poniżej).

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER`Argument nie jest obsługiwany.

Nazwa poświadczeń na poziomie serwera musi być zgodna z pełną ścieżką do konta magazynu (i opcjonalnie kontenera) w następującym formacie: `<prefix>://<storage_account_path>/<storage_path>` . Ścieżki kont magazynu są opisane w poniższej tabeli:

| Zewnętrzne źródło danych       | Prefiks | Ścieżka konta magazynu                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>. blob.core.windows.net             |
| Usługa Azure Data Lake Storage 1. generacji | https  | <storage_account>. azuredatalakestore.net/webhdfs/v1 |
| Usługa Azure Data Lake Storage 2. generacji | https  | <storage_account>. dfs.core.windows.net              |

Poświadczenia o zakresie serwera umożliwiają dostęp do usługi Azure Storage przy użyciu następujących typów uwierzytelniania:

### <a name="user-identity"></a>[Tożsamość użytkownika](#tab/user-identity)

Użytkownicy usługi Azure AD mogą uzyskać dostęp do dowolnego pliku w usłudze Azure Storage, jeśli ma `Storage Blob Data Owner` `Storage Blob Data Contributor` rolę, lub `Storage Blob Data Reader` . Użytkownicy usługi Azure AD nie potrzebują poświadczeń w celu uzyskania dostępu do magazynu. 

Użytkownicy SQL nie mogą uzyskać dostępu do magazynu przy użyciu uwierzytelniania usługi Azure AD.

### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

Poniższy skrypt tworzy poświadczenia na poziomie serwera, które mogą być używane przez `OPENROWSET` funkcję do uzyskiwania dostępu do dowolnych plików w usłudze Azure Storage przy użyciu tokenu SAS. Utwórz to poświadczenie, aby włączyć podmiot zabezpieczeń SQL, który wykonuje `OPENROWSET` funkcję do odczytu plików chronionych za pomocą klucza SAS w usłudze Azure Storage, które pasują do adresu URL w nazwie poświadczenia.

Program Exchange <*mystorageaccountname*> z rzeczywistą nazwą konta magazynu i <*mystorageaccountcontainername*> z rzeczywistą nazwą kontenera:

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

Poniższy skrypt tworzy poświadczenia na poziomie serwera, które mogą być używane przez `OPENROWSET` funkcję do uzyskiwania dostępu do dowolnych plików w usłudze Azure Storage przy użyciu tożsamości zarządzanej przez obszar roboczy.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Dostęp publiczny](#tab/public-access)

Poświadczenia w zakresie bazy danych nie są wymagane, aby zezwolić na dostęp do publicznie dostępnych plików. Utwórz [Źródło danych bez poświadczeń w zakresie bazy danych](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) , aby uzyskać dostęp do publicznie dostępnych plików w usłudze Azure Storage.

---

## <a name="database-scoped-credential"></a>Poświadczenia w zakresie bazy danych

Poświadczenia w zakresie bazy danych są używane, gdy każda `OPENROWSET` funkcja wywołuje `DATA_SOURCE` lub wybiera dane z [tabeli zewnętrznej](develop-tables-external-tables.md) , która nie uzyskuje dostępu do plików publicznych. Poświadczenia w zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu. Zostanie on jawnie użyty w źródle danych, który definiuje lokalizację magazynu.

Poświadczenia w zakresie bazy danych umożliwiają dostęp do usługi Azure Storage przy użyciu następujących typów uwierzytelniania:

### <a name="azure-ad-identity"></a>[Tożsamość usługi Azure AD](#tab/user-identity)

Użytkownicy usługi Azure AD mogą uzyskać dostęp do dowolnego pliku w usłudze Azure Storage, jeśli ma ona co najmniej jedną `Storage Blob Data Owner` `Storage Blob Data Contributor` `Storage Blob Data Reader` rolę. Użytkownicy usługi Azure AD nie potrzebują poświadczeń w celu uzyskania dostępu do magazynu.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

Użytkownicy SQL nie mogą uzyskać dostępu do magazynu przy użyciu uwierzytelniania usługi Azure AD.

### <a name="shared-access-signature"></a>[Sygnatura dostępu współdzielonego](#tab/shared-access-signature)

Poniższy skrypt tworzy poświadczenie, które jest używane do uzyskiwania dostępu do plików w magazynie przy użyciu tokenu SAS określonego w poświadczeniu. Skrypt utworzy przykładowe zewnętrzne źródło danych, które używa tego tokenu SAS do uzyskiwania dostępu do magazynu.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Tożsamość zarządzana](#tab/managed-identity)

Poniższy skrypt tworzy poświadczenia w zakresie bazy danych, które mogą służyć do personifikacji bieżącego użytkownika usługi Azure AD jako tożsamości zarządzanej usługi. Skrypt utworzy przykładowe zewnętrzne źródło danych korzystające z tożsamości obszaru roboczego w celu uzyskania dostępu do magazynu.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

Poświadczenia w zakresie bazy danych nie muszą być zgodne z nazwą konta magazynu, ponieważ zostaną jawnie użyte w źródle danych, które definiuje lokalizację magazynu.

### <a name="public-access"></a>[Dostęp publiczny](#tab/public-access)

Poświadczenia w zakresie bazy danych nie są wymagane, aby zezwolić na dostęp do publicznie dostępnych plików. Utwórz [Źródło danych bez poświadczeń w zakresie bazy danych](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) , aby uzyskać dostęp do publicznie dostępnych plików w usłudze Azure Storage.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

Poświadczenia w zakresie bazy danych są używane w zewnętrznych źródłach danych w celu określenia metody uwierzytelniania, która będzie używana w celu uzyskania dostępu do tego magazynu:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Przykłady

### <a name="access-a-publicly-available-data-source"></a>**Dostęp do publicznie dostępnego źródła danych**

Użyj poniższego skryptu, aby utworzyć tabelę, która uzyskuje dostęp do publicznie dostępnego źródła danych.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

Użytkownik bazy danych może odczytać zawartość plików ze źródła danych przy użyciu tabeli zewnętrznej lub funkcji [OPENROWSET](develop-openrowset.md) , która odwołuje się do źródła danych:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**Uzyskiwanie dostępu do źródła danych przy użyciu poświadczeń**

Zmodyfikuj Poniższy skrypt, aby utworzyć zewnętrzną tabelę, która uzyskuje dostęp do usługi Azure Storage przy użyciu tokenu SAS, tożsamości usługi Azure AD dla użytkownika lub zarządzanej tożsamości obszaru roboczego.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

Użytkownik bazy danych może odczytać zawartość plików ze źródła danych przy użyciu [tabeli zewnętrznej](develop-tables-external-tables.md) lub funkcji [OPENROWSET](develop-openrowset.md)  , która odwołuje się do źródła danych:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>Następne kroki

Artykuły wymienione poniżej zawierają informacje o sposobie wykonywania zapytań dotyczących różnych typów folderów, typów plików i tworzenia i używania widoków:

- [Kwerenda pojedynczego pliku CSV](query-single-csv-file.md)
- [Foldery zapytań i wiele plików CSV](query-folders-multiple-csv-files.md)
- [Kwerenda określonych plików](query-specific-files.md)
- [Wykonywanie zapytań względem plików Parquet](query-parquet-files.md)
- [Tworzenie widoków i korzystanie z nich](create-use-views.md)
- [Wykonywanie zapytań względem plików JSON](query-json-files.md)
- [Wykonywanie zapytań względem typów zagnieżdżonych Parquet](query-parquet-nested-types.md)
