---
title: Kopiuj dane do i z Azure Database for PostgreSQL
description: Informacje o kopiowaniu danych do i z Azure Database for PostgreSQL przy użyciu działania kopiowania w potoku Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296509"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiowanie danych do i z Azure Database for PostgreSQL przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania funkcji działania kopiowania w programie Azure Data Factory do kopiowania danych z Azure Database for PostgreSQL. Jest ona oparta na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md) artykule, który przedstawia ogólne omówienie działania kopiowania.

Ten łącznik jest wyspecjalizowany dla [usługi Azure Database for PostgreSQL](../postgresql/overview.md). Aby skopiować dane z ogólnej bazy danych PostgreSQL znajdującej się lokalnie lub w chmurze, użyj [łącznika PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik Azure Database for PostgreSQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z Azure Database for PostgreSQL można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Można też skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do Azure Database for PostgreSQL. Listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

Azure Data Factory udostępnia wbudowany sterownik umożliwiający nawiązywanie połączeń. W związku z tym nie trzeba ręcznie instalować żadnego sterownika, aby można było używać tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania Data Factory jednostek specyficznych dla łącznika Azure Database for PostgreSQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla Azure Database for PostgreSQL połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzurePostgreSql**. | Tak |
| Parametry połączenia | Parametry połączenia ODBC do połączenia z Azure Database for PostgreSQL.<br/>Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Aby uzyskać więcej informacji, zobacz następujące przykłady i [przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md) . | Tak |
| Właściwością connectvia | Ta właściwość reprezentuje [środowisko Integration Runtime](concepts-integration-runtime.md) , które ma być używane do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>` . Poniżej przedstawiono więcej właściwości, które można ustawić dla danego przypadku:

| Właściwość | Opis | Opcje | Wymagany |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Metoda wykorzystywana przez sterownik do szyfrowania danych przesyłanych między sterownikiem a serwerem bazy danych. Na przykład  `EncryptionMethod=<0/1/6>;`| 0 (bez szyfrowania) **(wartość domyślna)** /1 (SSL)/6 (RequestSSL) | Nie |
| ValidateServerCertificate (VSC) | Określa, czy sterownik sprawdza poprawność certyfikatu wysyłanego przez serwer bazy danych, gdy włączone jest szyfrowanie SSL (metoda szyfrowania = 1). Na przykład  `ValidateServerCertificate=<0/1>;`| 0 (wyłączone) **(wartość domyślna)** /1 (włączone) | Nie |

**Przykład**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Przykład**:

**_Zapisz hasło w Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [zestawy danych w Azure Data Factory](concepts-datasets-linked-services.md). Ta sekcja zawiera listę właściwości, które Azure Database for PostgreSQL obsługiwane w zestawach danych.

Aby skopiować dane z Azure Database for PostgreSQL, ustaw właściwość Type zestawu danych na _ * AzurePostgreSqlTable * *. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość **AzurePostgreSqlTable** | Tak |
| tableName | Nazwa tabeli | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przykład**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [potoki i działania w Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez źródło Azure Database for PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Usługa Azure Database for PostgreSql jako źródło

Aby skopiować dane z Azure Database for PostgreSQL, ustaw typ źródła w działaniu Copy na **AzurePostgreSqlSource**. W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość **AzurePostgreSqlSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `SELECT * FROM mytable` lub `SELECT * FROM "MyTable"` . Uwaga w PostgreSQL Nazwa jednostki jest traktowana jako niezależna od wielkości liter, jeśli nie jest ujęta w cudzysłów. | Nie (Jeśli określono Właściwość TableName w zestawie danych) |

**Przykład**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL jako ujścia

Aby skopiować dane do Azure Database for PostgreSQL, w sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type ujścia działania Copy musi być ustawiona na wartość **AzurePostgreSQLSink**. | Tak |
| preCopyScript | Określ zapytanie SQL dla działania kopiowania, które ma zostać wykonane, zanim zapiszesz dane w Azure Database for PostgreSQL w każdym przebiegu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| writeBatchSize | Wstawia dane do tabeli Azure Database for PostgreSQL, gdy rozmiar buforu osiągnie writeBatchSize.<br>Dozwolona wartość jest liczbą całkowitą reprezentującą liczbę wierszy. | Nie (domyślnie 10 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed upływem limitu czasu.<br>Dozwolone wartości to ciągi TimeSpan. Przykładem jest 00:30:00 (30 minut). | Nie (domyślnie 00:00:30) |

**Przykład**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby uzyskać więcej informacji o właściwościach, zobacz [aktywność Lookup w Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
