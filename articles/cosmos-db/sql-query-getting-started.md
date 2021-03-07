---
title: Wprowadzenie do zapytań SQL w Azure Cosmos DB
description: Dowiedz się, jak używać zapytań SQL do wykonywania zapytań dotyczących danych z Azure Cosmos DB. Przykładowe dane można przekazać do kontenera w Azure Cosmos DB i wykonać zapytanie.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 21b26bae5fe04d48214a0c62cbb530ea89ad1448
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426029"
---
# <a name="getting-started-with-sql-queries"></a>Wprowadzenie do zapytań SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W Azure Cosmos DB kontach interfejsu API SQL istnieją dwa sposoby odczytywania danych:

**Odczyty punktów** — można wykonać wyszukiwanie klucza/wartości dla *identyfikatora pojedynczego elementu* i klucza partycji. Kombinacja *identyfikatora elementu* i klucza partycji jest kluczem, a sam element jest wartością. W przypadku dokumentu o 1 KB punkt odczytuje zazwyczaj jednostkę 1 [jednostki żądania](request-units.md) z opóźnieniem poniżej 10 ms. Odczyty punktów zwracają pojedynczy element.

Poniżej przedstawiono kilka przykładów **odczytu punktów** przy użyciu każdego zestawu SDK:

- [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.cosmos.container.readitemasync)
- [Zestaw SDK Java](/java/api/com.azure.cosmos.cosmoscontainer.readitem#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Zestaw SDK dla platformy Node.js](/javascript/api/@azure/cosmos/item#read-requestoptions-)
- [Zestaw SDK dla języka Python](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

**Zapytania SQL** — możesz wykonywać zapytania dotyczące danych, pisząc zapytania przy użyciu STRUCTURED Query Language (SQL) jako języka zapytań JSON. Zapytania zawsze mają koszt co najmniej 2,3 jednostek żądań i, ogólnie rzecz biorąc, będzie miało większe i więcej zmiennych opóźnień niż odczyty punktów. Zapytania mogą zwracać wiele elementów.

Większość obciążeń mocno odczytanych na Azure Cosmos DB należy używać kombinacji odczytów punktów i zapytań SQL. Jeśli musisz tylko odczytać pojedynczy element, odczyty punktów są tańsze i szybsze niż zapytania. Odczyty punktów nie muszą używać aparatu zapytań do uzyskiwania dostępu do danych i mogą odczytywać dane bezpośrednio. Oczywiście nie jest możliwe, że wszystkie obciążenia wyłącznie odczytują dane przy użyciu odczytów punktów, dlatego obsługa języka SQL jako język zapytań i [indeksowanie schematu niezależny od](index-overview.md) zapewniają bardziej elastyczny sposób uzyskiwania dostępu do danych.

Poniżej przedstawiono kilka przykładów wykonywania **zapytań SQL** przy użyciu poszczególnych zestawów SDK:

- [Zestaw SDK platformy .NET](./sql-api-dotnet-v3sdk-samples.md#query-examples)
- [Zestaw SDK Java](./sql-api-java-sdk-samples.md#query-examples)
- [Zestaw SDK dla platformy Node.js](./sql-api-nodejs-samples.md#item-examples)
- [Zestaw SDK dla języka Python](./sql-api-python-samples.md#item-examples)

W pozostałej części tego dokumentu pokazano, jak rozpocząć pisanie zapytań SQL w Azure Cosmos DB. Zapytania SQL można uruchamiać za pomocą zestawu SDK lub Azure Portal.

## <a name="upload-sample-data"></a>Przekazywanie przykładowych danych

Na koncie Cosmos DB interfejsu API SQL Otwórz [Eksplorator danych](./data-explorer.md) , aby utworzyć kontener o nazwie `Families` . Po jego utworzeniu Użyj przeglądarki struktury danych, aby znaleźć i otworzyć ją. W `Families` kontenerze zobaczysz opcję znajdującą się `Items` bezpośrednio pod nazwą kontenera. Otwórz tę opcję i zobaczysz przycisk na pasku menu na środku ekranu, aby utworzyć nowy element. Ta funkcja zostanie użyta do utworzenia poniższych elementów JSON.

### <a name="create-json-items"></a>Tworzenie elementów JSON

Następujące 2 elementy JSON są dokumentami dotyczącymi rodzin Andersen i Wakefield. Obejmują one rodzice, dzieci i informacje o rejestracji. 

Pierwszy element zawiera ciągi, liczby, wartości logiczne, tablice i właściwości zagnieżdżone:

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

Drugi element używa `givenName` i `familyName` zamiast `firstName` i `lastName` :

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

* Kontener Cosmos to niezależna od schematu Kolekcja elementów JSON. Relacje między elementami kontenera i między nimi są niejawnie przechwytywane przez zawieranie, a nie według klucza podstawowego i relacji klucza obcego. Ta funkcja jest ważna dla sprzężeń wewnątrz elementu, które są opisane w [sprzężeniach w Azure Cosmos DB](sql-query-join.md).

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT — klauzula](sql-query-select.md)
