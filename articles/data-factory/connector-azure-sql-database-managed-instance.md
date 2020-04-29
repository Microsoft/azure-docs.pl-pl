---
title: Kopiuj dane do i z Azure SQL Database wystąpienia zarządzanego
description: Dowiedz się, jak przenosić dane do i z Azure SQL Database wystąpienia zarządzanego przy użyciu Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: fe803c918cbf60b8f0af76d8c9a94d022153acbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417501"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiowanie danych do i z Azure SQL Database wystąpienia zarządzanego przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych do i z Azure SQL Database wystąpienia zarządzanego. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten Azure SQL Database łącznika wystąpienia zarządzanego jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

Dane z Azure SQL Database wystąpienia zarządzanego można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Można także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do wystąpienia zarządzanego. Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W tym Azure SQL Database łącznik wystąpienia zarządzanego obsługuje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji z jednostką usług lub tożsamościami zarządzanymi dla zasobów platformy Azure.
- Jako źródło, pobieranie danych przy użyciu zapytania SQL lub procedury składowanej.
- Jako ujścia, dołączanie danych do tabeli docelowej lub wywoływanie procedury składowanej z logiką niestandardową podczas kopiowania.

>[!NOTE]
>[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) Azure SQL Database wystąpienia zarządzanego nie jest teraz obsługiwane przez ten łącznik. Aby obejść ten krok, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika SQL Server ODBC za pośrednictwem własnego środowiska Integration Runtime. Postępuj zgodnie z [tymi wskazówkami](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) przy użyciu opcji pobierania sterowników ODBC i parametrów połączenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do [publicznego punktu końcowego](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)Azure SQL Database wystąpienia zarządzanego, można użyć Azure Data Factory zarządzanego środowiska Azure Integration Runtime. Upewnij się, że włączono publiczny punkt końcowy, a także Zezwól na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń, tak aby Azure Data Factory mógł nawiązać połączenie z bazą danych. Aby uzyskać więcej informacji, zobacz [te wskazówki](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Aby uzyskać dostęp do prywatnego punktu końcowego Azure SQL Database wystąpienia zarządzanego, skonfiguruj [własne środowisko Integration Runtime](create-self-hosted-integration-runtime.md) , które może uzyskać dostęp do bazy danych. Jeśli udostępniasz własne środowisko Integration Runtime w tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że maszyna Integration Runtime znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli udostępniasz własne środowisko Integration Runtime w innej sieci wirtualnej niż wystąpienie zarządzane, możesz użyć komunikacji równorzędnej sieci wirtualnej lub sieci wirtualnej do połączenia sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Azure Data Factory specyficznych dla łącznika wystąpienia zarządzanego Azure SQL Database.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Dla połączonej usługi wystąpienia zarządzanego Azure SQL Database są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi być ustawiona na wartość **AzureSqlMI**. | Tak |
| Parametry połączenia |Ta właściwość określa informacje o **ConnectionString** , które są konieczne do nawiązania połączenia z wystąpieniem zarządzanym przy użyciu uwierzytelniania SQL. Więcej informacji można znaleźć w poniższych przykładach. <br/>Domyślny port to 1433. Jeśli używasz Azure SQL Database wystąpienia zarządzanego z publicznym punktem końcowym, jawnie określ port 3342.<br> Można również umieścić hasło w Azure Key Vault. Jeśli jest to uwierzytelnianie SQL, należy ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po zalogowaniu do tabeli i [przechowywania w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element **SecureString** , aby bezpiecznie przechowywać go w Azure Data Factory lub [odwołać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
| dzierżaw | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w której znajduje się aplikacja. Pobierz ją przez umieszczenie kursora myszy w prawym górnym rogu Azure Portal. | Tak, w przypadku korzystania z uwierzytelniania usługi Azure AD za pomocą nazwy głównej usługi |
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
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

1. Postępuj zgodnie z instrukcjami, aby [zainicjować obsługę administracyjną Azure Active Directory administratora wystąpienia zarządzanego](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Utwórz aplikację Azure Active Directoryową](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z Azure Portal. Zanotuj nazwę aplikacji oraz następujące wartości, które definiują połączoną usługę:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

3. [Utwórz identyfikatory logowania](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) dla Azure Data Factory tożsamości zarządzanej. W SQL Server Management Studio (SSMS) Połącz się z wystąpieniem zarządzanym przy użyciu konta SQL Server, które jest **administratorem**systemu. W bazie danych **Master** Uruchom następujące polecenie T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla Azure Data Factory tożsamości zarządzanej. Nawiąż połączenie z bazą danych programu lub, do której chcesz skopiować dane, uruchom następujące polecenie T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Przyznaj Data Factoryj zarządzanej tożsamości, które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom następujący kod. Aby uzyskać więcej informacji, zobacz [ten dokument](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Skonfiguruj połączoną usługę Azure SQL Databasego wystąpienia zarządzanego w programie Azure Data Factory.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md) , które reprezentują określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do Azure SQL Database uwierzytelniania wystąpienia zarządzanego. Wydaną fabrykę mogą uzyskać dostęp do danych z lub do bazy danych lub z niej kopiować przy użyciu tej tożsamości.

Aby korzystać z uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki.

1. Postępuj zgodnie z instrukcjami, aby [zainicjować obsługę administracyjną Azure Active Directory administratora wystąpienia zarządzanego](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Utwórz identyfikatory logowania](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) dla Azure Data Factory tożsamości zarządzanej. W SQL Server Management Studio (SSMS) Połącz się z wystąpieniem zarządzanym przy użyciu konta SQL Server, które jest **administratorem**systemu. W bazie danych **Master** Uruchom następujące polecenie T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla Azure Data Factory tożsamości zarządzanej. Nawiąż połączenie z bazą danych programu lub, do której chcesz skopiować dane, uruchom następujące polecenie T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Przyznaj Data Factoryj zarządzanej tożsamości, które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom następujący kod. Aby uzyskać więcej informacji, zobacz [ten dokument](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Skonfiguruj połączoną usługę Azure SQL Databasego wystąpienia zarządzanego w programie Azure Data Factory.

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

Pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych można znaleźć w artykule zestawy danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych wystąpienia zarządzanego Azure SQL Database.

Aby skopiować dane do i z Azure SQL Database wystąpienia zarządzanego, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość **AzureSqlMITable**. | Tak |
| schematy | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table`. | Nie dla źródła, tak dla ujścia |

**Przyklad**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Aby zapoznać się z pełną listą sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia wystąpienia zarządzanego Azure SQL Database.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database wystąpieniem zarządzanym jako źródło

Aby skopiować dane z wystąpienia zarządzanego Azure SQL Database, w sekcji Źródło działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość **SqlMISource**. | Tak |
| sqlReaderQuery |Ta właściwość używa niestandardowego zapytania SQL do odczytywania danych. Może to być na przykład `select * from MyTable`. |Nie |
| sqlReaderStoredProcedureName |Ta właściwość jest nazwą procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| storedProcedureParameters |Te parametry dotyczą procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| isolationLevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **READCOMMITTED** (wartość domyślna), **READUNCOMMITTED**, **REPEATABLEREAD**, **Serializable**, **migawka**. Aby uzyskać więcej informacji, zapoznaj się z [tym dokumentem](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) . | Nie |

**Pamiętaj o następujących kwestiach:**

- Jeśli **sqlReaderQuery** jest określony dla **SqlMISource**, działanie Copy uruchamia to zapytanie względem źródła wystąpienia zarządzanego w celu uzyskania danych. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters** , jeśli procedura składowana pobiera parametry.
- Jeśli nie określisz właściwości **sqlReaderQuery** lub **sqlReaderStoredProcedureName** , kolumny zdefiniowane w sekcji "Structure" w notacji JSON zestawu danych są używane do konstruowania zapytania. Zapytanie `select column1, column2 from mytable` jest wykonywane względem wystąpienia zarządzanego. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "referenceName": "<Managed Instance input dataset name>",
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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database wystąpienia zarządzanego jako ujścia

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych rozwiązaniach od [najlepszych rozwiązań dotyczących ładowania danych do Azure SQL Database wystąpienia zarządzanego](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Aby skopiować dane do Azure SQL Database wystąpienia zarządzanego, w sekcji ujścia działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **SqlMISink**. | Tak |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości to liczby całkowite dla liczby wierszy. Domyślnie Azure Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza.  |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania na zakończenie operacji wstawiania wsadowego przed upływem limitu czasu.<br/>Dozwolone wartości są dla przedziału czasu. Przykładem jest "00:30:00", co to 30 minut. |Nie |
| preCopyScript |Ta właściwość określa zapytanie SQL do uruchomienia działania kopiowania przed zapisaniem danych w zarządzanym wystąpieniu. Jest on wywoływany tylko raz dla każdego przebiegu kopiowania. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób zastosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura składowana jest *wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają niczego do wykonania z danymi źródłowymi, na przykład Usuń lub Obetnij `preCopyScript` , użyj właściwości. | Nie |
| storedProcedureTableTypeParameterName |Nazwa parametru typu tabeli określona w procedurze składowanej.  |Nie |
| sqlWriterTableType |Nazwa typu tabeli, która ma zostać użyta w procedurze składowanej. Działanie kopiowania sprawia, że dane są dostępne w tabeli tymczasowej z tym typem tabeli. Kod procedury składowanej może następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| storedProcedureParameters |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| tableOption | Określa, czy tabela ujścia ma być automatycznie tworzona, jeśli nie istnieje na podstawie schematu źródłowego. Funkcja autotworzenia tabeli nie jest obsługiwana, gdy obiekt ujścia określa procedurę przechowywaną lub kopię etapową skonfigurowaną w działaniu kopiowania. Dozwolone wartości to: `none` (domyślnie), `autoCreate`. |Nie |

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
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
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
                "referenceName": "<Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Najlepsze rozwiązanie dotyczące ładowania danych do Azure SQL Database wystąpienia zarządzanego

Podczas kopiowania danych do wystąpienia zarządzanego Azure SQL Database może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): moje dane źródłowe są wstawiane i aktualizowane.
- [Zastąp](#overwrite-the-entire-table): chcę ponownie załadować całą tabelę wymiarów za każdym razem.
- [Zapisz z logiką niestandardową](#write-data-with-custom-logic): Potrzebuję dodatkowego przetwarzania przed ostatnim wstawieniem do tabeli docelowej. 

Zapoznaj się z odpowiednimi sekcjami dotyczącymi konfigurowania programu w Azure Data Factory i najlepszych rozwiązaniach.

### <a name="append-data"></a>Dołącz dane

Dołączanie danych jest domyślnym zachowaniem tego Azure SQL Database łącznika obiektu sink wystąpienia zarządzanego. Azure Data Factory wykonuje zbiorcze Wstawianie w celu wydajnego zapisu w tabeli. Można odpowiednio skonfigurować źródło i ujścia w działaniu kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Opcja 1:** Jeśli masz dużą ilość danych do skopiowania, użyj następującego podejścia, aby wykonać upsert: 

- Najpierw należy użyć [tabeli tymczasowej](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) do zbiorczego ładowania wszystkich rekordów przy użyciu działania kopiowania. Ponieważ operacje związane z tabelami tymczasowymi nie są rejestrowane, można ładować miliony rekordów w kilka sekund.
- Uruchom działanie procedury składowanej w Azure Data Factory, aby zastosować instrukcję [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub Insert/Update. Użyj tabeli tymczasowej jako źródła, aby wykonać wszystkie aktualizacje lub wstawioną jako pojedynczą transakcję. W ten sposób liczba operacji błądzenia i dzienników jest ograniczona. Na końcu działania procedury składowanej tabela tymczasowa może zostać obcięta, aby była gotowa do następnego cyklu upsert.

Przykładowo w Azure Data Factory można utworzyć potok z **działaniem kopiowania** łańcucha z **działaniem procedury składowanej**. Dawniej kopiuje dane z magazynu źródłowego do tabeli tymczasowej, na przykład **# #UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie drugi wywołuje procedurę przechowywaną, aby scalić dane źródłowe z tabeli tymczasowej do tabeli docelowej i oczyścić tabelę tymczasową.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych Zdefiniuj procedurę składowaną z logiką scalania, taką jak Poniższy przykład, który jest wskazywany przez poprzednią aktywność procedury składowanej. Załóżmy, że element docelowy jest tabelą **marketingową** z trzema kolumnami: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie kolumny **ProfileID** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opcja 2:** Możesz również wybrać opcję [wywołania procedury składowanej w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). To podejście uruchamia każdy wiersz w tabeli źródłowej zamiast używać instrukcji BULK INSERT jako podejścia domyślnego w działaniu kopiowania, które nie jest odpowiednie dla upsert w dużej skali.

### <a name="overwrite-the-entire-table"></a>Zastąp całą tabelę

Właściwość **preCopyScript** można skonfigurować w ujścia działania kopiowania. W tym przypadku dla każdego działania kopiowania, które działa, Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład aby zastąpić całą tabelę najnowszymi danymi, należy określić skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapisz dane za pomocą logiki niestandardowej

Kroki zapisu danych za pomocą logiki niestandardowej są podobne do tych opisanych w sekcji [dane upsert](#upsert-data) . Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatnim wstawieniem danych źródłowych do tabeli docelowej, możesz wykonać jedną z dwóch czynności: 

- Załaduj do tabeli tymczasowej, a następnie Wywołaj procedurę składowaną.
- Wywołaj procedurę przechowywaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do wystąpienia zarządzanego Azure SQL Database można także skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami. Funkcja procedury składowanej wykorzystuje parametry z [wartościami przechowywanymi w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Wywołanie procedury składowanej przetwarza wiersz danych według wiersza zamiast przy użyciu operacji zbiorczej, której nie zalecamy w przypadku kopiowania na dużą skalę. Dowiedz się więcej z [najlepszych rozwiązań dotyczących ładowania danych do Azure SQL Database wystąpienia zarządzanego](#best-practice-for-loading-data-into-azure-sql-database-managed-instance).

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do tego celu. Przykładem jest to, że chcesz zastosować dodatkowe przetwarzanie przed ostatnim wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są potrzebne do scalania kolumn, wyszukiwania dodatkowych wartości i wstawiania danych do więcej niż jednej tabeli.

Poniższy przykład przedstawia sposób użycia procedury składowanej do wykonania upsert w tabeli w bazie danych SQL Server. Załóżmy, że dane wejściowe i tabela **marketingu** ujścia mają trzy kolumny: **ProfileID**, **stan**i **Kategoria**. Zrób upsert na podstawie kolumny **ProfileID** i Zastosuj ją tylko dla określonej kategorii o nazwie "Product".

1. W swojej bazie danych Zdefiniuj typ tabeli o tej samej nazwie co **sqlWriterTableType**. Schemat typu tabeli jest taki sam jak schemat zwrócony przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych Zdefiniuj procedurę składowaną o takiej samej nazwie jak **sqlWriterStoredProcedureName**. Obsługuje dane wejściowe z określonego źródła i scala do tabeli danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **tabelaname** zdefiniowana w zestawie danych.

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapowanie typu danych dla Azure SQL Database wystąpienia zarządzanego

Gdy dane są kopiowane do i z Azure SQL Database wystąpienia zarządzanego, następujące mapowania są używane z typów danych wystąpienia zarządzanego Azure SQL Database do Azure Data Factory typów danych pośrednich. Aby dowiedzieć się, jak działanie Copy mapuje ze schematu źródłowego i typu danych do ujścia, zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md).

| Azure SQL Database typ danych wystąpienia zarządzanego | Azure Data Factory typ danych pośrednich |
|:--- |:--- |
| bigint |Int64 |
| binarny |Byte [] |
| bit |Boolean |
| char |String, Char [] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Wartość dziesiętna |Wartość dziesiętna |
| FILESTREAM — atrybut (varbinary (max)) |Byte [] |
| Liczba zmiennoprzecinkowa |Double |
| image |Byte [] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |String, Char [] |
| ntext |String, Char [] |
| numeryczne |Wartość dziesiętna |
| nvarchar |String, Char [] |
| liczba rzeczywista |Single |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| sql_variant |Obiekt |
| tekst |String, Char [] |
| time |przedział_czasu |
| sygnatura czasowa |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Byte [] |
| varchar |String, Char [] |
| xml |Xml |

>[!NOTE]
> W przypadku typów danych, które są mapowane na typ pośredni dziesiętny, obecnie Azure Data Factory obsługuje precyzję do 28. Jeśli masz dane wymagające dokładności większej niż 28, Rozważ przekonwertowanie na ciąg w zapytaniu SQL.

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
