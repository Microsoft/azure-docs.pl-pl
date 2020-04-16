---
title: Kopiowanie danych z mysql przy użyciu usługi Azure Data Factory
description: Dowiedz się więcej o łączniku MySQL w usłudze Azure Data Factory, który umożliwia kopiowanie danych z bazy danych MySQL do magazynu danych obsługiwanego jako ujście.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: jingwang
ms.openlocfilehash: 8467bbe1512e45342b86ff62d51a3f66b3096f03
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418137"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopiowanie danych z mysql przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-mysql-connector.md)
> * [Aktualna wersja](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych MySQL. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!NOTE]
>Aby skopiować dane z lub do usługi [Azure Database for MySQL,](../mysql/overview.md) użyj specjalistycznego [łącznika usługi Azure Database for MySQL.](connector-azure-database-for-mysql.md)

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik MySQL jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Dane z bazy danych MySQL można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze MySQL obsługuje MySQL **w wersji 5.6 i 5.7.**

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime zapewnia wbudowany sterownik MySQL, począwszy od wersji 3.7, dlatego nie trzeba ręcznie zainstalować żadnego sterownika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika MySQL.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Dla połączonej usługi MySQL są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **MySql** | Tak |
| Parametry połączenia | Określ informacje potrzebne do nawiązania połączenia z usługą Azure Database dla wystąpienia MySQL.<br/> Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

Typowym ciągiem `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`połączenia jest . Więcej właściwości, które można ustawić w przypadku:

| Właściwość | Opis | Opcje | Wymagany |
|:--- |:--- |:--- |:--- |
| Tryb SSL | Ta opcja określa, czy sterownik używa szyfrowania TLS i weryfikacji podczas łączenia się z MySQL. Przykład.`SSLMode=<0/1/2/3/4>`| WYŁĄCZONE (0) / PREFEROWANE (1) **(domyślnie)** / WYMAGANE (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Nie |
| Użyj SystemuTrustStore | Ta opcja określa, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Na przykład `UseSystemTrustStore=<0/1>;`| Włączone (1) / Wyłączone (0) **(domyślnie)** | Nie |

**Przykład:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: hasło magazynu w usłudze Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
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

Jeśli używasz usługi połączonej MySQL z następującym ładunkiem, nadal jest obsługiwana w stanie as-is, podczas gdy sugeruje się użycie nowej usługi w przyszłości.

**Poprzednia ładowność:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych MySQL.

Aby skopiować dane z MySQL, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **MySqlTable** | Tak |
| tableName | Nazwa tabeli w bazie danych MySQL. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MySQL.

### <a name="mysql-as-source"></a>MySQL jako źródło

Aby skopiować dane z MySQL, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **MySqlSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="data-type-mapping-for-mysql"></a>Mapowanie typów danych dla MySQL

Podczas kopiowania danych z MySQL, następujące mapowania są używane z typów danych MySQL do usługi Azure Data Factory tymczasowych typów danych. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych MySQL | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
