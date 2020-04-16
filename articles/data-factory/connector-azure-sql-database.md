---
title: Kopiowanie i przekształcanie danych w bazie danych SQL usługi Azure
description: Dowiedz się, jak kopiować dane do i z usługi Azure SQL Database i przekształcać dane w bazie danych SQL azure przy użyciu usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: db55e685fb50c89eb850e1b9ee9dcf13d20fb614
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417539"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w bazie danych SQL usługi Azure przy użyciu usługi Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz wersję usługi Azure Data Factory, której używasz:"]
> * [Wersja 1](v1/data-factory-azure-sql-connector.md)
> * [Bieżąca wersja](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych SQL azure i do przekształcania danych w bazie danych SQL azure. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik bazy danych SQL platformy Azure jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródła/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku działania kopiowania ten łącznik bazy danych SQL platformy Azure obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu jednostki usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Jako źródło pobieranie danych przy użyciu kwerendy SQL lub procedury składowanej.
- Jako ujście dołączanie danych do tabeli docelowej lub wywoływanie procedury składowanej z niestandardową logiką podczas kopiowania.

>[!NOTE]
>Usługa Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) nie jest teraz obsługiwana przez ten łącznik. Aby obejść, można użyć [ogólnego łącznika ODBC](connector-odbc.md) i sterownika ODBC programu SQL Server za pośrednictwem środowiska wykonawczego integracji hostowanego samodzielnie. Postępuj zgodnie z [tymi wskazówkami](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) z konfiguracjami pobierania sterowników ODBC i ciągów połączeń.

> [!IMPORTANT]
> Jeśli kopiujesz dane przy użyciu środowiska wykonawczego integracji usługi Azure Data Factory, skonfiguruj [zaporę programu Azure SQL Server,](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) aby usługi platformy Azure mogły uzyskać dostęp do serwera.
> Jeśli kopiujesz dane przy użyciu środowiska uruchomieniowego integracji hostowanego samodzielnie, skonfiguruj zaporę programu Azure SQL Server, aby zezwoliła na odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP komputera, który jest używany do łączenia się z usługą Azure SQL Database.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Azure Data Factory specyficznych dla łącznika bazy danych SQL platformy Azure.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Te właściwości są obsługiwane dla usługi połączonej usługi Azure SQL Database:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **AzureSqlDatabase**. | Tak |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z wystąpieniem bazy danych SQL platformy Azure dla właściwości **connectionString.** <br/>Można również umieścić hasło lub klucz jednostkowy usługi w usłudze Azure Key Vault. Jeśli jest to uwierzytelnianie `password` SQL, pociągnij konfigurację z ciągu połączenia. Aby uzyskać więcej informacji, zobacz przykład JSON po tabeli i [poświadczenia sklepu w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Tak |
| servicePrincipalId | Określ identyfikator klienta aplikacji. | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako **SecureString,** aby bezpiecznie przechowywać go w usłudze Azure Data Factory lub [odwoływać się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| Dzierżawy | Określ informacje o dzierżawie, takie jak nazwa domeny lub identyfikator dzierżawy, w ramach którego znajduje się aplikacja. Pobierz go, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi |
| connectVia | To [środowisko wykonawcze integracji](concepts-integration-runtime.md) jest używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska uruchomieniowego integracji hostowanego samodzielnie, jeśli magazyn danych znajduje się w sieci prywatnej. Jeśli nie zostanie określony, używany jest domyślny środowiska wykonawczego integracji platformy Azure. | Nie |

W przypadku różnych typów uwierzytelniania należy zapoznać się z następującymi sekcjami dotyczącymi wymagań wstępnych i przykładów JSON:

- [Uwierzytelnianie SQL](#sql-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: podmiot zabezpieczeń usługi](#service-principal-authentication)
- [Uwierzytelnianie tokenu aplikacji usługi Azure AD: tożsamości zarządzane dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli natkniesz się na błąd z kodem błędu "UserErrorFailedToConnectToSqlServer" i komunikatem typu `Pooling=false` "Limit sesji dla bazy danych jest XXX i został osiągnięty", dodaj do ciągu połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, która używa uwierzytelniania SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Hasło w usłudze Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. [Utwórz aplikację usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) z witryny Azure portal. Zanotuj nazwę aplikacji i następujące wartości definiujące usługę połączone:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. [Aprowizuj administratora usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) dla serwera SQL Server platformy Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD musi być użytkownikiem usługi Azure AD lub grupą usługi Azure AD, ale nie może być jednostką usługi. Ten krok jest wykonywany tak, że w następnym kroku można użyć tożsamości usługi Azure AD, aby utworzyć użytkownika zawartej bazy danych dla jednostki usługi.

3. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla jednostki usługi. Połącz się z bazą danych z lub do którego chcesz skopiować dane przy użyciu narzędzi, takich jak SQL Server Management Studio, z tożsamością usługi Azure AD, która ma co najmniej alter dowolnego użytkownika uprawnienia. Uruchom następujący program T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Udziel jednostki usługi potrzebne uprawnienia, jak zwykle w przypadku użytkowników SQL lub innych. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Konfigurowanie połączonej usługi azure SQL Database w usłudze Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład usługi połączonej, która używa uwierzytelniania głównego usługi

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md) która reprezentuje określoną fabrykę danych. Tej tożsamości zarządzanej można użyć do uwierzytelniania usługi Azure SQL Database. Wyznaczona fabryka może uzyskiwać dostęp do danych z bazy danych lub do niej do bazy danych przy użyciu tej tożsamości.

Aby użyć uwierzytelnienia tożsamości zarządzanej, wykonaj następujące kroki.

1. [Aprowizuj administratora usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) dla serwera SQL Server platformy Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli przyznasz grupie z zarządzaną tożsamością rolę administratora, pomiń kroki 3 i 4. Administrator ma pełny dostęp do bazy danych.

2. [Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) dla tożsamości zarządzanej usługi Azure Data Factory. Połącz się z bazą danych z lub do którego chcesz skopiować dane przy użyciu narzędzi, takich jak SQL Server Management Studio, z tożsamością usługi Azure AD, która ma co najmniej alter dowolnego użytkownika uprawnienia. Uruchom następujący program T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Udziel tożsamości zarządzanej przez usługę Data Factory, wymaganych uprawnień, jak zwykle dla użytkowników SQL i innych osób. Uruchom następujący kod. Aby uzyskać więcej opcji, zobacz [ten dokument](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Konfigurowanie połączonej usługi azure SQL Database w usłudze Azure Data Factory.

**Przykład**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Następujące właściwości są obsługiwane dla zestawu danych usługi Azure SQL Database:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **AzureSqlTable**. | Tak |
| Schematu | Nazwa schematu. |Nie dla źródła, tak dla zlewu  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla zlewu  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `schema` `table`obciążenia, użycia i programu . | Nie dla źródła, tak dla zlewu |

#### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujście bazy danych SQL azure.

### <a name="azure-sql-database-as-the-source"></a>Usługa Azure SQL Database jako źródło

Aby skopiować dane z usługi Azure SQL Database, następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **AzureSqlSource**. Typ "SqlSource" jest nadal obsługiwany ze zgodnością z powrotem. | Tak |
| sqlReaderQuery | Ta właściwość używa niestandardowej kwerendy SQL do odczytu danych. Może to być na przykład `select * from MyTable`. | Nie |
| nazwa sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. | Nie |
| przechowywaneParametryprocedure | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| Isolationlevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Więcej informacji można znaleźć [w tym dok.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nie |

**Uwagi do uwag:**

- Jeśli **sqlReaderQuery** jest określony dla **usługi AzureSqlSource**, działanie kopiowania uruchamia tę kwerendę względem źródła bazy danych SQL Azure, aby uzyskać dane. Można również określić procedurę składowaną, określając **sqlReaderStoredProcedureName** i **storedProcedureParameters,** jeśli procedura składowana przyjmuje parametry.
- Jeśli nie określisz **sqlReaderQuery** lub **sqlReaderStoredProcedureName**, kolumny zdefiniowane w sekcji "struktura" zestawu danych JSON są używane do konstruowania kwerendy. Kwerenda `select column1, column2 from mytable` jest uruchamiana w bazie danych SQL Azure. Jeśli definicja zestawu danych nie ma "struktury", wszystkie kolumny są wybierane z tabeli.

#### <a name="sql-query-example"></a>Przykład kwerendy SQL

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

### <a name="azure-sql-database-as-the-sink"></a>Usługa Azure SQL Database jako ujście

> [!TIP]
> Dowiedz się więcej o obsługiwanych zachowaniach zapisu, konfiguracjach i najlepszych praktykach z [najlepszych rozwiązań dotyczących ładowania danych do bazy danych SQL Azure.](#best-practice-for-loading-data-into-azure-sql-database)

Aby skopiować dane do usługi Azure SQL Database, następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** ujścia działania kopiowania musi być ustawiona na **AzureSqlSink**. Typ "SqlSink" jest nadal obsługiwany ze zgodnością z powrotem. | Tak |
| writeBatchSize | Liczba wierszy do wstawienia do tabeli SQL *na partię*.<br/> Dozwoloną wartością jest **liczba całkowita** (liczba wierszy). Domyślnie usługa Azure Data Factory dynamicznie określa odpowiedni rozmiar partii na podstawie rozmiaru wiersza. | Nie |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed jej przesuniem czasu.<br/> Dozwoloną wartością jest **timespan**. Przykładem jest "00:30:00" (30 minut). | Nie |
| preCopyScript | Określ kwerendę SQL dla działania kopiowania do uruchomienia przed zapisaniem danych w bazie danych SQL Azure. Jest wywoływana tylko raz na uruchomienie kopii. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| nazwa sqlWriterStoredProcedureName | Nazwa procedury składowanej, która definiuje sposób stosowania danych źródłowych do tabeli docelowej. <br/>Ta procedura *składowana jest wywoływana na partię*. W przypadku operacji, które są uruchamiane tylko raz i nie mają nic `preCopyScript` wspólnego z danymi źródłowymi, na przykład usuń lub obcięć, użyj właściwości. | Nie |
| przechowywaneProcedureTableTypeParameterName |Nazwa parametru typu tabeli określonego w procedurze składowanej.  |Nie |
| sqlWriterTableType (Typ tabeli maszyn sqlWriterTableType) |Nazwa typu tabeli, która ma być używana w procedurze składowanej. Działanie kopiowania sprawia, że dane są przenoszone dostępne w tabeli tymczasowej z tego typu tabeli. Kod procedury składowanej można następnie scalić dane, które są kopiowane z istniejącymi danymi. |Nie |
| przechowywaneParametryprocedure |Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw i wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie |
| tableOpcja | Określa, czy tabela ujścia ma być automatycznie tworzędliwa, jeśli nie istnieje na podstawie schematu źródłowego. Automatyczne tworzenie tabeli nie jest obsługiwane, gdy zlew określa procedurę składowaną lub kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (domyślnie), `autoCreate`. |Nie |
| disableMetricsCollection | Fabryka danych zbiera metryki, takie jak usługi DTU bazy danych SQL azure do optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, `true` określ, aby go wyłączyć. | Nie (domyślnie jest) `false` |

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
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Przykład 2: Wywołanie procedury składowanej podczas kopiowania**

Dowiedz się więcej szczegółów z [Wywołać procedurę składowaną z ujścia SQL](#invoke-a-stored-procedure-from-a-sql-sink).

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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Najważniejsze wskazówki dotyczące ładowania danych do bazy danych SQL usługi Azure

Podczas kopiowania danych do usługi Azure SQL Database może być wymagane inne zachowanie zapisu:

- [Dołącz](#append-data): Moje dane źródłowe mają tylko nowe rekordy.
- [Upsert](#upsert-data): Moje dane źródłowe mają zarówno wstawia i aktualizacje.
- [Zastąp](#overwrite-the-entire-table): Chcę za każdym razem przeładować całą tabelę wymiarów.
- [Pisanie z niestandardową logiką:](#write-data-with-custom-logic)Potrzebuję dodatkowego przetwarzania przed ostatecznym wstawieniem do tabeli docelowej.

Zapoznaj się z odpowiednimi sekcjami dotyczącymi konfigurowania usługi Azure Data Factory i najlepszych rozwiązań.

### <a name="append-data"></a>Dołączanie danych

Dołączanie danych jest domyślnym zachowaniem tego łącznika ujścia bazy danych SQL platformy Azure. Usługa Azure Data Factory wykonuje zbiorcze wstawianie, aby skutecznie zapisywać w tabeli. Można skonfigurować źródło i sink odpowiednio w działania kopiowania.

### <a name="upsert-data"></a>Wykonywanie operacji upsert dla danych

**Wariant 1:** Jeśli masz dużą ilość danych do skopiowania, użyj następującej metody, aby wykonać ofertę dodatkową: 

- Najpierw należy użyć [tabeli tymczasowej o zakresie bazy danych,](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) aby zbiorczo załadować wszystkie rekordy przy użyciu działania kopiowania. Ponieważ operacje względem tabel tymczasowych o zakresie bazy danych nie są rejestrowane, można załadować miliony rekordów w ciągu kilku sekund.
- Uruchom działanie procedury składowanej w usłudze Azure Data Factory, aby zastosować instrukcję [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) lub INSERT/UPDATE. Użyj tabeli tymczasowej jako źródła, aby wykonać wszystkie aktualizacje lub wstawia jako pojedynczą transakcję. W ten sposób zmniejsza się liczba operacji w obie strony i dzienników. Po zakończeniu działania procedury składowanej tabela tymczasowa może zostać obcięta, aby była gotowa do następnego cyklu upsert.

Na przykład w usłudze Azure Data Factory można utworzyć potok z **działaniem kopiowania** połączone z **działaniem procedury przechowywanej.** Pierwszy kopiuje dane z magazynu źródłowego do tabeli tymczasowej usługi Azure SQL Database, na przykład **##UpsertTempTable**, jako nazwę tabeli w zestawie danych. Następnie ten ostatni wywołuje procedurę składowaną, aby scalić dane źródłowe z tabeli tymczasowej do tabeli docelowej i oczyścić tabelę tymczasową.

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

**Wariant 2:** Można również [wywołać procedurę składowaną w ramach działania kopiowania](#invoke-a-stored-procedure-from-a-sql-sink). Takie podejście uruchamia każdą partię `writeBatchSize` (zgodnie z zasadami właściwości) w tabeli źródłowej zamiast przy użyciu zbiorczego wstawiania jako domyślnego podejścia w działaniu kopiowania.

### <a name="overwrite-the-entire-table"></a>Zastępowanie całej tabeli

Właściwość **preCopyScript** można skonfigurować w ujściu działania kopiowania. W takim przypadku dla każdego działania kopiowania, który jest uruchamiany, usługa Azure Data Factory uruchamia najpierw skrypt. Następnie uruchamia kopię, aby wstawić dane. Na przykład, aby zastąpić całą tabelę najnowszymi danymi, określ skrypt, aby najpierw usunąć wszystkie rekordy przed zbiorczym załadowaniem nowych danych ze źródła.

### <a name="write-data-with-custom-logic"></a>Zapis danych za pomocą niestandardowej logiki

Kroki zapisywania danych za pomocą logiki niestandardowej są podobne do opisanych w sekcji [Dane Upsert.](#upsert-data) Jeśli musisz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej, na dużą skalę można wykonać jedną z dwóch czynności:

- Wczytaj do tabeli tymczasowej o określonym zakresie bazy danych, a następnie wywołaj procedurę składowaną. 
- Wywołać procedurę składowaną podczas kopiowania.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a>Wywoływanie procedury składowanej z ujścia SQL

Podczas kopiowania danych do usługi Azure SQL Database można również skonfigurować i wywołać procedurę składowaną określoną przez użytkownika z dodatkowymi parametrami dla każdej partii tabeli źródłowej. Funkcja procedury składowanej korzysta z [parametrów wycenionych w tabeli](https://msdn.microsoft.com/library/bb675163.aspx).

Można użyć procedury składowanej, gdy wbudowane mechanizmy kopiowania nie służą do celu. Przykładem jest, gdy chcesz zastosować dodatkowe przetwarzanie przed ostatecznym wstawieniem danych źródłowych do tabeli docelowej. Niektóre dodatkowe przykłady przetwarzania są, gdy chcesz scalić kolumny, wyszukać dodatkowe wartości i wstawić do więcej niż jednej tabeli.

W poniższym przykładzie pokazano, jak użyć procedury składowanej, aby wykonać upsert do tabeli w usłudze Azure SQL Database. Załóżmy, że dane wejściowe i zlew **Tabela marketingowa** mają po trzy kolumny: **ProfileID**, **State**i **Category**. Wykonaj upsert na podstawie **kolumny ProfileID** i zastosuj ją tylko dla określonej kategorii o nazwie "ProductA".

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

3. W usłudze Azure Data Factory zdefiniuj sekcję **ujścia SQL** w działaniu kopiowania w następujący sposób:

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

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania, można odczytywać i zapisywać do tabel z usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w przepływach danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla usługi Azure SQL Database są dostępne na karcie **Opcje źródłowe** transformacji źródła. 

**Wejście:** Wybierz, czy punktujesz źródło przy ```Select * from <table-name>```tabeli (odpowiednik) czy wprowadzasz niestandardową kwerendę SQL.

**Kwerenda:** Jeśli w polu wejściowym zostanie wybrana kwerenda, wprowadź kwerendę SQL dla źródła. To ustawienie zastępuje dowolną tabelę wybraną w zestawie danych. **Klauzule Order By** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanych przez użytkownika. **select * z udfGetData()** jest UDF w języku SQL, który zwraca tabelę. Ta kwerenda spowoduje powstanie tabeli źródłowej, która będzie używana w przepływie danych. Korzystanie z zapytań jest również doskonałym sposobem, aby zmniejszyć wiersze do testowania lub wyszukiwania. 

* Przykład SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Rozmiar partii:** Wprowadź rozmiar partii, aby podzielić duże dane na odczyty.

**Poziom izolacji:** Domyślna wartość źródeł SQL w przepływie danych mapowania jest odczytywana niezatwierdzone. W tym miejscu można zmienić poziom izolacji na jedną z następujących wartości:
* Odczytu zatwierdzonego
* Odczyt niezatwierdzony
* Powtarzalny odczyt
* Serializacji
* Brak (ignorowanie poziomu izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

### <a name="sink-transformation"></a>Transformacja zlewu

Ustawienia specyficzne dla usługi Azure SQL Database są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwalaj tylko na wstawia. Aby zaktualizować, upsert lub usunąć wiersze, transformacja alter-rowu jest wymagana do oznaczania wierszy dla tych akcji. W przypadku aktualizacji, upserts i deletes, kolumna klucza lub kolumny muszą być ustawione, aby określić, który wiersz do zmiany.

![Kolumny kluczy](media/data-flow/keycolumn.png "Kolumny kluczy")

Nazwa kolumny, którą wybierzesz jako klucz w tym miejscu, będzie używana przez podajnik ADF jako część kolejnej aktualizacji, upsert, delete. W związku z tym należy wybrać kolumnę, która istnieje w mapowaniu ujścia. Jeśli nie chcesz zapisywać wartości w tej kolumnie klucza, kliknij przycisk "Pomiń kolumny kluczy".

**Akcja tabeli:** Określa, czy wszystkie wiersze mają być ponownietworzone, czy usuwane z tabeli docelowej przed zapisaniem.
* Brak: Nie zostaną wykonane żadne działania w tabeli.
* Odtwórz: Tabela zostanie upuszczona i odtworzona. Wymagane, jeśli dynamicznie tworzysz nową tabelę.
* Obcinanie: Wszystkie wiersze z tabeli docelowej zostaną usunięte.

**Rozmiar partii**: Określa, ile wierszy jest zapisywanych w każdym zasobniku. Większe rozmiary partii poprawić kompresji i optymalizacji pamięci, ale ryzyko z wyjątków pamięci podczas buforowania danych.

**Skrypty SQL przed i po opublikowaniu:** Wprowadź wielowierszowe skrypty SQL, które będą wykonywane przed zapisaniem danych (przetwarzania wstępnego) i po zapisaniu danych (przetwarzania końcowego) w bazie danych sink

![skrypty przetwarzania sql przed i po](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapowanie typów danych dla bazy danych SQL usługi Azure

Gdy dane są kopiowane z lub do usługi Azure SQL Database, następujące mapowania są używane z typów danych usługi Azure SQL Database do tymczasowych typów danych usługi Azure Data Factory. Aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia, zobacz [Mapowanie schematu i typów danych](copy-activity-schema-and-type-mapping.md).

| Typ danych bazy danych SQL usługi Azure | Tymczasowy typ danych usługi Azure Data Factory |
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
| tinyint |Byte |
| uniqueidentifier |Guid (identyfikator GUID) |
| varbinary |Bajt[] |
| varchar |Ciąg, Char[] |
| xml |Xml |

>[!NOTE]
> Dla typów danych, które mapują do dziesiętnego typu tymczasowego, obecnie Usługa Azure Data Factory obsługuje dokładność do 28. Jeśli masz dane z dokładnością większą niż 28, należy rozważyć konwersję na ciąg w zapytaniu SQL.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
