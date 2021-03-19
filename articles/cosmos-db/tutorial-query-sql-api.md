---
title: 'Samouczek: jak wykonywać zapytania przy użyciu języka SQL w Azure Cosmos DB?'
description: 'Samouczek: informacje na temat wykonywania zapytań przy użyciu zapytań SQL w Azure Cosmos DB przy użyciu zapytania plac zabaw'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: c206b68495b09988e49f37433ccd5616acd29cb7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337782"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>Samouczek: Wykonywanie zapytań w usłudze Azure Cosmos DB przy użyciu interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Interfejs API SQL](./introduction.md) usługi Azure Cosmos DB obsługuje wykonywanie zapytań względem dokumentów przy użyciu języka SQL. Ten artykuł zawiera przykładowy dokument i dwa przykładowe zapytania SQL oraz ich wyniki.

W tym artykule opisano następujące zadania: 

> [!div class="checklist"]
> * Wykonywanie zapytań o dane za pomocą usługi SQL

## <a name="sample-document"></a>Przykładowy dokument

Zapytania SQL w tym artykule korzystają z następującego przykładowego dokumentu.

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

## <a name="where-can-i-run-sql-queries"></a>Gdzie można uruchomić zapytania SQL?

Zapytania można uruchamiać przy użyciu Eksploratora danych w witrynie Azure Portal, za pomocą [interfejsu API REST i zestawów SDK](sql-api-sdk-dotnet.md), a nawet [placu zabaw dla zapytań](https://www.documentdb.com/sql/demo), który uruchamia zapytania na istniejącym zestawie danych przykładowych.

Aby uzyskać więcej informacji na temat zapytań SQL zobacz:
* [Zapytanie SQL i składnia SQL](sql-query-getting-started.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że masz konto usługi Azure Cosmos DB i kolekcję. Nie masz żadnego z tych zasobów? Skorzystaj z [pięciominutowego samouczka Szybki start](create-cosmosdb-resources-portal.md).

## <a name="example-query-1"></a>Przykładowe zapytanie 1

Zgodnie z powyższym przykładowym dokumentem rodziny, następujące zapytanie SQL zwraca dokumenty, w których pole ID jest zgodne `WakefieldFamily` . Ponieważ jest to instrukcja `SELECT *`, dane wyjściowe zapytania są kompletnym dokumentem JSON:

**Zapytanie**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**Wyniki**

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

## <a name="example-query-2"></a>Przykładowe zapytanie 2

Następne zapytanie zwraca wszystkie podane nazwy elementów podrzędnych w rodzinie, której identyfikator pasuje `WakefieldFamily` .

**Zapytanie**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**Wyniki**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>Następne kroki

W tym samouczku wykonano następujące zadania:

> [!div class="checklist"]
> * Przedstawiono sposób wykonywania zapytań przy użyciu języka SQL  

Możesz teraz przejść do następnego samouczka, aby dowiedzieć się, jak dystrybuować swoje dane globalnie.

> [!div class="nextstepaction"]
> [Globalna dystrybucja danych](tutorial-global-distribution-sql-api.md)