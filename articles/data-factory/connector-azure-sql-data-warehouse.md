---
title: Kopiowanie i Przekształcanie danych w usłudze Azure Synapse Analytics
description: Dowiedz się, jak kopiować dane do i z usługi Azure Synapse Analytics oraz przekształcać dane w usłudze Azure Synapse Analytics przy użyciu Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/29/2021
ms.openlocfilehash: 386547aa6e815ad6ba7d860c513a3e24c4040cca
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223237"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory"></a>Kopiowanie i Przekształcanie danych w usłudze Azure Synapse Analytics przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Bieżąca wersja](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z i do usługi Azure Synapse Analytics oraz do przekształcania danych w Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej na temat Azure Data Factory, Przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Synapse Analytics jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródłowej/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku działania kopiowania ten łącznik usługi Azure Synapse Analytics obsługuje te funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i usługi Azure Active Directory (Azure AD) uwierzytelnianie tokenu aplikacji przy użyciu nazwy głównej usług lub zarządzanych tożsamości dla zasobów platformy Azure.
- Jako źródło Pobierz dane przy użyciu zapytania SQL lub procedury składowanej. Możesz również wybrać opcję kopiowania równoległego ze źródła analizy usługi Azure Synapse, aby uzyskać szczegółowe informacje, zobacz sekcję [Kopiowanie równoległe z usługi Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics) .
- Jako ujścia Załaduj dane przy użyciu instrukcji [Base](#use-polybase-to-load-data-into-azure-synapse-analytics) lub [copy](#use-copy-statement) lub INSERT BULK. Zalecamy użycie instrukcji Base lub COPY w celu uzyskania lepszej wydajności kopiowania. Łącznik obsługuje również automatyczne tworzenie tabeli docelowej, jeśli nie istnieje na podstawie schematu źródłowego.

> [!IMPORTANT]
> W przypadku kopiowania danych przy użyciu Integration Runtime Azure Data Factory należy skonfigurować [regułę zapory na poziomie serwera](../azure-sql/database/firewall-configure.md) , tak aby usługi platformy Azure mogły uzyskiwać dostęp do [serwera logicznego SQL](../azure-sql/database/logical-servers.md).
> W przypadku kopiowania danych przy użyciu własnego środowiska Integration Runtime Skonfiguruj zaporę tak, aby zezwalała na odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP maszyny, który jest używany do nawiązywania połączenia z usługą Azure Synapse Analytics.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby uzyskać najlepszą wydajność, należy użyć instrukcji Base lub COPY do ładowania danych do usługi Azure Synapse Analytics. [Użyj podstawy do ładowania danych do usługi Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) i [używania instrukcji Copy do ładowania danych do sekcji Azure Synapse Analytics](#use-copy-statement) . Aby zapoznać się z przewodnikiem dotyczącym przypadku użycia, zobacz [ładowanie 1 TB do usługi Azure Synapse Analytics na 15 minut z Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które definiują Data Factory jednostek specyficznych dla łącznika usługi Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi Azure Synapse Analytics:

| Właściwość            | Opis                                                  | Wymagane                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| typ                | Właściwość Type musi być ustawiona na wartość **AzureSqlDW**.             | Tak                                                          |
| Parametry połączenia    | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem usługi Azure Synapse Analytics dla właściwości **ConnectionString** . <br/>Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory. Klucz nazwy głównej usługi można również umieścić w Azure Key Vault i jeśli jest to uwierzytelnianie SQL, należy ściągnąć `password` konfigurację z parametrów połączenia. Zobacz przykład JSON poniżej tabeli i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak                                                          |
| servicePrincipalId  | Określ identyfikator klienta aplikacji.                         | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| dzierżaw              | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak, w przypadku używania uwierzytelniania usługi Azure AD z jednostką usługi. |
| azureCloudType | W polu Uwierzytelnianie nazwy głównej usługi Określ typ środowiska chmury platformy Azure, w którym zarejestrowano aplikację usługi Azure AD. <br/> Dozwolone wartości to **AzurePublic**, **AzureChina**, **AzureUsGovernment** i **AzureGermany**. Domyślnie używane jest środowisko chmury fabryki danych. | Nie |
| Właściwością connectvia          | [Środowisko Integration Runtime](concepts-integration-runtime.md) służy do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. | Nie                                                           |

W przypadku różnych typów uwierzytelniania zapoznaj się z poniższymi sekcjami dotyczącymi wymagań wstępnych i próbek JSON odpowiednio:

- [Uwierzytelnianie SQL](#sql-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: nazwa [główna usługi](#service-principal-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: [zarządzane tożsamości dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Podczas tworzenia połączonej usługi dla puli SQL **bezserwerowej** usługi Azure Synapse z poziomu interfejsu użytkownika wybierz opcję "wprowadź ręcznie" zamiast przeglądać z poziomu subskrypcji.

>[!TIP]
>Jeśli wystąpi błąd z kodem błędu jako "UserErrorFailedToConnectToSqlServer" i komunikatem "limit sesji dla bazy danych to XXX i został osiągnięty.", Dodaj `Pooling=false` do parametrów połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi korzystającej z uwierzytelniania SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Hasło w Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. **[Utwórz aplikację Azure Active Directoryową](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** z Azure Portal. Zanotuj nazwę aplikacji oraz następujące wartości, które definiują połączoną usługę:

   - Identyfikator aplikacji
   - Klucz aplikacji
   - Identyfikator dzierżawy

2. Jeśli jeszcze tego nie zrobiono, **[zainicjuj Azure Active Directory administratorem](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** serwera w Azure Portal. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych programu.

3. **[Utwórz użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** dla jednostki usługi. Nawiąż połączenie z magazynem danych z lub, do którego chcesz skopiować dane przy użyciu narzędzi, takich jak program SSMS, z tożsamością usługi Azure AD, która ma co najmniej zmienione uprawnienia użytkownika. Uruchom następujące T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Przyznaj jednostce usługi odpowiednie uprawnienia** , które zwykle są przeznaczone dla użytkowników SQL lub innych. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Skonfiguruj połączoną usługę Azure Synapse Analytics** w Azure Data Factory.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład połączonej usługi korzystającej z uwierzytelniania jednostki usługi

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Zarządzane tożsamości na potrzeby uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [zarządzaną tożsamością dla zasobów platformy Azure](data-factory-service-identity.md) , która reprezentuje konkretną fabrykę. Tej tożsamości zarządzanej można użyć do uwierzytelniania usługi Azure Synapse Analytics. Wydaną fabrykę mogą uzyskać dostęp do danych z lub do hurtowni danych lub je z niej kopiować przy użyciu tej tożsamości.

Aby korzystać z uwierzytelniania tożsamości zarządzanej, wykonaj następujące kroki:

1. Jeśli jeszcze tego nie zrobiono, **[Zapewnij administratorowi Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** dla serwera na Azure Portal. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli grupa ma zarządzaną tożsamość rolę administratora, Pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych programu.

2. **[Utwórz użytkowników zawartej bazy danych](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** dla Data Factory tożsamości zarządzanej. Nawiąż połączenie z magazynem danych z lub, do którego chcesz skopiować dane przy użyciu narzędzi, takich jak program SSMS, z tożsamością usługi Azure AD, która ma co najmniej zmienione uprawnienia użytkownika. Uruchom następujące T-SQL.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Przyznaj Data Factoryj zarządzanej tożsamości** , które są potrzebne w zwykły sposób dla użytkowników SQL i innych. Uruchom Poniższy kod lub zapoznaj się z innymi opcjami [tutaj](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Jeśli chcesz użyć bazy danych, aby załadować dane, zapoznaj się z [wymaganym uprawnieniem](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Skonfiguruj połączoną usługę Azure Synapse Analytics** w Azure Data Factory.

**Przykład:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) .

Następujące właściwości są obsługiwane dla zestawu danych usługi Azure Synapse Analytics:

| Właściwość  | Opis                                                  | Wymagane                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| typ      | Właściwość **Type** zestawu danych musi być ustawiona na wartość **AzureSqlDWTable**. | Tak                         |
| schema | Nazwa schematu. |Nie dla źródła, tak dla ujścia  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla ujścia  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. W przypadku nowych obciążeń Użyj `schema` i `table` . | Nie dla źródła, tak dla ujścia |

### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło i ujścia usługi Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Usługa Azure Synapse Analytics jako źródło

>[!TIP]
>Aby efektywnie ładować dane z usługi Azure Synapse Analytics przy użyciu partycjonowania danych, Dowiedz się więcej z [kopii równoległej z usługi Azure Synapse Analytics](#parallel-copy-from-azure-synapse-analytics).

Aby skopiować dane z usługi Azure Synapse Analytics, ustaw właściwość **Type** w źródle działania Copy na **SqlDWSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość                     | Opis                                                  | Wymagane |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| typ                         | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **SqlDWSource**. | Tak      |
| sqlReaderQuery               | Użyj niestandardowego zapytania SQL, aby odczytać dane. Przykład: `select * from MyTable`. | Nie       |
| sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. | Nie       |
| storedProcedureParameters    | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie       |
| isolationLevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **READCOMMITTED**, **READUNCOMMITTED**, **REPEATABLEREAD**, **Serializable**, **migawka**. Jeśli nie zostanie określony, używany jest domyślny poziom izolacji bazy danych. Aby uzyskać więcej informacji, zapoznaj się z [tym dokumentem](/dotnet/api/system.data.isolationlevel) . | Nie |
| partitionOptions | Określa opcje partycjonowania danych używane do ładowania danych z usługi Azure Synapse Analytics. <br>Dozwolone wartości to **none** (wartość domyślna), **PhysicalPartitionsOfTable** i **DynamicRange**.<br>Gdy opcja partycji jest włączona (to nie jest `None` ), stopień równoległości do współbieżnego ładowania danych z usługi Azure Synapse Analytics jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowania danych. <br>Zastosuj, gdy opcja partycji nie jest `None` . | Nie |
| **_W obszarze `partitionSettings` :_* _ | | |
| partitionColumnName | Określ nazwę kolumny źródłowej _ *w postaci liczby całkowitej lub typu daty/godziny** ( `int` ,,,,, `smallint` `bigint` `date` `smalldatetime` `datetime` , `datetime2` lub `datetimeoffset` ), która będzie używana przez partycjonowanie zakresu na potrzeby kopiowania równoległego. Jeśli nie zostanie określony, indeks lub klucz podstawowy tabeli są wykrywane i używane jako kolumny partycji.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Jeśli używasz zapytania do pobierania danych źródłowych, hak  `?AdfDynamicRangePartitionCondition ` w klauzuli WHERE. Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-azure-synapse-analytics) . | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji dla dzielenia zakresu partycji. Ta wartość jest używana do określenia krok partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.  <br>Zastosuj, gdy opcja partycji to `DynamicRange` . Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-azure-synapse-analytics) . | Nie |
| partitionLowerBound | Minimalna wartość kolumny partycji dla dzielenia zakresu partycji. Ta wartość jest używana do określenia krok partycji, a nie do filtrowania wierszy w tabeli. Wszystkie wiersze w tabeli lub wyniku zapytania zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.<br>Zastosuj, gdy opcja partycji to `DynamicRange` . Przykład można znaleźć w sekcji [Kopiowanie równoległe z bazy danych SQL](#parallel-copy-from-azure-synapse-analytics) . | Nie |

**Zwróć uwagę na następujący punkt:**

- W przypadku używania procedury składowanej w źródle do pobierania danych należy zauważyć, że procedura składowana została zaprojektowana jako zwracająca inny schemat, gdy jest przenoszona inna wartość parametru, może wystąpić błąd lub zobaczyć nieoczekiwany wynik podczas importowania schematu z interfejsu użytkownika lub kopiowania danych do bazy danych SQL przy użyciu opcji autotworzenia tabeli.

**Przykład: użycie zapytania SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Przykład: używanie procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "type": "SqlDWSource",
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

**Przykładowa procedura składowana:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics jako ujścia

Azure Data Factory obsługuje trzy sposoby ładowania danych do usługi Azure Synapse Analytics.

![Opcje kopiowania ujścia usługi Azure Synapse Analytics](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Użyj bazy](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [Użyj instrukcji COPY](#use-copy-statement)
- Użyj wstawiania zbiorczego

Najszybszym i najbardziej skalowalnym sposobem ładowania danych jest użycie instrukcji [Base](/sql/relational-databases/polybase/polybase-guide) lub [copy](/sql/t-sql/statements/copy-into-transact-sql).

Aby skopiować dane do usługi Azure Synapse Analytics, ustaw typ ujścia w działaniu Copy na **SqlDWSink**. W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| typ              | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **SqlDWSink**. | Tak                                           |
| allowPolyBase     | Wskazuje, czy baza danych ma zostać załadowana do usługi Azure Synapse Analytics. `allowCopyCommand` i `allowPolyBase` nie może mieć jednocześnie wartości true. <br/><br/>Aby uzyskać informacje o ograniczeniach i szczegółach, zobacz temat Tworzenie [bazy danych w sekcji Analiza usługi Azure Synapse](#use-polybase-to-load-data-into-azure-synapse-analytics) .<br/><br/>Dozwolone wartości to **true** i **false** (wartość domyślna). | Nie.<br/>Zastosuj podczas korzystania z bazy.     |
| polyBaseSettings  | Grupa właściwości, które można określić, gdy `allowPolybase` Właściwość ma **wartość true**. | Nie.<br/>Zastosuj podczas korzystania z bazy. |
| allowCopyCommand | Wskazuje, czy użyć [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql) do ładowania danych do usługi Azure Synapse Analytics. `allowCopyCommand` i `allowPolyBase` nie może mieć jednocześnie wartości true. <br/><br/>Zobacz [używanie instrukcji Copy do ładowania danych do usługi Azure Synapse Analytics](#use-copy-statement) w celu uzyskania ograniczeń i szczegółów.<br/><br/>Dozwolone wartości to **true** i **false** (wartość domyślna). | Nie.<br>Zastosuj, gdy jest używana kopia. |
| copyCommandSettings | Grupa właściwości, które można określić, gdy `allowCopyCommand` Właściwość ma wartość true. | Nie.<br/>Zastosuj, gdy jest używana kopia. |
| writeBatchSize    | Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/><br/>Dozwolona wartość to liczba **całkowita** (liczba wierszy). Domyślnie Data Factory dynamicznie określa odpowiedni rozmiar wsadu na podstawie rozmiaru wiersza. | Nie.<br/>Zastosuj przy użyciu wstawiania zbiorczego.     |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed przekroczeniem limitu czasu.<br/><br/>Dozwolona wartość to **TimeSpan**. Przykład: "00:30:00" (30 minut). | Nie.<br/>Zastosuj przy użyciu wstawiania zbiorczego.        |
| preCopyScript     | Określ zapytanie SQL dla działania kopiowania, które ma zostać uruchomione przed zapisaniem danych w usłudze Azure Synapse Analytics w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie                                            |
| tableOption | Określa, czy [tabela ujścia ma być automatycznie tworzona,](copy-activity-overview.md#auto-create-sink-tables) Jeśli nie istnieje na podstawie schematu źródłowego. Dozwolone wartości to: `none` (domyślnie), `autoCreate` . |Nie |
| disableMetricsCollection | Data Factory zbiera takie metryki jak Azure Synapse Analytics jednostek dwu na potrzeby optymalizacji wydajności kopiowania i zaleceń, które wprowadzają dodatkowy dostęp do bazy danych Master. W przypadku tego zachowania należy określić, `true` aby je wyłączyć. | Nie (domyślnie `false` ) |

#### <a name="azure-synapse-analytics-sink-example"></a>Przykład ujścia usługi Azure Synapse Analytics

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="parallel-copy-from-azure-synapse-analytics"></a>Kopiowanie równoległe z usługi Azure Synapse Analytics

Łącznik usługi Azure Synapse Analytics w działaniu Copy zapewnia wbudowane Partycjonowanie danych, które umożliwia równoległe kopiowanie danych. Opcje partycjonowania danych można znaleźć na karcie **Źródło** działania kopiowania.

![Zrzut ekranu przedstawiający opcje partycji](./media/connector-sql-server/connector-sql-partition-options.png)

Po włączeniu kopiowania partycjonowanego działanie Copy uruchamia zapytania równoległe względem źródła usługi Azure Synapse Analytics w celu załadowania danych przez partycje. Stopień równoległy jest kontrolowany przez [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) ustawienie działania kopiowania. Jeśli na przykład ustawisz `parallelCopies` cztery, Data Factory współbieżnie generuje i uruchamia cztery zapytania w oparciu o określoną opcję partycji i ustawienia, a każde zapytanie pobiera część danych z usługi Azure Synapse Analytics.

Przed załadowaniem dużej ilości danych z usługi Azure Synapse Analytics zaleca się włączenie kopiowania równoległego przy użyciu partycjonowania danych. Poniżej przedstawiono sugerowane konfiguracje dla różnych scenariuszy. Podczas kopiowania danych do magazynu danych opartego na plikach zaleca się zapisanie do folderu jako wielu plików (określenie tylko nazwy folderu), w którym to przypadku wydajność jest lepsza niż zapis do pojedynczego pliku.

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużej tabeli z partycjami fizycznymi.        | **Opcja partycji**: partycje fizyczne tabeli. <br><br/>Podczas wykonywania Data Factory automatycznie wykrywa partycje fizyczne i kopiuje dane przez partycje. <br><br/>Aby sprawdzić, czy tabela zawiera partycję fizyczną, można odwołać się do [tego zapytania](#sample-query-to-check-physical-partition). |
| Pełne ładowanie z dużej tabeli bez partycji fizycznych, z kolumną typu Integer lub DateTime na potrzeby partycjonowania danych. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Kolumna partycji** (opcjonalnie): określ kolumnę używaną do partycjonowania danych. Jeśli nie zostanie określony, zostanie użyta kolumna indeks lub klucz podstawowy.<br/>**Górna** granica partycji i **Dolna granica partycji** (opcjonalnie): Określ, czy chcesz określić krok partycji. Nie służy do filtrowania wierszy w tabeli, wszystkie wiersze w tabeli zostaną podzielone na partycje i skopiowane. Jeśli nie zostanie określony, działanie Copy automatycznie wykrywa wartości.<br><br>Na przykład, jeśli kolumna partycji "ID" ma wartości z zakresu od 1 do 100 i ustawimy dolną granicę jako 20 i górną granicę jako 80, z kopią równoległą jako 4, Data Factory pobiera dane przez 4 partycje-identyfikatory w zakresie <= 20, [21, 50], [51, 80] i >= 81. |
| Załaduj dużą ilość danych przy użyciu niestandardowego zapytania, bez partycji fizycznych, z kolumną liczb całkowitych lub dat/DateTime na potrzeby partycjonowania danych. | **Opcje partycji**: dynamiczna partycja zakresu.<br>**Zapytanie**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Kolumna partycji**: określ kolumnę używaną do partycjonowania danych.<br>**Górna** granica partycji i **Dolna granica partycji** (opcjonalnie): Określ, czy chcesz określić krok partycji. Nie służy do filtrowania wierszy w tabeli, wszystkie wiersze w wyniku zapytania zostaną partycjonowane i skopiowane. Jeśli nie zostanie określona, działanie Copy automatycznie wykryje wartość.<br><br>Podczas wykonywania Data Factory zastępowane `?AdfRangePartitionColumnName` rzeczywistą nazwą kolumny i zakresem wartości dla każdej partycji i wysyła do usługi Azure Synapse Analytics. <br>Na przykład, jeśli kolumna partycji "ID" ma wartości z zakresu od 1 do 100 i ustawimy dolną granicę jako 20 i górną granicę jako 80, z kopią równoległą jako 4, Data Factory pobiera dane przez 4 partycje-identyfikatory w zakresie <= 20, [21, 50], [51, 80] i >= 81. <br><br>Oto więcej przykładowych zapytań dla różnych scenariuszy:<br> 1. Zbadaj całą tabelę: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. zapytanie z tabeli z zaznaczaniem kolumn i dodatkowymi filtrami WHERE-klauzula: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. zapytanie z podzapytaniami: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. zapytanie z partycją w podzapytaniu: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Najlepsze rozwiązania dotyczące ładowania danych za pomocą opcji partycji:

1. Wybierz kolumnę odróżniającą jako kolumnę partycji (na przykład klucz podstawowy lub unikatowy klucz), aby uniknąć pochylenia danych. 
2. Jeśli tabela zawiera wbudowaną partycję, użyj opcji partycji "partycje fizyczne tabeli", aby uzyskać lepszą wydajność.
3. W przypadku używania Azure Integration Runtime do kopiowania danych można ustawić większe wartości "[Data Integration units (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4), aby użyć więcej zasobów obliczeniowych. Sprawdź odpowiednie scenariusze.
4. "[Stopień równoległości kopiowania](copy-activity-performance-features.md#parallel-copy)" kontrolowanie numerów partycji, ustawienie zbyt dużej liczby jest nadmiernie pogarszające wydajność, zalecamy ustawienie tej liczby jako (DIU lub liczba węzłów samodzielnego środowiska IR) * (od 2 do 4).
5. Uwaga Usługa Azure Synapse Analytics może jednocześnie wykonywać maksymalnie 32 zapytań, co oznacza, że ustawienie "stopień równoległości kopiowania" jest zbyt duże, co może spowodować problem z ograniczeniami Synapse.

**Przykład: pełne ładowanie z dużej tabeli z partycjami fizycznymi**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Przykład: zapytanie z dynamiczną partycją zakresu**

```json
"source": {
    "type": "SqlDWSource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Jeśli tabela zawiera partycję fizyczną, zobaczysz "HasPartition" jako "yes" (tak).

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Ładowanie danych do usługi Azure Synapse Analytics przy użyciu wielobazowego

Korzystanie z [bazy danych Base](/sql/relational-databases/polybase/polybase-guide) jest wydajnym sposobem na ładowanie dużej ilości dane do usługi Azure Synapse Analytics z wysoką przepływność. Zostanie wyświetlony duży wzrost przepływności przy użyciu sieci podstawowej zamiast domyślnego mechanizmu BULKINSERT. Przewodnik z przypadkiem użycia można znaleźć w temacie [Load 1 TB do usługi Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Jeśli dane źródłowe są w **obiekcie blob platformy Azure, Azure Data Lake Storage Gen1 lub Azure Data Lake Storage Gen2**, a **Format jest zgodny z bazą** danych, możesz użyć działania kopiowania, aby bezpośrednio wywołać usługę Azure Synapse Analytics ściągać dane ze źródła. Aby uzyskać szczegółowe informacje, zobacz **[Direct Copy przy użyciu bazy](#direct-copy-by-using-polybase)**.
- Jeśli źródłowy magazyn danych i format nie są pierwotnie obsługiwane przez bazę kodu, Użyj zamiast niej funkcji **[Base](#staged-copy-by-using-polybase)** . Funkcja kopiowania etapowego zapewnia lepszą przepływność. Dane są automatycznie konwertowane w formacie zgodnym z podstawą, są przechowywane w usłudze Azure Blob Storage, a następnie są odbierane w celu załadowania danych do usługi Azure Synapse Analytics.

> [!TIP]
> Dowiedz się więcej na temat [najlepszych rozwiązań dotyczących korzystania z bazy Base](#best-practices-for-using-polybase). W przypadku korzystania z bazy danych z Azure Integration Runtime, skuteczne [jednostki integracji (DIU)](copy-activity-performance-features.md#data-integration-units) dla bezpośredniego lub przemieszczanego magazynu do Synapse są zawsze 2. Dostrajanie DIU nie ma wpływu na wydajność, ponieważ ładowanie danych z magazynu jest obsługiwane przez aparat Synapse.

Następujące ustawienia podstawowe są obsługiwane w ramach `polyBaseSettings` działania kopiowania:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Określa liczbę lub procent wierszy, które można odrzucić przed zakończeniem wykonywania zapytania.<br/><br/>Dowiedz się więcej o opcjach odrzucania podstawowych elementów w sekcji argumentów [instrukcji CREATE External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql). <br/><br/>Dozwolone wartości to 0 (wartość domyślna), 1, 2 itd. | Nie                                            |
| rejectType        | Określa, czy opcja **rejectValue** jest wartością literału, czy wartością procentową.<br/><br/>Dozwolone wartości to **wartość** (domyślna) i **procent**. | Nie                                            |
| rejectSampleValue | Określa liczbę wierszy do pobrania przed ponownym obliczeniem procentu odrzuconych wierszy.<br/><br/>Dozwolone wartości to 1, 2 itd. | Tak, jeśli **odrzucenie** ma **wartość procentowo**. |
| useTypeDefault    | Określa, jak obsługiwać brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego.<br/><br/>Więcej informacji na temat tej właściwości znajduje się w sekcji argumentów w temacie [Create External File Format (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql).<br/><br/>Dozwolone wartości to **true** i **false** (wartość domyślna).<br><br> | Nie                                            |

### <a name="direct-copy-by-using-polybase"></a>Bezpośrednie kopiowanie przy użyciu bazy

Baza wiedzy Azure Synapse Analytics jest bezpośrednio obsługiwana przez usługę Azure Blob, Azure Data Lake Storage Gen1 i Azure Data Lake Storage Gen2. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, do kopiowania bezpośrednio z magazynu danych źródłowych do usługi Azure Synapse Analytics należy użyć bazy. W przeciwnym razie użyj opcji [kopia przygotowana z użyciem bazy](#staged-copy-by-using-polybase).

> [!TIP]
> Aby efektywnie kopiować dane do usługi Azure Synapse Analytics, Dowiedz się więcej od [Azure Data Factory sprawia, że jeszcze łatwiej i wygodnie odkrywać dane z danych przy użyciu Data Lake Store z usługą Azure Synapse Analytics](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Jeśli wymagania nie są spełnione, Azure Data Factory sprawdza ustawienia i automatycznie powraca do mechanizmu BULKINSERT na potrzeby przenoszenia danych.

1. **Źródłowa usługa połączona** z następującymi typami i metodami uwierzytelniania:

    | Obsługiwany typ magazynu danych źródłowych                             | Obsługiwany typ uwierzytelniania źródłowego                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Obiekt bob Azure](connector-azure-blob-storage.md)                | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |
    | [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | Uwierzytelnianie jednostki usługi                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >- W przypadku korzystania z uwierzytelniania tożsamości zarządzanej dla połączonej usługi magazynu należy odpowiednio poznać potrzebne konfiguracje [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
    >- Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ korzystania z punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. **Format danych źródłowych** jest **Parquet**, **Orc** lub **rozdzielany tekstem** z następującymi konfiguracjami:

   1. Ścieżka folderu nie zawiera filtru symboli wieloznacznych.
   2. Nazwa pliku jest pusta lub wskazuje na pojedynczy plik. W przypadku określenia wieloznacznej nazwy pliku w działaniu kopiowania może to być tylko `*` lub `*.*` .
   3. `rowDelimiter` jest **wartością domyślną**, **\n**, **\r\n** lub **\r**.
   4. `nullValue` jest pozostawiony jako domyślny lub ustawiony jako **pusty ciąg** ("") i `treatEmptyAsNull` jest pozostawiany jako domyślny lub ma ustawioną wartość true.
   5. `encodingName` jest pozostawiony jako domyślny lub ustawiony na **UTF-8**.
   6. `quoteChar`, `escapeChar` i `skipLineCount` nie są określone. Obsługa wielopodstawau pomija wiersz nagłówka, który można skonfigurować `firstRowAsHeader` w ramach ADF.
   7. `compression` nie może to być **kompresja**, **gzip** ani **Wklęśnięcie**.

3. Jeśli źródło jest folderem, `recursive` w działaniu Kopiuj musi być ustawiona wartość true.

4. `wildcardFolderPath` , `wildcardFilename` ,,, `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` i `additionalColumns` nie są określone.

>[!NOTE]
>Jeśli źródło jest folderem, należy zwrócić uwagę, że plik jest pobierany z folderu i wszystkich jego podfolderów, a nie pobiera danych z plików, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.), zgodnie z opisem w [miejscu ARGUMENTU lokalizacji](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Przygotowana kopia przy użyciu bazy

Jeśli dane źródłowe nie są natywnie zgodne z bazą danych Base, włącz je do kopiowania przez tymczasowy tymczasowy obiekt blob platformy Azure lub Azure Data Lake Storage Gen2 (nie może to być Premium Storage platformy Azure). W takim przypadku Azure Data Factory automatycznie konwertuje dane, aby spełniały wymagania formatu danych Base. Następnie wywołuje bazę danych Base, aby załadować dane do usługi Azure Synapse Analytics. Na koniec czyści dane tymczasowe z magazynu. Aby uzyskać szczegółowe informacje o kopiowaniu danych za pośrednictwem przemieszczania, zobacz [przygotowane kopie](copy-activity-performance-features.md#staged-copy) .

Aby użyć tej funkcji, Utwórz [połączoną usługę Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) [Azure Data Lake Storage Gen2 lub połączoną usługę](connector-azure-data-lake-storage.md#linked-service-properties) z **kluczem konta lub uwierzytelnianiem tożsamości zarządzanej** , które odwołuje się do konta usługi Azure Storage jako magazynu tymczasowego.

>[!IMPORTANT]
>- W przypadku korzystania z uwierzytelniania tożsamości zarządzanej dla połączonej usługi przejściowej należy odpowiednio poznać konfiguracje potrzebne dla [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
>- Jeśli przejściowy Magazyn Azure jest skonfigurowany z punktem końcowym usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ używania punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Najlepsze rozwiązania dotyczące korzystania z bazy

W poniższych sekcjach znajdują się najlepsze rozwiązania, które są opisane w [najlepszych rozwiązaniach dotyczących usługi Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Wymagane uprawnienie do bazy danych

Aby można było korzystać z bazy danych, użytkownik ładujący dane do usługi Azure Synapse Analytics musi mieć [uprawnienie "kontrola"](/sql/relational-databases/security/permissions-database-engine) w docelowej lokacji. Jednym ze sposobów na osiągnięcie tego jest dodanie użytkownika jako członka roli **db_owner** . Dowiedz się, jak to zrobić za pomocą [usługi Azure Synapse Analytics — Omówienie](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limity rozmiaru wierszy i typów danych

Obciążenia wielopodstawowe są ograniczone do wierszy mniejszych niż 1 MB. Nie można jej użyć do załadowania do VARCHR (MAX), NVARCHAR (MAX) lub VARBINARY (MAX). Aby uzyskać więcej informacji, zobacz [limity pojemności usługi analizy Synapse Azure](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Gdy dane źródłowe mają wiersze większe niż 1 MB, możesz chcieć podzielić tabele źródłowe na kilka małych. Upewnij się, że największy rozmiar każdego wiersza nie przekracza limitu. Mniejsze tabele mogą następnie zostać załadowane przy użyciu bazy Base i scalone ze sobą w usłudze Azure Synapse Analytics.

Alternatywnie, w przypadku danych z takimi szerokimi kolumnami, możesz użyć niebazowej do załadowania danych za pomocą funkcji ADF, wyłączając ustawienie "Zezwalaj na podstawę".

#### <a name="azure-synapse-analytics-resource-class"></a>Klasa zasobów analizy usługi Azure Synapse

Aby osiągnąć najlepszą możliwą przepływność, należy przypisać do użytkownika większą klasę zasobów, która ładuje dane do usługi Azure Synapse Analytics za pośrednictwem bazy danych.

#### <a name="polybase-troubleshooting"></a>Podstawowe rozwiązywanie problemów

**Ładowanie do kolumny dziesiętnej**

Jeśli dane źródłowe znajdują się w formacie tekstowym lub w innych niebazowych magazynach zgodnych (przy użyciu kopii etapowej i wielobazowej) i zawiera pustą wartość do załadowania do kolumny dziesiętnej usługi Azure Synapse Analytics, może wystąpić następujący błąd:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Rozwiązaniem jest wyznaczanie opcji "**Użyj typu domyślnego**" (jako FAŁSZ) w obszarze ujścia działania kopiowania — > ustawienia podstawowe. "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)" jest podstawową konfiguracją natywną, która określa, jak obsłużyć brakujące wartości w rozdzielanych plikach tekstowych, gdy baza danych pobiera dane z pliku tekstowego.

**`tableName` w usłudze Azure Synapse Analytics**

W poniższej tabeli przedstawiono przykłady sposobu określania właściwości **TableName** w zestawie danych JSON. Pokazuje kilka kombinacji nazw schematu i tabeli.

| Schemat bazy danych | Nazwa tabeli | **tabela** Właściwość JSON               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable lub dbo. MyTable lub [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable lub [dbo1]. MyTable          |
| dbo       | My. Table   | [My. Table] lub [dbo]. [My. Table]            |
| dbo1      | My. Table   | [dbo1]. [My. Table]                         |

Jeśli zostanie wyświetlony następujący błąd, problem może być wartością określoną dla właściwości **TableName** . Poprzednia tabela zawiera poprawną metodę określania wartości dla właściwości **TableName** JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumny z wartościami domyślnymi**

Obecnie funkcja wielobase w Data Factory akceptuje tylko taką samą liczbę kolumn jak w tabeli docelowej. Przykładem jest tabela z czterema kolumnami, w których jedna z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal muszą zawierać cztery kolumny. Zestaw danych wejściowych z trzema kolumnami zwraca błąd podobny do następującego komunikatu:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalną formą wartości domyślnej. Jeśli kolumna dopuszcza wartość null, dane wejściowe w obiekcie blob dla tej kolumny mogą być puste. Ale nie może być brak w wejściowym zestawie danych. Baza jest wstawiana do wartości NULL w przypadku brakujących wartości w usłudze Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> Używanie instrukcji COPY do ładowania danych do usługi Azure Synapse Analytics

[Instrukcja Copy](/sql/t-sql/statements/copy-into-transact-sql) usługi Azure Synapse Analytics bezpośrednio obsługuje ładowanie danych z **obiektów blob platformy Azure i Azure Data Lake Storage Gen2**. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, można użyć instrukcji COPY w module ADF do ładowania danych do usługi Azure Synapse Analytics. Azure Data Factory sprawdza ustawienia i uruchamia działanie kopiowania w przypadku niespełnienia kryteriów.

>[!NOTE]
>Obecnie Data Factory obsługują tylko kopiowanie ze źródeł zgodnych z instrukcją COPY wymienione poniżej.

>[!TIP]
>W przypadku używania instrukcji COPY z Azure Integration Runtime, efektywna [Jednostka integracji danych (DIU)](copy-activity-performance-features.md#data-integration-units) to zawsze 2. Dostrajanie DIU nie ma wpływu na wydajność, ponieważ ładowanie danych z magazynu jest obsługiwane przez aparat Synapse.

Użycie instrukcji COPY obsługuje następującą konfigurację:

1. **Źródłowa połączona usługa i format** są z następującymi typami i metodami uwierzytelniania:

    | Obsługiwany typ magazynu danych źródłowych                             | Obsługiwany format           | Obsługiwany typ uwierzytelniania źródłowego                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Obiekt bob Azure](connector-azure-blob-storage.md)                | [Tekst rozdzielany](format-delimited-text.md)             | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego, uwierzytelnianie nazwy głównej usługi, uwierzytelnianie tożsamości zarządzanej |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Uwierzytelnianie klucza konta, uwierzytelnianie sygnatury dostępu współdzielonego |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Tekst rozdzielany](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Uwierzytelnianie klucza konta, uwierzytelnianie jednostki usługi, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >- W przypadku korzystania z uwierzytelniania tożsamości zarządzanej dla połączonej usługi magazynu należy odpowiednio poznać potrzebne konfiguracje [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
    >- Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ korzystania z punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. Ustawienia formatu są następujące:

   1. Dla **Parquet**: `compression` nie może być **kompresją**, **przyciągiem** ani **gzip**.
   2. Dla **Orc**: `compression` nie może być **kompresji**, **```zlib```** ani **przyciągania**.
   3. Dla **tekstu rozdzielanego**:
      1. `rowDelimiter` jest jawnie ustawiona jako **pojedynczy znak** lub "**\r\n**", wartość domyślna nie jest obsługiwana.
      2. `nullValue` jest pozostawiony jako domyślny lub ustawiony jako **pusty ciąg** ("").
      3. `encodingName` jest domyślnie ustawiony na wartość **UTF-8 lub UTF-16**.
      4. `escapeChar` musi być taka sama jak `quoteChar` i nie jest pusta.
      5. `skipLineCount` jest pozostawiony jako domyślny lub ustawiony na 0.
      6. `compression` nie może to być **kompresja** ani **gzip**.

3. Jeśli źródło jest folderem, `recursive` w działaniu kopiowania musi być ustawiona wartość true i musi `wildcardFilename` być `*` . 

4. `wildcardFolderPath` , `wildcardFilename` (inne niż `*` ), `modifiedDateTimeStart` , `modifiedDateTimeEnd` , `prefix` `enablePartitionDiscovery` i `additionalColumns` nie są określone.

Następujące ustawienia instrukcji COPY są obsługiwane w ramach `allowCopyCommand` działania kopiowania:

| Właściwość          | Opis                                                  | Wymagane                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| wartości Defaults | Określa wartości domyślne dla każdej kolumny docelowej w usłudze Azure Synapse Analytics.  Wartości domyślne we właściwości zastępują domyślne ograniczenie ustawione w magazynie danych, a kolumna tożsamości nie może mieć wartości domyślnej. | Nie |
| additionalOptions | Dodatkowe opcje, które zostaną przesłane do instrukcji COPY usługi Azure Synapse Analytics bezpośrednio w klauzuli "with" w [instrukcji Copy](/sql/t-sql/statements/copy-into-transact-sql). W razie potrzeby podaj wartość, aby dostosować ją do wymagań instrukcji COPY. | Nie |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w mapowaniu przepływu danych można czytać i zapisywać w tabelach z usługi Azure Synapse Analytics. Aby uzyskać więcej informacji, zobacz [przekształcenie źródłowe](data-flow-source.md) i [przekształcanie ujścia](data-flow-sink.md) w mapowaniu przepływów danych.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Opcje źródła** transformacji źródłowej.

**Dane wejściowe** Wybierz, czy chcesz wskazać źródło w tabeli (równoważnej ```Select * from <table-name>``` ), czy wprowadzić niestandardowe zapytanie SQL.

**Włączanie przemieszczania** Zdecydowanie zaleca się użycie tej opcji w obciążeniach produkcyjnych przy użyciu źródeł analitycznych usługi Azure Synapse. Po wykonaniu [działania przepływu danych](control-flow-execute-data-flow-activity.md) z źródłami analiz usługi Azure Synapse z potoku, funkcja ADF wyświetli monit o podanie konta magazynu lokalizacji tymczasowej i będzie używać go do ładowania przemieszczania danych. Jest to najszybszy mechanizm ładowania danych z usługi Azure Synapse Analytics.

- W przypadku korzystania z uwierzytelniania tożsamości zarządzanej dla połączonej usługi magazynu należy odpowiednio poznać potrzebne konfiguracje [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
- Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ korzystania z punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).
- W przypadku używania puli SQL bez **serwera** usługi Azure Synapse jako źródła, Włączanie przemieszczania nie jest obsługiwane.

**Zapytanie**: w przypadku wybrania zapytania w polu wejściowym wprowadź zapytanie SQL dla źródła. To ustawienie przesłania każdą tabelę, która została wybrana w zestawie danych. Klauzule **order by** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanej przez użytkownika. **SELECT * FROM udfGetData ()** to format UDF w języku SQL, który zwraca tabelę. To zapytanie spowoduje utworzenie tabeli źródłowej, której można użyć w przepływie danych. Używanie zapytań jest również doskonałym sposobem zredukowania liczby wierszy do testowania lub wyszukiwania.

Przykład SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Rozmiar wsadu**: wprowadź rozmiar partii, aby podzielić duże ilości danych na odczyt. W obszarze przepływy danych moduł ADF użyje tego ustawienia, aby ustawić buforowanie w pamięci podręcznej Spark. Jest to pole opcji, które będzie używać wartości domyślnych platformy Spark, jeśli pole pozostanie puste.

**Poziom izolacji**: wartość domyślna dla źródeł SQL w mapowaniu przepływu danych jest odczytana. Poziom izolacji można zmienić tutaj na jedną z następujących wartości:

- Odczytaj zatwierdzone
- Odczytaj niezatwierdzone
- Odczyt powtarzalny
- Serializable
- Brak (Ignoruj poziom izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>Przekształcanie ujścia

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwala na operacje wstawiania. Aby zaktualizować, upsert lub usunąć wiersze, przekształcenie ALTER Row jest wymagane do tagowania wierszy dla tych działań. W przypadku aktualizacji, upserts i usunięć należy ustawić kolumnę klucza lub kolumny, aby określić, który wiersz ma być zmieniany.

**Akcja tabeli:** Określa, czy należy ponownie utworzyć lub usunąć wszystkie wiersze z tabeli docelowej przed zapisem.

- Brak: w tabeli nie zostanie wykonana żadna akcja.
- Utwórz ponownie: tabela zostanie porzucona i utworzona ponownie. Wymagane w przypadku dynamicznego tworzenia nowej tabeli.
- Obcinanie: wszystkie wiersze z tabeli docelowej zostaną usunięte.

**Włącz przemieszczanie:** Określa, czy podczas zapisywania do usługi Azure Synapse Analytics ma być używana [baza Base](/sql/relational-databases/polybase/polybase-guide) . Magazyn przemieszczania jest skonfigurowany w [działaniu wykonywania przepływu danych](control-flow-execute-data-flow-activity.md). 

- W przypadku korzystania z uwierzytelniania tożsamości zarządzanej dla połączonej usługi magazynu należy odpowiednio poznać potrzebne konfiguracje [obiektów blob platformy Azure](connector-azure-blob-storage.md#managed-identity) i [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) .
- Jeśli usługa Azure Storage jest skonfigurowana za pomocą punktu końcowego usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej z włączoną opcją "Zezwalaj na zaufaną usługę firmy Microsoft" na koncie magazynu, zapoznaj się z tematem [wpływ korzystania z punktów końcowych usługi sieci wirtualnej w usłudze Azure Storage](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

**Rozmiar wsadu**: określa, ile wierszy jest zapisywanych w każdym przedziale. Większe rozmiary partii zwiększają optymalizację kompresji i pamięci, ale grozi wyjątkami dotyczącymi pamięci podczas buforowania danych.

**Wstępne i gotowe skrypty SQL**: wprowadź wielowierszowe skrypty SQL, które zostaną wykonane przed (przetwarzanie wstępne) i po wykonaniu (po przetworzeniu) dane są zapisywane w bazie danych ujścia

![wstępne i końcowe skrypty przetwarzania SQL](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby uzyskać szczegółowe informacje na temat właściwości, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapowanie typu danych dla usługi Azure Synapse Analytics

W przypadku kopiowania danych z programu lub do usługi Azure Synapse Analytics następujące mapowania są używane z typów danych usługi Azure Synapse Analytics do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

>[!TIP]
>Zapoznaj się z tematem [typy danych w tabeli w artykule Analiza usługi Azure Synapse](../synapse-analytics/sql/develop-tables-data-types.md) na platformie Azure Synapse Analytics obsługiwane typy danych i obejścia dla nieobsługiwanych.

| Typ danych usługi Azure Synapse Analytics    | Data Factory typ danych pośrednich |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binarny                                | Byte []                         |
| bit                                   | Wartość logiczna                        |
| char                                  | String, Char []                 |
| data                                  | DateTime                       |
| Datetime (data/godzina)                              | DateTime                       |
| datetime2                             | DateTime                       |
| DateTimeOffset                        | DateTimeOffset                 |
| Liczba dziesiętna                               | Liczba dziesiętna                        |
| FILESTREAM — atrybut (varbinary (max)) | Byte []                         |
| Float                                 | Double                         |
| image (obraz)                                 | Byte []                         |
| int                                   | Int32                          |
| pieniędzy                                 | Liczba dziesiętna                        |
| nchar                                 | String, Char []                 |
| numeryczne                               | Liczba dziesiętna                        |
| nvarchar                              | String, Char []                 |
| liczba rzeczywista                                  | Pojedynczy                         |
| rowversion                            | Byte []                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Liczba dziesiętna                        |
| time                                  | przedział_czasu                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid (identyfikator GUID)                           |
| varbinary                             | Byte []                         |
| varchar                               | String, Char []                 |

## <a name="next-steps"></a>Następne kroki

Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).