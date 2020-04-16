---
title: Ładowanie danych samouczka z usługi Azure Data Lake Storage
description: Użyj tabel zewnętrznych PolyBase, aby załadować dane z usługi Azure Data Lake Storage dla synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/08/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 9713d73ee132f743ceea98cbaca6a83f36fd3a45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416113"
---
# <a name="load-data-from-azure-data-lake-storage-for-sql-analytics"></a>Ładowanie danych z usługi Azure Data Lake Storage for SQL Analytics

W tym przewodniku opisano, jak używać tabel zewnętrznych PolyBase do ładowania danych z usługi Azure Data Lake Storage. Mimo że można uruchamiać kwerendy adhoc na danych przechowywanych w magazynie usługi Data Lake, zaleca się importowanie danych w celu uzyskania najlepszej wydajności.

> [!NOTE]  
> Alternatywą dla ładowania jest [instrukcja COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) aktualnie w publicznej wersji zapoznawczej.  Instrukcja COPY zapewnia największą elastyczność. Aby przekazać opinię na temat instrukcji COPY, wyślij sqldwcopypreview@service.microsoft.comwiadomość e-mail na następującą listę dystrybucyjną: .
>
> [!div class="checklist"]
>
> * Tworzenie obiektów bazy danych wymaganych do załadowania z magazynu usługi Data Lake.
> * Połącz się z katalogiem magazynu usługi Data Lake.
> * Załaduj dane do magazynu danych.

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Zanim rozpoczniesz ten samouczek, pobierz i zainstaluj najnowszą wersję programu [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

Aby uruchomić ten samouczek, potrzebujesz:

* Pula SQL. Zobacz [Tworzenie puli SQL i danych kwerend](create-data-warehouse-portal.md).
* Konto magazynu usługi Data Lake. Zobacz [Wprowadzenie do usługi Azure Data Lake Storage](../../data-lake-store/data-lake-store-get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Dla tego konta magazynu należy skonfigurować lub określić jeden z następujących poświadczeń do załadowania: klucz konta magazynu, użytkownik aplikacji usługi Azure Directory lub użytkownika usługi AAD, który ma odpowiednią rolę RBAC do konta magazynu.

## <a name="create-a-credential"></a>Tworzenie poświadczenia

Możesz pominąć tę sekcję i przejść do "Utwórz zewnętrzne źródło danych" podczas uwierzytelniania przy użyciu przekazywania usługi AAD. Poświadczenia o zakresie bazy danych nie jest wymagane do utworzenia lub określone podczas korzystania z usługi AAD przekazywania, ale upewnij się, że użytkownik usługi AAD ma odpowiednią rolę RBAC (czytnik danych obiektów blob magazynu, współautor lub rola właściciela) do konta magazynu. Więcej szczegółów przedstawiono [tutaj](https://techcommunity.microsoft.com/t5/Azure-SQL-Data-Warehouse/How-to-use-PolyBase-by-authenticating-via-AAD-pass-through/ba-p/862260).

Aby uzyskać dostęp do konta Data Lake Storage, musisz utworzyć klucz główny bazy danych, aby zaszyfrować klucz tajny poświadczeń. Następnie należy utworzyć poświadczenia o określonym zakresie bazy danych do przechowywania klucza tajnego. Podczas uwierzytelniania przy użyciu podmiotów usługi (użytkownik aplikacji usługi Azure Directory), poświadczenia o zakresie bazy danych przechowuje poświadczenia jednostki usługi skonfigurowane w Usłudze AAD. Można również użyć poświadczenia o zakresie bazy danych do przechowywania klucza konta magazynu dla Gen2.

Aby połączyć się z magazynem usługi Usługi przy użyciu podmiotów usługi, należy **najpierw** utworzyć aplikację usługi Azure Active Directory, utworzyć klucz dostępu i udzielić aplikacji dostępu do konta usługi Data Lake Storage. Aby uzyskać instrukcje, zobacz [Uwierzytelnij w usłudze Azure Data Lake Storage przy użyciu usługi Active Directory](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Zaloguj się do puli SQL z użytkownikiem posiadającym uprawnienia na poziomie CONTROL i wykonaj następujące instrukcje SQL dla bazy danych:

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE MASTER KEY;


-- B (for service principal authentication): Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    -- Always use the OAuth 2.0 authorization endpoint (v1)
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- B (for Gen2 storage key authentication): Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.

CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;

-- It should look something like this when authenticating using service principal:
CREATE DATABASE SCOPED CREDENTIAL ADLSCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```

## <a name="create-the-external-data-source"></a>Tworzenie zewnętrznego źródła danych

Użyj tego polecenia [UTWÓRZ ZEWNĘTRZNE ŹRÓDŁO DANYCH,](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ABY zapisać lokalizację danych. Jeśli uwierzytelniasz się za pomocą przekazywania AAD, parametr CREDENTIAL nie jest wymagany. Jeśli uwierzytelniasz się przy użyciu tożsamości zarządzanej dla punktów końcowych usługi, postępuj zgodnie z tą [dokumentacją,](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-sql-data-warehouse-polybase) aby skonfigurować zewnętrzne źródło danych.

```sql
-- C (for Gen1): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen1 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<datalakestoregen1accountname>.azuredatalakestore.net',
    CREDENTIAL = ADLSCredential
);

-- C (for Gen2): Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Storage.
-- LOCATION: Provide Data Lake Storage Gen2 account name and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStorage
WITH (
    TYPE = HADOOP,
    LOCATION='abfs[s]://<container>@<AzureDataLake account_name>.dfs.core.windows.net', -- Please note the abfss endpoint for when your account has secure transfer enabled
    CREDENTIAL = ADLSCredential
);
```

## <a name="configure-data-format"></a>Konfigurowanie formatu danych

Aby zaimportować dane z magazynu usługi Data Lake, należy określić format pliku zewnętrznego. Ten obiekt definiuje sposób zapisywania plików w magazynie usługi Data Lake.
Aby uzyskać pełną listę, zapoznaj się z naszą dokumentacją T-SQL [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store missing values as default for datatype.

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>Tworzenie tabel zewnętrznych

Po określeniu źródła danych i formatu pliku można przystąpić do tworzenia tabel zewnętrznych. Tabele zewnętrzne to sposób interakcji z danymi zewnętrznymi. Parametr lokalizacji może określać plik lub katalog. Jeśli określa katalog, wszystkie pliki w katalogu zostaną załadowane.

```sql
-- D: Create an External Table
-- LOCATION: Folder under the Data Lake Storage root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStorage
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>Zagadnienia dotyczące tabeli zewnętrznej

Tworzenie zewnętrznego stołu jest łatwe, ale istnieją pewne niuanse, które należy omówić.

Tabele zewnętrzne są silnie wpisywane. Oznacza to, że każdy wiersz pozyskiwania danych musi spełniać definicję schematu tabeli.
Jeśli wiersz nie jest zgodny z definicją schematu, wiersz zostanie odrzucony z obciążenia.

Opcje REJECT_TYPE i REJECT_VALUE umożliwiają zdefiniowanie liczby wierszy lub procentu danych w tabeli końcowej. Podczas ładowania, jeśli wartość odrzucenia zostanie osiągnięta, obciążenie nie powiedzie się. Najczęstszą przyczyną odrzuconych wierszy jest niezgodność definicji schematu. Na przykład jeśli kolumna jest niepoprawnie podane schemat int, gdy dane w pliku jest ciągiem, każdy wiersz nie można załadować.

Data Lake Storage Gen1 używa kontroli dostępu opartej na rolach (RBAC) do kontrolowania dostępu do danych. Oznacza to, że podmiot zabezpieczeń usługi musi mieć uprawnienia do odczytu do katalogów zdefiniowanych w parametrze lokalizacji i do podstawowych podstawowych katalogów i plików. Dzięki temu PolyBase do uwierzytelniania i ładowania tych danych.

## <a name="load-the-data"></a>Ładowanie danych

Aby załadować dane z magazynu usługi Data Lake, użyj instrukcji [CREATE TABLE AS SELECT (Transact-SQL).](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

CTAS tworzy nową tabelę i wypełnia ją wynikami instrukcji select. CTAS definiuje nową tabelę, aby mieć te same kolumny i typy danych, jak wyniki select instrukcji. Jeśli wybierzesz wszystkie kolumny z tabeli zewnętrznej, nowa tabela będzie repliką kolumn i typów danych w tabeli zewnętrznej.

W tym przykładzie tworzymy tabelę rozproszoną skrótu o nazwie DimProduct z naszej tabeli zewnętrznej DimProduct_external.

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```

## <a name="optimize-columnstore-compression"></a>Optymalizacja kompresji magazynu kolumn

Domyślnie tabele są definiowane jako indeks klastrowanego magazynu kolumn. Po zakończeniu ładowania niektóre wiersze danych mogą nie zostać skompresowane do magazynu kolumn.  Istnieje wiele powodów, dla których może się to zdarzyć. Aby dowiedzieć się więcej, zobacz [zarządzanie indeksami magazynu kolumn](sql-data-warehouse-tables-index.md).

Aby zoptymalizować wydajność kwerend i kompresji magazynu kolumn po obciążeniu, odbuduj tabelę, aby wymusić indeks magazynu kolumn do kompresji wszystkich wierszy.

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

## <a name="optimize-statistics"></a>Optymalizacja statystyk

Najlepiej jest utworzyć statystyki jednokolumnowe natychmiast po załadowaniu. Istnieją pewne możliwości wyboru statystyk. Na przykład jeśli utworzysz statystyki jednokolumnowe w każdej kolumnie może zająć dużo czasu, aby odbudować wszystkie statystyki. Jeśli wiesz, że niektóre kolumny nie będą w predykatach kwerendy, możesz pominąć tworzenie statystyk dla tych kolumn.

Jeśli zdecydujesz się utworzyć statystyki jednokolumnowe dla każdej kolumny każdej tabeli, możesz użyć przykładu `prc_sqldw_create_stats` kodu procedury składowanej w [artykule statystyk.](sql-data-warehouse-tables-statistics.md)

Poniższy przykład jest dobrym punktem wyjścia do tworzenia statystyk. Tworzy jednokolumnowe statystyki dla każdej kolumny w tabeli wymiarów i dla każdej kolumny łączącej w tabelach faktów. Zawsze możesz później dodać statystyki jedno- lub wielokolumnowe do innych kolumn tabeli faktów.

## <a name="achievement-unlocked"></a>Osiągnięcie odblokowane!

Pomyślnie załadowano dane do magazynu danych. Świetna robota!

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono tabele zewnętrzne w celu zdefiniowania struktury danych przechowywanych w udziale gen1 magazynu usługi Data Lake, a następnie użyto instrukcji PolyBase CREATE TABLE AS SELECT do załadowania danych do magazynu danych.

Zostały wykonane następujące zadania:
> [!div class="checklist"]
>
> * Utworzone obiekty bazy danych wymagane do załadowania z magazynu usługi Data Lake.
> * Połączony z katalogiem magazynu usługi Data Lake.
> * Załadowano dane do magazynu danych.
>

Ładowanie danych jest pierwszym krokiem do opracowania rozwiązania magazynu danych przy użyciu usługi Azure Synapse Analytics. Zapoznaj się z naszymi zasobami rozwojowymi.

> [!div class="nextstepaction"]
> [Dowiedz się, jak tworzyć tabele do magazynowania danych](sql-data-warehouse-tables-overview.md)
