---
title: Kopiowanie danych z programu DB2 przy użyciu Azure Data Factory
description: Informacje o kopiowaniu danych z programu DB2 do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417418"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Kopiowanie danych z programu DB2 przy użyciu Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-onprem-db2-connector.md)
> * [Bieżąca wersja](connector-db2.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych programu DB2. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Ten łącznik bazy danych DB2 jest obsługiwany dla następujących działań:

- [Działanie kopiowania](copy-activity-overview.md) z [obsługiwaną macierzą źródłową/ujścia](copy-activity-overview.md)
- [Działanie Lookup](control-flow-lookup-activity.md)

Dane z bazy danych programu DB2 można kopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika bazy danych DB2 obsługiwane są następujące platformy i wersje programu IBM DB2 z użyciem rozproszonej architektury relacyjnej bazy (DRDA) (SQLAM) w wersji 9, 10 i 11:

* IBM DB2 dla systemu z/OS 12,1
* IBM DB2 dla systemu z/OS 11,1
* IBM DB2 dla systemu z/OS 10,1
* IBM DB2 dla i 7,3
* IBM DB2 dla i 7,2
* IBM DB2 dla i 7,1
* IBM DB2 for LUW 11
* IBM DB2 dla LUW 10,5
* IBM DB2 dla LUW 10,1

>[!TIP]
>Łącznik DB2 jest zbudowany na podstawie Microsoft OLE DB Provider dla bazy danych DB2. Aby rozwiązać problemy z błędami łącznika DB2, zapoznaj się z [dostawca danych kodami błędów](https://docs.microsoft.com/host-integration-server/db2oledbv/data-provider-error-codes#drda-protocol-errors).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime udostępnia wbudowany sterownik programu DB2, dlatego nie trzeba ręcznie instalować żadnego sterownika podczas kopiowania danych z programu DB2.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika bazy danych DB2.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane w przypadku usługi połączonej z bazą danych DB2:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type musi mieć wartość: **DB2** | Tak |
| serwer |Nazwa serwera bazy danych DB2. Możesz określić numer portu następujący po nazwie serwera rozdzielany średnikiem, np. `server:port`. |Tak |
| database |Nazwa bazy danych DB2. |Tak |
| authenticationType |Typ uwierzytelniania używany do łączenia się z bazą danych programu DB2.<br/>Dozwolona wartość to: **podstawowa**. |Tak |
| nazwa użytkownika |Określ nazwę użytkownika w celu nawiązania połączenia z bazą danych programu DB2. |Tak |
| hasło |Określ hasło dla konta użytkownika określonego dla nazwy użytkownika. Oznacz to pole jako element SecureString, aby bezpiecznie przechowywać go w Data Factory, lub [odwoływać się do wpisu tajnego przechowywanego w Azure Key Vault](store-credentials-in-key-vault.md). |Tak |
| pakietcollection | Określ w obszarze, w którym mają być tworzone pakiety do automatycznego tworzenia przez ADF podczas wykonywania zapytania dotyczącego bazy danych. | Nie |
| certificateCommonName | Korzystając z szyfrowania SSL (SSL) lub Transport Layer Security (TLS), należy wprowadzić wartość Nazwa pospolita certyfikatu. | Nie |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie informujący `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`o tym, że przyczyną jest wymagany pakiet nie jest tworzony dla użytkownika. Domyślnie ADF spróbuje utworzyć pakiet w obszarze Kolekcja o nazwie jako użytkownik, który został użyty do nawiązania połączenia z bazą danych DB2. Określ właściwość kolekcji pakietów, aby wskazać miejsce, w którym ma zostać utworzony zestaw danych na potrzeby wysyłania zapytań do bazy danych.

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

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania zestawów danych, zobacz artykuł [zestawy danych](concepts-datasets-linked-services.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu DB2.

Aby skopiować dane z bazy danych DB2, obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type zestawu danych musi być ustawiona na wartość: **Db2Table** | Tak |
| schematy | Nazwa schematu. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tabela | Nazwa tabeli. |Nie (Jeśli określono "zapytanie" w źródle aktywności)  |
| tableName | Nazwa tabeli ze schematem. Ta właściwość jest obsługiwana w celu zapewnienia zgodności z poprzednimi wersjami. Użyj `schema` i `table` dla nowego obciążenia. | Nie (Jeśli określono "zapytanie" w źródle aktywności) |

**Przyklad**

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

Jeśli używasz `RelationalTable` określonego zestawu danych, jest on nadal obsługiwany w stanie takim, w jakim będziesz mieć możliwość korzystania z nowej usługi.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło bazy danych DB2.

### <a name="db2-as-source"></a>DB2 jako źródło

Aby skopiować dane z bazy danych DB2, w sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **Db2Source** | Tak |
| query | Użyj niestandardowego zapytania SQL, aby odczytać dane. Na przykład: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | Nie (Jeśli określono "TableName" w zestawie danych) |

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

Jeśli używasz `RelationalSource` typu source, nadal jest ono obsługiwane w stanie takim, w jakim będziesz mieć możliwość użycia nowego.

## <a name="data-type-mapping-for-db2"></a>Mapowanie typu danych dla programu DB2

Podczas kopiowania danych z programu DB2 następujące mapowania są używane z typów danych DB2 do Azure Data Factory danych pośrednich. Zobacz [Mapowanie schematu i typu danych](copy-activity-schema-and-type-mapping.md) , aby dowiedzieć się, jak działanie kopiowania mapuje schemat źródłowy i typ danych do ujścia.

| Typ bazy danych DB2 | Typ danych pośrednich fabryki danych |
|:--- |:--- |
| BigInt |Int64 |
| plików binarnych |Byte [] |
| Obiekt blob |Byte [] |
| Char |String |
| Obiektów CLOB |String |
| Date |Datetime (data/godzina) |
| DB2DynArray |String |
| DbClob |String |
| Wartość dziesiętna |Wartość dziesiętna |
| DecimalFloat |Wartość dziesiętna |
| Double |Double |
| Liczba zmiennoprzecinkowa |Double |
| Zdjęć |String |
| Liczba całkowita |Int32 |
| LongVarBinary |Byte [] |
| LongVarChar |String |
| LongVarGraphic |String |
| Liczbowe |Wartość dziesiętna |
| Rzeczywiste |Single |
| SmallInt |Int16 |
| Time |przedział_czasu |
| Znacznik czasu |DateTime |
| Liczby |Byte [] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte [] |

## <a name="lookup-activity-properties"></a>Właściwości działania Lookup

Aby dowiedzieć się więcej o właściwościach, sprawdź [działanie Lookup (wyszukiwanie](control-flow-lookup-activity.md)).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
