---
title: Kopiowanie danych z postgreSQL przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z PostgreSQL do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
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
ms.openlocfilehash: 6d10e7b9b24817eb738172bd0f2d2c3e7f8f2cbf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416762"
---
# <a name="copy-data-from-postgresql-by-using-azure-data-factory"></a>Kopiowanie danych z postgreSql przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-postgresql-connector.md)
> * [Aktualna wersja](connector-postgresql.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych PostgreSQL. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik PostgreSQL jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z bazy danych PostgreSQL do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze PostgreSQL obsługuje PostgreSQL **w wersji 7.4 i wyższej**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime zapewnia wbudowany sterownik PostgreSQL, począwszy od wersji 3.7, dlatego nie trzeba ręcznie zainstalować żadnego sterownika.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika PostgreSQL.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej PostgreSQL:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **PostgreSql** | Tak |
| Parametry połączenia | Ciąg połączenia ODBC do łączenia się z usługą Azure Database for PostgreSQL. <br/>Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć w poniższych przykładach i [poświadczeniach sklepu w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

Typowym ciągiem `Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`połączenia jest . Więcej właściwości, które można ustawić w przypadku:

| Właściwość | Opis | Opcje | Wymagany |
|:--- |:--- |:--- |:--- |
| Metoda szyfrowania (EM)| Metoda używana przez sterownik do szyfrowania danych wysyłanych między sterownikiem a serwerem bazy danych. Przykład.`EncryptionMethod=<0/1/6>;`| 0 (Brak szyfrowania) **(domyślnie)** / 1 (SSL) / 6 (RequestSSL) | Nie |
| Certyfikat ValidateServerCertificate (VSC) | Określa, czy sterownik sprawdza poprawność certyfikatu wysyłanego przez serwer bazy danych po włączeniu szyfrowania SSL (Metoda szyfrowania=1). Przykład.`ValidateServerCertificate=<0/1>;`| 0 (Wyłączone) **(domyślnie)** / 1 (włączone) | Nie |

**Przykład:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
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
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>;Database=<database>;Port=<port>;UID=<username>;",
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

Jeśli używasz postgreSql połączone usługi z następującym ładunku, nadal jest obsługiwany jako - jest, podczas gdy zaleca się, aby użyć nowego w przyszłości.

**Poprzednia ładowność:**

```json
{
    "name": "PostgreSqlLinkedService",
    "properties": {
        "type": "PostgreSql",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych PostgreSQL.

Aby skopiować dane z PostgreSQL, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **PostgreSqlTable** | Tak |
| Schematu | Nazwa schematu. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. Użyj `schema` `table` i dla nowego obciążenia. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "PostgreSQLDataset",
    "properties":
    {
        "type": "PostgreSqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<PostgreSQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, nadal jest obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło PostgreSQL.

### <a name="postgresql-as-source"></a>PostgreSQL jako źródło

Aby skopiować dane z PostgreSQL, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **PostgreSqlSource** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"query": "SELECT * FROM \"MySchema\".\"MyTable\""`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

> [!NOTE]
> W nazwach schematów i tabel rozróżniana jest wielkość liter. Ująć `""` je w (cudzysłowy podwójne) w kwerendzie.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<PostgreSQL input dataset name>",
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
                "type": "PostgreSqlSource",
                "query": "SELECT * FROM \"MySchema\".\"MyTable\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
