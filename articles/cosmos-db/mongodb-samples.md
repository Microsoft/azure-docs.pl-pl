---
title: Tworzenie aplikacji platformy Node.js za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Samouczek, w którym przedstawiono tworzenie bazy danych online za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: devx-track-js
ms.openlocfilehash: 5a1a3b28e7bf0ef5d6aa7c3339925d4f66f1e3a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322669"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Tworzenie aplikacji przy użyciu środowiska Node.js i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB 
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js dla MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

W tym przykładzie przedstawiono sposób tworzenia aplikacji konsolowej przy użyciu środowiska Node.js i interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

Aby użyć tego przykładu, musisz:

* [Utworzyć](create-mongodb-dotnet.md#create-account) konto usługi Cosmos skonfigurowane do korzystania z interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
* Pobrać informacje o [parametrach połączenia](connect-mongodb-account.md).

## <a name="create-the-app"></a>Tworzenie aplikacji

1. Utwórz plik *app.js*, a następnie skopiuj i wklej poniższy kod.

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Opcjonalnie**: jeśli używasz **sterownika MongoDB Node.js 2.2**, zamień następujący fragment kodu:

    Oryginalny kod:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Należy zamienić na:

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Zmodyfikuj następujące zmienne w pliku *app.js* zgodnie z ustawieniami konta (dowiedz się, jak znaleźć [parametry połączenia](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > **Sterownik MongoDB Node.js 3.0** wymaga kodowania znaków specjalnych w haśle usługi Cosmos DB. Upewnij się, że znaki „=” są kodowane jako %3D
    >
    > Przykład: hasło *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* jest kodowane jako *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > **Sterownik MongoDB Node.js 2.2** nie wymaga kodowania znaków specjalnych w haśle usługi Cosmos DB.
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Otwórz swój ulubiony terminal, uruchom polecenie **npm install mongodb --save**, następnie uruchom aplikację za pomocą polecenia **node app.js**

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
