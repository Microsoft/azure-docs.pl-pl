---
title: Kopiowanie danych z MongoDB
description: Dowiedz się, jak skopiować dane z mongo db do obsługiwanych magazynów danych ujścia przy użyciu działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 3b6476b794d2e1b2e9a36aa26f35c247641d44e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418154"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z mongodb przy użyciu usługi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano, jak używać działania kopiowania w usłudze Azure Data Factory do kopiowania danych z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykuł, który przedstawia ogólny przegląd działania kopiowania.

>[!IMPORTANT]
>Wydanie ADF ta nowa wersja złącza MongoDB, który zapewnia lepszą obsługę natywnej MongoDB. Jeśli używasz poprzedniego łącznika MongoDB w rozwiązaniu, które jest obsługiwane jako — jest zgodne z poprzednimi wersjami, zapoznaj się z artykułem [złącza MongoDB (starsza).](connector-mongodb-legacy.md)

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Można skopiować dane z bazy danych MongoDB do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, które są obsługiwane jako źródła/pochłaniacze przez działanie kopiowania, zobacz tabelę [Obsługiwane magazyny danych.](copy-activity-overview.md#supported-data-stores-and-formats)

W szczególności to złącze MongoDB obsługuje **wersje do 3.4**.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych specyficznych dla łącznika MongoDB.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla usługi połączonej MongoDB:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type |Właściwość typu musi być ustawiona na: **MongoDbV2** |Tak |
| Parametry połączenia |Określ parametry połączenia MongoDB `mongodb://[username:password@]host[:port][/[database][?options]]`np. Więcej informacji można znaleźć [w instrukcji MongoDB na parametry połączenia.](https://docs.mongodb.com/manual/reference/connection-string/) <br/><br /> Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia. Więcej informacji można znaleźć [w witrynie Store credentials w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| database | Nazwa bazy danych, do której chcesz uzyskać dostęp. | Tak |
| connectVia | [Środowisko wykonawcze integracji,](concepts-integration-runtime.md) które mają być używane do łączenia się z magazynem danych. Dowiedz się więcej z sekcji [Wymagania wstępne.](#prerequisites) Jeśli nie zostanie określony, używa domyślnego środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
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

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla zestawu danych MongoDB:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu zestawu danych musi być ustawiona na: **MongoDbV2Collection** | Tak |
| Collectionname |Nazwa kolekcji w bazie danych MongoDB. |Tak |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Pipelines](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło MongoDB.

### <a name="mongodb-as-source"></a>MongoDB jako źródło

Następujące właściwości są obsługiwane w sekcji **źródła** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być ustawiona na: **MongoDbV2Source** | Tak |
| filtr | Określa filtr zaznaczenia przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, należy pominąć{}ten parametr lub przekazać pusty dokument ( ). | Nie |
| kursMethods.project | Określa pola, które mają być zwracane w dokumentach do projekcji. Aby zwrócić wszystkie pola w pasujących dokumentach, pomiń ten parametr. | Nie |
| kursMethods.sort | Określa kolejność zwracania pasujących dokumentów przez kwerendę. Patrz [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| kursMethods.limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Patrz [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie |
| kursMethods.skip | Określa liczbę dokumentów do pominięcia i od miejsca, w którym MongoDB zaczyna zwracać wyniki. Patrz [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów do zwrócenia w każdej partii odpowiedzi z wystąpienia MongoDB. W większości przypadków modyfikowanie rozmiaru partii nie wpłynie na użytkownika ani na aplikację. Cosmos DB ogranicza każdą partię nie może przekraczać 40 MB w rozmiarze, który jest sumą batchSize liczba dokumentów rozmiar, więc zmniejszyć tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100)** |

>[!TIP]
>Obsługa podajnika ADF zużywająca dokument BSON w **trybie stricte**. Upewnij się, że zapytanie filtru jest w trybie ścisłym zamiast w trybie powłoki. Więcej opisu można znaleźć w [instrukcji MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
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

## <a name="export-json-documents-as-is"></a>Eksportowanie dokumentów JSON w stanie tożsamości

Tego łącznika MongoDB można użyć do eksportowania dokumentów JSON jako — z kolekcji MongoDB do różnych magazynów opartych na plikach lub do usługi Azure Cosmos DB. Aby osiągnąć taką kopię niezależną od schematu, pomiń sekcję "struktura" (zwaną również *schematem)* w zestawie danych i mapowanie schematu w działaniu kopiowania.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z MongoDB do ujścia tabelaryczne, zapoznaj się [z mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
