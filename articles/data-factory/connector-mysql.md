---
title: Kopiowanie danych z programu MySQL przy użyciu Azure Data Factory
description: Więcej informacji na temat łącznika MySQL w programie Azure Data Factory umożliwia kopiowanie danych z bazy danych MySQL do magazynu danych obsługiwanego jako ujścia.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: jingwang
ms.openlocfilehash: b6a2253a4a124fe5e3725863c799f91714e66cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375259"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Kopiowanie danych z programu MySQL przy użyciu Azure Data Factory

> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-mysql-connector.md)
> * [Bieżąca wersja](connector-mysql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych MySQL. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

>[!NOTE]
>Aby skopiować dane z usługi lub do [Azure Database for MySQL](../mysql/overview.md) , użyj [łącznika](connector-azure-database-for-mysql.md)wyspecjalizowane Azure Database for MySQL.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Ten łącznik MySQL jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych MySQL można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika MySQL obsługuje **program MySQL w wersji 5,6, 5,7 i 8,0**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik MySQL, począwszy od wersji 3,7, dlatego nie trzeba ręcznie instalować żadnego sterownika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika MySQL.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane w przypadku usługi połączonej MySQL:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi być ustawiona na wartość: **MySQL** | Tak |
| Parametry połączenia | Określ informacje, które są konieczne do nawiązania połączenia z wystąpieniem Azure Database for MySQL.<br/> Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć `password` konfigurację z parametrów połączenia. Zapoznaj się z poniższymi przykładami i [Zapisz poświadczenia w Azure Key Vault](store-credentials-in-key-vault.md) artykule, aby uzyskać więcej szczegółów. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

Typowe parametry połączenia to `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>` . Więcej właściwości, które można ustawić dla danego przypadku:

| Właściwość | Opis | Opcje | Wymagane |
|:--- |:--- |:--- |:--- |
| SSLMode | Ta opcja określa, czy sterownik używa szyfrowania i weryfikacji TLS podczas łączenia z bazą danych MySQL. Np  `SSLMode=<0/1/2/3/4>` ..| WYŁĄCZONE (0)/PREFEROWANe (1) **(wartość domyślna)** /wymagane (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nie |
| SSLCert | Pełna ścieżka i nazwa pliku PEM zawierającego certyfikat SSL używany do udowodnienia tożsamości klienta. <br/> Aby określić klucz prywatny służący do szyfrowania tego certyfikatu przed jego wysłaniem do serwera, użyj `SSLKey` właściwości.| | Tak, w przypadku korzystania z dwukierunkowej weryfikacji protokołu SSL. |
| SSLKey | Pełna ścieżka i nazwa pliku zawierającego klucz prywatny służący do szyfrowania certyfikatu po stronie klienta podczas dwukierunkowej weryfikacji protokołu SSL.|  | Tak, w przypadku korzystania z dwukierunkowej weryfikacji protokołu SSL. |
| UseSystemTrustStore | Ta opcja umożliwia określenie, czy certyfikat urzędu certyfikacji ma być używany z magazynu zaufania systemu, czy z określonego pliku PEM. Na przykład `UseSystemTrustStore=<0/1>;`| Włączone (1)/wyłączone (0) **(wartość domyślna)** | Nie |

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

**Przykład: Przechowuj hasło w Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
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

Jeśli używasz połączonej usługi MySQL z następującym ładunkiem, nadal jest ona obsługiwana w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

**Poprzedni ładunek:**

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych MySQL.

Aby skopiować dane z programu MySQL, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na: **MySQL** | Tak |
| tableName | Nazwa tabeli w bazie danych MySQL. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

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

Jeśli używasz `RelationalTable` określonego zestawu danych, jest on nadal obsługiwany w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MySQL.

### <a name="mysql-as-source"></a>MySQL jako źródło

Aby skopiować dane z programu MySQL, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MySqlSource** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"SELECT * FROM MyTable"`. | Nie (Jeśli określono "TableName" w zestawie danych) |

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

Jeśli używasz `RelationalSource` typu source, nadal jest ono obsługiwane w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

## <a name="data-type-mapping-for-mysql"></a>Mapowanie typu danych dla programu MySQL

Podczas kopiowania danych z programu MySQL następujące mapowania są używane z typów danych MySQL do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ danych MySQL | Typ danych pośrednich fabryki danych |
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


## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
