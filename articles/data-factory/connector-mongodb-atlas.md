---
title: Kopiuj dane z MongoDB atlasu
description: Informacje o kopiowaniu danych z MongoDB Atlas do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: b2f77e4bd8df66084937da3dd203ebb71d9a3511
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368799"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>Kopiowanie danych z MongoDB Atlas przy użyciu Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w Azure Data Factory do kopiowania danych z bazy danych szczytowej usługi MongoDB. Jest ona oparta na [przeglądzie działania kopiowania](copy-activity-overview.md) , która przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Dane z bazy danych MongoDB Atlas można skopiować do dowolnego obsługiwanego magazynu danych ujścia. Listę magazynów danych obsługiwanych jako źródła/ujścia przez działanie kopiowania można znaleźć w tabeli [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) .

W przypadku tego łącznika MongoDB Atlas obsługuje **wersje do 4,2**.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz Azure Integration Runtime do kopiowania, upewnij się, że dodano [Azure Integration Runtime adresy](azure-integration-runtime-ip-addresses.md) IP obszaru efektywnego do listy dostępu do MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek Data Factory specyficznych dla łącznika MongoDB Atlas.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla połączonej usługi MongoDB w Atlasie:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **MongoDbAtlas** |Tak |
| Parametry połączenia |Określ parametry połączenia MongoDB Atlas, np. `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` . <br/><br /> Parametry połączenia można również umieścić w Azure Key Vault. Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md) . |Tak |
| database | Nazwa bazy danych, do której chcesz uzyskać dostęp. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Dowiedz się więcej z sekcji [wymagania wstępne](#prerequisites) . Jeśli nie zostanie określony, zostanie użyta domyślna Azure Integration Runtime. |Nie |

**Przykład:**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla zestawu danych szczytu MongoDB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type zestawu danych musi być ustawiona na wartość: **MongoDbAtlasCollection** | Tak |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB Atlas. |Tak |

**Przykład:**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz artykuł [potoki](concepts-pipelines-activities.md) . Ta sekcja zawiera listę właściwości obsługiwanych przez źródło w Atlasie MongoDB.

### <a name="mongodb-atlas-as-source"></a>MongoDB Atlas jako źródło

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type źródła działania Copy musi być ustawiona na wartość: **MongoDbAtlasSource** | Tak |
| filter | Określa filtr wyboru przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, Pomiń ten parametr lub Przekaż pusty dokument ( {} ). | Nie |
| cursorMethods. Project | Określa pola do zwrócenia w dokumentach dla projekcji. Aby zwrócić wszystkie pola w pasujących dokumentach, Pomiń ten parametr. | Nie |
| cursorMethods. Sort | Określa kolejność, w której zapytanie zwraca pasujące dokumenty. Zapoznaj się z [kursorem. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| cursorMethods. limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Zapoznaj się z [kursorem. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie |
| cursorMethods. Skip | Określa liczbę dokumentów do pominięcia i miejsce, w którym MongoDB Atlas zaczyna zwracać wyniki. Zapoznaj się z [kursorem. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów do zwrócenia w każdej partii odpowiedzi z wystąpienia MongoDB Atlas. W większości przypadków modyfikacja rozmiaru partii nie wpłynie na użytkownika ani aplikację. Cosmos DB ogranicza każda partia zadań nie może przekroczyć 40MB rozmiaru, która jest sumą rozmiaru batchSize dokumentów, dlatego Zmniejsz tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100**) |

>[!TIP]
>ADF obsługuje dokument BSON w **trybie Strict**. Upewnij się, że zapytanie filtru jest w trybie Strict zamiast w trybie powłoki. Więcej opisu można znaleźć w [podręczniku MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>Eksportowanie dokumentów JSON jako-is

Za pomocą tego łącznika MongoDB Atlas można eksportować dokumenty JSON jako-pochodzą z kolekcji w Atlasie MongoDB do różnych magazynów opartych na plikach lub do Azure Cosmos DB. Aby uzyskać taką kopię schematu niezależny od, Pomiń sekcję "struktura" (nazywaną również *schematem*) w temacie zestaw danych i schemat w działaniu kopiowania.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z MongoDB Atlas do tabelarycznego ujścia, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
