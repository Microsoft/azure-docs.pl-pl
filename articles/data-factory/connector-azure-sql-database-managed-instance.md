---
title: Kopiowanie danych do i z wystąpienia zarządzanego usługi Azure SQL Database
description: Dowiedz się, jak przenosić dane do i z wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi Azure Data Factory.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417501"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Kopiowanie danych do i z wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych do i z wystąpienia zarządzanego usługi Azure SQL Database. Opiera się na [kopiowanie działania omówienie](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik wystąpienia zarządzanego bazy danych SQL platformy Azure jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

Można skopiować dane z wystąpienia zarządzanego usługi Azure SQL Database do dowolnego obsługiwanego magazynu danych ujścia. Można również skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do wystąpienia zarządzanego. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła i pochłania przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik wystąpienia zarządzanego bazy danych SQL platformy Azure obsługuje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu jednostki usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Jako źródło pobieranie danych przy użyciu kwerendy SQL lub procedury składowanej.
- Jako ujście dołączanie danych do tabeli docelowej lub wywoływanie procedury składowanej z niestandardową logiką podczas kopiowania.

>[!NOTE]
>Wystąpienie zarządzanej usługi Azure SQL Database [zawsze szyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) nie jest teraz obsługiwane przez ten łącznik. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika ODBC programu SQL Server za pośrednictwem środowiska wykonawczego integracji hostowanego samodzielnie. Postępuj zgodnie z [tymi wskazówkami](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) z konfiguracjami pobierania sterowników ODBC i ciągów połączeń.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do [publicznego punktu końcowego wystąpienia](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)zarządzanego bazy danych SQL platformy Azure, można użyć zarządzanego środowiska wykonawczego integracji platformy Azure Data Factory. Upewnij się, że włączysz publiczny punkt końcowy, a także zezwalasz na publiczny ruch punktów końcowych w sieciowej grupie zabezpieczeń, aby usługa Azure Data Factory mogła łączyć się z bazą danych. Aby uzyskać więcej informacji, zobacz [te wskazówki](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Aby uzyskać dostęp do prywatnego punktu końcowego wystąpienia zarządzanego bazy danych SQL azure, [skonfiguruj środowisko uruchomieniowe integracji hostowanego samodzielnie,](create-self-hosted-integration-runtime.md) które może uzyskać dostęp do bazy danych. Jeśli aprowizujesz środowisko uruchomieniowe integracji hostowanego samodzielnie w tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że komputer środowiska wykonawczego integracji znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli aprowizujesz środowisko uruchomieniowe integracji hostowanego samodzielnie w innej sieci wirtualnej niż wystąpienie zarządzane, możesz użyć komunikacji równorzędnej sieci wirtualnej lub sieci wirtualnej do połączenia z siecią wirtualną. Aby uzyskać więcej informacji, zobacz [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Azure Data Factory specyficznych dla łącznika wystąpienia zarządzanego bazy danych SQL azure.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej z wystąpieniem zarządzanym bazy danych SQL Azure:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na **AzureSqlMI**. | Tak |
| Parametry połączenia |Ta właściwość określa **connectionString** informacje, które są potrzebne do połączenia się z wystąpieniem zarządzanym przy użyciu uwierzytelniania SQL. Aby uzyskać więcej informacji, zobacz poniższe przykłady. <br/>Domyślny port to 1433. Jeśli używasz wystąpienia zarządzanego usługi Azure SQL Database z publicznym punktem końcowym, jawnie określ port 3342.<br> Można również umieścić hasło w usłudze Azure Key Vault. Jeśli jest to uwierzytelnianie `password` SQL, pociągnij konfigurację z ciągu połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po tabeli i [poświadczenia sklepu w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w usłudze Azure Data Factory lub [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| Dzierżawy | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w ramach którego znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| connectVia | To [środowisko wykonawcze integracji](concepts-integration-runtime.md) jest używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji hostowanego samodzielnie lub środowiska wykonawczego integracji platformy Azure, jeśli wystąpienie zarządzane ma publiczny punkt końcowy i umożliwia usługi Azure Data Factory dostęp do niego. Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Tak |

W przypadku różnych typów uwierzytelniania należy zapoznać się z następującymi sekcjami dotyczącymi wymagań wstępnych i przykładów JSON:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: podmiot zabezpieczeń usługi](#service-principal-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: tożsamości zarządzane dla zasobów platformy Azure](#managed-identity)

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

**Przykład 1: używanie uwierzytelniania SQL**

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

**Przykład 2: używanie uwierzytelniania SQL z hasłem w usłudze Azure Key Vault**

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

Aby użyć uwierzytelniania tokenu aplikacji usługi Azure AD opartego na usłudze, wykonaj następujące kroki:

1. Wykonaj kroki, aby [aprowizować administratora usługi Azure Active Directory dla wystąpienia zarządzanego](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Utwórz aplikację usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z witryny Azure portal. Zanotuj nazwę aplikacji i następujące wartości definiujące usługę połączone:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

3. [Tworzenie danych logowania](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) dla tożsamości zarządzanej usługi Azure Data Factory. W programie SQL Server Management Studio (SSMS) połącz się z wystąpieniem zarządzanym przy użyciu konta programu SQL Server, które jest **sysadmin**. W **głównej** bazie danych uruchom następujący program T-SQL:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla tożsamości zarządzanej usługi Azure Data Factory. Połącz się z bazą danych z lub do którego chcesz skopiować dane, uruchom następujący T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Udziel tożsamości zarządzanej przez usługę Data Factory, wymaganych uprawnień, jak zwykle dla użytkowników SQL i innych osób. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Konfigurowanie połączonej usługi wystąpienia zarządzanego bazy danych SQL platformy Azure w usłudze Azure Data Factory.

**Przykład: użyj uwierzytelniania głównego usługi**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md) która reprezentuje określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do uwierzytelniania wystąpienia zarządzanego usługi Azure SQL Database. Wyznaczona fabryka może uzyskiwać dostęp do danych z bazy danych lub do niej do bazy danych przy użyciu tej tożsamości.

Aby użyć uwierzytelnienia tożsamości zarządzanej, wykonaj następujące kroki.

1. Wykonaj kroki, aby [aprowizować administratora usługi Azure Active Directory dla wystąpienia zarządzanego](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).

2. [Tworzenie danych logowania](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) dla tożsamości zarządzanej usługi Azure Data Factory. W programie SQL Server Management Studio (SSMS) połącz się z wystąpieniem zarządzanym przy użyciu konta programu SQL Server, które jest **sysadmin**. W **głównej** bazie danych uruchom następujący program T-SQL:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla tożsamości zarządzanej usługi Azure Data Factory. Połącz się z bazą danych z lub do którego chcesz skopiować dane, uruchom następujący T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Udziel tożsamości zarządzanej przez usługę Data Factory, wymaganych uprawnień, jak zwykle dla użytkowników SQL i innych osób. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Konfigurowanie połączonej usługi wystąpienia zarządzanego bazy danych SQL platformy Azure w usłudze Azure Data Factory.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do użycia do definiowania zestawów danych, zobacz artykuł o zestawach danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych wystąpienia zarządzanego usługi Azure SQL Database.

Aby skopiować dane do i z wystąpienia zarządzanego usługi Azure SQL Database, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **AzureSqlMITable**. | Tak |
| Schematu | Nazwa schematu. |Nie dla źródła, tak dla zlewu  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla zlewu  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `schema` `table`obciążenia, użycia i programu . | Nie dla źródła, tak dla zlewu |

**Przykład**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do użycia do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujście wystąpienia zarządzanego bazy danych SQL azure.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Wystąpienie zarządzanego bazy danych SQL platformy Azure jako źródło

Aby skopiować dane z wystąpienia zarządzanego usługi Azure SQL Database, następujące właściwości są obsługiwane w sekcji źródła działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **SqlMISource**. | Tak |
| sqlReaderQuery |Ta właściwość używa niestandardowej kwerendy SQL do odczytu danych. Może to być na przykład `select * from MyTable`. |Nie |
| nazwa sqlReaderStoredProcedureName |Ta właściwość jest nazwą procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. |Nie |
| przechowywaneParametryprocedure |Parametry te są dla procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. |Nie |
| Isolationlevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Więcej informacji można znaleźć [w tym dok.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nie |

**Pamiętaj o następujących kwestiach:**

- Jeśli **sqlReaderQuery** jest określony dla **SqlMISource**, działanie kopiowania uruchamia tę kwerendę względem źródła wystąpienia zarządzanego, aby uzyskać dane. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters,** jeśli procedura składowana przyjmuje parametry.
- Jeśli nie określisz właściwości **sqlReaderQuery** lub **sqlReaderStoredProcedureName,** kolumny zdefiniowane w sekcji "struktura" zestawu danych JSON są używane do konstruowania kwerendy. Kwerenda `select column1, column2 from mytable` jest uruchamiana względem wystąpienia zarządzanego. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

**Przykład: Użyj kwerendy SQL**

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

**Przykład: użyj procedury składowanej**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Wystąpienie zarządzanego bazy danych SQL platformy Azure jako ujście

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych praktykach z [najlepszych rozwiązań dotyczących ładowania danych do wystąpienia zarządzanego bazy danych SQL platformy Azure.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

Aby skopiować dane do wystąpienia zarządzanego usługi Azure SQL Database, następujące właściwości są obsługiwane w sekcji ujście działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **SqlMISink**. | Tak |
| writeBatchSize |Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/>Dozwolone wartości są liczbami całkowitymi dla liczby wierszy. Domyślnie usługa Azure Data Factory dynamicznie określa odpowiedni rozmiar partii na podstawie rozmiaru wiersza.  |Nie |
| writeBatchTimeout |Ta właściwość określa czas oczekiwania dla operacji wstawiania partii, aby zakończyć przed jej limit czasu.<br/>Dozwolone wartości są dla timespan. Przykładem jest "00:30:00", czyli 30 minut. |Nie |
| preCopyScript |Ta właściwość określa kwerendę SQL dla działania kopiowania do uruchomienia przed zapisaniem danych w wystąpieniu zarządzanym. Jest wywoływana tylko raz na uruchomienie kopii. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. |Nie |
| nazwa sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób stosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura *składowana jest wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają nic `preCopyScript` wspólnego z danymi źródłowymi, na przykład usuń lub obcięć, użyj właściwości. | Nie |
| przechowywaneProcedureTableTypeParameterName |Nazwa parametru typu tabeli określonego w procedurze składowanej.  |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Nazwa typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są przenoszone dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| tableOpcja | Określa, czy tabela ujścia ma być automatycznie tworzędliwa, jeśli nie istnieje na podstawie schematu źródłowego. Automatyczne tworzenie tabeli nie jest obsługiwane, gdy zlew określa procedurę składowaną lub kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (domyślnie), `autoCreate`. |Nie |

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

**Przykład 2: Wywołanie procedury składowanej podczas kopiowania**

Dowiedz się więcej szczegółów z [Wywołać procedurę składowaną z ujścia MI SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Najlepsze rozwiązanie dotyczące ładowania danych do wystąpienia zarządzanego usługi Azure SQL Database

Podczas kopiowania danych do wystąpienia zarządzanego usługi Azure SQL Database może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): Moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): Moje dane źródłowe mają zarówno wstawia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Chcę za każdym razem przeładować całą tabelę wymiarów.
- [Pisanie z niestandardową logiką:](#write-data-with-custom-logic)Potrzebuję dodatkowego przetwarzania przed ostatecznym wstawieniem do tabeli docelowej. 

Zobacz odpowiednie sekcje, jak skonfigurować w usłudze Azure Data Factory i najlepsze rozwiązania.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych jest domyślnym zachowaniem tego łącznika ujścia wystąpienia zarządzanego usługi Azure SQL Database. Usługa Azure Data Factory wykonuje zbiorcze wstawianie, aby skutecznie zapisywać w tabeli. Można skonfigurować źródło i sink odpowiednio w działania kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Wariant 1:** Jeśli masz dużą ilość danych do skopiowania, użyj następującej metody, aby wykonać ofertę dodatkową: 

- Najpierw użyj [tabeli tymczasowej,](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) aby zbiorczo załadować wszystkie rekordy przy użyciu działania kopiowania. Ponieważ operacje względem tabel tymczasowych nie są rejestrowane, można załadować miliony rekordów w ciągu kilku sekund.
- Uruchom działanie procedury składowanej w usłudze Azure Data Factory, aby zastosować instrukcję [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub INSERT/UPDATE. Użyj tabeli tymczasowej jako źródła, aby wykonać wszystkie aktualizacje lub wstawia jako pojedynczą transakcję. W ten sposób zmniejsza się liczba operacji w obie strony i dzienników. Po zakończeniu działania procedury składowanej tabela tymczasowa może zostać obcięta, aby była gotowa do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory można utworzyć potok z **działaniem kopiowania** połączone z **działaniem procedury przechowywanej.** Pierwszy kopiuje dane z magazynu źródłowego do tabeli tymczasowej, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę składowaną, aby scalić dane źródłowe z tabeli tymczasowej do tabeli docelowej i oczyścić tabelę tymczasową.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

W bazie danych należy zdefiniować procedurę składowaną z logiką SCALANIA, podobnie jak w poniższym przykładzie, który jest wskazywany na poprzednie działanie procedury składowanej. Załóżmy, że celem jest **tabela Marketing** z trzema kolumnami: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie **kolumny ProfileID.**

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

**Wariant 2:** Można również [wywołać procedurę składowaną w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). To podejście uruchamia każdy wiersz w tabeli źródłowej zamiast przy użyciu zbiorczego wstawiania jako domyślnego podejścia w działaniu kopiowania, co nie jest odpowiednie dla dużych skalowania upsert.

### <a name="overwrite-the-entire-table"></a>Zastępowanie całej tabeli

Właściwość **preCopyScript** można skonfigurować w ujściu działania kopiowania. W takim przypadku dla każdego działania kopiowania, który jest uruchamiany, usługa Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład, aby zastąpić całą tabelę najnowszymi danymi, określ skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapis danych za pomocą niestandardowej logiki

Kroki zapisywania danych za pomocą logiki niestandardowej są podobne do opisanych w sekcji [Dane Upsert.](#upsert-data) Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej, na dużą skalę można wykonać jedną z dwóch czynności: 

- Wczytaj do tabeli tymczasowej, a następnie wywołaj procedurę składowaną.
- Wywołać procedurę składowaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do wystąpienia zarządzanego bazy danych SQL usługi Azure można również skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami. Funkcja procedury składowanej korzysta z [parametrów wycenionych w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Wywoływanie procedury składowanej przetwarza wiersz danych według wiersza zamiast przy użyciu operacji zbiorczej, która nie jest zalecana dla kopiowania na dużą skalę. Dowiedz się więcej o [najlepszych praktykach dotyczących ładowania danych do wystąpienia zarządzanego usługi Azure SQL Database.](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do celu. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są, gdy chcesz scalić kolumny, wyszukać dodatkowe wartości i wstawić dane do więcej niż jednej tabeli.

W poniższym przykładzie pokazano, jak użyć procedury składowanej, aby wykonać upsert do tabeli w bazie danych programu SQL Server. Załóżmy, że dane wejściowe i zlew **Tabela marketingowa** mają po trzy kolumny: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie **kolumny ProfileID** i zastosuj ją tylko dla określonej kategorii o nazwie "ProductA".

1. W bazie danych zdefiniuj typ tabeli o takiej samej nazwie jak **sqlWriterTableType**. Schemat typu tabeli jest taki sam jak schemat zwracany przez dane wejściowe.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. W bazie danych zdefiniuj procedurę składowaną o tej samej nazwie co **sqlWriterStoredProcedureName**. Obsługuje dane wejściowe z określonego źródła i scala się z tabelą danych wyjściowych. Nazwa parametru typu tabeli w procedurze składowanej jest taka sama jak **nazwa tabeli** zdefiniowana w zestawie danych.

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

3. W usłudze Azure Data Factory zdefiniuj sekcję **ujścia MI SQL** w działaniu kopiowania w następujący sposób:

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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapowanie typu danych dla wystąpienia zarządzanego usługi Azure SQL Database

Gdy dane są kopiowane do i z wystąpienia zarządzanego bazy danych SQL azure, następujące mapowania są używane z typów danych wystąpienia zarządzanego usługi Azure SQL Database do usługi Azure Data Factory tymczasowych typów danych. Aby dowiedzieć się, jak działanie kopiowania jest mapami ze schematu źródłowego i typu danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych wystąpienia zarządzanego bazy danych SQL usługi Azure | Tymczasowy typ danych usługi Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binarny |Bajt[] |
| bit |Wartość logiczna |
| char |Ciąg, Char[] |
| date |DateTime |
| Datetime (data/godzina) |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Wartość dziesiętna |Wartość dziesiętna |
| Atrybut FILESTREAM (varbinary(max)) |Bajt[] |
| Liczba zmiennoprzecinkowa |Double |
| image |Bajt[] |
| int |Int32 |
| pieniędzy |Wartość dziesiętna |
| nchar |Ciąg, Char[] |
| Ntext |Ciąg, Char[] |
| numeryczne |Wartość dziesiętna |
| nvarchar |Ciąg, Char[] |
| rzeczywiste |Single |
| Rowversion |Bajt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Wartość dziesiętna |
| Sql_variant |Obiekt |
| tekst |Ciąg, Char[] |
| time |przedział_czasu |
| sygnatura czasowa |Bajt[] |
| tinyint |Int16 |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

>[!NOTE]
> Dla typów danych, które mapują do dziesiętnego typu tymczasowego, obecnie Usługa Azure Data Factory obsługuje dokładność do 28. Jeśli masz dane, które wymagają precyzji większej niż 28, należy rozważyć konwersję na ciąg w kwerendzie SQL.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
