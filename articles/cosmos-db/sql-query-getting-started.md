---
title: Wprowadzenie do zapytań SQL w Azure Cosmos DB
description: Dowiedz się, jak używać zapytań SQL do wykonywania zapytań dotyczących danych z Azure Cosmos DB. Przykładowe dane można przekazać do kontenera w Azure Cosmos DB i wykonać zapytanie.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873339"
---
# <a name="getting-started-with-sql-queries"></a>Wprowadzenie do zapytań SQL

Azure Cosmos DB konta interfejsu API SQL obsługują wykonywanie zapytań dotyczących elementów przy użyciu Structured Query Language (SQL) jako języka zapytań JSON. Celem projektowania języka zapytań Azure Cosmos DB jest:

* Obsługa języka SQL, jednego z najpopularniejszych i popularnych języków zapytań zamiast tworzenia nowych języków zapytań. SQL zawiera formalny model programowania dla zaawansowanych zapytań dotyczących elementów JSON.  

* Używaj modelu programowania JavaScript jako podstawy dla języka zapytań. System typów języka JavaScript, obliczanie wyrażeń i wywołania funkcji są katalogami głównymi interfejsu API SQL. Te katalogi główne zapewniają naturalny model programowania dla funkcji, takich jak relacyjne projekcje, hierarchiczne Nawigowanie między elementami JSON, samosprzężenia, zapytania przestrzenne i wywołania funkcji zdefiniowanych przez użytkownika (UDF), które są zapisywane w całości w języku JavaScript.

## <a name="upload-sample-data"></a>Przekazywanie przykładowych danych

Na koncie Cosmos DB interfejsu API SQL Utwórz kontener o nazwie `Families`. Utwórz dwa proste elementy JSON w kontenerze. Większość przykładowych zapytań można uruchomić w Azure Cosmos DB dokumentach zapytań przy użyciu tego zestawu danych.

### <a name="create-json-items"></a>Tworzenie elementów JSON

Poniższy kod tworzy dwa proste elementy JSON dotyczące rodzin. Proste elementy JSON dla rodzin Andersen i Wakefield obejmują rodzice, dzieci i informacje o rejestracji. Pierwszy element zawiera ciągi, liczby, wartości logiczne, tablice i właściwości zagnieżdżone.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

`givenName` Drugi element używa `familyName` i zamiast `firstName` i. `lastName`

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
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Wykonywanie zapytań dotyczących elementów JSON

Wypróbuj kilka zapytań dotyczących danych JSON, aby poznać niektóre kluczowe aspekty języka zapytań SQL Azure Cosmos DB.

Poniższe zapytanie zwraca elementy, w których pole `id` jest zgodne `AndersenFamily`. Ponieważ jest to `SELECT *` zapytanie, dane wyjściowe zapytania są kompletnym elementem JSON. Aby uzyskać więcej informacji na temat składni SELECT, zobacz [SELECT Statement](sql-query-select.md). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Wyniki zapytania są następujące: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Poniższe zapytanie umożliwia przeformatowanie danych wyjściowych JSON do innego kształtu. Zapytanie projektuje nowy obiekt JSON `Family` z dwoma wybranymi polami `Name` i `City`, gdy miasto adresu jest taka sama jak stan. "NY, NY" pasuje do tego przypadku.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Wyniki zapytania są następujące:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Następujące zapytanie zwraca wszystkie nazwy elementów podrzędnych w rodzinie, których `id` dopasowania `WakefieldFamily`są uporządkowane według miasta.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Wyniki są następujące:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Uwagi

W powyższych przykładach przedstawiono kilka aspektów języka zapytań Cosmos DB:  

* Ponieważ interfejs API SQL działa na wartościach JSON, zajmuje on się jednostkami w kształcie drzewa zamiast wierszy i kolumn. Możesz odwoływać się do węzłów drzewa na dowolnym dowolnym poziomie, na `Node1.Node2.Node3…..Nodem`przykład, podobnie jak `<table>.<column>` w przypadku odwołania DWUCZĘŚCIOWEGO w języku SQL ANSI.

* Ponieważ język zapytań działa z danymi bez schematu, system typu musi być powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynikiem zapytania jest prawidłowa wartość JSON, ale nie ma gwarancji, że jest to stała schemat.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. System typów i wyrażenia są ograniczone do obsługi tylko typów JSON. Aby uzyskać więcej informacji, zobacz [specyfikację JSON](https://www.json.org/).  

* Kontener Cosmos to niezależna od schematu Kolekcja elementów JSON. Relacje między elementami kontenera i między nimi są niejawnie przechwytywane przez zawieranie, a nie według klucza podstawowego i relacji klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrz elementu omówionych w dalszej części tego artykułu.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT — klauzula](sql-query-select.md)
