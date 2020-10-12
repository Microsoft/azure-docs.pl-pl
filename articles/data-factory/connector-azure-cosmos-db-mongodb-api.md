---
title: Kopiuj dane z interfejsu API Azure Cosmos DB dla MongoDB
description: Informacje o kopiowaniu danych z obsługiwanych magazynów danych źródłowych do programu lub z interfejsu API Azure Cosmos DB dla usługi MongoDB do obsługiwanych magazynów ujścia przy użyciu Data Factory.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415445"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiowanie danych do lub z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB za pomocą usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB. Artykuł jest oparty na [działaniu kopiowania w Azure Data Factory](copy-activity-overview.md), co przedstawia ogólne omówienie działania kopiowania.

>[!NOTE]
>Ten łącznik obsługuje tylko kopiowanie danych do/z interfejsu API Azure Cosmos DB dla usługi MongoDB. Aby uzyskać informacje dotyczące interfejsu API SQL, zobacz [Łącznik usługi SQL api Cosmos DB](connector-azure-cosmos-db.md). Inne typy interfejsów API nie są obecnie obsługiwane.

## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z interfejsu API Azure Cosmos DB MongoDB do dowolnego obsługiwanego magazynu danych ujścia lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do interfejsu API Azure Cosmos DB dla MongoDB. Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia, zobacz [obsługiwane magazyny i formaty danych](copy-activity-overview.md#supported-data-stores-and-formats).

Możesz użyć interfejsu API Azure Cosmos DB dla łącznika MongoDB, aby:

- Skopiuj dane z i do [interfejsu API Azure Cosmos DB dla MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Zapisz do Azure Cosmos DB jako **INSERT** lub **upsert**.
- Importuj i Eksportuj dokumenty JSON jako-is lub Kopiuj dane z lub do tabelarycznego zestawu danych. Przykładami mogą być baza danych SQL i plik CSV. Aby skopiować dokumenty jako-is do lub z plików JSON lub z innej kolekcji Azure Cosmos DB, zobacz Importowanie lub eksportowanie dokumentów JSON.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, których można użyć do zdefiniowania Data Factory jednostek, które są specyficzne dla interfejsu API Azure Cosmos DB dla MongoDB.

## <a name="linked-service-properties"></a>Właściwości połączonej usługi

Następujące właściwości są obsługiwane dla interfejsu API Azure Cosmos DB dla połączonej usługi MongoDB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** musi być ustawiona na wartość **CosmosDbMongoDbApi**. | Tak |
| Parametry połączenia |Określ parametry połączenia dla interfejsu API Azure Cosmos DB dla MongoDB. Można go znaleźć w Azure Portal-> Cosmos DB w bloku > podstawowego lub pomocniczego, z wzorcem `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` . <br/><br />Możesz również wprowadzić hasło w Azure Key Vault i ściągnąć  `password`   konfigurację z parametrów połączenia.Aby uzyskać więcej informacji, zobacz temat [poświadczenia sklepu w Azure Key Vault](store-credentials-in-key-vault.md)   .|Tak |
| database | Nazwa bazy danych, do której chcesz uzyskać dostęp. | Tak |
| Właściwością connectvia | [Integration Runtime](concepts-integration-runtime.md) używany do nawiązywania połączenia z magazynem danych. Możesz użyć Azure Integration Runtime lub własnego środowiska Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli ta właściwość nie jest określona, zostanie użyta domyślna Azure Integration Runtime. |Nie |

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

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane przez interfejs API Azure Cosmos DB dla zestawu danych MongoDB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** zestawu danych musi być ustawiona na wartość **CosmosDbMongoDbApiCollection**. |Tak |
| CollectionName |Nazwa kolekcji Azure Cosmos DB. |Tak |

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

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Ta sekcja zawiera listę właściwości, które są obsługiwane przez interfejs API Azure Cosmos DB na potrzeby obsługi źródła i ujścia MongoDB.

Aby zapoznać się z pełną listą sekcji i właściwości, które są dostępne do definiowania działań, zobacz [potoki](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Interfejs API Azure Cosmos DB dla MongoDB jako źródło

W sekcji **Źródło** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** źródła działania Copy musi być ustawiona na wartość **CosmosDbMongoDbApiSource**. |Tak |
| filter | Określa filtr wyboru przy użyciu operatorów zapytań. Aby zwrócić wszystkie dokumenty w kolekcji, Pomiń ten parametr lub Przekaż pusty dokument ( {} ). | Nie |
| cursorMethods. Project | Określa pola do zwrócenia w dokumentach dla projekcji. Aby zwrócić wszystkie pola w pasujących dokumentach, Pomiń ten parametr. | Nie |
| cursorMethods. Sort | Określa kolejność, w której zapytanie zwraca pasujące dokumenty. Zapoznaj się z [kursorem. Sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nie |
| cursorMethods. limit | Określa maksymalną liczbę dokumentów zwracanych przez serwer. Zapoznaj się z [kursorem. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nie | 
| cursorMethods. Skip | Określa liczbę dokumentów do pominięcia oraz miejsce, z którego MongoDB zaczyna zwracać wyniki. Zapoznaj się z [kursorem. Skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nie |
| batchSize | Określa liczbę dokumentów do zwrócenia w każdej partii odpowiedzi z wystąpienia MongoDB. W większości przypadków modyfikacja rozmiaru partii nie wpłynie na użytkownika ani aplikację. Cosmos DB ogranicza każda partia zadań nie może przekroczyć 40MB rozmiaru, która jest sumą rozmiaru batchSize dokumentów, dlatego Zmniejsz tę wartość, jeśli rozmiar dokumentu jest duży. | Nie<br/>(wartość domyślna to **100**) |

>[!TIP]
>ADF obsługuje dokument BSON w **trybie Strict**. Upewnij się, że zapytanie filtru jest w trybie Strict zamiast w trybie powłoki. Więcej opisu można znaleźć w [podręczniku MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Interfejs API Azure Cosmos DB dla MongoDB jako ujścia

W sekcji **ujścia** działania kopiowania są obsługiwane następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość **Type** ujścia działania Copy musi być ustawiona na wartość **CosmosDbMongoDbApiSink**. |Tak |
| writeBehavior |Opisuje sposób zapisywania danych w Azure Cosmos DB. Dozwolone wartości: **INSERT** i **upsert**.<br/><br/>Zachowanie **upsert** polega na zastępowaniu dokumentu, jeśli dokument o tym samym `_id` już istnieje; w przeciwnym razie Wstaw dokument.<br /><br />**Uwaga**: `_id` Jeśli element `_id` nie został określony w oryginalnym dokumencie lub mapowaniu kolumn, Data Factory automatycznie generuje dokument dla dokumentu. Oznacza to, że aby program **upsert** działał zgodnie z oczekiwaniami, dokument ma identyfikator. |Nie<br />(wartość domyślna to **INSERT**) |
| writeBatchSize | Właściwość **writeBatchSize** określa rozmiar dokumentów do zapisu w każdej partii. Możesz spróbować zwiększyć wartość **writeBatchSize** , aby zwiększyć wydajność i zmniejszyć wartość w przypadku dużego rozmiaru dokumentu. |Nie<br />(wartość domyślna to **10 000**) |
| writeBatchTimeout | Czas oczekiwania na zakończenie operacji wstawiania wsadowego przed przekroczeniem limitu czasu. Dozwolona wartość to TimeSpan. | Nie<br/>(wartość domyślna to **00:30:00** – 30 minut) |

>[!TIP]
>Aby zaimportować dokumenty JSON, należy zapoznać się z sekcją [Importowanie lub eksportowanie dokumentów JSON](#import-and-export-json-documents) . Aby skopiować dane w kształcie tabelarycznym, zapoznaj się z [mapowaniem schematu](#schema-mapping).

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

Za pomocą tego łącznika Azure Cosmos DB można łatwo:

* Kopiuj dokumenty między dwoma kolekcjami Azure Cosmos DB.
* Zaimportuj dokumenty JSON z różnych źródeł do Azure Cosmos DB, w tym z MongoDB, Azure Blob Storage, Azure Data Lake Store i innych magazynów opartych na plikach, które Azure Data Factory obsługiwane przez program.
* Eksportuj dokumenty JSON z kolekcji Azure Cosmos DB do różnych magazynów opartych na plikach.

Aby uzyskać kopię schematu niezależny od:

* Korzystając z narzędzia Kopiowanie danych, wybierz opcję **Eksportuj jako plik JSON lub Cosmos DB kolekcji** .
* W przypadku korzystania z funkcji tworzenia działań wybierz format JSON z odpowiednim magazynem plików dla źródła lub ujścia.

## <a name="schema-mapping"></a>Mapowanie schematu

Aby skopiować dane z interfejsu API Azure Cosmos DB MongoDB do tabelarycznego ujścia lub odwrotnie, zapoznaj się z [mapowaniem schematu](copy-activity-schema-and-type-mapping.md#schema-mapping).

W odniesieniu do zapisu w Cosmos DB, aby upewnić się, że Cosmos DB z prawidłowym IDENTYFIKATORem obiektu z danych źródłowych, na przykład w tabeli usługi SQL Database istnieje kolumna "ID" i chcesz użyć jej jako identyfikatora dokumentu w MongoDB dla Insert/Upsert, należy ustawić odpowiednie mapowanie schematu zgodnie z definicją w trybie Strict MongoDB ( `_id.$oid` ) w następujący sposób:

![Identyfikator mapy w MongoDB ujścia](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Po wykonaniu działania kopiowania poniżej BSON ObjectId jest generowany w ujścia:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać listę magazynów danych obsługiwanych przez działanie kopiowania jako źródła i ujścia w Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
