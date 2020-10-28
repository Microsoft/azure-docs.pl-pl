---
title: Kopiowanie i Przekształcanie danych w wystąpieniu zarządzanym Azure SQL
description: Informacje o kopiowaniu i przekształcaniu danych w wystąpieniu zarządzanym usługi Azure SQL przy użyciu Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 805b6ed649a3ce301a3246ce1f672475ed47b9ea
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636463"
---
# <a name="copy-and-transform-data-in-azure-sql-managed-instance-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w wystąpieniu zarządzanym usługi Azure SQL przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z oraz do wystąpienia zarządzanego usługi Azure SQL i używania przepływu danych w celu przekształcania danych w wystąpieniu zarządzanym usługi Azure SQL. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik wystąpienia zarządzanego SQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku działania kopiowania ten łącznik Azure SQL Database obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji z jednostką usług lub tożsamościami zarządzanymi dla zasobów platformy Azure.
- Jako źródło, pobieranie danych przy użyciu zapytania SQL lub procedury składowanej. Możesz również wybrać opcję kopiowania równoległego ze źródła SQL MI źródło, aby uzyskać szczegółowe informacje, zobacz sekcję [copy Parallel from SQL mi](#parallel-copy-from-sql-mi) .
- Jako ujścia, automatyczne tworzenie tabeli docelowej, jeśli nie istnieje, na podstawie schematu źródłowego; Dołączanie danych do tabeli lub wywoływanie procedury składowanej z logiką niestandardową podczas kopiowania.

>[!NOTE]
> [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) wystąpienia zarządzanego SQL nie jest już obsługiwane przez ten łącznik. Aby obejść ten krok, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika SQL Server ODBC za pośrednictwem własnego środowiska Integration Runtime. Dowiedz się więcej z sekcji [używanie Always Encrypted](#using-always-encrypted) . 

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do [publicznego punktu końcowego](../azure-sql/managed-instance/public-endpoint-overview.md)wystąpienia zarządzanego SQL, możesz użyć Azure Data Factory zarządzanego środowiska Azure Integration Runtime. Upewnij się, że włączono publiczny punkt końcowy, a także Zezwól na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń, tak aby Azure Data Factory mógł nawiązać połączenie z bazą danych. Aby uzyskać więcej informacji, zobacz [te wskazówki](../azure-sql/managed-instance/public-endpoint-configure.md).

Aby uzyskać dostęp do prywatnego punktu końcowego wystąpienia zarządzanego SQL, skonfiguruj [własne środowisko Integration Runtime](create-self-hosted-integration-runtime.md) , które może uzyskać dostęp do bazy danych. Jeśli udostępniasz własne środowisko Integration Runtime w tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że maszyna Integration Runtime znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli udostępniasz własne środowisko Integration Runtime w innej sieci wirtualnej niż wystąpienie zarządzane, możesz użyć komunikacji równorzędnej sieci wirtualnej lub sieci wirtualnej do połączenia sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym bazy danych SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Azure Data Factory specyficznych dla łącznika wystąpienia zarządzanego SQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi wystąpienia zarządzanego SQL:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość **AzureSqlMI** . | Tak |
| Parametry połączenia |Ta właściwość określa informacje o **ConnectionString** , które są konieczne do nawiązania połączenia z wystąpieniem zarządzanym SQL przy użyciu uwierzytelniania SQL. Więcej informacji można znaleźć w poniższych przykładach. <br/>Domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego SQL z publicznym punktem końcowym, jawnie określ port 3342.<br> Można również umieścić hasło w Azure Key Vault. Jeśli jest to uwierzytelnianie SQL, należy ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po zalogowaniu do tabeli i [przechowywania w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Azure Data Factory lub [odwołać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
| dzierżaw | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w której znajduje się aplikacja. Pobierz ją przez umieszczenie kursora myszy w prawym górnym rogu Azure Portal. | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
| azureCloudType | W polu Uwierzytelnianie nazwy głównej usługi Określ typ środowiska chmury platformy Azure, w którym zarejestrowano aplikację usługi Azure AD. <br/> Dozwolone wartości to **AzurePublic** , **AzureChina** , **AzureUsGovernment** i **AzureGermany** . Domyślnie używane jest środowisko chmury fabryki danych. | Nie |
| Właściwością connectvia | To [środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć własnego środowiska Integration Runtime lub środowiska Azure Integration Runtime, jeśli zarządzane wystąpienie ma publiczny punkt końcowy i umożliwia Azure Data Factory dostępu do niego. Jeśli nie zostanie określony, zostanie użyta domyślna usługa Azure Integration Runtime. |Tak |

W przypadku różnych typów uwierzytelniania zapoznaj się z poniższymi sekcjami dotyczącymi wymagań wstępnych i próbek JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: Nazwa główna usługi](#service-principal-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: zarządzane tożsamości dla zasobów platformy Azure](#managed-identity)

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

**Przykład 1: korzystanie z uwierzytelniania SQL**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład 2: Użyj uwierzytelniania SQL z hasłem w Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

Aby skorzystać z uwierzytelniania tokena aplikacji opartego na jednostce usługi Azure AD, wykonaj następujące kroki:

1. Postępuj zgodnie z instrukcjami, aby [zainicjować obsługę administracyjną Azure Active Directory administratora wystąpienia zarządzanego](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Utwórz aplikację Azure Active Directoryową](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) z Azure Portal. Zanotuj nazwę aplikacji oraz następujące wartości, które definiują połączoną usługę:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

3. [Utwórz identyfikatory logowania](/sql/t-sql/statements/create-login-transact-sql) dla Azure Data Factory tożsamości zarządzanej. W SQL Server Management Studio (SSMS) Połącz się z wystąpieniem zarządzanym przy użyciu konta SQL Server, które jest **administratorem** systemu. W bazie danych **Master** Uruchom następujące polecenie T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Utwórz użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) dla Azure Data Factory tożsamości zarządzanej. Nawiąż połączenie z bazą danych programu lub, do której chcesz skopiować dane, uruchom następujące polecenie T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Przyznaj Data Factoryj zarządzanej tożsamości, które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom następujący kod. Aby uzyskać więcej informacji, zobacz [ten dokument](/sql/t-sql/statements/alter-role-transact-sql).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Skonfiguruj połączoną usługę wystąpienia zarządzanego SQL w Azure Data Factory.

**Przykład: Użyj uwierzytelniania nazwy głównej usługi**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md) , które reprezentują określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do uwierzytelniania wystąpienia zarządzanego SQL. Wydaną fabrykę mogą uzyskać dostęp do danych z lub do bazy danych lub z niej kopiować przy użyciu tej tożsamości.

Aby korzystać z uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki.

1. Postępuj zgodnie z instrukcjami, aby [zainicjować obsługę administracyjną Azure Active Directory administratora wystąpienia zarządzanego](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Utwórz identyfikatory logowania](/sql/t-sql/statements/create-login-transact-sql) dla Azure Data Factory tożsamości zarządzanej. W SQL Server Management Studio (SSMS) Połącz się z wystąpieniem zarządzanym przy użyciu konta SQL Server, które jest **administratorem** systemu. W bazie danych **Master** Uruchom następujące polecenie T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Utwórz użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) dla Azure Data Factory tożsamości zarządzanej. Nawiąż połączenie z bazą danych programu lub, do której chcesz skopiować dane, uruchom następujące polecenie T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Przyznaj Data Factoryj zarządzanej tożsamości, które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom następujący kod. Aby uzyskać więcej informacji, zobacz [ten dokument](/sql/t-sql/statements/alter-role-transact-sql).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Skonfiguruj połączoną usługę wystąpienia zarządzanego SQL w Azure Data Factory.

**Przykład: używa uwierzytelniania tożsamości zarządzanej**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych można znaleźć w artykule zestawy danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych wystąpienia zarządzanego SQL.

Aby skopiować dane do i z wystąpienia zarządzanego SQL, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureSqlMITable** . | Tak |
| schema | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| table (stolik) | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table` . | Nie dla źródła, tak dla ujścia |

**Przykład**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i obiekt sink wystąpienia zarządzanego SQL.

### <a name="sql-managed-instance-as-a-source"></a>Wystąpienie zarządzane SQL jako źródło

>[!TIP]
>Aby wydajnie ładować dane z programu SQL Server przy użyciu partycjonowania danych, Dowiedz się więcej z [kopii równoległej z programu SQL mi](#parallel-copy-from-sql-mi).

Aby skopiować dane z wystąpienia zarządzanego SQL, w sekcji Źródło działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SqlMISource** . | Tak |
| sqlReaderQuery |Ta właściwość używa niestandardowego zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwą procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Te parametry dotyczą procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| isolationLevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **READCOMMITTED** , **READUNCOMMITTED** , **REPEATABLEREAD** , **Serializable** , **migawka** . Jeśli nie zostanie określony, używany jest domyślny poziom izolacji bazy danych. Aby uzyskać więcej informacji, zapoznaj się z [tym dokumentem](/dotnet/api/system.data.isolationlevel) . | Nie |
| partitionOptions | Określa opcje partycjonowania danych, które są używane do ładowania danych z programu SQL MI. <br>Dozwolone wartości to **none** (wartość domyślna), **PhysicalPartitionsOfTable** i **DynamicRange** .<br>Gdy opcja partycji jest włączona (to nie jest `None` ), stopień równoległości do współbieżnego ładowania danych z SQL mi jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` . | Nie |
| **_W obszarze `partitionSettings` :_* _ | | |
| partitionColumnName | Określ nazwę kolumny źródłowej _ *w postaci typu Integer lub Data/DateTime* *, która będzie używana przez partycjonowanie zakresu na potrzeby kopiowania równoległego. Jeśli nie zostanie określony, indeks lub klucz podstawowy tabeli są wykrywane i używane jako kolumny partycji.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak  `?AdfDynamicRangePartitionCondition ` w klauzuli WHERE. Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-sql-mi) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji dla dzielenia zakresu partycji. Ta wartość jest używana do określenia krok partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.  <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-sql-mi) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji dla dzielenia zakresu partycji. Ta wartość jest używana do określenia krok partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-sql-mi) . | Nie |

**Pamiętaj o następujących kwestiach:**

- Jeśli **sqlReaderQuery** jest określony dla **SqlMISource** , działanie Copy uruchamia to zapytanie względem źródła wystąpienia zarządzanego SQL w celu pobrania danych. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** , jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz właściwości **sqlReaderQuery** lub **sqlReaderStoredProcedureName** , kolumny zdefiniowane w sekcji "Structure" w notacji JSON zestawu danych są używane do konstruowania zapytania. Zapytanie `select column1, column2 from mytable` jest wykonywane względem wystąpienia zarządzanego SQL. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład: Użyj procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**Definicja procedury składowanej**

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

### <a name="sql-managed-instance-as-a-sink"></a>Wystąpienie zarządzane SQL jako ujścia

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych rozwiązaniach od [najlepszych rozwiązań dotyczących ładowania danych do wystąpienia zarządzanego SQL](#best-practice-for-loading-data-into-sql-managed-instance).

Aby skopiować dane do wystąpienia zarządzanego SQL, w sekcji ujścia działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SqlMISink** . | Tak |
| preCopyScript |Ta właściwość określa zapytanie SQL do uruchomienia działania kopiowania przed zapisaniem danych w wystąpieniu zarządzanym SQL. Jest on wywoływany tylko raz dla każdego przebiegu kopiowania. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| tableOption | Określa, czy [tabela ujścia ma być automatycznie tworzona,](copy-activity-overview.md#auto-create-sink-tables) Jeśli nie istnieje na podstawie schematu źródłowego. Funkcja autotworzenia tabeli nie jest obsługiwana, gdy obiekt ujścia określa procedurę przechowywaną. Dozwolone wartości to: `none` (domyślnie), `autoCreate` . |Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób zastosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura składowana jest *wywoływana na partię* . W przypadku operacji, które są uruchamiane tylko raz i nie mają niczego do wykonania z danymi źródłowymi, na przykład Usuń lub Obetnij, użyj `preCopyScript` właściwości.<br>Zobacz przykład od [wywołania procedury składowanej z ujścia bazy danych SQL](#invoke-a-stored-procedure-from-a-sql-sink). | Nie |
| storedProcedureTableTypeParameterName |Nazwa parametru typu tabeli określona w procedurze składowanej.  |Nie |
| sqlWriterTableType |Nazwa typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię* .<br/>Dozwolone wartości to liczby całkowite dla liczby wierszy. Domyślnie Azure Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza.  |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie operacji wstawiania wsadowego przed upływem limitu czasu.<br/>Dozwolone wartości są dla przedziału czasu. Przykładem jest "00:30:00", co to 30 minut. |Nie |

**Przykład 1: Dołączanie danych**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Przykład 2: wywoływanie procedury składowanej podczas kopiowania**

Dowiedz się więcej szczegółowo od [Wywołaj procedurę składowaną z UJŚCIA mi](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="parallel-copy-from-sql-mi"></a>Kopiowanie równoległe z programu SQL MI

Łącznik wystąpienia zarządzanego usługi Azure SQL w działaniu kopiowania zapewnia wbudowane Partycjonowanie danych do równoległego kopiowania danych. Opcje partycjonowania danych można znaleźć na karcie **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-sql-server/connector-sql-partition-options.png)

Po włączeniu kopiowania z podziałem na partycje działanie Copy uruchamia zapytania równoległe względem źródła SQL MI, aby załadować dane przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z programu SQL mi.

Przed załadowaniem dużej ilości danych z programu SQL MI zaleca się włączenie kopiowania równoległego przy użyciu partycjonowania danych. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach zaleca się zapisanie do folderu jako wielu plików (określenie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli z partycjami fizycznymi.        | **Opcja partycji** : partycje fizyczne tabeli. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa partycje fizyczne i kopiuje dane przez partycje. <br><br/>Aby sprawdzić, czy tabela zawiera partycję fizyczną, można odwołać się do [tego zapytania](#sample-query-to-check-physical-partition). |
| Pełne ładowanie z dużej tabeli bez partycji fizycznych, z kolumną typu Integer lub DateTime na potrzeby partycjonowania danych. | **Opcje partycji** : dynamiczna partycja zakresu.<br>**Kolumna partycji** (opcjonalnie): określ kolumnę używaną do partycjonowania danych. Jeśli nie zostanie określony, zostanie użyta kolumna indeks lub klucz podstawowy.<br/>**Górna** granica partycji i **Dolna granica partycji** (opcjonalnie): Określ, czy chcesz określić krok partycji. Nie służy do filtrowania wierszy w tabeli, wszystkie wiersze w tabeli zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie Copy automatycznie wykrywa wartości.<br><br>Na przykład, jeśli kolumna partycji "ID" ma wartości z zakresu od 1 do 100 i ustawimy dolną granicę jako 20 i górną granicę jako 80, z kopią równoległą jako 4, Data Factory pobiera dane przez 4 partycje-identyfikatory w zakresie <= 20, [21, 50], [51, 80] i >= 81. |
| Załaduj dużą ilość danych przy użyciu niestandardowego zapytania, bez partycji fizycznych, z kolumną liczb całkowitych lub dat/DateTime na potrzeby partycjonowania danych. | **Opcje partycji** : dynamiczna partycja zakresu.<br>**Zapytanie** : `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Kolumna partycji** : określ kolumnę używaną do partycjonowania danych.<br>**Górna** granica partycji i **Dolna granica partycji** (opcjonalnie): Określ, czy chcesz określić krok partycji. Nie służy do filtrowania wierszy w tabeli, wszystkie wiersze w wyniku zapytania zostaną partycjonowane i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.<br><br>Podczas wykonywania Data Factory zastępuje `?AdfRangePartitionColumnName` z rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do programu SQL mi. <br>Na przykład, jeśli kolumna partycji "ID" ma wartości z zakresu od 1 do 100 i ustawimy dolną granicę jako 20 i górną granicę jako 80, z kopią równoległą jako 4, Data Factory pobiera dane przez 4 partycje-identyfikatory w zakresie <= 20, [21, 50], [51, 80] i >= 81. <br><br>Oto więcej przykładowych zapytań dla różnych scenariuszy:<br> 1. Zbadaj całą tabelę: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. zapytanie z tabeli z zaznaczaniem kolumn i dodatkowymi filtrami WHERE-klauzula: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. zapytanie z podzapytaniami: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. zapytanie z partycją w podzapytaniu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Najlepsze rozwiązania dotyczące ładowania danych za pomocą opcji partycji:

1. Wybierz kolumnę odróżniającą jako kolumnę partycji (na przykład klucz podstawowy lub unikatowy klucz), aby uniknąć pochylenia danych. 
2. Jeśli tabela zawiera wbudowaną partycję, użyj opcji partycji "partycje fizyczne tabeli", aby uzyskać lepszą wydajność.  
3. W przypadku używania Azure Integration Runtime do kopiowania danych można ustawić większe wartości "[Data Integration units (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4), aby użyć więcej zasobów obliczeniowych. Sprawdź odpowiednie scenariusze.
4. "[Stopień równoległości kopiowania](copy-activity-performance-features.md#parallel-copy)" kontrolowanie numerów partycji, ustawienie zbyt dużej liczby jest nadmiernie pogarszające wydajność, zalecamy ustawienie tej liczby jako (DIU lub liczba węzłów samodzielnego środowiska IR) * (od 2 do 4).

**Przykład: pełne ładowanie z dużej tabeli z partycjami fizycznymi**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "SqlMISource",
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

Jeśli tabela zawiera partycję fizyczną, zobaczysz "HasPartition" jako "yes" (tak) jak poniżej.

![Wynik zapytania SQL](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Najlepsze rozwiązanie dotyczące ładowania danych do wystąpienia zarządzanego SQL

Podczas kopiowania danych do wystąpienia zarządzanego SQL może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): moje dane źródłowe są wstawiane i aktualizowane.
- [Zastąp](#overwrite-the-entire-table): chcę ponownie załadować całą tabelę wymiarów za każdym razem.
- [Zapisz z logiką niestandardową](#write-data-with-custom-logic): Potrzebuję dodatkowego przetwarzania przed ostatnim wstawieniem do tabeli docelowej. 

Zapoznaj się z odpowiednimi sekcjami dotyczącymi konfigurowania programu w Azure Data Factory i najlepszych rozwiązaniach.

### <a name="append-data"></a>Dołącz dane

Dołączanie danych jest domyślnym zachowaniem łącznika obiektu sink wystąpienia zarządzanego SQL. Azure Data Factory wykonuje zbiorcze Wstawianie w celu wydajnego zapisu w tabeli. Można odpowiednio skonfigurować źródło i ujścia w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** W przypadku dużej ilości danych do skopiowania można zbiorczo ładować wszystkie rekordy do tabeli przemieszczania za pomocą działania kopiowania, a następnie uruchamiać działanie procedury składowanej w celu zastosowania instrukcji [merge](/sql/t-sql/statements/merge-transact-sql) lub Insert/Update w jednym zrzucie. 

Działania kopiowania obecnie nie obsługują natywnie ładowania danych do tabeli tymczasowej bazy danych. Istnieje zaawansowany sposób skonfigurowania go przy użyciu kombinacji wielu działań. Zapoznaj się z tematem [optymalizacja SQL Database zbiorczych scenariuszy upsert](https://github.com/scoriani/azuresqlbulkupsert). Poniżej przedstawiono przykład użycia trwałej tabeli jako przejściowej.

Przykładowo w Azure Data Factory można utworzyć potok z **działaniem kopiowania** łańcucha z **działaniem procedury składowanej** . Dawniej kopiuje dane z magazynu źródłowego do tabeli przemieszczania wystąpienia zarządzanego usługi Azure SQL, na przykład **UpsertStagingTable** , jako nazwę tabeli w zestawie danych. Następnie drugi wywołuje procedurę przechowywaną w celu scalenia danych źródłowych z tabeli przemieszczania do tabeli docelowej i wyczyszczenia tabeli przemieszczania.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych Zdefiniuj procedurę składowaną z logiką scalania, taką jak Poniższy przykład, który jest wskazywany przez poprzednią aktywność procedury składowanej. Załóżmy, że element docelowy jest tabelą **marketingową** z trzema kolumnami: **ProfileID** , **State** i **Category** . Wykonaj upsert na podstawie kolumny **ProfileID** .

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

**Opcja 2:** Można wybrać opcję [wywołania procedury składowanej w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). Takie podejście uruchamia każdą partię (zgodnie z `writeBatchSize` właściwością) w tabeli źródłowej, zamiast używać instrukcji BULK INSERT jako podejścia domyślnego w działaniu kopiowania.

### <a name="overwrite-the-entire-table"></a>Zastąp całą tabelę

Właściwość **preCopyScript** można skonfigurować w ujścia działania kopiowania. W tym przypadku dla każdego działania kopiowania, które działa, Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład aby zastąpić całą tabelę najnowszymi danymi, należy określić skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapisz dane za pomocą logiki niestandardowej

Kroki zapisu danych za pomocą logiki niestandardowej są podobne do tych opisanych w sekcji [dane upsert](#upsert-data) . Jeśli konieczne jest zastosowanie dodatkowego przetwarzania przed końcową wstawieniem danych źródłowych do tabeli docelowej, można załadować do tabeli przemieszczania, a następnie wywołać działanie procedury składowanej lub wywołać procedurę składowaną w ujścia działania kopiowania, aby zastosować dane.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do wystąpienia zarządzanego SQL można także skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami każdej partii tabeli źródłowej. Funkcja procedury składowanej wykorzystuje parametry z [wartościami przechowywanymi w tabeli](/dotnet/framework/data/adonet/sql/table-valued-parameters).

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do tego celu. Przykładem jest to, że chcesz zastosować dodatkowe przetwarzanie przed ostatnim wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są potrzebne do scalania kolumn, wyszukiwania dodatkowych wartości i wstawiania do więcej niż jednej tabeli.

Poniższy przykład przedstawia sposób użycia procedury składowanej do wykonania upsert w tabeli w bazie danych SQL Server. Załóżmy, że dane wejściowe i tabela **marketingu** ujścia mają trzy kolumny: **ProfileID** , **stan** i **Kategoria** . Zrób upsert na podstawie kolumny **ProfileID** i Zastosuj ją tylko dla określonej kategorii o nazwie "Product".

1. W swojej bazie danych Zdefiniuj typ tabeli o tej samej nazwie co **sqlWriterTableType** . Schemat typu tabeli jest taki sam jak schemat zwrócony przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych Zdefiniuj procedurę składowaną o takiej samej nazwie jak **sqlWriterStoredProcedureName** . Obsługuje dane wejściowe z określonego źródła i scala do tabeli danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **tabelaname** zdefiniowana w zestawie danych.

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

3. W Azure Data Factory Zdefiniuj w obszarze działania kopiowania sekcję **ujścia programu SQL mi** w następujący sposób:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można odczytywać i zapisywać tabele z wystąpienia zarządzanego Azure SQL. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych.

> [!NOTE]
> Łącznik wystąpienia zarządzanego usługi Azure SQL w przepływie danych mapowania jest obecnie dostępny jako publiczna wersja zapoznawcza. Możesz nawiązać połączenie z publicznym punktem końcowym wystąpienia zarządzanego SQL, ale nie z jeszcze prywatnym punktem końcowym.

### <a name="source-transformation"></a>Transformacja źródła

Poniższa tabela zawiera listę właściwości obsługiwanych przez źródło wystąpienia zarządzanego Azure SQL. Można edytować te właściwości na karcie **Opcje źródła** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabela | Jeśli wybierzesz opcję tabela jako dane wejściowe, przepływ danych pobierze wszystkie dane z tabeli określonej w zestawie danych. | Nie | - |- |
| Zapytanie | Jeśli wybierzesz pozycję zapytanie jako dane wejściowe, określ zapytanie SQL, aby pobrać dane ze źródła, które zastępuje każdą tabelę określoną w zestawie danych. Korzystanie z zapytań jest świetnym sposobem zredukowania wierszy do testowania i wyszukiwania.<br><br>Klauzula **order by** nie jest obsługiwana, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę, której można użyć w przepływie danych.<br>Przykład zapytania: `Select * from MyTable where customerId > 1000 and customerId < 2000`| Nie | Ciąg | query |
| Rozmiar partii | Określ rozmiar partii, aby podzielić duże ilości danych na odczyt. | Nie | Liczba całkowita | batchSize |
| Poziom izolacji | Wybierz jeden z następujących poziomów izolacji:<br>-Odczytaj zatwierdzone<br>-Odczytaj niezatwierdzone (wartość domyślna)<br>— Odczyt powtarzalny<br>— Możliwy do serializacji<br>-Brak (Ignoruj poziom izolacji) | Nie | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>DAWAJ</small> |isolationLevel |

#### <a name="azure-sql-managed-instance-source-script-example"></a>Przykład skryptu źródłowego wystąpienia zarządzanego usługi Azure SQL

W przypadku używania wystąpienia zarządzanego usługi Azure SQL jako typu źródła skojarzony skrypt przepływu danych to:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLMISource
```

### <a name="sink-transformation"></a>Przekształcanie ujścia

Poniższa tabela zawiera listę właściwości obsługiwanych przez ujścia wystąpienia zarządzanego Azure SQL. Można edytować te właściwości na karcie **Opcje ujścia** .

| Nazwa | Opis | Wymagane | Dozwolone wartości | Właściwość skryptu przepływu danych |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update — Metoda | Określ, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania.<br>Aby zaktualizować, upsert lub usunąć wiersze, [przekształcenie ALTER Row](data-flow-alter-row.md) jest wymagane, aby można było oznaczyć wiersze dla tych działań. | Tak | `true` lub `false` | usuwaln <br/>wstawialny <br/>aktualizowalne <br/>upsertable |
| Kolumny klucza | W przypadku aktualizacji, upserts i usunięć należy ustawić kolumny kluczy, aby określić, który wiersz ma być zmieniany.<br>Nazwa kolumny, która jest wybierana jako klucz, będzie używana jako część kolejnej aktualizacji, Upsert, Usuń. W związku z tym należy wybrać kolumnę, która istnieje w mapowaniu ujścia. | Nie | Tablica | keys |
| Pomiń zapisywanie kolumn klucza | Jeśli chcesz, aby nie zapisywać wartości w kolumnie klucz, wybierz pozycję "Pomiń zapisywanie kolumn klucza". | Nie | `true` lub `false` | skipKeyWrites |
| Akcja tabeli |Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.<br>- **Brak** : w tabeli nie zostanie wykonana żadna akcja.<br>- **Utwórz ponownie** : tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.<br>- **Obcinanie** : wszystkie wiersze z tabeli docelowej zostaną usunięte. | Nie | `true` lub `false` | Utwórz ponownie<br/>obciąć |
| Rozmiar partii | Określ liczbę wierszy, które są zapisywane w każdej partii. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych. | Nie | Liczba całkowita | batchSize |
| Pre i post skrypty SQL | Określ wielowierszowe skrypty SQL, które będą wykonywane przed (przed przetwarzaniem) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia. | Nie | Ciąg | preSQLs<br>postSQLs |

#### <a name="azure-sql-managed-instance-sink-script-example"></a>Przykład skryptu ujścia wystąpienia zarządzanego usługi Azure SQL

Jeśli używasz wystąpienia zarządzanego Azure SQL jako typu ujścia, skojarzony skrypt przepływu danych to:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLMISink
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-sql-managed-instance"></a>Mapowanie typu danych dla wystąpienia zarządzanego SQL

Gdy dane są kopiowane do i z wystąpienia zarządzanego SQL za pomocą działania kopiowania, następujące mapowania są używane z typów danych wystąpienia zarządzanego SQL do Azure Data Factory pośrednich typów danych. Aby dowiedzieć się, jak działanie Copy mapuje ze schematu źródłowego i typu danych do ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Typ danych wystąpienia zarządzanego SQL | Azure Data Factory typ danych pośrednich |
|:--- |:--- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Boolean |
| char |String, Char [] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Liczba dziesiętna |Liczba dziesiętna |
| FILESTREAM — atrybut (varbinary (max)) |Byte [] |
| Float |Double |
| image (obraz) |Byte [] |
| int |Int32 |
| pieniędzy |Liczba dziesiętna |
| nchar |String, Char [] |
| ntext |String, Char [] |
| numeryczne |Liczba dziesiętna |
| nvarchar |String, Char [] |
| liczba rzeczywista |Pojedynczy |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Liczba dziesiętna |
| sql_variant |Obiekt |
| tekst |String, Char [] |
| time |przedział_czasu |
| sygnatura czasowa |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Byte [] |
| varchar |String, Char [] |
| xml |String |

>[!NOTE]
> W przypadku typów danych, które są mapowane na typ pośredni dziesiętnego, obecnie działanie kopiowania obsługuje dokładność do 28. Jeśli masz dane wymagające dokładności większej niż 28, Rozważ przekonwertowanie na ciąg w zapytaniu SQL.

## <a name="using-always-encrypted"></a>Używanie Always Encrypted

Podczas kopiowania danych z lub do wystąpienia zarządzanego usługi Azure SQL przy użyciu [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)Użyj [ogólnego łącznika ODBC](connector-odbc.md) i SQL Server sterownika ODBC za pośrednictwem samodzielnego Integration Runtime. Ten łącznik wystąpienia zarządzanego usługi Azure SQL nie obsługuje teraz Always Encrypted. 

Więcej szczegółów:

1. Skonfiguruj samodzielny Integration Runtime, jeśli go nie masz. Aby uzyskać szczegółowe informacje, zobacz artykuł [Integration Runtime samodzielny](create-self-hosted-integration-runtime.md) .

2. Pobierz sterownik 64-bitowy ODBC dla SQL Server z tego [miejsca](/sql/connect/odbc/download-odbc-driver-for-sql-server)i zainstaluj go na maszynie Integration Runtime. Dowiedz się więcej o tym, w jaki sposób ten sterownik działa z [wykorzystaniem Always Encrypted ze sterownikiem ODBC dla SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Utwórz połączoną usługę z typem ODBC, aby połączyć się z bazą danych SQL, zapoznaj się z poniższymi przykładami:

    - Aby używać **uwierzytelniania SQL** : Określ parametry połączenia ODBC poniżej, a następnie wybierz pozycję uwierzytelnianie **podstawowe** , aby ustawić nazwę użytkownika i hasło.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Aby użyć **Data Factory uwierzytelniania tożsamości zarządzanej** : 

        1. Wykonaj te same [wymagania wstępne](#managed-identity) , aby utworzyć użytkownika bazy danych dla zarządzanej tożsamości i udzielić odpowiedniej roli w bazie danych.
        2. W polu połączona usługa Określ parametry połączenia ODBC poniżej, a następnie wybierz opcję uwierzytelnianie **anonimowe** jako parametry połączenia `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Utwórz odpowiednio zestaw danych i działanie Copy z typem ODBC. Dowiedz się więcej z artykułu [łącznika ODBC](connector-odbc.md) .

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).