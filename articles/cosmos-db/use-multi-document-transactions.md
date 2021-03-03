---
title: Korzystanie z wielodokumentowych transakcji w interfejsie API Azure Cosmos DB MongoDB
description: Dowiedz się, jak utworzyć przykładową aplikację powłoki Mongo, która umożliwia wykonywanie transakcji obejmujących wiele dokumentów (semantyka "all-or-Nothing") w przypadku stałej kolekcji w Azure Cosmos DB API for MongoDB 4,0.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: 4d7dcc829f25b7f1b7c6cb6b1d13a664d301bfe6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663656"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Korzystanie z wielodokumentowych transakcji w interfejsie API Azure Cosmos DB MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule utworzysz aplikację powłoki Mongo, która wykonuje transdokumentową transakcję na stałe kolekcje w Azure Cosmos DB API dla MongoDB z serwerem w wersji 4,0.

## <a name="what-are-multi-document-transactions"></a>Co to są transakcje wielodokumentowe?

W Azure Cosmos DB interfejsie API MongoDB operacje na pojedynczym dokumencie są niepodzielne. Transakcje dla wielu dokumentów umożliwiają aplikacjom wykonywanie operacji niepodzielnych w wielu dokumentach. Oferuje semantykę "all-or-Nothing" dla operacji. Po zatwierdzeniu zmiany wprowadzone w ramach transakcji są utrwalane i jeśli transakcja nie powiedzie się, wszystkie zmiany wewnątrz transakcji zostaną odrzucone.

Transakcje wielodokumentowe są zgodne z semantyką **kwasową** :

* Niepodzielność: wszystkie operacje traktowane jako jeden
* Spójność: zatwierdzone dane są prawidłowe
* Izolacja: odizolowana od innych operacji
* Trwałość: dane transakcji są utrwalane, gdy klient zostanie poinformował

## <a name="requirements"></a>Wymagania

Transakcje wielodokumentowe są obsługiwane w ramach kolekcji unsharded w wersji 4,0. Transakcje wielodokumentowe nie są obsługiwane między kolekcjami ani kolekcjami podzielonej na fragmenty.

Wszystkie sterowniki obsługujące protokół MongoDB w wersji 4,0 lub nowszej będą obsługiwały interfejs API Azure Cosmos DB dla transakcji obejmujących wiele dokumentów.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>Uruchamianie transakcji wielodokumentowych w powłoce MongoDB

1. Otwórz wiersz polecenia, przejdź do katalogu, w którym jest zainstalowana powłoka Mongo w wersji 4,0 lub nowszej:

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Utwórz skrypt powłoki Mongo *connect_friends.js* i Dodaj następującą zawartość

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. Uruchom następujące polecenie, aby wykonać transakcję wielodokumentową. Host, port, użytkownik i klucz można znaleźć w Azure Portal.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>Następne kroki

Poznaj nowości w [interfejsie API Azure Cosmos DB MongoDB 4,0](mongodb-feature-support-40.md)
