---
title: Kopiowanie i przekształcanie danych w Azure SQL Database
description: Dowiedz się, jak kopiować dane do i z Azure SQL Database oraz przekształcać dane w Azure SQL Database przy użyciu Azure Data Factory.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365233"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w Azure SQL Database przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję Azure Data Factory, z których korzystasz:"]
>
> - [Wersja 1](v1/data-factory-azure-sql-connector.md)
> - [Bieżąca wersja](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w programie Azure Data Factory do kopiowania danych z i do Azure SQL Database oraz używania Przepływ danych do przekształcania danych w Azure SQL Database. Aby dowiedzieć się więcej Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten Azure SQL Database jest obsługiwany w następujących działaniach:

- [działanie Kopiuj](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródła/ujścia](copy-activity-overview.md)
- [Przepływ danych mapowania](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

Na działanie Kopiuj łącznik Azure SQL Database obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji przy użyciu jednostki usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Jako źródło pobieranie danych przy użyciu zapytania SQL lub procedury składowanej. Możesz również wybrać kopiowanie równoległe ze źródła Azure SQL Database, zobacz [sekcję Kopiowanie](#parallel-copy-from-sql-database) równoległe z bazy danych SQL, aby uzyskać szczegółowe informacje.
- Jako ujścia, automatycznie tworząc tabelę docelową, jeśli nie istnieje, na podstawie schematu źródłowego; dołączanie danych do tabeli lub wywołania procedury składowanej przy użyciu logiki niestandardowej podczas kopiowania.

Jeśli używasz warstwy Azure SQL Database bez [serwera,](../azure-sql/database/serverless-tier-overview.md)pamiętaj, że gdy serwer jest wstrzymany, uruchomienie działania nie powiedzie się, zamiast czekać, aż automatyczne wznowienie będzie gotowe. Możesz dodać ponawianie działań lub utworzyć łańcuch dodatkowych działań, aby upewnić się, że serwer jest na żywo po rzeczywistym wykonaniu.

>[!NOTE]
> Azure SQL Database [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) nie jest teraz obsługiwany przez ten łącznik. Do pracy można użyć ogólnego łącznika [ODBC](connector-odbc.md) i sterownika SQL SERVER ODBC za pośrednictwem własnego środowiska Integration Runtime. Dowiedz się więcej z [sekcji Using Always Encrypted (Używanie](#using-always-encrypted) Always Encrypted). 

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu środowiska Azure Integration Runtime skonfiguruj [regułę](../azure-sql/database/firewall-configure.md) zapory na poziomie serwera, aby usługi platformy Azure mogą uzyskać dostęp do serwera.
> W przypadku kopiowania danych przy użyciu własnego środowiska Integration Runtime skonfiguruj zaporę tak, aby zezwalała na odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP maszyny używany do nawiązywania połączenia z Azure SQL Database.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Azure Data Factory jednostki specyficzne dla Azure SQL Database łącznika.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Te właściwości są obsługiwane w przypadku Azure SQL Database połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **type** musi być ustawiona na **wartość AzureSqlDatabase.** | Tak |
| Parametry połączenia | Określ informacje wymagane do nawiązania połączenia z Azure SQL Database dla **właściwości connectionString.** <br/>Możesz również umieścić hasło lub klucz jednostki usługi w Azure Key Vault. Jeśli jest to uwierzytelnianie SQL, `password` ściągnij konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz przykładowy kod JSON po tabeli i [Store credentials in Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, jeśli używasz uwierzytelniania usługi Azure AD z jednostką usługi |
| klucz servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać je w Azure Data Factory lub odwołać się do tajnego Azure Key Vault [.](store-credentials-in-key-vault.md) | Tak, jeśli używasz uwierzytelniania usługi Azure AD z jednostką usługi |
| Dzierżawy | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w których znajduje się aplikacja. Pobierz go, umieszczając wskaźnik myszy w prawym górnym rogu ekranu Azure Portal. | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| azureCloudType | W przypadku uwierzytelniania jednostki usługi określ typ środowiska chmury platformy Azure, w którym zarejestrowano aplikację usługi Azure AD. <br/> Dozwolone wartości to **AzurePublic,** **AzureChina,** **AzureUsGovernment** i **AzureGermany.** Domyślnie jest używane środowisko chmury fabryki danych. | Nie |
| connectVia | To [środowisko Integration Runtime](concepts-integration-runtime.md) jest używane do nawiązywania połączenia z magazynem danych. Jeśli magazyn danych znajduje się w sieci prywatnej, możesz użyć środowiska Azure Integration Runtime lub własnego środowiska Integration Runtime. Jeśli nie zostanie określony, zostanie użyte domyślne środowisko Azure Integration Runtime. | Nie |

W przypadku różnych typów uwierzytelniania zapoznaj się z następującymi sekcjami odpowiednio na temat wymagań wstępnych i przykładów JSON:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: jednostki usługi](#service-principal-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: tożsamości zarządzane dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli wystąpi błąd z kodem błędu "UserErrorFailedToConnectToSqlServer" i komunikatem, taki jak "Limit sesji dla bazy danych to XXX i został osiągnięty", dodaj ciąg do parametrów połączenia i spróbuj `Pooling=false` ponownie. `Pooling=false`Jest również zalecane w przypadku konfiguracji połączonej **usługi typu SHIR (self hosted Integration Runtime).** Pulę i inne parametry połączenia można dodać jako nowe nazwy parametrów i wartości w sekcji Dodatkowe **właściwości** połączenia formularza tworzenia połączonej usługi.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

**Przykład: korzystanie z uwierzytelniania SQL**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: hasło w Azure Key Vault**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": {
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                },
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi

Aby użyć uwierzytelniania tokenu aplikacji usługi Azure AD opartego na jednostki usługi, wykonaj następujące kroki:

1. [Utwórz aplikację Azure Active Directory z](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) Azure Portal. Zanotuj nazwę aplikacji i następujące wartości, które definiują połączona usługę:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. [Jeśli nie Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) jeszcze tego nie zrobiono, zaaprowizuj administratora usługi Azure Portal na serwerze. Administrator usługi Azure AD musi być użytkownikiem usługi Azure AD lub grupą usługi Azure AD, ale nie może być jednostką usługi. Ten krok jest wykonywane w taki sposób, aby w następnym kroku można było użyć tożsamości usługi Azure AD do utworzenia użytkownika zawartej bazy danych dla jednostki usługi.

3. [Tworzenie użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) dla jednostki usługi. Połącz się z bazą danych, z której lub do której chcesz skopiować dane, przy użyciu narzędzi takich jak SQL Server Management Studio z tożsamością usługi Azure AD, która ma co najmniej uprawnienie ALTER ANY USER. Uruchom następujące polecenia języka T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Przyznaj jednostki usługi wymagane uprawnienia, tak jak zwykle w przypadku użytkowników SQL lub innych. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Skonfiguruj Azure SQL Database połączonej usługi w Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi, która korzysta z uwierzytelniania jednostki usługi

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure

Fabrykę danych można skojarzyć z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md) która reprezentuje określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do Azure SQL Database uwierzytelniania. Wyznaczona fabryka może uzyskać dostęp do danych z bazy danych lub do bazy danych oraz kopiować je do nich przy użyciu tej tożsamości.

Aby użyć uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki.

1. [Jeśli nie Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) jeszcze tego nie zrobiono, zaaprowizuj administratora usługi Azure Portal na serwerze. Administratorem usługi Azure AD może być użytkownik usługi Azure AD lub grupa usługi Azure AD. Jeśli przyznasz grupie rolę administratora przy użyciu tożsamości zarządzanej, pomiń kroki 3 i 4. Administrator ma pełny dostęp do bazy danych.

2. [Tworzenie użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) dla Azure Data Factory tożsamości zarządzanej. Połącz się z bazą danych, z której lub do której chcesz skopiować dane, przy użyciu narzędzi takich jak SQL Server Management Studio z tożsamością usługi Azure AD, która ma co najmniej uprawnienie ALTER ANY USER. Uruchom następujące polecenia języka T-SQL:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Przyznaj Data Factory tożsamości zarządzanej, tak jak zwykle w przypadku użytkowników SQL i innych. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Skonfiguruj Azure SQL Database połączonej usługi w Azure Data Factory.

**Przykład**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](./concepts-datasets-linked-services.md).

Następujące właściwości są obsługiwane w przypadku Azure SQL Database danych:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **type** zestawu danych musi mieć wartość **AzureSqlTable.** | Tak |
| schema | Nazwa schematu. |Nie dla źródła, Tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, Tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowego obciążenia użyj i `schema` `table` . | Nie dla źródła, Tak dla ujścia |

### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz Pipelines ( [Potoki).](concepts-pipelines-activities.md) Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Azure SQL Database ujścia.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database jako źródło

>[!TIP]
>Aby efektywnie załadować dane z Azure SQL Database przy użyciu partycjonowania danych, dowiedz się więcej z [tematu Parallel copy from SQL database](#parallel-copy-from-sql-database)(Kopiowanie równoległe z bazy danych SQL).

Aby skopiować dane z Azure SQL Database, w sekcji źródło działania kopiowania są obsługiwane **następujące** właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **type** źródła działania kopiowania musi mieć wartość **AzureSqlSource.** Typ "SqlSource" jest nadal obsługiwany w celu zapewnienia zgodności z poprzednimi wersjami. | Tak |
| sqlReaderQuery | Ta właściwość używa niestandardowego zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. | Nie |
| sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcjią SELECT w procedurze składowanej. | Nie |
| storedProcedureParameters | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkości nazw parametrów muszą być zgodne z nazwami i wielkością wielkością nazw parametrów procedury składowanej. | Nie |
| Isolationlevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **ReadCommitted,** **ReadUncommitted,** **RepeatableRead,** **Serializable,** **Snapshot**. Jeśli nie zostanie określony, zostanie użyty domyślny poziom izolacji bazy danych. Aby uzyskać [więcej informacji, zapoznaj](/dotnet/api/system.data.isolationlevel) się z tym doc. | Nie |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z Azure SQL Database. <br>Dozwolone wartości to: **None** (wartość domyślna), **PhysicalPartitionsOfTable** i **DynamicRange.**<br>Gdy opcja partycji jest włączona (czyli nie ), stopień równoległości jednoczesnego ładowania danych z serwera Azure SQL Database jest kontrolowany przez ustawienie w działaniu `None` [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` opcją . | Nie |
| ***W `partitionSettings` obszarze :*** | | |
| partitionColumnName | Określ nazwę kolumny źródłowej w postaci liczby całkowitej lub typu **data/data** (, , , , , , lub ), która będzie używana przez partycjonowanie zakresu do kopiowania `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` równoległego. Jeśli nie zostanie określony, indeks lub klucz podstawowy tabeli zostanie automatycznie określony i użyty jako kolumna partycji.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobrania danych źródłowych, wpiąć  `?AdfDynamicRangePartitionCondition ` się do klauzuli WHERE. Aby uzyskać przykład, zobacz [sekcję Kopiowanie równoległe z bazy danych SQL.](#parallel-copy-from-sql-database) | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji dla podziału zakresu partycji. Ta wartość jest używana do decydowania o kroku partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie kopiowania automatycznie wykryje wartość.  <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Aby uzyskać przykład, zobacz [sekcję Kopiowanie równoległe z bazy danych SQL.](#parallel-copy-from-sql-database) | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji dla podziału zakresu partycji. Ta wartość jest używana do decydowania o kroku partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie kopiowania automatycznie wykryje wartość.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Aby uzyskać przykład, zobacz [sekcję Kopiowanie równoległe z bazy danych SQL.](#parallel-copy-from-sql-database) | Nie |

**Pamiętaj o następujących kwestiach:**

- Jeśli dla usługi **AzureSqlSource** określono wartość **sqlReaderQuery,** działanie kopiowania uruchamia to zapytanie względem Azure SQL Database w celu uzyskania danych. Można również określić procedurę składowaną, określając **parametry sqlReaderStoredProcedureName** i **storedProcedureParameters,** jeśli procedura składowana przyjmuje parametry.
- Podczas pobierania danych przy użyciu procedury składowanej w źródle należy pamiętać, że procedura składowana została zaprojektowana tak, aby zwracała inny schemat, gdy przekazano inną wartość parametru, może wystąpić błąd lub nieoczekiwany wynik podczas importowania schematu z interfejsu użytkownika lub kopiowania danych do bazy danych SQL przy użyciu automatycznego tworzenia tabeli.

#### <a name="sql-query-example"></a>Przykład zapytania SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Przykład procedury składowanej

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definicja procedury składowanej

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database jako ujścia

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych rozwiązaniach z tematu [Best practice for loading data into Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database)(Najlepsze rozwiązanie dotyczące ładowania danych do Azure SQL Database ).

Aby skopiować dane do Azure SQL Database, w sekcji ujścia działania kopiowania są obsługiwane **następujące** właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **type** ujścia działania kopiowania musi mieć wartość **AzureSqlSink.** Typ "SqlSink" jest nadal obsługiwany w celu zapewnienia zgodności z poprzednimi wersjami. | Tak |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma być uruchamiane przed zapisem danych w Azure SQL Database. Jest on wywoływany tylko raz podczas każdego uruchomienia kopiowania. Użyj tej właściwości, aby wyczyścić wstępnie załadowane dane. | Nie |
| tableOption (tableOption) | Określa, czy [ma być automatycznie tworzyć tabelę ujścia,](copy-activity-overview.md#auto-create-sink-tables) jeśli nie istnieje na podstawie schematu źródłowego. <br>Automatyczne tworzenie tabeli nie jest obsługiwane, gdy ujścia określa procedurę składowaną. <br>Dozwolone wartości to: `none` (wartość domyślna), `autoCreate` . | Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób stosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura składowana jest *wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają nic wspólnego z danymi źródłowymi, na przykład usuwania lub obcinania, użyj `preCopyScript` właściwości .<br>Zobacz przykład z [wywołania procedury składowanej z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Nie |
| storedProcedureTableTypeParameterName |Nazwa parametru typu tabeli określonego w procedurze składowanej.  |Nie |
| sqlWriterTableType |Nazwa typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania udostępnia przenoszone dane w tabeli tymczasowej tego typu. Kod procedury składowanej może następnie scalić kopiowane dane z istniejącymi danymi. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkości nazw parametrów muszą być zgodne z nazwami i wielkością wielkością nazw parametrów procedury składowanej. | Nie |
| writeBatchSize | Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/> Dozwolona wartość to **liczba całkowita** (liczba wierszy). Domyślnie program Azure Data Factory dynamicznie określa odpowiedni rozmiar partii na podstawie rozmiaru wiersza. | Nie |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania wsadowego przed jej przeożeniem.<br/> Dozwolona wartość to **czas.** Przykładem jest "00:30:00" (30 minut). | Nie |
| disableMetricsCollection | Data Factory zbiera metryki, takie jak Azure SQL Database DTU w celu optymalizacji wydajności kopiowania i rekomendacji, co wprowadza dodatkowy dostęp do głównej bazy danych. Jeśli to zachowanie jest dla Ciebie ważne, określ, `true` aby je wyłączyć. | Nie (wartość domyślna to `false` ) |
| maxConcurrentConnections |Górny limit połączeń współbieżnych ustanowionych z magazynem danych podczas uruchamiania działania. Określ wartość tylko wtedy, gdy chcesz ograniczyć liczbę połączeń współbieżnych.| Nie |

**Przykład 1: Dołączanie danych**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Przykład 2: Wywoływanie procedury składowanej podczas kopiowania**

Dowiedz się więcej z [tematu Invoke a stored procedure from a SQL sink (Wywoływanie procedury składowanej z ujścia SQL).](#invoke-a-stored-procedure-from-a-sql-sink)

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-database"></a>Kopiowanie równoległe z bazy danych SQL

Łącznik Azure SQL Database w działaniu kopiowania zapewnia wbudowane partycjonowanie danych w celu równoległego kopiowania danych. Opcje partycjonowania danych można znaleźć na **karcie Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-sql-server/connector-sql-partition-options.png)

Po włączeniu kopiowania podzielonego na partycje działanie kopiowania uruchamia zapytania równoległe względem źródła Azure SQL Database w celu załadowania danych według partycji. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie w działaniu kopiowania. Jeśli na przykład ustawisz wartość cztery, program Data Factory jednocześnie wygeneruje i uruchomi cztery zapytania na podstawie określonej opcji partycji i ustawień, a każde zapytanie pobierze część danych z `parallelCopies` Azure SQL Database.

Zalecamy włączenie kopiowania równoległego z partycjonowania danych, szczególnie w przypadku ładowania dużej ilości danych z Azure SQL Database. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach zaleca się zapisywanie w folderze jako wiele plików (określ tylko nazwę folderu), w takim przypadku wydajność jest lepsza niż zapisywanie w jednym pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli z partycjami fizycznymi.        | **Opcja partycji:** Partycje fizyczne tabeli. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa partycje fizyczne i kopiuje dane według partycji. <br><br/>Aby sprawdzić, czy tabela ma partycję fizyczną, możesz odwołać się do [tego zapytania](#sample-query-to-check-physical-partition). |
| Pełne ładowanie z dużej tabeli, bez partycji fizycznych, z liczbą całkowitą lub kolumną daty/godziny na potrzeby partycjonowania danych. | **Opcje partycji:** partycja zakresu dynamicznego.<br>**Kolumna partycji** (opcjonalnie): określ kolumnę używaną do partycjonowania danych. Jeśli nie zostanie określony, używana jest kolumna indeksu lub klucza podstawowego.<br/>**Górna granica partycji** **i dolna granica partycji** (opcjonalnie): określ, czy chcesz określić krok partycji. Nie jest to filtrowanie wierszy w tabeli. Wszystkie wiersze w tabeli zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie kopiowania automatycznie wykryje wartości.<br><br>Jeśli na przykład kolumna partycji "ID" ma wartości z zakresu od 1 do 100 i ustawiono dolną granicę jako 20, a górną granicę jako 80, z kopią równoległą równą 4, Data Factory pobiera dane odpowiednio według 4 partycji — identyfikatorów w zakresie <=20, [21, 50], [51, 80] i >=81. |
| Załaduj dużą ilość danych przy użyciu zapytania niestandardowego, bez partycji fizycznych, natomiast w przypadku partycjonowania danych należy użyć liczby całkowitej lub kolumny daty/daty/daty/daty/czasu. | **Opcje partycji:** partycja zakresu dynamicznego.<br>**Zapytanie:** `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Kolumna partycji:** określ kolumnę używaną do partycjonowania danych.<br>**Górna granica partycji** **i dolna granica partycji** (opcjonalnie): określ, czy chcesz określić krok partycji. Nie jest to filtrowanie wierszy w tabeli. Wszystkie wiersze w wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie kopiowania automatycznie wykryje wartość.<br><br>Podczas wykonywania Data Factory wartość rzeczywistą nazwą kolumny i zakresami wartości dla każdej partycji i wysyła `?AdfRangePartitionColumnName` do Azure SQL Database. <br>Jeśli na przykład kolumna partycji "ID" ma wartości z zakresu od 1 do 100, a dolna granica jest ustawiona na 20, a górna granica ma wartość 80, z kopią równoległą równą 4, Data Factory pobiera dane odpowiednio według 4 partycji — identyfikatorów w zakresie <=20, [21, 50], [51, 80] i >=81. <br><br>Oto więcej przykładowych zapytań dla różnych scenariuszy:<br> 1. Zapytanie dotyczące całej tabeli: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. Wykonywanie zapytań z tabeli przy użyciu wyboru kolumny i dodatkowych filtrów klauzuli where: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. Zapytanie za pomocą podzapytań: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. Zapytanie z partycją w podzapytaniu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Najlepsze rozwiązania dotyczące ładowania danych przy użyciu opcji partycji:

1. Wybierz kolumnę kolumny logicznej jako kolumnę partycji (na przykład klucz podstawowy lub unikatowy klucz), aby uniknąć niesyć danych. 
2. Jeśli tabela ma wbudowaną partycję, użyj opcji partycji "Partycje fizyczne tabeli", aby uzyskać lepszą wydajność.    
3. Jeśli używasz Azure Integration Runtime do kopiowania danych, możesz ustawić większe jednostki["Jednostki integracji](copy-activity-performance-features.md#data-integration-units)danych" (>4), aby wykorzystać więcej zasobów obliczeniowych. Sprawdź odpowiednie scenariusze.
4. ["Stopień](copy-activity-performance-features.md#parallel-copy)równoległości kopiowania" steruje numerami partycji, ustawiając zbyt dużą liczbę w pewnym momencie, co może zaszkodzić wydajności, zaleca się ustawienie tej liczby jako (diu lub liczby węzłów własnego irednoliczbowego) * (od 2 do 4).

**Przykład: pełne ładowanie z dużej tabeli z partycjami fizycznymi**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Przykład: zapytanie z partycją zakresu dynamicznego**

```json
"source": {
    "type": "AzureSqlSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Przykładowe zapytanie do sprawdzania partycji fizycznej

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Jeśli tabela ma partycję fizyczną, zostanie wyświetlony stan "HasPartition" jako "yes" (tak) w następujący sposób.

![Wynik zapytania SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Najlepsze rozwiązanie dotyczące ładowania danych do Azure SQL Database

Podczas kopiowania danych do Azure SQL Database może być wymagane inne zachowanie zapisu:

- [Dołącz:](#append-data)Moje dane źródłowe mają tylko nowe rekordy.
- [Upsert:](#upsert-data)Moje dane źródłowe mają zarówno wstawienia, jak i aktualizacje.
- [Zastąp:](#overwrite-the-entire-table)chcę za każdym razem ponownie załadować całą tabelę wymiarów.
- [Pisanie przy użyciu logiki niestandardowej:](#write-data-with-custom-logic)potrzebuję dodatkowego przetwarzania przed ostatecznym wstawieniem do tabeli docelowej.

Zapoznaj się z odpowiednimi sekcjami na temat sposobu konfigurowania w Azure Data Factory i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych jest domyślnym zachowaniem tego Azure SQL Database ujścia. Azure Data Factory wstawianie zbiorcze, aby efektywnie zapisywać dane w tabeli. Źródło i ujścia można odpowiednio skonfigurować w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1.** Jeśli masz dużą ilość danych do skopiowania, możesz zbiorczo załadować wszystkie rekordy do tabeli przejściowej przy użyciu działania kopiowania, a następnie uruchomić działanie procedury składowanej w celu zastosowania instrukcji MERGE lub [INSERT/UPDATE](/sql/t-sql/statements/merge-transact-sql) w jednym oknie. 

działanie Kopiuj obecnie nie obsługuje natywnie ładowania danych do tabeli tymczasowej bazy danych. Istnieje zaawansowany sposób skonfigurowania go za pomocą kombinacji wielu działań. Zobacz Optimize Azure SQL Database Bulk Upsert scenarios (Optymalizacja operacji zbiorczych operacji [upsert).](https://github.com/scoriani/azuresqlbulkupsert) Poniżej przedstawiono przykład użycia stałej tabeli jako tabeli przejściowej.

Na przykład w Azure Data Factory można utworzyć potok z  potokiem z działanie Kopiuj łańcuchem za pomocą działania **Procedura składowana**. Pierwsza z nich kopiuje dane z magazynu źródłowego do Azure SQL Database tabeli przejściowej, na przykład **UpsertStagingTable**, jako nazwę tabeli w zestawie danych. Następnie ta druga metoda wywołuje procedurę składowaną w celu scalenia danych źródłowych z tabeli przejściowej z tabelą docelową i oczyszczenia tabeli przejściowej.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych zdefiniuj procedurę składowaną z logiką MERGE, jak w poniższym przykładzie, który wskazuje poprzednie działanie procedury składowanej. Załóżmy, że obiektem docelowym jest **tabela Marketing** z trzema kolumnami: **ProfileID**(Kolumna), **State**(Stan) i **Category (Kategoria).** Wykonaj operacji upsert na podstawie **kolumny ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
   MERGE TargetTable AS target
   USING UpsertStagingTable AS source
   ON (target.[ProfileID] = source.[ProfileID])
   WHEN MATCHED THEN
      UPDATE SET State = source.State
    WHEN NOT matched THEN
       INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opcja 2.** Można wywołać procedurę [składowaną w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). Takie podejście uruchamia każdą partię (zgodnie z właściwością) w tabeli źródłowej zamiast używać wstawiania zbiorczego jako domyślnego podejścia w `writeBatchSize` działaniu kopiowania.

**Opcja 3.** Możesz użyć mapowania [Przepływ danych](#sink-transformation) który oferuje wbudowane metody wstawiania/upsert/aktualizacji.

### <a name="overwrite-the-entire-table"></a>Zastępowanie całej tabeli

Właściwość **preCopyScript można** skonfigurować w ujściu działania kopiowania. W takim przypadku dla każdego uruchamianego działania kopiowania Azure Data Factory najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Aby na przykład zastąpić całą tabelę najnowszymi danymi, określ skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapis danych za pomocą logiki niestandardowej

Kroki zapisu danych za pomocą logiki niestandardowej są podobne do kroków opisanych w sekcji [Dane operacji Upsert.](#upsert-data) Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej, możesz załadować do tabeli przejściowej, a następnie wywołać działanie procedury składowanej lub wywołać procedurę składowaną w ujściu działania kopiowania, aby zastosować dane, lub użyć mapowania Przepływ danych.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do Azure SQL Database można również skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami w każdej partii tabeli źródłowej. Funkcja procedury składowanej wykorzystuje parametry [o wartościach tabeli](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Procedury składowanej można użyć, gdy wbudowane mechanizmy kopiowania nie służą temu celowi. Przykładem może być zastosowanie dodatkowego przetwarzania przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania to scalanie kolumn, wyszukiwania dodatkowych wartości i wstawiania do więcej niż jednej tabeli.

W poniższym przykładzie pokazano, jak za pomocą procedury składowanej wykonać operacji upsert w tabeli w Azure SQL Database. Załóżmy, że dane wejściowe i tabela **Marketing** ujścia mają trzy kolumny: **ProfileID**(Kolumna), **State (Stan)** i **Category (Kategoria).** Wykonaj operacji upsert na podstawie **kolumny ProfileID** i zastosuj ją tylko dla określonej kategorii o nazwie "ProductA".

1. W bazie danych zdefiniuj typ tabeli o takiej samej nazwie jak **sqlWriterTableType**. Schemat typu tabeli jest taki sam jak schemat zwracany przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych zdefiniuj procedurę składowaną o takiej samej nazwie jak **sqlWriterStoredProcedureName**. Obsługuje dane wejściowe z określonego źródła i scala je z tabelą wyjściową. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **nazwa_tabeli zdefiniowana** w zestawie danych.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. W Azure Data Factory zdefiniuj **sekcję** ujścia SQL w działaniu kopiowania w następujący sposób:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Właściwości przepływu mapowania danych

Podczas przekształcania danych w przepływie mapowania danych można odczytywać i zapisywać dane w tabelach z Azure SQL Database. Aby uzyskać więcej informacji, zobacz [przekształcanie źródła i](data-flow-source.md) przekształcanie ujścia [w](data-flow-sink.md) przepływach danych mapowania.

### <a name="source-transformation"></a>Przekształcanie źródła

Ustawienia specyficzne Azure SQL Database są dostępne na karcie **Opcje** źródła przekształcenia źródła.

**Dane wejściowe:** Wybierz, czy wskażesz źródło tabeli (odpowiednik ) lub ```Select * from <table-name>``` wprowadź niestandardowe zapytanie SQL.

**Zapytanie:** jeśli w polu wejściowym wybierzesz pozycję Zapytanie, wprowadź zapytanie SQL dla źródła. To ustawienie zastępuje dowolną tabelę wybraną w zestawie danych. **Klauzule Order By** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcje SELECT FROM. Można również użyć funkcji tabel zdefiniowanych przez użytkownika. **Funkcja select * z funkcji udfGetData() to** funkcja UDF w języku SQL, która zwraca tabelę. To zapytanie spowoduje wytworzyć tabelę źródłową, która będzie można wykorzystać w przepływie danych. Używanie zapytań jest również doskonałym sposobem na zmniejszenie liczby wierszy na czas testowania lub wyszukiwania.

**Procedura składowana:** wybierz tę opcję, jeśli chcesz wygenerować dane projekcji i źródła na podstawie procedury składowanej wykonywanej ze źródłowej bazy danych. Możesz wpisać schemat, nazwę procedury i parametry lub kliknąć przycisk Odśwież, aby poprosić usługę ADF o odnajdywanie schematów i nazw procedur. Następnie możesz kliknąć pozycję Importuj, aby zaimportować wszystkie parametry procedury przy użyciu formularza ``@paraName`` .

![Procedura składowana](media/data-flow/stored-procedure-2.png "Procedura składowana")

- Przykład SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Sparametryzowane przykładowe bazy danych SQL: ``"select * from {$tablename} where orderyear > {$year}"``

**Rozmiar partii:** wprowadź rozmiar partii, aby fragmentować duże dane na odczyty.

**Poziom izolacji:** wartością domyślną dla źródeł SQL w przepływie danych mapowania jest odczyt niezatwierdzony. Poziom izolacji można zmienić na jedną z tych wartości:

- Odczyt zatwierdzone
- Odczyt niezatwierdzony
- Powtarzalny odczyt
- Serializacji
- Brak (ignoruj poziom izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

### <a name="sink-transformation"></a>Przekształcanie ujścia

Ustawienia specyficzne dla Azure SQL Database są dostępne na **karcie Ustawienia** przekształcenia ujścia.

**Update, metoda:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Wartość domyślna to zezwalanie tylko na wstawianie. Do aktualizowania, operacji upsert lub usuwania wierszy wymagane jest przekształcenie alter-row w celu tagu wierszy dla tych akcji. W przypadku aktualizacji, operacji upsert i usuwania należy ustawić kolumnę klucza lub kolumny w celu określenia, który wiersz należy zmienić.

![Kolumny kluczy](media/data-flow/keycolumn.png "Kolumny kluczy")

Nazwa kolumny wybranej w tym miejscu jako klucz będzie używana przez usługę ADF w ramach kolejnej aktualizacji, operacji upsert i usuwania. W związku z tym należy wybrać kolumnę, która istnieje w mapowaniu ujścia. Jeśli nie chcesz zapisywać wartości w tej kolumnie klucza, kliknij pozycję "Pomiń pisanie kolumn kluczy".

Możesz sparametryzować kolumnę klucza używaną w tym miejscu do aktualizowania tabeli Azure SQL Database docelowej. Jeśli masz wiele kolumn dla klucza złożonego, kliknij pozycję "Wyrażenie niestandardowe" i będzie można dodać zawartość dynamiczną przy użyciu języka wyrażeń przepływu danych usługi ADF, który może zawierać tablicę ciągów z nazwami kolumn dla klucza złożonego.

**Akcja tabeli:** Określa, czy przed zapisem należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej.

- Brak: w tabeli nie zostanie wykonana żadna akcja.
- Utwórz ponownie: tabela zostanie upuszczona i ponownie tworzona. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.
- Truncate: wszystkie wiersze z tabeli docelowej zostaną usunięte.

**Rozmiar partii:** określa liczbę wierszy zapisywanych w każdym zasobniku. Większe rozmiary partii poprawiają kompresję i optymalizację pamięci, ale ryzyko wystąpienia wyjątków pamięci podczas buforowania danych.

**Użyj bazy danych TempDB:** Domyślnie Data Factory będzie używać globalnej tabeli tymczasowej do przechowywania danych w ramach procesu ładowania. Alternatywnie możesz usunąć zaznaczenie opcji "Użyj bazy danych TempDB", a zamiast tego poprosić program Data Factory o przechowywanie tabeli tymczasowej przechowującego w bazie danych użytkownika, która znajduje się w bazie danych używanej dla tego ujścia.

![Korzystanie z tymczasowej bazy danych](media/data-flow/tempdb.png "Korzystanie z bazy danych tymczasowej")

**Skrypty wstępne** i po skryptach SQL: wprowadź wielo wierszowe skrypty SQL, które będą wykonywane przed (przetwarzanie wstępne) i po (przetwarzaniu po) są zapisywane w bazie danych ujścia

![skrypty przetwarzania wstępnego i po przetwarzaniu kodu SQL](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

### <a name="error-row-handling"></a>Obsługa wierszy błędów

Podczas zapisywania w Azure SQL DB niektóre wiersze danych mogą zakończyć się niepowodzeniem z powodu ograniczeń ustawionych przez miejsce docelowe. Niektóre typowe błędy to:

*    Dane ciągów lub danych binarnych zostałyby obcięte w tabeli
*    Nie można wstawić wartości NULL do kolumny
*    Instrukcja INSERT powoduje konflikt z ograniczeniem CHECK

Domyślnie uruchomienie przepływu danych nie powiedzie się przy pierwszym błędzie, który otrzymuje. Możesz wybrać opcję **Kontynuuj przy błędzie,** która umożliwia ukończenie przepływu danych, nawet jeśli poszczególne wiersze mają błędy. Azure Data Factory udostępnia różne opcje obsługi tych wierszy błędów.

**Zatwierdzenie transakcji:** Wybierz, czy dane są zapisywane w jednej transakcji, czy w partiach. Pojedyncza transakcja zapewni gorszą wydajność, ale żadne zapisane dane nie będą widoczne dla innych, dopóki transakcja nie zostanie ukończona.  

**Dane wyjściowe odrzucone:** Jeśli ta opcja jest włączona, możesz wyprowadzać wiersze błędów do pliku csv w Azure Blob Storage lub Azure Data Lake Storage Gen2 własnego konta. Spowoduje to napisanie wierszy błędu z trzema dodatkowymi kolumnami: operacją SQL, taką jak INSERT lub UPDATE, kodem błędu przepływu danych i komunikatem o błędzie w wierszu.

**Zgłoś powodzenie w przypadku błędu:** Jeśli ta opcja jest włączona, przepływ danych zostanie oznaczony jako powodzenie, nawet jeśli zostaną znalezione wiersze błędów. 

![Obsługa wierszy błędów](media/data-flow/sql-error-row-handling.png "Obsługa wierszy błędów")


## <a name="data-type-mapping-for-azure-sql-database"></a>Mapowanie typu danych dla Azure SQL Database

Gdy dane są kopiowane z lub do Azure SQL Database, następujące mapowania są używane z Azure SQL Database typów danych do Azure Data Factory typów danych tymczasowych. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych na ujścia, zobacz [Mapowanie](copy-activity-schema-and-type-mapping.md)schematu i typu danych .

| Azure SQL Database typu danych | Azure Data Factory typu danych tymczasowych |
|:--- |:--- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |String, Char[] |
| data |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Liczba dziesiętna |Liczba dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Float |Double |
| image (obraz) |Bajt[] |
| int |Int32 |
| pieniędzy |Liczba dziesiętna |
| nchar |String, Char[] |
| Ntext |String, Char[] |
| numeryczne |Liczba dziesiętna |
| nvarchar |String, Char[] |
| liczba rzeczywista |Pojedynczy |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Liczba dziesiętna |
| Sql_variant |Obiekt |
| tekst |String, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Ciąg |

>[!NOTE]
> W przypadku typów danych, które są mapowane na typ tymczasowy Dziesiętny, działanie Kopiuj obsługuje precyzję do 28. Jeśli masz dane o dokładności większej niż 28, rozważ przekonwertowanie na ciąg w zapytaniu SQL.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (Odnośnik).](control-flow-lookup-activity.md)

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md)

## <a name="using-always-encrypted"></a>Korzystanie z Always Encrypted

Podczas kopiowania danych z/do usługi Azure SQL Database za pomocą Always Encrypted [należy](/sql/relational-databases/security/encryption/always-encrypted-database-engine)użyć ogólnego łącznika [ODBC](connector-odbc.md) i sterownika SQL SERVER ODBC za pośrednictwem własnego Integration Runtime. Ten Azure SQL Database nie obsługuje obecnie Always Encrypted. 

Więcej szczegółów:

1. Skonfiguruj własne Integration Runtime, jeśli go nie masz. Aby [uzyskać szczegółowe informacje, zobacz artykuł self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article (Własne samodzielnie).

2. Pobierz 64-bitowy sterownik ODBC dla SQL Server [tutaj](/sql/connect/odbc/download-odbc-driver-for-sql-server)i zainstaluj go na Integration Runtime komputerze. Dowiedz się więcej na temat działania tego sterownika z [tematu Using Always Encrypted with the ODBC Driver for SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Utwórz połączoną usługę z typem ODBC, aby nawiązać połączenie z bazą danych SQL, zapoznaj się z następującymi przykładami:

    - Aby użyć **uwierzytelniania SQL:** określ poniższe ciągi połączenia ODBC, a następnie wybierz pozycję **Uwierzytelnianie** podstawowe, aby ustawić nazwę użytkownika i hasło.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Jeśli uruchamiasz samodzielnie hostowane maszyny Integration Runtime maszynie wirtualnej platformy Azure, możesz użyć uwierzytelniania tożsamości zarządzanej **przy** użyciu tożsamości maszyny wirtualnej platformy Azure:

        1. Wykonaj te same [wymagania wstępne, aby](#managed-identity) utworzyć użytkownika bazy danych dla tożsamości zarządzanej i przyznać odpowiednią rolę w bazie danych.
        2. W połączonej usłudze określ poniższe ciągi połączenia ODBC, a następnie wybierz pozycję **Uwierzytelnianie** anonimowe, ponieważ same ciągi połączenia wskazują wartość `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Odpowiednio utwórz zestaw danych i działanie kopiowania z typem ODBC. Dowiedz się więcej z [artykułu łącznika ODBC.](connector-odbc.md)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w programie Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
