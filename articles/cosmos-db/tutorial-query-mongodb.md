---
title: Wykonywanie zapytań o dane za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
description: Dowiedz się, jak wykonywać zapytania dotyczące danych z interfejsu API Azure Cosmos DB dla MongoDB za pomocą poleceń powłoki MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93074322"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Wykonywanie zapytań o dane przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md) obsługuje [zapytania bazy danych MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytania o dane przechowywane w bazie danych Cosmos za pomocą powłoki bazy danych MongoDB

Możesz rozpocząć pracę, korzystając z przykładów zawartych w tym dokumencie i obejrzeć wideo [Query Azure Cosmos DB with MongoDB shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) (Wykonywanie zapytań względem usługi Azure Cosmos DB za pomocą powłoki bazy danych MongoDB).

## <a name="sample-document"></a>Przykładowy dokument

Zapytania w tym artykule korzystają z następującego przykładowego dokumentu.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="example-query-1"></a><a id="examplequery1"></a> Przykładowe zapytanie 1 

Bazując na powyższym przykładowym dokumencie dotyczącym rodziny, następujące zapytanie zwraca dokumenty, dla których pole id ma wartość `WakefieldFamily`.

**Zapytanie**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**Wyniki**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>Przykładowe zapytanie 2 

Następne zapytanie zwraca wszystkie dzieci w rodzinie. 

**Zapytanie**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**Wyniki**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a> Przykładowe zapytanie 3 

Następne zapytanie zwraca wszystkie zarejestrowane rodziny. 

**Zapytanie**

```bash
db.families.find( { "isRegistered" : true })
``` 

**Wyniki**

Żaden dokument nie zostanie zwrócony. 

## <a name="example-query-4"></a><a id="examplequery4"></a> Przykładowe zapytanie 4

Następne zapytanie zwraca wszystkie niezarejestrowane rodziny. 

**Zapytanie**

```bash
db.families.find( { "isRegistered" : false })
``` 

**Wyniki**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a> Przykładowe zapytanie 5

Następne zapytanie zwraca wszystkie rodziny, które nie zostały zarejestrowane i dla których stan to NY. 

**Zapytanie**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**Wyniki**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a> Przykładowe zapytanie 6

Następne zapytanie zwraca wszystkie rodziny, w których dzieci chodzą do 8 klasy.

**Zapytanie**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**Wyniki**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a> Przykładowe zapytanie 7

Następne zapytanie zwraca wszystkie rodziny, w których rozmiar tablicy z dziećmi to 3.

**Zapytanie**

```bash
db.Family.find( {children: { $size:3} } )
```

**Wyniki**

Żadne wyniki nie zostaną zwrócone, ponieważ nie ma żadnych rodzin z więcej niż 2 dzieci. To zapytanie powiedzie się i zwróci pełny dokument tylko wtedy, gdy parametr będzie równy 2.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-sql-api.md)

