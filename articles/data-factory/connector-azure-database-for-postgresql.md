---
title: Kopiowanie danych do i z bazy danych platformy Azure dla postgreSQL
description: Dowiedz się, jak kopiować dane do i z usługi Azure Database for PostgreSQL przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410464"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopiowanie danych do i z bazy danych platformy Azure dla postgreSQL przy użyciu usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób kopiowania funkcji działania w usłudze Azure Data Factory do kopiowania danych z usługi Azure Database for PostgreSQL. Opiera się na [kopiuj działania w usłudze Azure Data Factory](copy-activity-overview.md) artykuł, który przedstawia ogólne omówienie działania kopiowania.

Ten łącznik jest wyspecjalizowany dla [usługi Azure Database for PostgreSQL](../postgresql/overview.md). Aby skopiować dane z ogólnej bazy danych PostgreSQL znajdującej się lokalnie lub w chmurze, użyj [łącznika PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik usługi Azure Database for PostgreSQL jest obsługiwany dla następujących działań:

- [Kopiowanie działania](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z usługi Azure Database for PostgreSQL do dowolnego obsługiwanego magazynu danych ujścia. Można też skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do usługi Azure Database for PostgreSQL. Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze, zobacz [tabelę Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

Usługa Azure Data Factory udostępnia wbudowany sterownik umożliwiający łączność. W związku z tym nie trzeba ręcznie zainstalować żadnego sterownika, aby użyć tego łącznika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek usługi Data Factory specyficznych dla usługi Azure Database dla łącznika PostgreSQL.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Database for PostgreSQL:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **AzurePostgreSql**. | Tak |
| Parametry połączenia | Ciąg połączenia ODBC do łączenia się z usługą Azure Database for PostgreSQL.<br/>Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia. Aby uzyskać więcej informacji, zobacz poniższe przykłady i [poświadczenia sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | Ta właściwość reprezentuje [środowisko uruchomieniowe integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

Typowym ciągiem `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`połączenia jest . Oto więcej właściwości, które można ustawić w przypadku:

| Właściwość | Opis | Opcje | Wymagany |
|:--- |:--- |:--- |:--- |
| Metoda szyfrowania (EM)| Metoda używana przez sterownik do szyfrowania danych wysyłanych między sterownikiem a serwerem bazy danych. Na przykład,`EncryptionMethod=<0/1/6>;`| 0 (Brak szyfrowania) **(domyślnie)** / 1 (SSL) / 6 (RequestSSL) | Nie |
| Certyfikat ValidateServerCertificate (VSC) | Określa, czy sterownik sprawdza poprawność certyfikatu wysyłanego przez serwer bazy danych po włączeniu szyfrowania SSL (Metoda szyfrowania=1). Na przykład,`ValidateServerCertificate=<0/1>;`| 0 (Wyłączone) **(domyślnie)** / 1 (włączone) | Nie |

**Przykład:**

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

**Przykład:**

***Przechowywanie hasła w usłudze Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz [Zestawy danych w usłudze Azure Data Factory](concepts-datasets-linked-services.md). Ta sekcja zawiera listę właściwości, które usługa Azure Database for PostgreSQL obsługuje w zestawach danych.

Aby skopiować dane z usługi Azure Database for PostgreSQL, ustaw właściwość typu zestawu danych na **AzurePostgreSqlTable**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na **AzurePostgreSqlTable** | Tak |
| tableName | Nazwa tabeli | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład:**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Potoki i działania w usłudze Azure Data Factory](concepts-pipelines-activities.md). Ta sekcja zawiera listę właściwości obsługiwanych przez usługę Azure Database dla źródła PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>Usługa Azure Database for PostgreSql jako źródło

Aby skopiować dane z usługi Azure Database for PostgreSQL, ustaw typ źródła w działaniu kopiowania na **AzurePostgreSqlSource**. Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na **AzurePostgreSqlSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"` | Nie (jeśli określono właściwość tableName w zestawie danych) |

**Przykład:**

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
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Usługa Azure Database for PostgreSQL jako zlew

Aby skopiować dane do usługi Azure Database for PostgreSQL, następujące właściwości są obsługiwane w sekcji ujście działania **kopiowania:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu ujścia działania kopiowania musi być ustawiona na **AzurePostgreSQLSink**. | Tak |
| preCopyScript | Określ kwerendę SQL dla działania kopiowania do wykonania przed zapisaniem danych w usłudze Azure Database for PostgreSQL w każdym uruchomieniu. Ta właściwość służy do czyszczenia wstępnie załadowanych danych. | Nie |
| writeBatchSize | Wstawia dane do tabeli Usługi Azure Database for PostgreSQL, gdy rozmiar buforu osiągnie writeBatchSize.<br>Dozwolona wartość jest liczbą całkowitą reprezentującą liczbę wierszy. | Nie (wartość domyślna to 10 000) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed jej limitem czasu.<br>Dozwolone wartości to ciągi Timespan. Przykładem jest 00:30:00 (30 minut). | Nie (domyślnie 00:00:30) |

**Przykład:**

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

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby uzyskać więcej informacji na temat właściwości, zobacz [Działanie odnośnika w usłudze Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [Obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
