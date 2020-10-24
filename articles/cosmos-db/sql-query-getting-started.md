---
title: Wprowadzenie do zapytań SQL w Azure Cosmos DB
description: Dowiedz się, jak używać zapytań SQL do wykonywania zapytań dotyczących danych z Azure Cosmos DB. Przykładowe dane można przekazać do kontenera w Azure Cosmos DB i wykonać zapytanie.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: a95fe72df152e297114f4bde59b11137c6accdb6
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488150"
---
# <a name="getting-started-with-sql-queries"></a>Wprowadzenie do zapytań SQL

W Azure Cosmos DB kontach interfejsu API SQL istnieją dwa sposoby odczytywania danych:

**Odczyty punktów** — można wykonać wyszukiwanie klucza/wartości dla *identyfikatora pojedynczego elementu* i klucza partycji. Kombinacja *identyfikatora elementu* i klucza partycji jest kluczem, a sam element jest wartością. W przypadku dokumentu o 1 KB punkt odczytuje zazwyczaj jednostkę 1 [jednostki żądania](request-units.md) z opóźnieniem poniżej 10 ms. Odczyty punktów zwracają pojedynczy element.

**Zapytania SQL** — możesz wykonywać zapytania dotyczące danych, pisząc zapytania przy użyciu STRUCTURED Query Language (SQL) jako języka zapytań JSON. Zapytania zawsze mają koszt co najmniej 2,3 jednostek żądań i, ogólnie rzecz biorąc, będzie miało większe i więcej zmiennych opóźnień niż odczyty punktów. Zapytania mogą zwracać wiele elementów.

Większość obciążeń mocno odczytanych na Azure Cosmos DB należy używać kombinacji odczytów punktów i zapytań SQL. Jeśli musisz tylko odczytać pojedynczy element, odczyty punktów są tańsze i szybsze niż zapytania. Odczyty punktów nie muszą używać aparatu zapytań do uzyskiwania dostępu do danych i mogą odczytywać dane bezpośrednio. Oczywiście nie jest możliwe, że wszystkie obciążenia wyłącznie odczytują dane przy użyciu odczytów punktów, dlatego obsługa języka SQL jako język zapytań i [indeksowanie schematu niezależny od](index-overview.md) zapewniają bardziej elastyczny sposób uzyskiwania dostępu do danych.

Poniżej przedstawiono kilka przykładów odczytu punktów przy użyciu każdego zestawu SDK:

- [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet)
- [Zestaw SDK Java](/java/api/com.azure.cosmos.cosmoscontainer.readitem?preserve-view=true&view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Zestaw SDK dla platformy Node.js](/javascript/api/@azure/cosmos/item?preserve-view=true&view=azure-node-latest#read-requestoptions-)
- [Zestaw SDK dla języka Python](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

W pozostałej części tego dokumentu pokazano, jak rozpocząć pisanie zapytań SQL w Azure Cosmos DB. Zapytania SQL można uruchamiać za pomocą zestawu SDK lub Azure Portal.

## <a name="upload-sample-data"></a>Przekazywanie przykładowych danych

Na koncie Cosmos DB interfejsu API SQL Utwórz kontener o nazwie `Families` . Utwórz dwa proste elementy JSON w kontenerze. Większość przykładowych zapytań można uruchomić w dokumentacji zapytania Azure Cosmos DB przy użyciu tego zestawu danych.

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

Drugi element używa `givenName` i `familyName` zamiast `firstName` i `lastName` .

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

Poniższe zapytanie zwraca elementy, w których `id` pole jest zgodne `AndersenFamily` . Ponieważ jest to `SELECT *` zapytanie, dane wyjściowe zapytania są kompletnym elementem JSON. Aby uzyskać więcej informacji na temat składni SELECT, zobacz [SELECT Statement](sql-query-select.md).

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

Poniższe zapytanie umożliwia przeformatowanie danych wyjściowych JSON do innego kształtu. Zapytanie projektuje nowy obiekt JSON `Family` z dwoma wybranymi polami `Name` i `City` , gdy miasto adresu jest taka sama jak stan. "NY, NY" pasuje do tego przypadku.

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

Następujące zapytanie zwraca wszystkie nazwy elementów podrzędnych w rodzinie `id` , których dopasowania są `WakefieldFamily` uporządkowane według miasta.

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

* Ponieważ interfejs API SQL działa na wartościach JSON, zajmuje on się jednostkami w kształcie drzewa zamiast wierszy i kolumn. Możesz odwoływać się do węzłów drzewa na dowolnym dowolnym poziomie, na przykład `Node1.Node2.Node3…..Nodem` , podobnie jak w przypadku odwołania dwuczęściowego `<table>.<column>` w języku SQL ANSI.

* Ponieważ język zapytań działa z danymi bez schematu, system typu musi być powiązany dynamicznie. To samo wyrażenie może spowodować uzyskiwanie różnych typów w różnych elementach. Wynikiem zapytania jest prawidłowa wartość JSON, ale nie ma gwarancji, że jest to stała schemat.  

* Usługa Azure Cosmos DB obsługuje tylko ścisłe elementy JSON. System typów i wyrażenia są ograniczone do obsługi tylko typów JSON. Aby uzyskać więcej informacji, zobacz [specyfikację JSON](https://www.json.org/).  

* Kontener Cosmos to niezależna od schematu Kolekcja elementów JSON. Relacje między elementami kontenera i między nimi są niejawnie przechwytywane przez zawieranie, a nie według klucza podstawowego i relacji klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrz elementu omówionych w dalszej części tego artykułu.

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT — klauzula](sql-query-select.md)