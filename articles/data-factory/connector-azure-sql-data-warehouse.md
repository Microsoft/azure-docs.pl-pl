---
title: Kopiowanie i przekształcanie danych w usłudze Azure Synapse Analytics
description: Dowiedz się, jak kopiować dane do i z usługi Azure Synapse Analytics i przekształcać dane w usłudze Azure Synapse Analytics przy użyciu usługi Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417577"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiowanie i przekształcanie danych w usłudze Azure Synapse Analytics (dawniej Usługa Azure SQL Data Warehouse) przy użyciu usługi Azure Data Factory 

> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Bieżąca wersja](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Synapse Analytics oraz do przekształcania danych w usłudze Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure SQL Data Warehouse oraz do przekształcania danych w usłudze Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do usługi Azure Synapse Analytics oraz do przekształcania danych w usłudze Azure Data Lake Storage Gen2. Aby dowiedzieć się więcej o usłudze Azure Data Factory, przeczytaj [artykuł wprowadzający](introduction.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Synapse Analytics jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną tabelą macierzy źródła/ujścia](copy-activity-overview.md)
- [Mapowanie przepływu danych](concepts-data-flow-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)
- [Działanie GetMetadata](control-flow-get-metadata-activity.md)

W przypadku działania kopiowania ten łącznik usługi Azure Synapse Analytics obsługuje następujące funkcje:

- Kopiowanie danych przy użyciu uwierzytelniania SQL i uwierzytelniania tokenu aplikacji usługi Azure Active Directory (Azure AD) przy użyciu jednostki usługi lub tożsamości zarządzanych dla zasobów platformy Azure.
- Jako źródło pobierz dane przy użyciu kwerendy SQL lub procedury składowanej.
- Jako ujście, załadować dane przy użyciu [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) lub [COPY instrukcji](#use-copy-statement) (podgląd) lub wstawić zbiorczo. Firma Microsoft zaleca PolyBase lub COPY instrukcji (podgląd) dla lepszej wydajności kopiowania.

> [!IMPORTANT]
> Jeśli kopiujesz dane przy użyciu środowiska wykonawczego integracji usługi Azure Data Factory Integration, skonfiguruj [zaporę serwera SQL platformy Azure,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) aby usługi platformy Azure mogły uzyskać dostęp do serwera.
> Jeśli kopiujesz dane przy użyciu środowiska uruchomieniowego integracji hostowanego samodzielnie, skonfiguruj zaporę serwera SQL platformy Azure, aby zezwoliła na odpowiedni zakres adresów IP. Ten zakres obejmuje adres IP komputera, który jest używany do łączenia się z usługą Azure Synapse Analytics.

## <a name="get-started"></a>Rozpoczęcie pracy

> [!TIP]
> Aby osiągnąć najlepszą wydajność, użyj PolyBase, aby załadować dane do usługi Azure Synapse Analytics. [Sekcja Użyj polybase do ładowania danych do usługi Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) sekcja zawiera szczegółowe informacje. Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure Synapse Analytics w ramach 15 minut za pomocą usługi Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach definiujących jednostki usługi Data Factory specyficzne dla łącznika usługi Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Synapse Analytics:

| Właściwość            | Opis                                                  | Wymagany                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Właściwość typu musi być ustawiona na **AzureSqlDW**.             | Tak                                                          |
| Parametry połączenia    | Określ informacje potrzebne do nawiązania połączenia z wystąpieniem usługi Azure Synapse Analytics dla właściwości **connectionString.** <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych. Można również umieścić klucz główny hasła/usługi w usłudze Azure Key `password` Vault, a jeśli jest to uwierzytelnianie SQL wyciągnąć konfigurację z ciągu połączenia. Zobacz przykład JSON poniżej tabeli i [poświadczenia sklepu w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykuł z więcej szczegółów. | Tak                                                          |
| servicePrincipalId  | Określ identyfikator klienta aplikacji.                         | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi. |
| servicePrincipalKey | Określ klucz aplikacji. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi. |
| Dzierżawy              | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak, podczas korzystania z uwierzytelniania usługi Azure AD z jednostką usługi. |
| connectVia          | [Środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które ma być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. | Nie                                                           |

W przypadku różnych typów uwierzytelniania należy zapoznać się z następującymi sekcjami dotyczącymi wymagań wstępnych i przykładów JSON:

- [Uwierzytelnianie SQL](#sql-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: [podmiot zabezpieczeń usługi](#service-principal-authentication)
- Uwierzytelnianie tokenu aplikacji usługi Azure AD: [tożsamości zarządzane dla zasobów platformy Azure](#managed-identity)

>[!TIP]
>Jeśli zostanie wyświetlony błąd z kodem błędu jako "UserErrorFailedToConnectToSqlServer" i komunikatem w stylu `Pooling=false` "Limit sesji dla bazy danych to XXX i został osiągnięty.", dodaj do ciągu połączenia i spróbuj ponownie.

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Przykład połączonej usługi, która używa uwierzytelniania SQL

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

**Hasło w usłudze Azure Key Vault:**

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

Aby użyć uwierzytelniania tokenu aplikacji usługi Azure AD opartym na jednostkach usługi, wykonaj następujące kroki:

1. **[Utwórz aplikację usługi Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** z witryny Azure portal. Zanotuj nazwę aplikacji i następujące wartości definiujące usługę połączone:

    - Identyfikator aplikacji
    - Klucz aplikacji
    - Identyfikator dzierżawy

2. **[Aprowizuj administratora usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** dla serwera SQL platformy Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli przyznasz grupie z zarządzaną tożsamością rolę administratora, pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych.

3. **[Tworzenie użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** dla jednostki usługi. Połącz się z magazynem danych z lub do którego chcesz skopiować dane przy użyciu narzędzi, takich jak SSMS, z tożsamością usługi Azure AD, która ma co najmniej alter dowolnego użytkownika uprawnienia. Uruchom następujący program T-SQL:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Udziel jednostki usługi potrzebne uprawnienia,** jak zwykle w przypadku użytkowników SQL lub innych. Uruchom poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz załadować dane za pomocą polybase, zapoznaj się z [wymaganym uprawnieniem bazy danych.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Konfigurowanie połączonej usługi Azure Synapse Analytics** w usłudze Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Przykład usługi połączonej, która używa uwierzytelniania głównego usługi

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Tożsamości zarządzane do uwierzytelniania zasobów platformy Azure

Fabryka danych może być skojarzona z [tożsamością zarządzaną dla zasobów platformy Azure,](data-factory-service-identity.md) która reprezentuje określoną fabrykę. Tej tożsamości zarządzanej można użyć do uwierzytelniania usługi Azure Synapse Analytics. Wyznaczona fabryka może uzyskiwać dostęp do danych z magazynu danych lub do niego do magazynu danych przy użyciu tej tożsamości.

Aby użyć uwierzytelnienia tożsamości zarządzanej, wykonaj następujące kroki:

1. **[Aprowizuj administratora usługi Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** dla serwera SQL platformy Azure w witrynie Azure portal, jeśli jeszcze tego nie zrobiono. Administrator usługi Azure AD może być użytkownikiem usługi Azure AD lub grupą usługi Azure AD. Jeśli przyznasz grupie z zarządzaną tożsamością rolę administratora, pomiń kroki 3 i 4. Administrator będzie miał pełny dostęp do bazy danych.

2. **[Utwórz użytkowników zawartej bazy danych](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** dla tożsamości zarządzanej fabrycznie danych. Połącz się z magazynem danych z lub do którego chcesz skopiować dane przy użyciu narzędzi, takich jak SSMS, z tożsamością usługi Azure AD, która ma co najmniej alter dowolnego użytkownika uprawnienia. Uruchom następujący T-SQL. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Udziel tożsamości zarządzanej fabrycznie danych potrzebne uprawnienia,** jak zwykle w przypadku użytkowników SQL i innych. Uruchom poniższy kod lub zapoznaj się z innymi opcjami [tutaj](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Jeśli chcesz załadować dane za pomocą polybase, zapoznaj się z [wymaganym uprawnieniem bazy danych.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Konfigurowanie połączonej usługi Azure Synapse Analytics** w usłudze Azure Data Factory.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [Zestawy danych.](concepts-datasets-linked-services.md) 

Następujące właściwości są obsługiwane dla zestawu danych Usługi Azure Synapse Analytics:

| Właściwość  | Opis                                                  | Wymagany                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Właściwość **typu** zestawu danych musi być ustawiona na **AzureSqlDWTable**. | Tak                         |
| Schematu | Nazwa schematu. |Nie dla źródła, tak dla zlewu  |
| tabela | Nazwa tabeli/widoku. |Nie dla źródła, tak dla zlewu  |
| tableName | Nazwa tabeli/widoku ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. W przypadku nowego `schema` `table`obciążenia, użycia i programu . | Nie dla źródła, tak dla zlewu |

#### <a name="dataset-properties-example"></a>Przykład właściwości zestawu danych

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

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło usługi Azure Synapse Analytics i ujście.

### <a name="azure-synapse-analytics-as-the-source"></a>Usługa Azure Synapse Analytics jako źródło

Aby skopiować dane z usługi Azure Synapse Analytics, ustaw właściwość **typu** w źródle działania kopiowania na **SqlDWSource**. Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość                     | Opis                                                  | Wymagany |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **SqlDWSource**. | Tak      |
| sqlReaderQuery               | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Przykład: `select * from MyTable`. | Nie       |
| nazwa sqlReaderStoredProcedureName | Nazwa procedury składowanej, która odczytuje dane z tabeli źródłowej. Ostatnia instrukcja SQL musi być instrukcją SELECT w procedurze składowanej. | Nie       |
| przechowywaneParametryprocedure    | Parametry procedury składowanej.<br/>Dozwolone wartości to pary nazw lub wartości. Nazwy i wielkość liter parametrów muszą być zgodne z nazwami i wielkością liter parametrów procedury składowanej. | Nie       |
| Isolationlevel | Określa zachowanie blokowania transakcji dla źródła SQL. Dozwolone wartości to: **ReadCommitted** (default), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Więcej informacji można znaleźć [w tym dok.](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) | Nie |

**Przykład: używanie kwerendy SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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

**Przykład: użycie procedury składowanej**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Usługa Azure Synapse Analytics jako zlew

Usługa Azure Data Factory obsługuje trzy sposoby ładowania danych do usługi SQL Data Warehouse.

![Opcje kopiowania ujścia SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Korzystanie z Bazy PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Użyj instrukcji COPY (wersja zapoznawcza)](#use-copy-statement)
- Użyj wstawiania zbiorczego

Najszybszym i najbardziej skalowalnym sposobem ładowania danych jest [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) lub [copy instrukcji](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (podgląd).

Aby skopiować dane do usługi Azure SQL Data Warehouse, należy ustawić typ ujścia w działaniu kopiowania na **SqlDWSink**. Następujące właściwości są obsługiwane w sekcji **Zlewnik działania kopiowania:**

| Właściwość          | Opis                                                  | Wymagany                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Właściwość **typu** ujścia działania kopiowania musi być ustawiona na **SqlDWSink**. | Tak                                           |
| allowPolyBase     | Wskazuje, czy używać PolyBase do ładowania danych do magazynu danych SQL. `allowCopyCommand`i `allowPolyBase` nie może być zarówno prawdziwe. <br/><br/>Aby uzyskać ograniczenia i szczegóły, zobacz [Korzystanie z bazy danych PolyBase w](#use-polybase-to-load-data-into-azure-sql-data-warehouse) celu załadowania danych do sekcji usługi Azure SQL Data Warehouse.<br/><br/>Dozwolone wartości to **Prawda** i **Fałsz** (domyślnie). | Nie.<br/>Zastosuj podczas korzystania z PolyBase.     |
| lyBaseSettings (190)  | Grupa właściwości, które można określić, gdy właściwość jest ustawiona `allowPolybase` na **true**. | Nie.<br/>Zastosuj podczas korzystania z PolyBase. |
| allowCopyCommand | Wskazuje, czy do załadowania danych do magazynu danych SQL ma być używana [instrukcja COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza). `allowCopyCommand`i `allowPolyBase` nie może być zarówno prawdziwe. <br/><br/>Aby uzyskać ograniczenia i szczegóły, zobacz [Użyj instrukcji COPY, aby załadować dane do](#use-copy-statement) sekcji usługi Azure SQL Data Warehouse.<br/><br/>Dozwolone wartości to **Prawda** i **Fałsz** (domyślnie). | Nie.<br>Zastosuj podczas korzystania z KOPIUJ. |
| copyCommandSettings | Grupa właściwości, które można określić, gdy `allowCopyCommand` właściwość jest ustawiona na WARTOŚĆ PRAWDA. | Nie.<br/>Zastosuj podczas korzystania z KOPIUJ. |
| writeBatchSize    | Liczba wierszy do wstawienia do tabeli SQL **na partię**.<br/><br/>Dozwoloną wartością jest **liczba całkowita** (liczba wierszy). Domyślnie usługa Data Factory dynamicznie określa odpowiedni rozmiar partii na podstawie rozmiaru wiersza. | Nie.<br/>Stosować podczas używania wstawiania zbiorczego.     |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed jej przesuniem czasu.<br/><br/>Dozwoloną wartością jest **timespan**. Przykład: "00:30:00" (30 minut). | Nie.<br/>Stosować podczas używania wstawiania zbiorczego.        |
| preCopyScript     | Określ kwerendę SQL dla działania kopiowania do uruchomienia przed zapisaniem danych w usłudze Azure SQL Data Warehouse w każdym uruchomieniu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie                                            |
| tableOpcja | Określa, czy tabela ujścia ma być automatycznie tworzędliwa, jeśli nie istnieje na podstawie schematu źródłowego. Automatyczne tworzenie tabeli nie jest obsługiwane, gdy kopia etapowa jest skonfigurowana w działaniu kopiowania. Dozwolone wartości to: `none` (domyślnie), `autoCreate`. |Nie |
| disableMetricsCollection | Fabryka danych zbiera metryki, takie jak DWU magazynu danych SQL do optymalizacji wydajności kopiowania i zaleceń. Jeśli chodzi o to zachowanie, `true` określ, aby go wyłączyć. | Nie (domyślnie jest) `false` |

#### <a name="sql-data-warehouse-sink-example"></a>Przykład ujścia magazynu danych SQL

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

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Ładowanie danych do usługi Azure SQL Data Warehouse za pomocą bazy danych PolyBase

Korzystanie z [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) jest skutecznym sposobem ładowania dużej ilości danych do usługi Azure Synapse Analytics o wysokiej przepływności. Zobaczysz duży zysk w przepływności przy użyciu PolyBase zamiast domyślnego mechanizmu BULKINSERT. Aby uzyskać instruktaż z przypadkiem użycia, zobacz [Ładowanie 1 TB do usługi Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Jeśli dane źródłowe znajdują się w **usłudze Azure Blob, usłudze Azure Data Lake Storage Gen1 lub Azure Data Lake Storage Gen2**, a **format jest zgodny z PolyBase,** można użyć działania kopiowania, aby bezpośrednio wywołać polybase, aby umożliwić usługi Azure SQL Data Warehouse pobierać dane ze źródła. Aby uzyskać szczegółowe informacje, zobacz **[Kopiowanie bezpośrednie przy użyciu polybase](#direct-copy-by-using-polybase)**.
* Jeśli źródłowy magazyn danych i format nie są pierwotnie obsługiwane przez PolyBase, użyj kopii etapowej przy użyciu funkcji **[PolyBase.](#staged-copy-by-using-polybase)** Funkcja kopii etapowej zapewnia również lepszą przepływność. Automatycznie konwertuje dane do formatu zgodnego z PolyBase, przechowuje dane w magazynie obiektów Blob platformy Azure., a następnie wywołuje PolyBase, aby załadować dane do magazynu danych SQL.

>[!TIP]
>Dowiedz się więcej o [najlepszych praktykach dotyczących korzystania z polybase](#best-practices-for-using-polybase).

Następujące ustawienia PolyBase są `polyBaseSettings` obsługiwane w obszarze działania kopiowania:

| Właściwość          | Opis                                                  | Wymagany                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| odrzucanieValue       | Określa liczbę lub procent wierszy, które można odrzucić, zanim kwerenda zakończy się niepowodzeniem.<br/><br/>Dowiedz się więcej o opcjach odrzucania bazy PolyBase w sekcji Argumenty w sekcji [TWORZENIE TABELI ZEWNĘTRZNEJ (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Dozwolone wartości to 0 (domyślnie), 1, 2 itp. | Nie                                            |
| rejectType        | Określa, czy opcja **rejectValue** jest wartością literału, czy wartością procentową.<br/><br/>Dozwolone wartości **to Wartość** (domyślna) i **Procent**. | Nie                                            |
| odrzucanawłaksza wartość | Określa liczbę wierszy do pobrania, zanim PolyBase ponownie obliczy procent odrzuconych wierszy.<br/><br/>Dozwolone wartości to 1, 2 itp. | Tak, jeśli **rejectType** jest **procent .** |
| użyjTypeDefault    | Określa sposób obsługi brakujących wartości w rozdzielonych plikach tekstowych, gdy PolyBase pobiera dane z pliku tekstowego.<br/><br/>Dowiedz się więcej o tej właściwości z sekcji Argumenty w [programie CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Dozwolone wartości to **Prawda** i **Fałsz** (domyślnie).<br><br> | Nie                                            |

### <a name="direct-copy-by-using-polybase"></a>Kopiowanie bezpośrednie przy użyciu polybase

PolyBase magazynu danych SQL obsługuje bezpośrednio obiekt Azure Blob, Usługa Azure Data Lake Storage Gen1 i Usługa Azure Data Lake Storage Gen2. Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, użyj PolyBase, aby skopiować bezpośrednio ze źródłowego magazynu danych do usługi Azure Synapse Analytics. W przeciwnym razie należy użyć [kopii etapowej przy użyciu polybase](#staged-copy-by-using-polybase).

> [!TIP]
> Aby efektywnie kopiować dane do usługi SQL Data Warehouse, dowiedz się więcej z [usługi Azure Data Factory, dzięki czemu odkrywanie szczegółowych informacji z danych podczas korzystania z usługi Data Lake Store za pomocą usługi SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)jest jeszcze łatwiejsze i wygodne.

Jeśli wymagania nie są spełnione, usługa Azure Data Factory sprawdza ustawienia i automatycznie przechodzi z powrotem do mechanizmu BULKINSERT dla przenoszenia danych.

1. **Usługa połączona ze źródłem** jest obsługiwana przez następujące typy i metody uwierzytelniania:

    | Typ obsługiwanego magazynu danych źródłowych                             | Typ uwierzytelniania obsługiwanego źródła                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Obiekt bob Azure](connector-azure-blob-storage.md)                | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |
    | [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | Uwierzytelnianie jednostki usługi                            |
    | [Usługa Azure Data Lake Storage 2. generacji](connector-azure-data-lake-storage.md) | Uwierzytelnianie klucza konta, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >Jeśli usługa Azure Storage jest skonfigurowana z punktem końcowym usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — należy zapoznać się [z wpływem korzystania z punktów końcowych usługi sieci wirtualnej z magazynem platformy Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Poznaj wymagane konfiguracje w fabryce danych z [usługi Azure Blob — uwierzytelnianie tożsamości zarządzanej](connector-azure-blob-storage.md#managed-identity) i usługi Azure Data Lake Storage Gen2 — odpowiednio zarządzana sekcja [uwierzytelniania tożsamości.](connector-azure-data-lake-storage.md#managed-identity)

2. **Format danych źródłowych** to **Parquet**, **ORC**lub **Tekst rozdzielany,** z następującymi konfiguracjami:

   1. Ścieżka folderu nie zawiera filtru wieloznacznego.
   2. Nazwa pliku jest pusta lub wskazuje jeden plik. Jeśli określisz nazwę pliku z symbolami wieloznacznych w działaniu kopiowania, może to być `*` tylko lub `*.*`.
   3. `rowDelimiter`jest **domyślnie**, **\n**, **\r\n**lub **\r**.
   4. `nullValue`jest domyślnie lub ustawiona na pusty `treatEmptyAsNull` **ciąg** (") i pozostaje domyślnie lub ustawiona na true.
   5. `encodingName`jest domyślnie lub ustawiona na **utf-8**.
   6. `quoteChar`, `escapeChar`i `skipLineCount` nie są określone. Obsługa PolyBase pomiń wiersz nagłówka, który można skonfigurować tak, jak `firstRowAsHeader` w podajniku ADF.
   7. `compression`może być **bez kompresji,** **GZip**lub **Deflate**.

3. Jeśli źródłem jest `recursive` folder, w aktywność kopiowania musi być ustawiona na true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` i nie są określone.

>[!NOTE]
>Jeśli źródłem jest folder, uwaga PolyBase pobiera pliki z folderu i wszystkich jego podfolderów i nie pobiera danych z plików, dla których nazwa pliku zaczyna się od podkreślenia (_) lub kropki (.), jak udokumentowano [tutaj - ARGUMENT LOKALIZACJA](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

### <a name="staged-copy-by-using-polybase"></a>Kopia etapowa przy użyciu bazy PolyBase

Jeśli dane źródłowe nie są natywnie zgodne z PolyBase, włącz kopiowanie danych za pośrednictwem tymczasowego przejściowego wystąpienia magazynu obiektów Blob platformy Azure (nie może to być usługa Azure Premium Storage). W takim przypadku usługa Azure Data Factory automatycznie konwertuje dane w celu spełnienia wymagań dotyczących formatu danych polybase. Następnie wywołuje PolyBase załadować dane do magazynu danych SQL. Na koniec czyści dane tymczasowe z magazynu obiektów blob. Zobacz [kopia etapowa, aby](copy-activity-performance-features.md#staged-copy) uzyskać szczegółowe informacje na temat kopiowania danych za pośrednictwem przejściowego wystąpienia magazynu obiektów Blob.

Aby użyć tej funkcji, utwórz [usługę połączona usługi Azure Blob Storage,](connector-azure-blob-storage.md#linked-service-properties) która odwołuje się do konta magazynu platformy Azure z tymczasowym magazynem obiektów blob. Następnie `enableStaging` określ `stagingSettings` i właściwości działania kopiowania, jak pokazano w poniższym kodzie.

>[!IMPORTANT]
>Jeśli usługa Azure Storage jest skonfigurowana z punktem końcowym usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — należy zapoznać się [z wpływem korzystania z punktów końcowych usługi sieci wirtualnej z magazynem platformy Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Poznaj wymagane konfiguracje w usłudze Data Factory z [usługi Azure Blob — uwierzytelnianie tożsamości zarządzanej.](connector-azure-blob-storage.md#managed-identity)

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
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Najważniejsze wskazówki dotyczące korzystania z bazy PolyBase

W poniższych sekcjach przedstawiono najlepsze rozwiązania oprócz tych wymienionych w [najlepszych rozwiązaniach dotyczących usługi Azure Synapse Analytics.](../synapse-analytics/sql/best-practices-sql-pool.md)

#### <a name="required-database-permission"></a>Wymagane uprawnienie do bazy danych

Aby użyć PolyBase, użytkownik, który ładuje dane do usługi SQL Data Warehouse musi mieć [uprawnienie "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) w docelowej bazie danych. Jednym ze sposobów osiągnięcia tego celu jest dodanie użytkownika jako członka **roli db_owner.** Dowiedz się, jak to zrobić w [omówienie magazynu danych SQL](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Rozmiar wiersza i limity typów danych

Obciążenia Bazy są ograniczone do wierszy mniejszych niż 1 MB. Nie można go używać do ładowania do VARCHR(MAX), NVARCHAR(MAX) lub VARBINARY(MAX). Aby uzyskać więcej informacji, zobacz [limity pojemności usługi USŁUGI USŁUGI USŁUGI SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Gdy dane źródłowe mają wiersze większe niż 1 MB, można podzielić tabele źródłowe w pionie na kilka małych. Upewnij się, że największy rozmiar każdego wiersza nie przekracza limitu. Mniejsze tabele można następnie załadować przy użyciu PolyBase i scalone razem w usłudze Azure Synapse Analytics.

Alternatywnie w przypadku danych z tak szerokimi kolumnami można użyć non-PolyBase do załadowania danych przy użyciu podajnika ADF, wyłączając ustawienie "zezwalaj na PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Klasa zasobów magazynu danych SQL

Aby osiągnąć najlepszą możliwą przepływność, należy przypisać większą klasę zasobów do użytkownika, który ładuje dane do magazynu danych SQL za pośrednictwem PolyBase.

#### <a name="polybase-troubleshooting"></a>Rozwiązywanie problemów z bazą danych PolyBase

**Ładowanie do kolumny dziesiętnej**

Jeśli dane źródłowe są w formacie tekstowym lub innych magazynach niezgodnych z PolyBase (przy użyciu kopii etapowej i PolyBase) i zawierają pustą wartość do załadowania do kolumny dziesiętnej magazynu danych SQL, może wystąpić następujący błąd:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Rozwiązaniem jest odznaczenie opcji **"Użyj domyślnego typu**" (jako false) w ustawieniach działania kopiowania -> PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" to konfiguracja natywna PolyBase, która określa sposób obsługi brakujących wartości w rozdzielonych plikach tekstowych, gdy PolyBase pobiera dane z pliku tekstowego. 

**`tableName`w usłudze Azure Synapse Analytics**

W poniższej tabeli przedstawiono przykłady określania właściwości **tableName** w zestawie danych JSON. Pokazuje kilka kombinacji schematu i nazw tabel.

| Schemat bazy danych | Nazwa tabeli | **nazwa tabeli** Właściwość JSON               |
| --------- | ---------- | ----------------------------------------- |
| Dbo       | Mytable    | MyTable lub dbo. MyTable lub [dbo]. [MyTable] |
| dbo1      | Mytable    | dbo1. MyTable lub [dbo1]. [MyTable]          |
| Dbo       | My.Tabela   | [My.Table] lub [dbo]. [Moja.Tabela]            |
| dbo1      | My.Tabela   | [dbo1]. [Moja.Tabela]                         |

Jeśli zostanie wyświetlony następujący błąd, problem może być wartością określoną dla **właściwości tableName.** Zobacz poprzednią tabelę, aby uzyskać poprawny sposób określania wartości właściwości **tableName** JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Kolumny z wartościami domyślnymi**

Obecnie funkcja PolyBase w fabryce danych akceptuje tylko taką samą liczbę kolumn, jak w tabeli docelowej. Przykładem jest tabela z czterema kolumnami, w której jedna z nich jest zdefiniowana z wartością domyślną. Dane wejściowe nadal muszą mieć cztery kolumny. Trzykolumnowy wejściowy zestaw danych daje błąd podobny do następującego komunikatu:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Wartość NULL jest specjalną formą wartości domyślnej. Jeśli kolumna jest nullable, dane wejściowe w obiekcie blob dla tej kolumny może być pusty. Ale nie można go przegapić w zestawie danych wejściowych. PolyBase wstawia wartość NULL dla brakujących wartości w usłudze Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Użyj instrukcji COPY, aby załadować dane do usługi Azure SQL Data Warehouse (wersja zapoznawcza)

[Instrukcja SQL](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) Data Warehouse COPY (wersja zapoznawcza) obsługuje bezpośrednio ładowanie danych z **obiektów Blob platformy Azure i usługi Azure Data Lake Storage Gen2.** Jeśli dane źródłowe spełniają kryteria opisane w tej sekcji, można użyć instrukcji COPY w usłudze ADF, aby załadować dane do usługi Azure SQL Data Warehouse. Usługa Azure Data Factory sprawdza ustawienia i kończy się niepowodzeniem, jeśli kryteria nie są spełnione.

>[!NOTE]
>Obecnie Data Factory obsługuje tylko kopię ze źródeł zgodnych z instrukcjami COPY, o których mowa poniżej.

Użycie instrukcji COPY obsługuje następującą konfigurację:

1. **Źródło połączone usługi i formatu** są z następujących typów i metod uwierzytelniania:

    | Typ obsługiwanego magazynu danych źródłowych                             | Obsługiwany format           | Typ uwierzytelniania obsługiwanego źródła                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Obiekt bob Azure](connector-azure-blob-storage.md)                | [Tekst rozdzielany](format-delimited-text.md)             | Uwierzytelnianie klucza konta, uwierzytelnianie podpisu dostępu współdzielonego, uwierzytelnianie jednostkowe usługi, uwierzytelnianie tożsamości zarządzanej |
    | &nbsp;                                                       | [Parkiet](format-parquet.md)                    | Uwierzytelnianie klucza konta, uwierzytelnianie podpisu dostępu współdzielonego |
    | &nbsp;                                                       | [Orc](format-orc.md)                        | Uwierzytelnianie klucza konta, uwierzytelnianie podpisu dostępu współdzielonego |
    | [Usługa Azure Data Lake Storage 2. generacji](connector-azure-data-lake-storage.md) | [Tekst rozdzielany](format-delimited-text.md)<br/>[Parkiet](format-parquet.md)<br/>[Orc](format-orc.md) | Uwierzytelnianie klucza konta, uwierzytelnianie jednostkowe usługi, uwierzytelnianie tożsamości zarządzanej |

    >[!IMPORTANT]
    >Jeśli usługa Azure Storage jest skonfigurowana z punktem końcowym usługi sieci wirtualnej, należy użyć uwierzytelniania tożsamości zarządzanej — należy zapoznać się [z wpływem korzystania z punktów końcowych usługi sieci wirtualnej z magazynem platformy Azure.](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) Poznaj wymagane konfiguracje w fabryce danych z [usługi Azure Blob — uwierzytelnianie tożsamości zarządzanej](connector-azure-blob-storage.md#managed-identity) i usługi Azure Data Lake Storage Gen2 — odpowiednio zarządzana sekcja [uwierzytelniania tożsamości.](connector-azure-data-lake-storage.md#managed-identity)

2. Ustawienia formatu są następujące:

   1. Dla **Parkietu** `compression` : może być bez **kompresji**, **Snappy**lub **GZip**.
   2. Dla **ORC** `compression` : może **```zlib```** być bez **kompresji**, lub **Snappy**.
   3. W przypadku **tekstu rozdzielanych**:
      1. `rowDelimiter`jest jawnie ustawiona jako **pojedynczy znak** lub "**\r\n**", wartość domyślna nie jest obsługiwana.
      2. `nullValue`pozostaje jako domyślny lub ustawiony na **pusty ciąg** ("").
      3. `encodingName`domyślnie lub ustawiona na **utf-8 lub utf-16**.
      4. `escapeChar`musi być `quoteChar`taka sama jak i nie jest pusta.
      5. `skipLineCount`jest domyślnie lub ustawiona na 0.
      6. `compression`może być **brak kompresji** lub **GZip**.

3. Jeśli źródłem jest `recursive` folder, w aktywność kopiowania musi być ustawiona na true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` i nie są określone.

Następujące ustawienia instrukcji COPY są `allowCopyCommand` obsługiwane w obszarze działania kopiowania:

| Właściwość          | Opis                                                  | Wymagany                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| Defaultvalues | Określa wartości domyślne dla każdej kolumny docelowej w języku SQL DW.  Wartości domyślne we właściwości zastępują ustawienie ograniczenia DEFAULT ustawionego w magazynie danych, a kolumna tożsamości nie może mieć wartości domyślnej. | Nie |
| dodatkoweOpcje | Dodatkowe opcje, które zostaną przekazane do instrukcji SQL DW COPY bezpośrednio w klauzuli "Z" w [instrukcji COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Zacytuj wartość zgodnie z potrzebami, aby wyrównać z wymaganiami instrukcji COPY. | Nie |

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


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Właściwości działania GetMetadata

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapowanie typów danych dla usługi Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mapowanie właściwości przepływu danych

Podczas przekształcania danych w przepływie danych mapowania można odczytywać i zapisywać w tabelach z usługi Azure Synapse Analytics. Aby uzyskać więcej informacji, zobacz [transformację źródła](data-flow-source.md) i [transformację ujścia](data-flow-sink.md) w przepływach danych mapowania.

### <a name="source-transformation"></a>Transformacja źródła

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Opcje źródłowe** transformacji źródła. 

**Wejście:** Wybierz, czy punktujesz źródło przy ```Select * from <table-name>```tabeli (odpowiednik) czy wprowadzasz niestandardową kwerendę SQL.

**Kwerenda:** Jeśli w polu wejściowym zostanie wybrana kwerenda, wprowadź kwerendę SQL dla źródła. To ustawienie zastępuje dowolną tabelę wybraną w zestawie danych. **Klauzule Order By** nie są obsługiwane w tym miejscu, ale można ustawić pełną instrukcję SELECT FROM. Można również użyć funkcji tabeli zdefiniowanych przez użytkownika. **select * z udfGetData()** jest UDF w języku SQL, który zwraca tabelę. Ta kwerenda spowoduje powstanie tabeli źródłowej, która będzie używana w przepływie danych. Korzystanie z zapytań jest również doskonałym sposobem, aby zmniejszyć wiersze do testowania lub wyszukiwania. 

* Przykład SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Rozmiar partii:** Wprowadź rozmiar partii, aby podzielić duże dane na odczyty. W przepływach danych usługa ADF użyje tego ustawienia do ustawiania buforowania kolumnowego platformy Spark. Jest to pole opcji, które będzie używać domyślnych opcji Spark, jeśli pozostanie puste.

**Poziom izolacji:** Domyślna wartość źródeł SQL w przepływie danych mapowania jest odczytywana niezatwierdzone. W tym miejscu można zmienić poziom izolacji na jedną z następujących wartości:
* Odczytu zatwierdzonego
* Odczyt niezatwierdzony
* Powtarzalny odczyt
* Serializacji
* Brak (ignorowanie poziomu izolacji)

![Poziom izolacji](media/data-flow/isolationlevel.png "Poziom izolacji")

### <a name="sink-transformation"></a>Transformacja zlewu

Ustawienia specyficzne dla usługi Azure Synapse Analytics są dostępne na karcie **Ustawienia** transformacji ujścia.

**Metoda aktualizacji:** Określa, jakie operacje są dozwolone w miejscu docelowym bazy danych. Domyślnie zezwalaj tylko na wstawia. Aby zaktualizować, upsert lub usunąć wiersze, transformacja alter-rowu jest wymagana do oznaczania wierszy dla tych akcji. W przypadku aktualizacji, upserts i deletes, kolumna klucza lub kolumny muszą być ustawione, aby określić, który wiersz do zmiany.

**Akcja tabeli:** Określa, czy wszystkie wiersze mają być ponownietworzone, czy usuwane z tabeli docelowej przed zapisaniem.
* Brak: Nie zostaną wykonane żadne działania w tabeli.
* Odtwórz: Tabela zostanie upuszczona i odtworzona. Wymagane, jeśli dynamicznie tworzysz nową tabelę.
* Obcinanie: Wszystkie wiersze z tabeli docelowej zostaną usunięte.

**Włącz przemieszczania:** Określa, czy po zakończeniu pracy w usłudze Azure Synapse Analytics ma być używany [program PolyBase.](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)

**Rozmiar partii**: Określa, ile wierszy jest zapisywanych w każdym zasobniku. Większe rozmiary partii poprawić kompresji i optymalizacji pamięci, ale ryzyko z wyjątków pamięci podczas buforowania danych.

**Skrypty SQL przed i po opublikowaniu:** Wprowadź wielowierszowe skrypty SQL, które będą wykonywane przed zapisaniem danych (przetwarzania wstępnego) i po zapisaniu danych (przetwarzania końcowego) w bazie danych sink

![skrypty przetwarzania sql przed i po](media/data-flow/prepost1.png "Skrypty przetwarzania SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapowanie typów danych dla usługi Azure Synapse Analytics

Podczas kopiowania danych z lub do usługi Azure Synapse Analytics następujące mapowania są używane z typów danych usługi Azure Synapse Analytics do tymczasowych typów danych usługi Azure Data Factory. Zobacz [mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

>[!TIP]
>Zapoznaj się [z tabeli typów danych w usłudze Azure Synapse Analytics](../synapse-analytics/sql/develop-tables-data-types.md) artykuł na temat sql dw obsługiwanych typów danych i obejścia dla nieobsługiwanych.

| Typ danych usługi Azure Synapse Analytics    | Tymczasowy typ danych data factory |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binarny                                | Bajt[]                         |
| bit                                   | Wartość logiczna                        |
| char                                  | Ciąg, Char[]                 |
| date                                  | DateTime                       |
| Datetime (data/godzina)                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | Datetimeoffset                 |
| Wartość dziesiętna                               | Wartość dziesiętna                        |
| Atrybut FILESTREAM (varbinary(max)) | Bajt[]                         |
| Liczba zmiennoprzecinkowa                                 | Double                         |
| image                                 | Bajt[]                         |
| int                                   | Int32                          |
| pieniędzy                                 | Wartość dziesiętna                        |
| nchar                                 | Ciąg, Char[]                 |
| numeryczne                               | Wartość dziesiętna                        |
| nvarchar                              | Ciąg, Char[]                 |
| rzeczywiste                                  | Single                         |
| Rowversion                            | Bajt[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Wartość dziesiętna                        |
| time                                  | przedział_czasu                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid (identyfikator GUID)                           |
| varbinary                             | Bajt[]                         |
| varchar                               | Ciąg, Char[]                 |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze według działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).
