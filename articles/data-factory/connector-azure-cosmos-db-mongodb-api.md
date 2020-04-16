---
title: Kopiowanie danych z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB
description: Dowiedz się, jak kopiować dane z obsługiwanych źródłowych magazynów danych do lub z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB do obsługiwanych magazynów ujścia przy użyciu usługi Data Factory.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b23f46a418f2663531cc121f00b83d00d84e48d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415445"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiowanie danych do lub z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB za pomocą usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Artykuł opiera się na [copy activity w usłudze Azure Data Factory](copy-activity-overview.md), który przedstawia ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik obsługuje tylko dane kopiowania do/z interfejsu API usługi Azure Cosmos DB dla usługi MongoDB. W przypadku interfejsu API SQL zobacz [łącznik interfejsu API programu Cosmos DB SQL](connector-azure-cosmos-db.md). Inne typy interfejsu API nie są teraz obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Można skopiować dane z interfejsu API usługi Azure Cosmos DB dla mongodb do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do interfejsu API usługi Azure Cosmos DB dla mongodb. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i pochłaniacze, zobacz [Obsługiwane magazyny danych i formaty](copy-activity-overview.md#supported-data-stores-and-formats).

Za pomocą interfejsu API usługi Azure Cosmos DB dla łącznika MongoDB można użyć do:

- Kopiowanie danych z i do [interfejsu API usługi Azure Cosmos DB dla mongodb](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapisuj do usługi Azure Cosmos DB jako **wstawianie** lub **upsert**.
- Importowanie i eksportowanie dokumentów JSON w stanie stanu istnieje lub kopiowanie danych z lub do tabelaryczny zestaw danych. Przykłady obejmują bazę danych SQL i plik CSV. Aby skopiować dokumenty w stanie— do lub z plików JSON lub do lub z innej kolekcji usługi Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

W poniższych sekcjach podano szczegółowe informacje na temat właściwości, których można użyć do zdefiniowania jednostek usługi Data Factory, które są specyficzne dla interfejsu API usługi Azure Cosmos DB dla usługi MongoDB.

## <a name="linked-service-properties"></a>Połączone właściwości usługi

Następujące właściwości są obsługiwane dla interfejsu API usługi Azure Cosmos DB dla usługi połączonej mongodb:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** musi być ustawiona na **CosmosDbMongoDbApi**. | Tak |
| Parametry połączenia |Określ parametry połączenia dla interfejsu API usługi Azure Cosmos DB dla usługi MongoDB. Można go znaleźć w portalu Azure - > bloku usługi Cosmos DB -> ciągu połączenia `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`podstawowego lub pomocniczego, ze wzorcem . <br/><br />Można również umieścić hasło w usłudze `password` Azure Key Vault i wyciągnąć konfigurację z ciągu połączenia.Więcej informacji można znaleźć [w witrynie Store credentials w usłudze Azure Key Vault.](store-credentials-in-key-vault.md) |Tak |
| database | Nazwa bazy danych, do której chcesz uzyskać dostęp. | Tak |
| connectVia | [Środowisko wykonawcze integracji](concepts-integration-runtime.md) do nawiązywania połączenia z magazynem danych. Można użyć środowiska uruchomieniowego integracji platformy Azure lub środowiska wykonawczego integracji hostowanego samodzielnie (jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, używany jest domyślny środowiska wykonawczego integracji platformy Azure. |Nie |

**Przykład**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
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

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla interfejsu API usługi Azure Cosmos DB dla zestawu danych MongoDB:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** zestawu danych musi być ustawiona na **CosmosDbMongoDbApiCollection**. |Tak |
| Collectionname |Nazwa kolekcji usługi Azure Cosmos DB. |Tak |

**Przykład**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiowanie właściwości działania

Ta sekcja zawiera listę właściwości, które interfejs API usługi Azure Cosmos DB dla źródła MongoDB i obsługi ujścia.

Aby uzyskać pełną listę sekcji i właściwości, które są dostępne do definiowania działań, zobacz [Potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Interfejs API usługi Azure Cosmos DB dla mongodb jako źródło

Następujące właściwości są obsługiwane w sekcji **Źródło** działania kopiowania:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** źródła działania kopiowania musi być ustawiona na **CosmosDbMongoDbApiSource**. |Tak |
| filtr | Określa filtr zaznaczenia przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, należy pominąć{}ten parametr lub przekazać pusty dokument ( ). | Nie |
| kursMethods.project | Określa pola, które mają być zwracane w dokumentach do projekcji. Aby zwrócić wszystkie pola w pasujących dokumentach, pomiń ten parametr. | Nie |
| kursMethods.sort | Określa kolejność zwracania pasujących dokumentów przez kwerendę. Patrz [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| kursMethods.limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Patrz [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie | 
| kursMethods.skip | Określa liczbę dokumentów do pominięcia i od miejsca, w którym MongoDB zaczyna zwracać wyniki. Patrz [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów do zwrócenia w każdej partii odpowiedzi z wystąpienia MongoDB. W większości przypadków modyfikowanie rozmiaru partii nie wpłynie na użytkownika ani na aplikację. Cosmos DB ogranicza każdą partię nie może przekraczać 40 MB w rozmiarze, który jest sumą batchSize liczba dokumentów rozmiar, więc zmniejszyć tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100)** |

>[!TIP]
>Obsługa podajnika ADF zużywająca dokument BSON w **trybie stricte**. Upewnij się, że zapytanie filtru jest w trybie ścisłym zamiast w trybie powłoki. Więcej opisu można znaleźć w [instrukcji MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Przykład**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Interfejs API usługi Azure Cosmos DB dla mongodb jako zlew

Następujące właściwości są obsługiwane w sekcji **Zlewnik działania kopiowania:**

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość **typu** ujścia działania kopiowania musi być ustawiona na **CosmosDbMongoDbApiSink**. |Tak |
| writeZachody |W tym artykule opisano sposób zapisywania danych w usłudze Azure Cosmos DB. Dozwolone wartości: **insert** i **upsert**.<br/><br/>Zachowanie **upsert** jest zastąpienie dokumentu, jeśli dokument `_id` z tym samym już istnieje; w przeciwnym razie wstaw dokument.<br /><br />**Uwaga:** Fabryka danych automatycznie `_id` generuje dla `_id` dokumentu, jeśli nie jest określony w oryginalnym dokumencie lub przez mapowanie kolumn. Oznacza to, że należy upewnić się, że **aby upsert** działał zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(domyślnie jest **wstawić)** |
| writeBatchSize | Właściwość **writeBatchSize** kontroluje rozmiar dokumentów do zapisania w każdej partii. Można spróbować zwiększyć wartość **writeBatchSize,** aby zwiększyć wydajność i zmniejszenie wartości, jeśli rozmiar dokumentu jest duży. |Nie<br />(wartość domyślna to **10 000**) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania partii przed jej przesuniem czasu. Dozwoloną wartością jest timespan. | Nie<br/>(wartość domyślna **to 00:30:00** - 30 minut) |

>[!TIP]
>Aby zaimportować dokumenty JSON w stanie, w jakim są, zapoznaj się [z sekcją Importuj lub eksportuj dokumenty JSON;](#import-and-export-json-documents) , aby skopiować z danych w kształcie tabelarycznym, należy zapoznać się z [mapowaniem schematu](#schema-mapping).

**Przykład**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importowanie i eksportowanie dokumentów JSON

Możesz użyć tego łącznika usługi Azure Cosmos DB, aby łatwo:

* Kopiowanie dokumentów między dwiema kolekcjami usługi Azure Cosmos DB w stanie — jest.
* Importowanie dokumentów JSON z różnych źródeł do usługi Azure Cosmos DB, w tym z usługi MongoDB, magazynu obiektów Blob platformy Azure, usługi Azure Data Lake Store i innych magazynów opartych na plikach, które obsługuje usługa Azure Data Factory.
* Eksportuj dokumenty JSON z kolekcji usługi Azure Cosmos DB do różnych magazynów opartych na plikach.

Aby uzyskać kopię niezależną od schematu:

* Korzystając z narzędzia Kopiuj dane, wybierz opcję **Eksportuj jako jest do plików JSON lub Kolekcji usługi Cosmos DB.**
* Korzystając z tworzenia działań, wybierz format JSON z odpowiednim magazynem plików dla źródła lub ujścia.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z interfejsu API usługi Azure Cosmos DB dla mongodb do ujścia tabelaryczne lub odwrócone, należy zapoznać się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

Specjalnie do zapisywania w usłudze Cosmos DB, aby upewnić się, że wypełniasz usługę Cosmos DB odpowiednim identyfikatorem obiektu z danych źródłowych, na przykład masz kolumnę "id" w tabeli bazy danych SQL i chcesz użyć tej wartości`_id.$oid`jako identyfikatora dokumentu w MongoDB dla wstawiania/upsert, musisz ustawić odpowiednie mapowanie schematu zgodnie z ścisłą definicją trybu MongoDB ( ) w następujący sposób:

![Identyfikator mapy w zlewie MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po wykonaniu działania kopiowania poniżej identyfikatora obiektu BSON jest generowany w zlewie:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych, które działanie kopiowania obsługuje jako źródła i pochłaniacze w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
