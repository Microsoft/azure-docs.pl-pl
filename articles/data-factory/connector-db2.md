---
title: Kopiowanie danych z usługi DB2 przy użyciu usługi Azure Data Factory
description: Dowiedz się, jak skopiować dane z bazy danych DB2 do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: jingwang
ms.openlocfilehash: 2c2071e4b2a3daa528c7d01f64e38247b063e6f1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417418"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiowanie danych z bazy danych DB2 przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-db2-connector.md)
> * [Bieżąca wersja](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych DB2. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik bazy danych DB2 jest obsługiwany dla następujących działań:

- [Kopiowanie aktywności](copy-activity-overview.md) z [obsługiwaną macierzą źródło/ujście](copy-activity-overview.md)
- [Działanie odnośnika](control-flow-lookup-activity.md)

Można skopiować dane z bazy danych DB2 do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze DB2 obsługuje następujące platformy i wersje IBM DB2 z programem SQL Access Manager (SQLAM) w wersji 9, 10 i 11:

* IBM DB2 dla z/OS 12.1
* IBM DB2 dla z/OS 11.1
* IBM DB2 dla z/OS 10.1
* IBM DB2 dla i 7.3
* IBM DB2 dla i 7.2
* IBM DB2 dla i 7.1
* IBM DB2 dla LUW 11
* IBM DB2 dla LUW 10.5
* IBM DB2 dla LUW 10.1

>[!TIP]
>Łącznik DB2 jest zbudowany na podstawie dostawcy bazy danych Microsoft OLE DB dla DB2. Aby rozwiązać problem z błędami łącznika DB2, zapoznaj się [z kodami błędów dostawcy danych](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime zapewnia wbudowany sterownik DB2, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z DB2.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika DB2.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej DB2:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **Db2** | Tak |
| serwer |Nazwa serwera DB2. Można określić numer portu po nazwie serwera rozdzielonej `server:port`przez dwukropek np. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych DB2.<br/>Dozwoloną wartością jest: **Podstawowa**. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika, aby połączyć się z bazą danych DB2. |Tak |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać go w fabryce danych lub [odwołaj się do klucza tajnego przechowywanego w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| pakietKolekcja | Określ, gdzie potrzebne pakiety są automatycznie tworzone przez podajnik ADF podczas wykonywania zapytań o bazę danych. | Nie |
| certyfikatCommonName | W przypadku korzystania z szyfrowania SSL (Secure Sockets Layer) lub Transport Layer Security (TLS) należy wprowadzić wartość nazwy pospolitej certyfikatu. | Nie |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

> [!TIP]
> Jeśli zostanie wyświetlony komunikat `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`o błędzie informujący o tym, że powodem jest, gdy potrzebny pakiet nie jest tworzony dla użytkownika. Domyślnie ADF spróbuje utworzyć pakiet w ramach kolekcji o nazwie jako użytkownik użyty do połączenia z DB2. Określ właściwość kolekcji pakietów, aby wskazać w obszarze, gdzie chcesz ADF utworzyć potrzebne pakiety podczas wykonywania zapytań bazy danych.

**Przykład:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [o zestawach danych.](concepts-datasets-linked-services.md) Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych DB2.

Aby skopiować dane z DB2, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **Db2Table** | Tak |
| Schematu | Nazwa schematu. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tabela | Nazwa tabeli. |Nie (jeśli określono "zapytanie" w źródle działania)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z powrotem. Użyj `schema` `table` i dla nowego obciążenia. | Nie (jeśli określono "zapytanie" w źródle działania) |

**Przykład**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Jeśli używasz `RelationalTable` wpisanego zestawu danych, jest on nadal obsługiwany w stanie gotowym do użycia, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło DB2.

### <a name="db2-as-source"></a>DB2 jako źródło

Aby skopiować dane z DB2, w sekcji **źródła** działania kopiowania obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **Db2Source** | Tak |
| query | Użyj niestandardowej kwerendy SQL, aby odczytać dane. Na przykład: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nie (jeśli określono "nazwa tabela" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Jeśli używasz `RelationalSource` wpisanego źródła, jest ono nadal obsługiwane w stanie as-is, podczas gdy zaleca się użycie nowego w przyszłości.

## <a name="data-type-mapping-for-db2"></a>Mapowanie typu danych dla bazy danych DB2

Podczas kopiowania danych z DB2 następujące mapowania są używane z typów danych DB2 do tymczasowe typy danych usługi Azure Data Factory. Zobacz [Mapowania schematu i typu danych,](copy-activity-schema-and-type-mapping.md) aby dowiedzieć się, jak aktywność kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ bazy danych DB2 | Tymczasowy typ danych fabryki danych |
|:--- |:--- |
| Bigint |Int64 |
| plików binarnych |Bajt[] |
| Obiekt blob |Bajt[] |
| Char |Ciąg |
| Clob |Ciąg |
| Date |Datetime (data/godzina) |
| DB2DynArray |Ciąg |
| DbClob ( DbClob ) |Ciąg |
| Wartość dziesiętna |Wartość dziesiętna |
| Podłoga dziesiętna |Wartość dziesiętna |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Graficzny |Ciąg |
| Liczba całkowita |Int32 |
| LongVarBinary (LongVarBinary) |Bajt[] |
| LongVarChar (LongVarChar) |Ciąg |
| LongVarGraphic (LongVarGraphic) |Ciąg |
| Liczbowe |Wartość dziesiętna |
| Rzeczywiste |Single |
| Smallint |Int16 |
| Time |przedział_czasu |
| Znacznik czasu |DateTime |
| Varbinary |Bajt[] |
| Varchar |Ciąg |
| Grafika vargraficzna |Ciąg |
| Xml |Bajt[] |

## <a name="lookup-activity-properties"></a>Właściwości działania odnośnika

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie odnośnika](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
