---
title: Samouczek dotyczący konfigurowania dystrybucji globalnej za pomocą interfejsu API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak skonfigurować dystrybucję globalną przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e167ccf1717c539dc676d85a19eb6eacc3ec0bda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021123"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Konfigurowanie globalnie rozproszonej bazy danych przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB

W tym artykule przedstawiono, jak za pomocą witryny Azure Portal skonfigurować globalnie rozproszoną bazę danych i nawiązać z nią połączenie przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu [interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Weryfikowanie konfiguracji regionalnej 
Prostym sposobem sprawdzenia konfiguracji globalnej za pomocą interfejsu API usługi Cosmos DB dla bazy danych MongoDB jest uruchomienie polecenia *isMaster()* z poziomu powłoki Mongo.

Z poziomu powłoki Mongo:

   ```
      db.isMaster()
   ```
   
Przykładowe wyniki:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Nawiązywanie połączenia z preferowanym regionem 

Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB umożliwia określenie preferencji odczytu kolekcji dla globalnie rozproszonej bazy danych. Na potrzeby odczytów z małymi opóźnieniami i wysokiej dostępności globalnej zaleca się ustawienie preferencji odczytu kolekcji na wartość *najbliższe*. Preferencja odczytu *najbliższe* jest skonfigurowana do odczytu z najbliższego regionu.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Dla aplikacji z podstawowym regionem odczytu/zapisu i pomocniczym regionem na potrzeby scenariuszy odzyskiwania awaryjnego zalecamy ustawienie preferencji odczytu na wartość *preferowane pomocnicze*. Preferencja odczytu *preferowane pomocnicze* jest skonfigurowana do odczytu z regionu pomocniczego, gdy region podstawowy jest niedostępny.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Ponadto jeśli chcesz ręcznie określić regiony odczytu, możesz ustawić tag regionu w swoich preferencjach odczytu.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

To wszystko — na tym kończy się ten samouczek. Aby dowiedzieć się, jak zarządzać spójnością globalnie replikowanego konta, przeczytaj [Poziomy spójności w usłudze Azure Cosmos DB](consistency-levels.md). Natomiast aby uzyskać więcej informacji na temat sposobu działania globalnej replikacji w usłudze Azure Cosmos DB, zobacz [Dystrybuowanie danych globalnie za pomocą usługi Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie dystrybucji globalnej przy użyciu witryny Azure Portal
> * Konfigurowanie dystrybucji globalnej przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB

Teraz możesz przejść do następnego samouczka, aby dowiedzieć się, jak programować lokalnie przy użyciu lokalnego emulatora usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Opracowywanie lokalnie za pomocą emulatora Azure Cosmos DB](local-emulator.md)
